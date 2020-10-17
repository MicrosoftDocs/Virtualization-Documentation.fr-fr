---
title: Interface hypercall
description: Interface hypercall d’hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 88014624350a13b9f2672b3a08ac4161463fa602
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120538"
---
# <a name="hypercall-interface"></a>Interface hypercall

L’hyperviseur fournit un mécanisme d’appel pour les invités. De tels appels sont désignés sous le terme d’hyperappels. Chaque hypercall définit un ensemble de paramètres d’entrée et/ou de sortie. Ces paramètres sont spécifiés en termes d’une structure de données basée sur la mémoire. Tous les éléments des structures de données d’entrée et de sortie sont remplis à des frontières naturelles jusqu’à 8 octets (autrement dit, les éléments de deux octets doivent se trouver sur des limites de deux octets, et ainsi de suite).

Une deuxième Convention d’appel hypercall peut éventuellement être utilisée pour un sous-ensemble d’hyperappels, en particulier ceux qui ont au moins deux paramètres d’entrée et aucun paramètre de sortie. Lors de l’utilisation de cette Convention d’appel, les paramètres d’entrée sont passés dans des registres à usage général.

Une troisième Convention d’appel hypercall peut éventuellement être utilisée pour un sous-ensemble d’hyperappels où le bloc de paramètres d’entrée a une taille allant jusqu’à 112 octets. Lors de l’utilisation de cette Convention d’appel, les paramètres d’entrée sont passés dans les registres, y compris les registres XMM volatils.

Les structures de données d’entrée et de sortie doivent être placées en mémoire sur une limite de 8 octets et complétées sur un multiple de 8 octets. Les valeurs des zones de remplissage sont ignorées par l’hyperviseur.

Pour la sortie, l’hyperviseur est autorisé (mais n’est pas garanti) à remplacer les régions de remplissage. Si elle remplace des zones de remplissage, elle écrira des zéros.

## <a name="hypercall-classes"></a>Classes hypercall

Il existe deux classes d’hyperappels : simple et Rep (Short pour « REPEAT »). Un simple hypercall effectue une opération unique et un jeu de paramètres d’entrée et de sortie de taille fixe. Un hyperappel de REP agit comme une série d’hyperappels simples. Outre un ensemble de paramètres d’entrée et de sortie de taille fixe, les hyperappels REP impliquent une liste d’éléments d’entrée et/ou de sortie de taille fixe.

Lorsqu’un appelant appelle initialement un hypercall REP, il spécifie un nombre REP qui indique le nombre d’éléments dans la liste de paramètres d’entrée et/ou de sortie. Les appelants spécifient également un index de début REP qui indique l’élément d’entrée et/ou de sortie suivant qui doit être consommé. L’hyperviseur traite les paramètres REP dans l’ordre de la liste, c’est-à-dire en accroissant l’index d’élément.

Pour les appels suivants de l’hypercall REP, l’index de début REP indique le nombre d’éléments qui ont été terminés et, conjointement avec la valeur du nombre de REPS, le nombre d’éléments restants. Par exemple, si un appelant spécifie un nombre de REP de 25 et que seules 20 itérations sont effectuées dans les limites de temps, l’hyperappel renvoie le contrôle au processeur virtuel appelant après la mise à jour de l’index de début REP à 20. Lorsque l’hyperappel est réexécuté, l’hyperviseur reprend à l’élément 20 et complète les 5 éléments restants.

Si une erreur se produit lors du traitement d’un élément, un code d’état approprié est fourni avec un nombre de représentants terminés, ce qui indique le nombre d’éléments qui ont été traités avec succès avant que l’erreur ne soit rencontrée. En supposant que le mot de contrôle hypercall spécifié est valide (voir les éléments suivants) et que les listes de paramètres d’entrée/sortie sont accessibles, l’hyperviseur est assuré d’essayer au moins un REP, mais il n’est pas nécessaire de traiter toute la liste avant de retourner le contrôle à l’appelant.

## <a name="hypercall-continuation"></a>Continuation hypercall

Un hyperappel peut être considéré comme une instruction complexe qui prend de nombreux cycles. L’hyperviseur tente de limiter l’exécution de l’hyperappel à 50 μs ou moins avant de retourner le contrôle au processeur virtuel qui a appelé l’hyperappel. Certaines opérations hypercall sont suffisamment complexes pour qu’une garantie 50 μs soit difficile à réaliser. L’hyperviseur s’appuie donc sur un mécanisme de continuation hypercall pour certains hyperappels, y compris tous les formulaires d’hypercall Rep.

Le mécanisme de continuation hypercall est surtout transparent pour l’appelant. Si un hyperappel ne peut pas se terminer dans le délai imparti, le contrôle est retourné à l’appelant, mais le pointeur d’instruction n’est pas avancé après l’instruction qui a appelé l’hyperappel. Cela permet de gérer les interruptions en attente et d’autres processeurs virtuels à planifier. Lorsque le thread appelant d’origine reprend l’exécution, il Réexécute l’instruction hypercall et poursuit la progression vers la fin de l’opération.

La plupart des hyperappels simples sont assurés de se terminer dans le délai imparti. Toutefois, un petit nombre d’hyperappels simples peut nécessiter plus de temps. Ces hyperappels utilisent la continuation hypercall de la même manière que les hyperappels de rep. Dans ce cas, l’opération implique deux États internes ou plus. Le premier appel place l’objet (par exemple, la partition ou le processeur virtuel) dans un seul État et, après des appels répétés, l’état passe finalement à un état terminal. Pour chaque hypercall qui suit ce modèle, les effets secondaires visibles des États internes intermédiaires sont décrits.

## <a name="hypercall-atomicity-and-ordering"></a>Atomicité et classement hypercall

Sauf indication contraire, l’action effectuée par un hyperappel est atomique en ce qui concerne toutes les autres opérations invitées (par exemple, les instructions exécutées dans un invité) et tous les autres hyperappels exécutés sur le système. Un simple hypercall effectue une seule action atomique ; un hyperappel de REP effectue plusieurs actions atomiques indépendantes.

Les hyperappels simples qui utilisent la continuation hypercall peuvent impliquer plusieurs États internes qui sont visibles de l’extérieur. Ces appels comprennent plusieurs opérations atomiques.

Chaque action hypercall peut lire des paramètres d’entrée et/ou écrire des résultats. Les entrées de chaque action peuvent être lues à n’importe quel niveau de granularité et à tout moment une fois l’hyperappel effectué et avant l’exécution de l’action. Les résultats (autrement dit, les paramètres de sortie) associés à chaque action peuvent être écrits à toute granularité et à tout moment après l’exécution de l’action et avant le retour de l’hyperappel.

L’invité doit éviter l’examen et/ou la manipulation des paramètres d’entrée ou de sortie associés à un hypercall en cours d’exécution. Bien qu’un processeur virtuel qui exécute un hypercall ne puisse pas le faire (puisque son exécution est suspendue jusqu’à ce que l’hyperappel retourne), il n’y a rien pour empêcher d’autres processeurs virtuels de le faire. Les invités qui se comportent de cette manière peuvent se bloquer ou entraîner une altération de leur partition.

## <a name="legal-hypercall-environments"></a>Environnements d’hyperappel légaux

Les hyperappels peuvent être appelés uniquement à partir du mode de processeur invité le plus privilégié. Sur les platfoms x64, cela signifie que le mode protégé avec un niveau de privilège actuel (CPL) de zéro. Bien que le code en mode réel s’exécute avec un CPL efficace de zéro, les hyperappels ne sont pas autorisés en mode réel. Une tentative d’appel d’un hypercall dans un mode de processeur non conforme génère une exception #UD (opération non définie).

Tous les hyperappels doivent être appelés par le biais de l’interface hypercall définie architecturale (voir ci-dessous). Toute tentative d’appel d’un hypercall par d’autres moyens (par exemple, en copiant le code à partir de la page de codes hypercall à un autre emplacement et en l’exécutant à partir de là) peut entraîner une exception d’opération non définie (#UD). Il n’est pas garanti que l’hyperviseur remet cette exception.

## <a name="alignment-requirements"></a>Exigences d’alignement

Les appelants doivent spécifier l’adresse physique d’invité (GPA) 64 bits des paramètres d’entrée et/ou de sortie. Les pointeurs GPA doivent être alignés sur 8 octets. Si l’hyperappel ne nécessite aucun paramètre d’entrée ou de sortie, l’hyperviseur ignore le pointeur GPA correspondant.

Les listes de paramètres d’entrée et de sortie ne peuvent pas se chevaucher ni dépasser les limites de la page. Les pages d’entrée et de sortie d’hypercall sont supposées être des pages GPA et non des pages de « superposition ». Si le processeur virtuel écrit les paramètres d’entrée dans une page de superposition et spécifie une GPA dans cette page, l’accès hyperviseur à la liste de paramètres d’entrée n’est pas défini.

L’hyperviseur vérifie que la partition d’appel peut lire à partir de la page d’entrée avant d’exécuter l’hyperappel demandé. Cette validation est constituée de deux vérifications : l’amp spécifié est mappée et l’GPA est marquée comme accessible en lecture. Si l’un de ces tests échoue, l’hyperviseur génère un message d’interception de mémoire. Pour les hyperappels qui ont des paramètres de sortie, l’hyperviseur valide que la partition peut être écrite dans la page de sortie. Cette validation est constituée de deux vérifications : l’amp spécifié est mappée et l’amp est marquée comme accessible en écriture.

## <a name="hypercall-inputs"></a>Entrées hypercall

Les appelants spécifient un hyperappel par une valeur 64 bits appelée valeur d’entrée hypercall. Il est mis en forme comme suit :

| Champ                   | Bits    | Informations fournies                                        |
|-------------------------|---------|-------------------------------------------------------------|
| Appeler du code               | 15-0    | Spécifie l’hyperappel demandé                      |
| Rapide                    | 16      | Spécifie si l’hyperappel utilise la Convention d’appel basée sur le registre : 0 = basée sur la mémoire, 1 = basée sur un registre |
| Taille de l’en-tête variable    | 26-17   | Taille d’un en-tête de variable, en QWORDS.                   |
| RsvdZ                   | 31-27   | Doit être égal à zéro                                                |
| Nombre de REPS               | 43-32   | Nombre total de représentants (pour l’appel REP, doit avoir la valeur zéro sinon) |
| RsvdZ                   | 47-44   | Doit être égal à zéro                                                |
| Index de début REP         | 59-48   | Index de départ (pour l’appel REP, doit avoir la valeur zéro dans le cas contraire)       |
| RsvdZ                   | 64-60   | Doit être égal à zéro                                                |

Pour les hyperappels REP, le champ REP count indique le nombre total de représentants. L’index de début REP indique la répétition particulière relative au début de la liste (zéro indique que le premier élément de la liste doit être traité). Par conséquent, la valeur du nombre de REPS doit toujours être supérieure à l’index de début Rep.

Enregistrement du mappage des entrées hypercall lorsque l’indicateur Fast est égal à zéro :

| x64     | x86     | Informations fournies                                        |
|---------|---------|-------------------------------------------------------------|
| RCX     | EDX : EAX | Valeur d’entrée hypercall                                       |
| RDX     | EBX : ECX | Paramètres d’entrée GPA                                        |
| R8      | EDI : ESI | Paramètres de sortie GPA                                       |

La valeur d’entrée hypercall est passée dans les registres avec un GPA qui pointe vers les paramètres d’entrée et de sortie.

Sur x64, les mappages de registres varient selon que l’appelant s’exécute en mode 32 bits (x86) ou 64 bits (x64). L’hyperviseur détermine le mode de l’appelant en fonction de la valeur de EFER. LMA et CS. L. Si ces deux indicateurs sont définis, l’appelant est supposé être un appelant 64 bits.

Enregistrement du mappage des entrées hypercall lorsque l’indicateur Fast est un :

| x64     | x86     | Informations fournies                                        |
|---------|---------|-------------------------------------------------------------|
| RCX     | EDX : EAX | Valeur d’entrée hypercall                                       |
| RDX     | EBX : ECX | Paramètre d’entrée                                             |
| R8      | EDI : ESI | Paramètre de sortie                                            |

La valeur d’entrée hypercall est passée dans les registres avec les paramètres d’entrée.

### <a name="variable-sized-hypercall-input-headers"></a>En-têtes d’entrée hypercall de taille variable

La plupart des en-têtes d’entrée hypercall ont une taille fixe. La quantité de données d’en-tête transmises de l’invité à l’hyperviseur est donc implicitement spécifiée par le code hypercall et n’a pas besoin d’être spécifiée séparément. Toutefois, certains hyperappels nécessitent une quantité variable de données d’en-tête. Ces hyperappels ont généralement un en-tête d’entrée de taille fixe et une entrée d’en-tête supplémentaire de taille variable.

Un en-tête de taille variable est semblable à une entrée d’hyperappel fixe (alignée sur 8 octets et dimensionnée à un multiple de 8 octets). L’appelant doit spécifier la quantité de données qu’il fournit en tant qu’en-têtes d’entrée. Cette taille est fournie dans le cadre de la valeur d’entrée hypercall (voir « taille d’en-tête de variable » dans le tableau ci-dessus).

Étant donné que la taille de l’en-tête fixe est implicite, au lieu de fournir la taille totale de l’en-tête, seule la partie variable est fournie dans les contrôles d’entrée :

```
Variable Header Bytes = {Total Header Bytes - sizeof(Fixed Header)} rounded up to nearest multiple of 8

Variable HeaderSize = Variable Header Bytes / 8
```

Il n’est pas possible de spécifier une taille d’en-tête de variable différente de zéro pour un hyperappel qui n’est pas explicitement documenté comme acceptant les en-têtes d’entrée de taille variable. Dans ce cas, l’hyperappel génère un code de retour `HV_STATUS_INVALID_HYPERCALL_INPUT` .

Il est possible que, pour un appel donné d’un hypercall, accepte les en-têtes d’entrée de taille variable que toutes les entrées d’en-tête tiennent entièrement dans l’en-tête de taille fixe. Dans ce cas, l’en-tête d’entrée de taille variable est de taille zéro et les bits correspondants dans l’entrée hypercall doivent avoir la valeur zéro.

Dans tous les autres cas, les hyperappels acceptant des en-têtes d’entrée de taille variable sont autrement similaires aux hyperappels d’en-tête d’entrée de taille fixe en ce qui concerne les conventions d’appel. Il est également possible d’avoir un hyperappel d’en-tête à taille variable pour prendre en charge la sémantique Rep. Dans ce cas, les éléments REP se trouvent après l’en-tête de la manière habituelle, sauf que la taille totale de l’en-tête comprend à la fois les parties fixe et variable. Toutes les autres règles restent les mêmes, par exemple le premier élément REP doit être aligné sur 8 octets.

### <a name="xmm-fast-hypercall-input"></a>Entrée de l’hyperappel rapide XMM

Sur les plateformes x64, l’hyperviseur prend en charge l’utilisation des hyperappels XMM rapides, ce qui permet à certains hyperappels de tirer parti des performances améliorées de l’interface hypercall rapide, bien qu’elles nécessitent plus de deux paramètres d’entrée. L’interface Fast hypercall de XMM utilise six registres XMM pour permettre à l’appelant de passer un bloc de paramètres d’entrée d’une taille maximale de 112 octets.

La disponibilité de l’interface Fast hypercall de XMM est indiquée par le biais de la « identification de la fonctionnalité hyperviseur » (0x40000003) :

- Bit 4 : la prise en charge de la transmission de l’entrée hypercall via des registres XMM est disponible.

Notez qu’il existe un indicateur distinct pour indiquer la prise en charge de la sortie Fast XMM. Toute tentative d’utilisation de cette interface lorsque l’hyperviseur n’indique pas la disponibilité entraîne une erreur #UD.

#### <a name="register-mapping-input-only"></a>Inscrire le mappage (entrée uniquement)

| x64     | x86     | Informations fournies                                        |
|---------|---------|-------------------------------------------------------------|
| RCX     | EDX : EAX | Valeur d’entrée hypercall                                       |
| RDX     | EBX : ECX | Bloc de paramètres d’entrée                                       |
| R8      | EDI : ESI | Bloc de paramètres d’entrée                                       |
| XMM0    | XMM0    | Bloc de paramètres d’entrée                                       |
| XMM1    | XMM1    | Bloc de paramètres d’entrée                                       |
| XMM2    | XMM2    | Bloc de paramètres d’entrée                                       |
| XMM3    | XMM3    | Bloc de paramètres d’entrée                                       |
| XMM4    | XMM4    | Bloc de paramètres d’entrée                                       |
| XMM5    | XMM5    | Bloc de paramètres d’entrée                                       |

La valeur d’entrée hypercall est passée dans les registres avec les paramètres d’entrée. Les mappages de registres varient selon que l’appelant s’exécute en mode 32 bits (x86) ou 64 bits (x64). L’hyperviseur détermine le mode de l’appelant en fonction de la valeur de EFER. LMA et CS. L. Si ces deux indicateurs sont définis, l’appelant est supposé être un appelant 64 bits. Si le bloc de paramètres d’entrée est inférieur à 112 octets, les octets supplémentaires dans les registres sont ignorés.

## <a name="hypercall-outputs"></a>Sorties hypercall

Tous les hyperappels retournent une valeur 64 bits appelée valeur de résultat hypercall. Il est mis en forme comme suit :

| Champ           | Bits  | Commentaire                                                                               |
|-----------------|-------|---------------------------------------------------------------------------------------|
| Résultats          | 15-0  | `HV_STATUS` Code indiquant la réussite ou l’échec                                        |
| RSVD            | 31-16 | Les appelants doivent ignorer la valeur dans ces bits                                         |
| Représentants terminés  | 43-32 | Nombre de représentants terminés avec succès                                                 |
| RsvdZ           | 63-40 | Les appelants doivent ignorer la valeur dans ces bits                                         |

Pour les hyperappels REP, le champ total des représentants est le nombre total de représentants complets et non relatifs à l’index de début Rep. Par exemple, si l’appelant a spécifié un index de début Rep de 5 et un nombre de REPS de 10, le champ total des représentants indiquerait 10 après réussite.

La valeur du résultat de l’hyperappel est repassée dans les registres. Le mappage du Registre varie selon que l’appelant s’exécute en mode 32 bits (x86) ou 64 bits (x64) (voir ci-dessus). Le mappage de registres pour les sorties hypercall se présente comme suit :

| x64     | x86     | Informations fournies                                        |
|---------|---------|-------------------------------------------------------------|
| RAX     | EDX : EAX | Valeur de résultat d’hypercall                                      |

### <a name="xmm-fast-hypercall-output"></a>La sortie de l’hyperappel rapide XMM

À l’instar de la façon dont l’hyperviseur prend en charge les entrées d’hyperappel rapide XMM, les mêmes registres peuvent être partagés pour retourner la sortie. Cela est uniquement pris en charge sur les plateformes x64.

La possibilité de retourner la sortie via des registres XMM est indiquée par le biais de la « identification de la fonctionnalité hyperviseur » (0x40000003) :

- Bit 15 : la prise en charge du retour de la sortie d’hypercall via des registres XMM est disponible.

Notez qu’il existe un indicateur distinct pour indiquer la prise en charge de l’entrée Fast XMM. Toute tentative d’utilisation de cette interface lorsque l’hyperviseur n’indique pas la disponibilité entraîne une erreur #UD.

#### <a name="register-mapping-input-and-output"></a>Inscrire le mappage (entrée et sortie)

Les registres qui ne sont pas utilisés pour transmettre des paramètres d’entrée peuvent être utilisés pour retourner une sortie. En d’autres termes, si le bloc de paramètres d’entrée est inférieur à 112 octets (arrondi au segment aligné à 16 octets le plus proche), les registres restants retourneront une sortie hypercall.

| x64     | Informations fournies                                        |
|---------|-------------------------------------------------------------|
| RDX     | Bloc d’entrée ou de sortie                                       |
| R8      | Bloc d’entrée ou de sortie                                       |
| XMM0    | Bloc d’entrée ou de sortie                                       |
| XMM1    | Bloc d’entrée ou de sortie                                       |
| XMM2    | Bloc d’entrée ou de sortie                                       |
| XMM3    | Bloc d’entrée ou de sortie                                       |
| XMM4    | Bloc d’entrée ou de sortie                                       |
| XMM5    | Bloc d’entrée ou de sortie                                       |

Par exemple, si la taille du bloc de paramètres d’entrée est de 20 octets, l’hyperviseur ignore les 12 octets suivants. Les 80 octets restants contiendront une sortie hypercall (le cas échéant).

## <a name="volatile-registers"></a>Registres volatils

Les hyperappels modifient uniquement les valeurs de Registre spécifiées dans les conditions suivantes :

1. RAX (x64) et EDX : EAX (x86) sont toujours remplacés par la valeur de résultat hypercall et les paramètres de sortie, le cas échéant.
1. Les hyperappels de REP modifient RCX (x64) et EDX : EAX (x86) avec le nouvel index de début Rep.
1. [HvCallSetVpRegisters](./hypercalls/HvCallSetVpRegisters.md) peut modifier tous les registres pris en charge avec cet hyperappel.
1. RDX, R8 et XMM0 via XMM5, lorsqu’ils sont utilisés pour l’entrée d’hypercall rapide, ne sont pas modifiés. Toutefois, les registres utilisés pour la sortie d’hypercall rapide peuvent être modifiés, y compris RDX, R8 et XMM0 via XMM5. Hyper-V ne modifie ces registres que pour la sortie d’hypercall rapide, qui est limitée à x64.

## <a name="hypercall-restrictions"></a>Restrictions hypercall

Les hyperappels peuvent être associés à des restrictions pour qu’ils puissent exécuter leur fonction prévue. Si toutes les restrictions ne sont pas remplies, l’hyperappel se termine avec une erreur appropriée. Les restrictions suivantes s’appliquent, le cas échéant :

- La partition appelante doit posséder un privilège particulier
- La partition qui fait l’objet de l’action doit être dans un état particulier (par exemple, « active »)

## <a name="hypercall-status-codes"></a>Codes d’État hypercall

Chaque hypercall est documenté comme renvoyant une valeur de sortie qui contient plusieurs champs. Un champ de valeur d’État (de type `HV_STATUS` ) est utilisé pour indiquer si l’appel a réussi ou échoué.

### <a name="output-parameter-validity-on-failed-hypercalls"></a>Validité des paramètres de sortie sur les hyperappels ayant échoué

Sauf indication contraire, lorsqu’un hyperappel échoue (autrement dit, si le champ de résultat de la valeur du résultat hypercall contient une valeur autre que `HV_STATUS_SUCCESS` ), le contenu de tous les paramètres de sortie est indéterminé et ne doit pas être examiné par l’appelant. Tous les paramètres de sortie appropriés contiennent des résultats valides et attendus uniquement lorsque l’hyperappel aboutit.

### <a name="ordering-of-error-conditions"></a>Classement des conditions d’erreur

L’ordre dans lequel les conditions d’erreur sont détectées et signalées par l’hyperviseur n’est pas défini. En d’autres termes, si plusieurs erreurs existent, l’hyperviseur doit choisir la condition d’erreur à signaler. La priorité doit être donnée à ces codes d’erreur offrant une plus grande sécurité, ce qui empêche l’hyperviseur de révéler des informations aux appelants qui ne disposent pas de privilèges suffisants. Par exemple, le code d’état `HV_STATUS_ACCESS_DENIED` est le code d’État par défaut qui révèle des informations de contexte ou d’État basées sur des privilèges.

### <a name="common-hypercall-status-codes"></a>Codes d’état d’hypercall courants

Plusieurs codes de résultats sont communs à tous les hyperappels et ne sont donc pas documentés pour chaque hypercall individuellement. Elles sont associées aux limitations suivantes :

| Code d’état                        | État d’erreur                                                                       |
|------------------------------------|---------------------------------------------------------------------------------------|
| `HV_STATUS_SUCCESS`                | L’appel a réussi.                                                                   |
| `HV_STATUS_INVALID_HYPERCALL_CODE` | Le code hypercall n’est pas reconnu.                                                 |
| `HV_STATUS_INVALID_HYPERCALL_INPUT`| Le nombre de REP est incorrect (par exemple, un nombre de REP non égal à zéro est passé à un appel non REP ou un nombre de REP égal à zéro est passé à un appel REP).
|                                    | L’index de début de REP n’est pas inférieur au nombre de REPS.                                   |
|                                    | Un bit réservé dans la valeur d’entrée hypercall spécifiée est différent de zéro.                    |
| `HV_STATUS_INVALID_ALIGNMENT`      | Le pointeur d’entrée ou de sortie GPA spécifié n’est pas aligné sur 8 octets.                  |
|                                    | Les listes de paramètres d’entrée ou de sortie spécifiés s’étendent sur les pages.                            |
|                                    | Le pointeur d’entrée ou de sortie GPA ne se trouve pas dans les limites de l’espace GPA.            |

Le code de retour `HV_STATUS_SUCCESS` indique qu’aucune condition d’erreur n’a été détectée.

## <a name="reporting-the-guest-os-identity"></a>Signalement de l’identité du système d’exploitation invité

Le système d’exploitation invité qui s’exécute dans la partition doit s’identifier auprès de l’hyperviseur en écrivant sa signature et sa version sur un MSR ( `HV_X64_MSR_GUEST_OS_ID` ) avant de pouvoir appeler des hyperappels. Ce MSR est à l’ensemble de la partition et est partagé entre tous les processeurs virtuels.

La valeur de ce registre est initialement égale à zéro. Une valeur différente de zéro doit être écrite dans l’ID de système d’exploitation invité MSR pour que la page de codes hypercall puisse être activée (consultez [établissement de l’interface hypercall](#establishing-the-hypercall-interface)). Si ce registre est par la suite mis à zéro, la page de codes hypercall est désactivée.

 ```c
#define HV_X64_MSR_GUEST_OS_ID 0x40000000
 ```

### <a name="guest-os-identity-for-proprietary-operating-systems"></a>Identité du système d’exploitation invité pour les systèmes d’exploitation propriétaires

L’encodage recommandé pour ce MSR est le suivant. Certains champs peuvent ne pas s’appliquer à un système d’exploitation invité.

| Bits      | Champ           | Description                                                                 |
|-----------|-----------------|-----------------------------------------------------------------------------|
| 15:0      | Numéro de version    | Indique le numéro de version du système d’exploitation                                        |
| 23:16     | Version du service | Indique la version du service (par exemple, le numéro « Service Pack »)          |
| 31:24     | Version mineure   | Indique la version mineure du système d’exploitation                                       |
| 39:32     | Version majeure   | Indique la version principale du système d’exploitation                                       |
| 47:40     | ID DE SYSTÈME D’EXPLOITATION           | Indique la variante du système d’exploitation. L’encodage est unique pour le fournisseur. Les systèmes d’exploitation Microsoft sont encodés comme suit : 0 = non défini, 1 = MS-DOS®, 2 = Windows® 3. x, 3 = Windows® 9x, 4 = Windows® NT (et dérivés), 5 = Windows® CE
| 62:48     | ID de fournisseur       | Indique le fournisseur de système d’exploitation invité. La valeur 0 est réservée. Voir la liste des fournisseurs ci-dessous.
| 63        | Type de système d'exploitation         | Indique les types de système d’exploitation. La valeur 0 indique un système d’exploitation source propriétaire fermé. La valeur 1 indique un système d’exploitation open source.

Les valeurs des fournisseurs sont allouées par Microsoft. Pour demander un nouveau fournisseur, veuillez envoyer un problème dans le référentiel de documentation sur la virtualisation GitHub ( <https://aka.ms/VirtualizationDocumentationIssuesTLFS> ).

| Fournisseur    | Value                                                                                |
|-----------|--------------------------------------------------------------------------------------|
| Microsoft | 0x0001                                                                               |
| HPE       | 0x0002                                                                               |
| LANCOM    | 0x0200                                                                               |

### <a name="guest-os-identity-msr-for-open-source-operating-systems"></a>Identité MSR du système d’exploitation invité pour les systèmes d’exploitation open source

L’encodage suivant est proposé en tant que guide pour les fournisseurs de systèmes d’exploitation open source qui ont l’intention de se conformer à cette spécification. Nous vous suggérons de faire en sorte que les systèmes d’exploitation open source adaptent la Convention suivante.

| Bits      | Champ           | Description                                                                 |
|-----------|-----------------|-----------------------------------------------------------------------------|
| 15:0      | Numéro de version    | Informations supplémentaires                                                      |
| 47:16     | Version         | Informations sur la version du noyau en amont.                                        |
| 55:48     | ID DE SYSTÈME D’EXPLOITATION           | Informations supplémentaires sur le fournisseur                                               |
| 62:56     | Type de système d'exploitation         | Type de système d’exploitation (par exemple, Linux, FreeBSD, etc.). Voir la liste des types de système d’exploitation connus ci-dessous      |
| 63        | Open source     | La valeur 1 indique un système d’exploitation open source.                                   |

Les valeurs de type de système d’exploitation sont allouées par Microsoft. Pour demander un nouveau type de système d’exploitation, signalez un problème dans le référentiel de documentation sur la virtualisation GitHub ( <https://aka.ms/VirtualizationDocumentationIssuesTLFS> ).

| Type de système d'exploitation   | Value                                                                                |
|-----------|--------------------------------------------------------------------------------------|
| Linux     | 0x1                                                                                  |
| FreeBSD   | 0x2                                                                                  |
| Xen       | 0x3                                                                                  |
| Illumos   | 0x4                                                                                  |

## <a name="establishing-the-hypercall-interface"></a>Établissement de l’interface hypercall

Les hyperappels sont appelés à l’aide d’un opcode spécial. Étant donné que cet opcode diffère entre les implémentations de virtualisation, il est nécessaire que l’hyperviseur fasse abstraction de cette différence. Cette opération s’effectue par le biais d’une page spéciale hypercall. Cette page est fournie par l’hyperviseur et s’affiche dans l’espace des GPA de l’invité. L’invité est tenu de spécifier l’emplacement de la page en programmant le MSR hypercall de l’invité.

 ```c
#define HV_X64_MSR_HYPERCALL 0x40000001
 ```

| Bits    | Description                                                                        | Attributs      |
|---------|------------------------------------------------------------------------------------|-----------------|
| 63:12   | Hypercall GPFN : indique le numéro de page physique de l’invité de la page hypercall    | Lecture/écriture    |
| 11:2    | RsvdP. Les bits doivent être ignorés lors des lectures et conservées lors de l’écriture.                    | Réservé        |
| 1       | Fermée. Indique si la MSR est immuable. S’il est défini, ce MSR est verrouillé, empêchant ainsi le déplacement de la page hypercall. Une fois défini, seule une réinitialisation du système peut effacer le bit.                                                    | Lecture/écriture    |
| 0       | Page activer hypercall                                                              | Lecture/écriture    |

La page hypercall peut être placée n’importe où dans l’espace des GPA de l’invité, mais elle doit être alignée sur la page. Si l’invité tente de déplacer la page hypercall au-delà des limites de l’espace GPA, une erreur #GP se produit lors de l’écriture de la MSR.

Ce MSR est un MSR à l’ensemble de la partition. En d’autres termes, il est partagé par tous les processeurs virtuels de la partition. Si un processeur virtuel écrit correctement sur la MSR, un autre processeur virtuel lira la même valeur.

Avant que la page hypercall soit activée, le système d’exploitation invité doit signaler son identité en écrivant sa signature de version sur un LBM séparé (HV_X64_MSR_GUEST_OS_ID). Si aucune identité de système d’exploitation invité n’a été spécifiée, les tentatives d’activation de l’hyperappel échouent. Le bit d’activation reste zéro même si un octet y est écrit. En outre, si l’identité du système d’exploitation invité est effacée à zéro après l’activation de la page hypercall, elle est désactivée.

La page hypercall s’affiche sous la forme d’une « superposition » à l’espace GPA. autrement dit, il couvre tout le reste mappé à la plage de l’amp. Son contenu est lisible et exécutable par l’invité. Toute tentative d’écriture dans la page hypercall entraîne une exception de protection (#GP). Une fois la page hypercall activée, l’appel d’un hypercall implique simplement un appel au début de la page.

Vous trouverez ci-dessous une liste détaillée des étapes nécessaires à l’établissement de la page hypercall :

1. L’invité lit CPUID feuille 1 et détermine si un hyperviseur est présent en vérifiant le bit 31 du registre ECX.
2. L’invité lit CPUID feuille 0x40000000 pour déterminer la taille maximale de la feuille de l’hyperviseur (retournée dans Register EAX) et CPUID Leaf 0x40000001 pour déterminer la signature d’interface (retournée dans Register EAX). Il vérifie que la valeur de feuille maximale est au moins de 0x40000005 et que la signature d’interface est égale à « n ° 1 ». Cette signature implique que `HV_X64_MSR_GUEST_OS_ID` , `HV_X64_MSR_HYPERCALL` et `HV_X64_MSR_VP_INDEX` sont implémentés.
3. L’invité écrit son identité de système d’exploitation dans la MSR `HV_X64_MSR_GUEST_OS_ID` si ce registre est égal à zéro.
4. L’invité lit l’hypercall MSR ( `HV_X64_MSR_HYPERCALL` ).
5. L’invité vérifie le bit d’activation de la page hypercall. Si elle est définie, l’interface est déjà active et les étapes 6 et 7 doivent être omises.
6. L’invité trouve une page dans son espace GPA, de préférence une page qui n’est pas occupée par RAM, MMIO, et ainsi de suite. Si la page est occupée, l’invité doit éviter d’utiliser la page sous-jacente à d’autres fins.
7. L’invité écrit une nouvelle valeur dans l’hypercall MSR ( `HV_X64_MSR_HYPERCALL` ) qui comprend l’GPA de l’étape 6 et définit le bit de page activer hypercall pour activer l’interface.
8. L’invité crée un mappage de VA exécutable sur la page d’hyperappel amp.
9. L’invité consulte le 0x40000003 de la feuille CPUID pour déterminer quelles sont les fonctionnalités de l’hyperviseur disponibles.
Une fois l’interface établie, l’invité peut lancer un hypercall. Pour ce faire, il remplit les registres par le protocole hypercall et émet un appel au début de la page hypercall. L’invité doit supposer que la page hypercall effectue l’équivalent d’un 0xC3 (Near Return) pour retourner à l’appelant. Par conséquent, l’hyperappel doit être appelé avec une pile valide.

## <a name="extended-hypercall-interface"></a>Interface hypercall étendue

Les hyperappels avec des codes d’appel au-dessus de 0x8000 sont appelés hyperappels étendus. Les hyperappels étendus utilisent la même convention d’appel que les hyperappels normaux et apparaissent identiques du point de vue d’une machine virtuelle invitée. Les hyperappels étendus sont gérés en interne différemment dans l’hyperviseur Hyper-V.

Les fonctionnalités d’hypercall étendues peuvent être interrogées avec [HvExtCallQueryCapabilities](hypercalls/HvExtCallQueryCapabilities.md).