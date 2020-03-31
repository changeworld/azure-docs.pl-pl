---
title: Szablony tokenów łańcucha bloków azure
description: Szablony tokenów łańcucha bloków Azure są standardowymi szablonami wielokrotnego pożyczeniami, które upraszczają tworzenie i wdrażanie tokenów opartych na księdze.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252209"
---
# <a name="azure-blockchain-tokens-templates"></a>Szablony tokenów łańcucha bloków azure

[!INCLUDE [Preview note](./includes/preview.md)]

Szablon tokenów łańcucha bloków azure to standardowy i wielokrotnego stosowania szablon, który upraszcza tworzenie i wdrażanie tokenów opartych na księdze. Szablon składa się z formuły, która jest oparta na [gramatyki frameworka tokenu (TTF).](overview.md#token-taxonomy-framework) Gramatyka obejmuje typ tokenu podstawowego i zestaw zachowań dla tokenu.  

Na przykład szablon tokenu **τ{d,m,b,r}** opisuje wymienne tokeny podstawowe, które jest podzielone podpodzielnie, łumialne, spalalne i obsługuje rolę.
  
## <a name="base-token-types"></a>Typy tokenów bazowych

Podczas definiowania i tworzenia tokenu opartego na księdze dla określonego zasobu, należy wziąć pod uwagę, jaki token podstawowy do użycia.

### <a name="fungible"></a>Zamienny

Wymienne tokeny (τF) mają wymienną wartość ze sobą, o ile znajdują się w tej samej klasie lub serii. Jeden token ma taką samą wartość jak inny token lub dana ilość tokenów ma taką samą wartość jak inna równa ilość. Na przykład dolar jest wymijakiem tokenu. Jeśli dwie osoby posiadają banknot dolarowy, mogą wymieniać te dolary bez konsekwencji. Banknoty dolarowe mają taką samą wartość. 

### <a name="non-fungible"></a>Niewymiężne

Tokeny niewymienne (τN) nie są wymienne z innymi tokenami tego samego typu, ponieważ zazwyczaj mają różne wartości. Na przykład tytuł właściwości jest tokenem niewymiennym. Tytuły nieruchomości do dwóch różnych apartamentów w kompleksie mieszkaniowym niekoniecznie mają taką samą wartość, ze względu na lokalizację jednostki lub to, na którym piętrze znajduje się urządzenie. Postrzegana wartość tokenów tytułu dwóch właściwości nie są równe.

### <a name="hybrid"></a>Połączenie hybrydowe

Tokeny hybrydowe są tokenami, które mają składniki zarówno wymazykowych tokenów, jak i tokenów niewymiennych. Token hybrydowy jest typ tokenu podstawowego, który jest właścicielem klasy innego typu tokenu.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybrydowa baza niewymienna z segmentami zamiennymi

Hybrydowa baza niewymięcia z wymiennymi segmentami ma niewymienną bazę z segmentami zamiennymi tokenami.
Na przykład bilet na koncert jest tokenem hybrydowym, w którym data i godzina koncertu jest niewymiennym tokenem podstawowym. Bilety w różnych sekcjach miejsc siedzących na dany koncert są segmentami z wymiennymi żetonami. Bilety można wymieniać w poszczególnych sekcjach siedzących, ale nie na różnych odcinkach.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybrydowa baza zamienna z segmentami niewymiennymi

Hybrydowa baza zamienna z tokenem niewymiennych segmentów ma wymienną bazę z niewymiennymi segmentami tokenów. Na przykład zabezpieczenie zabezpieczone hipoteką jest tokenem hybrydowym, w którym wielu właścicieli jest zamienną bazą, która jest podzielona na wielu właścicieli. Zabezpieczenia są wymienne. Poszczególne kredyty hipoteczne są niewymiennych segmentów, które reprezentują konkretne zabezpieczenie zabezpieczone hipoteką.

## <a name="token-behaviors"></a>Zachowania tokenów

Zachowanie tokenu definiuje możliwości lub ograniczenia tokenu. Zachowanie obejmuje właściwości pomocnicze, które są częścią definicji tokenu. Zachowania mogą być stosowane we wszystkich typach tokenów lub tylko jeden. Zachowania mogą być wewnętrzne lub zewnętrzne w zależności od tego, jakie efekty zachowania. Zachowanie wewnętrzne włącza lub ogranicza właściwości samego tokenu. Zachowanie zewnętrzne umożliwia lub ogranicza wywołanie zachowania od aktora zewnętrznego.

Aby uzyskać więcej informacji na temat tokenów łańcucha bloków platformy Azure obsługiwane token taxonomy framework (TTF) zachowania tokenu, zobacz [możliwości tworzenia tokenów.](composability.md)

## <a name="pre-built-token-templates"></a>Wstępnie utworzone szablony tokenów

Tokeny łańcucha bloków azure zawiera cztery wstępnie utworzone szablony tokenów, które mogą być używane bez modyfikacji. Można wywołać te wstępnie utworzone szablony dla większości przypadków użycia, aby szybko rozpocząć tworzenie, wdrażanie i zarządzanie tokenami.

### <a name="commodity-tokens"></a>Tokeny towarowe

Tokeny towarowe mają stałą wartość i są zbywalne. Na przykład, baryłkę ropy naftowej lub jednostki energii.

**τF{~d,t,m,b,r}** - wymienne, całe, zbywalne, łuszalne, spalane i mają wsparcie roli

Wiele scenariuszy łańcucha bloków wymaga przejrzystości i widoczności w całym łańcuchu dostaw lub wielu organizacjach. Tokeny towarowe są oparte na tych typowych przypadkach użycia. Tokeny są wymienne i spójne. Szablon tokenu towarowego jest elastyczny i można go dostosować za pomocą metadanych.

### <a name="qualified-tokens"></a>Kwalifikowane tokeny

Kwalifikowane tokeny reprezentują coś zarobionego i są zwykle skojarzone z jedną jednostką i nie mogą być przenoszone. Na przykład dyplom lub naruszenie parkingu.

**τN{s,~t}** - niewymiężne, singleton i niezbywalne

Różne scenariusze inspekcji i zaświadczania wymagają, aby nie można zmienić własności tokenu. Istnieje zestaw przypadków użycia, które mają potrzebę zapewnienia kwalifikowanego tokenu, czy skojarzenie jest dobre, czy złe.

### <a name="asset-tokens"></a>Tokeny aktywów

Tokeny zasobów mają unikatową wartość zależną od towaru i nie są towarami. Na przykład artefakt muzeum lub tytuł właściwości.

**τN{s,t}** - niewymiężne, singleton i zbywalne

Tokeny aktywów mogą być mylone z tokenami towarowymi. Główną różnicą między dwoma tokenami jest to, że tokeny zasobów są z natury unikatowe, a wartość jest niezależna od typu tokenu, który jest. Na przykład dzieło sztuki, takie jak obraz olejny autorstwa ustalonego artysty, jest zasobem. Jednak druk sztuki Mona Lisa jest uważany za token towarowy. Podobnie tytuł właściwości jest tokenem środka trwałego, ponieważ wartość istnieje w subiektywnych jakości właściwości.

### <a name="ticket-tokens"></a>Tokeny biletowe

Tokeny biletu mają stałą wartość, ale zazwyczaj wygasają. Na przykład bilet lotniczy.

**τN{m,b,r}** - niewymiężne, łuczne, palne i mają wsparcie roli.

Tokeny biletu zazwyczaj mają datę ważności, która sprawia, że różnią się one od zwykłego tokenu towarowego. Na przykład bilet lotniczy, bilet na koncert lub bilet sportowy mają opcje przypisanego miejsca z określonymi datami użytkowania. Nie można łatwo wymieniać biletów między datami lub miejscami do siedzenia.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz większej elastyczności dla swojego scenariusza, dowiedz się więcej o tworzeniu własnych szablonów tokenów przy użyciu [możliwości komponowania tokenów.](composability.md)
