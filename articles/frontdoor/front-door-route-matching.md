---
title: Drzwi frontowe platformy Azure — monitorowanie dopasowywania reguł routingu | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia zrozumienie, jak usługi Azure Front Door dopasowuje regułę routingu do użycia dla żądania przychodzącego
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 420aa52293da14a0dfe8fbdfe681440ee4309e6b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878599"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Jak drzwi frontowe dopasowywać żądania do reguły routingu

Po ustanowieniu połączenia i wykonaniu uzgadniania TLS, gdy żądanie ląduje w środowisku drzwiami frontowymi, jedną z pierwszych rzeczy, które nie są dostępne w drzwiach frontowych, jest określenie ze wszystkich konfiguracji, która określona reguła routingu jest zgodna z żądaniem, a następnie podjęcie zdefiniowanej akcji. W poniższym dokumencie wyjaśniono, jak drzwi frontowe określają konfigurację trasy, której należy używać podczas przetwarzania żądania HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfiguracji trasy drzwi wejściowych
Konfiguracja reguły wyznaczania drzwi przednich składa się z dwóch głównych części: "po lewej stronie" i "prawej strony". Dopasujemy przychodzące żądanie do lewej strony trasy, podczas gdy po prawej stronie określa sposób przetwarzania żądania.

### <a name="incoming-match-left-hand-side"></a>Mecz przychodzący (po lewej stronie)
Następujące właściwości określają, czy żądanie przychodzące jest zgodne z regułą routingu (lub po lewej stronie):

* **Protokoły HTTP** (HTTP/HTTPS)
* **Hosty** (na\.przykład www \*foo.com, .bar.com)
* **Ścieżki** (na przykład\*/ ,\*/users/ , /file.gif)

Te właściwości są rozwijane wewnętrznie, tak aby każda kombinacja protokołu/hosta/ścieżki była zestawem potencjalnych dopasowań.

### <a name="route-data-right-hand-side"></a>Dane trasy (po prawej stronie)
Decyzja o sposobie przetwarzania żądania zależy od tego, czy buforowanie jest włączone, czy nie dla określonej trasy. Tak jeśli nie mamy buforowanej odpowiedzi dla żądania, przekażemy żądanie do odpowiedniej wewnętrznej bazy danych w skonfigurowanym puli wewnętrznej bazy danych.

## <a name="route-matching"></a>Dopasowywanie trasy
W tej sekcji skupimy się na tym, jak dopasować się do danej reguły routingu drzwiami frontowymi. Podstawową koncepcją jest to, że zawsze pasujemy do **najbardziej specyficznego meczu, najpierw** patrząc tylko na "lewą stronę".  Najpierw dopasujemy na podstawie protokołu HTTP, następnie hosta frontu, a następnie ścieżki.

### <a name="frontend-host-matching"></a>Dopasowywanie hosta frontu
Podczas dopasowywania hostów frontu używamy logiki, jak poniżej:

1. Poszukaj dowolnej routingu z dokładnym dopasowaniem na hoście.
2. Jeśli nie ma dokładnego dopasowania hostów frontendu, odrzuć żądanie i wyślij błąd 400 bad request.

Aby wyjaśnić ten proces, przyjrzyjmy się przykładowej konfiguracji tras drzwi przednich (tylko po lewej stronie):

| Reguła routingu | Hosty frontendu | Ścieżka |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /użytkownicy/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Jeśli następujące przychodzące żądania zostały wysłane do drzwi frontowych, będą one zgodne z następującymi regułami routingu z góry:

| Przychodzący host frontendu | Reguły routingu dopasowane |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Błąd 400: Złe żądanie |
| foo.adventure-works.com | C |
| contoso.com | Błąd 400: Złe żądanie |
| www\.adventure-works.com | Błąd 400: Złe żądanie |
| www\.northwindtraders.com | Błąd 400: Złe żądanie |

### <a name="path-matching"></a>Dopasowywanie ścieżki
Po określeniu określonego hosta frontendu i filtrowaniu możliwych reguł routingu tylko do tras z tym hostem frontendu, drzwiami frontowymi filtrują reguły routingu na podstawie ścieżki żądania. Używamy podobnej logiki jako hostów frontendu:

1. Poszukaj dowolnej reguły routingu z dokładnym dopasowaniem na ścieżce
2. Jeśli nie ma dopasowania ścisłego Ścieżki, poszukaj reguł routingu ze ścieżką wieloznaczną, która pasuje
3. Jeśli nie znaleziono reguł routingu z pasującą ścieżką, odrzuć żądanie i zwróć odpowiedź HTTP o błędzie 400: Nieprawidłowe żądanie.

>[!NOTE]
> Wszystkie ścieżki bez symbolu wieloznacznego są uważane za ścieżki dopasowania ścisłego. Nawet jeśli ścieżka kończy się ukośnikiem, nadal jest uważana za dokładne dopasowanie.

Aby wyjaśnić dalej, przyjrzyjmy się innej zestawowi przykładów:

| Reguła routingu | Host frontendu    | Ścieżka     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /ścieżka/   |

Biorąc pod uwagę tę konfigurację, wynikowa jest następująca tabela dopasowywania:

| Żądanie przychodzące    | Dopasowana trasa |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Jeśli nie ma żadnych reguł routingu dla hosta frontendu`/*`dopasowania ścisłego z trasą catch-all ( ), nie będzie zgodne z żadną regułą routingu.
>
> Przykładowa konfiguracja:
>
> | Trasa | Host             | Ścieżka    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Pasująca tabela:
>
> | Żądanie przychodzące       | Dopasowana trasa |
> |------------------------|---------------|
> | profile.domain.com/other | Brak. Błąd 400: Złe żądanie |

### <a name="routing-decision"></a>Decyzja o routingu
Po dopasowaniu do jednej reguły routingu drzwiami frontowymi musimy wybrać sposób przetwarzania żądania. Jeśli dla reguły routingu dopasowane, drzwiami frontowymi ma buforowaną odpowiedź dostępne następnie to samo jest obsługiwane z powrotem do klienta. W przeciwnym razie następną rzeczą, która zostanie oceniona, jest to, czy skonfigurowano [ponowne przepisanie adresu URL (niestandardową ścieżkę przekazywania)](front-door-url-rewrite.md) dla dopasowanej reguły routingu, czy nie. Jeśli nie zdefiniowano niestandardowej ścieżki przekazywania, żądanie zostanie przekazane do odpowiedniej wewnętrznej bazy danych w skonfigurowanym puli wewnętrznej bazy danych. W przeciwnym razie ścieżka żądania jest aktualizowana zgodnie ze zdefiniowaną [niestandardową ścieżką przekazywania,](front-door-url-rewrite.md) a następnie do wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
