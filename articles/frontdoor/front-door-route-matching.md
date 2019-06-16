---
title: Usługa Azure drzwiami frontowymi — routingu reguły dopasowania monitorowania | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak usługi Azure Service drzwiami frontowymi dopasowuje się do reguły routingu, które na potrzeby przychodzącego żądania
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
ms.openlocfilehash: eec99bde0ea73a99a9dc1345f938b821a95a7c05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736291"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Jak drzwiami frontowymi dopasowuje się do żądania do reguły routingu

Po ustanowieniu połączenia i wykonywania procedury uzgadniania protokołu SSL, gdy żądanie umieszczać swoje dokumenty w środowisku drzwiami frontowymi jedną z pierwszych czynności, które obsługuje drzwiami frontowymi jest określenie ze wszystkich konfiguracji, które określonej reguły routingu do dopasowania żądanie i następnie wykonywanie zdefiniowana Akcja. Poniższy dokument wyjaśnia, jak drzwiami frontowymi Określa, która Konfiguracja trasy do użycia podczas przetwarzania żądania HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfiguracji tras drzwi
Drzwiami frontowymi konfiguracji reguły routingu składa się z dwóch głównych składników: "po lewej stronie" i "po prawej stronie". Żądania przychodzącego, aby po lewej stronie trasy możemy odpowiadać podczas po prawej stronie definiuje, jak możemy przetworzyć żądania.

### <a name="incoming-match-left-hand-side"></a>Dopasowanie przychodzące (po lewej stronie)
Następujące właściwości określają, czy przychodzące żądanie dopasowuje reguły routingu (lub po lewej stronie):

* **Protokoły HTTP** (HTTP/HTTPS)
* **Hosty** (na przykład www\.foo.com, \*. bar.com)
* **Ścieżki** (na przykład /\*, /users/\*, /file.gif)

Te właściwości zostaną rozwinięte się wewnętrznie tak, aby każdej kombinacji protokołu/Host/ścieżkę zestawu potencjalnych zgodności.

### <a name="route-data-right-hand-side"></a>Dane trasy (po prawej stronie)
Decyzja sposób przetwarzania żądań, zależy od tego, czy włączone jest buforowanie lub dla określonej trasy. Tak Jeśli firma Microsoft nie ma buforowanych odpowiedzi dla żądania, firma Microsoft będzie przekazywać żądania do odpowiedniego zaplecza w puli zaplecza skonfigurowane.

## <a name="route-matching"></a>Kierowanie dopasowania
Ta sekcja koncentruje się na sposób możemy pasują do danej reguły routingu wejściu. Podstawowe pojęcia jest, że firma Microsoft zawsze odpowiada **specyficzne dla większości Dopasuj najpierw** wyglądających tylko na "po lewej stronie".  Firma Microsoft pierwsze wystąpienie zależności od protokołu HTTP serwera sieci Web hosta, a następnie ścieżki.

### <a name="frontend-host-matching"></a>Host frontonu dopasowania
Podczas dopasowywania hosty frontonu, używamy logiki zgodnie z poniższymi instrukcjami:

1. Zwróć uwagę na wszelkie routing za pomocą dokładnego dopasowania na hoście.
2. Jeśli żadne hosty frontonu dokładnie odpowiada, odrzucenia żądania i wysyłać błąd 400 Nieprawidłowe żądanie.

Aby wyjaśnić tego procesu dalsze, Przyjrzyjmy się przedstawiono przykładową konfigurację trasy drzwiami frontowymi (tylko po lewej stronie):

| Reguła routingu | Hosty frontonu | Ścieżka |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /Users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Jeśli następujące przychodzące żądania zostały wysłane na wejściu, czy dopasować następujące reguły routingu z powyższych:

| Przychodzące hosta serwera sieci Web | Dopasowane reguły routingu |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Błąd 400: Nieprawidłowe żądanie |
| foo.adventure-works.com | C |
| contoso.com | Błąd 400: Nieprawidłowe żądanie |
| www\.adventure-works.com | Błąd 400: Nieprawidłowe żądanie |
| www\.northwindtraders.com | Błąd 400: Nieprawidłowe żądanie |

### <a name="path-matching"></a>Dopasowywania ścieżek
Po określająca hosta określonego serwera sieci Web i filtrowanie możliwych reguł routingu w celu po prostu trasy z tego hosta serwera sieci Web, drzwiami frontowymi filtruje reguł routingu na podstawie ścieżki żądania. Używamy podobnej logiki jako hosty frontonu:

1. Zwróć uwagę na wszelkie reguły routingu dokładnie dopasowany do ścieżki
2. Jeśli żadne ścieżki dokładne dopasowanie, poszukaj reguł routingu symbolem wieloznacznym ścieżki, która jest zgodna
3. Jeśli nie reguły routingu znajdują się ze ścieżką dopasowania, odrzucić żądanie i zwracać 400: Błąd żądania złe odpowiedzi HTTP.

>[!NOTE]
> Wszystkie ścieżki bez symboli wieloznacznych są uznawane za dokładnie dopasowanego ścieżki. Nawet jeśli ścieżka kończy się ukośnikiem, nadal jest uważane za dokładnego dopasowania.

Aby wyjaśnić, dodatkowo, Spójrzmy na inny zestaw przykładów:

| Reguła routingu | Hosta serwera sieci Web    | Ścieżka     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Mając tę konfigurację, spowoduje przykładowej dopasowania tabeli poniżej:

| Żądanie przychodzące    | Dopasowanej trasy |
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
> </br> Jeśli nie reguły routingu dla hosta dokładnie dopasowanego frontonu z przechwytującą cały trasy ścieżki (`/*`), nie będzie dopasowanie do dowolnej reguły routingu.
>
> Przykładową konfigurację:
>
> | Trasa | Host             | Ścieżka    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /API/\* |
>
> Dopasowanej tabeli:
>
> | Żądanie przychodzące       | Dopasowanej trasy |
> |------------------------|---------------|
> | profile.domain.com/other | Brak. Błąd 400: Nieprawidłowe żądanie |

### <a name="routing-decision"></a>Decyzje dotyczące routingu
Po dopasowaliśmy jednej reguły routingu drzwiami frontowymi, następnie należy wybrać sposób przetworzyć żądanie. Jeśli dopasowane reguły routingu, drzwiami frontowymi ma buforowaną odpowiedź dostępne następnie takie same pobiera obsługiwane do klienta. W przeciwnym razie właśnie obliczaniu jest, czy skonfigurowano [(ścieżka przekazywanie niestandardowych) ponowne zapisywanie adresów URL](front-door-url-rewrite.md) dopasowane routingu reguły lub nie. Jeśli nie ma ścieżkę przekazywania niestandardowe zdefiniowane, żądanie zostanie przekazany do odpowiedniego zaplecza w puli zaplecza skonfigurowane, ponieważ jest. W przeciwnym wypadku ścieżki żądania zostanie zaktualizowany zgodnie [ścieżkę przekazywania niestandardowej](front-door-url-rewrite.md) zdefiniowane i następnie dalej do wewnętrznej bazy danych.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
