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
ms.openlocfilehash: 23582215654ff2d5003fe611c7149ad760d72bc5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957044"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Jak drzwiami frontowymi dopasowuje się do żądania do reguły routingu

Po ustanowieniu połączenia i wykonywania procedury uzgadniania protokołu SSL, gdy żądanie umieszczać swoje dokumenty w środowisku drzwiami frontowymi jedną z pierwszych czynności, które obsługuje drzwiami frontowymi jest określenie ze wszystkich konfiguracji, które określonej reguły routingu do dopasowania żądanie i następnie wykonywanie zdefiniowana Akcja. Poniższy dokument wyjaśnia, jak drzwiami frontowymi Określa, która Konfiguracja trasy do użycia podczas przetwarzania żądania HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfiguracji tras drzwi
Drzwiami frontowymi konfiguracji reguły routingu składa się z dwóch głównych składników: "po lewej stronie" i "po prawej stronie". Żądania przychodzącego, aby po lewej stronie trasy możemy odpowiadać podczas po prawej stronie definiuje, jak możemy przetworzyć żądania.

### <a name="incoming-match-left-hand-side"></a>Dopasowanie przychodzące (po lewej stronie)
Następujące właściwości określają, czy przychodzące żądanie dopasowuje reguły routingu (lub po lewej stronie):

* **Protokoły HTTP** (HTTP/HTTPS)
* **Hosty** (na przykład www.foo.com \*. bar.com)
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
| C | www.Fabrikam.com, foo.adventure works.com  | /\*, /images/\* |

Jeśli następujące przychodzące żądania zostały wysłane na wejściu, czy dopasować następujące reguły routingu z powyższych:

| Przychodzące hosta serwera sieci Web | Dopasowane reguły routingu |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www.Fabrikam.com | C |
| images.Fabrikam.com | Błąd 400: Nieprawidłowe żądanie. |
| foo.adventure works.com | C |
| contoso.com | Błąd 400: Nieprawidłowe żądanie. |
| www.Adventure-Works.com | Błąd 400: Nieprawidłowe żądanie. |
| www.northwindtraders.com | Błąd 400: Nieprawidłowe żądanie. |

### <a name="path-matching"></a>Dopasowywania ścieżek
Po określająca hosta określonego serwera sieci Web i filtrowanie możliwych reguł routingu w celu po prostu trasy z tego hosta serwera sieci Web, drzwiami frontowymi filtruje reguł routingu na podstawie ścieżki żądania. Używamy podobnej logiki jako hosty frontonu:

1. Zwróć uwagę na wszelkie reguły routingu dokładnie dopasowany do ścieżki
2. Jeśli żadne ścieżki dokładne dopasowanie, poszukaj reguł routingu symbolem wieloznacznym ścieżki, która jest zgodna
3. Jeśli nie reguły routingu znajdują się ze ścieżką dopasowania, odrzucić wniosek i zwrócić 400: błąd złe żądanie odpowiedzi HTTP.

>[!NOTE]
> Wszystkie ścieżki bez symboli wieloznacznych są uznawane za dokładnie dopasowanego ścieżki. Nawet jeśli ścieżka kończy się ukośnikiem, nadal jest uważane za dokładnego dopasowania.

Aby wyjaśnić, dodatkowo, Spójrzmy na inny zestaw przykładów:

| Reguła routingu | Hosta serwera sieci Web    | Ścieżka     |
|-------|---------|----------|
| A     | www.contoso.com | /        |
| B     | www.contoso.com | /\*      |
| C     | www.contoso.com | / AB      |
| D     | www.contoso.com | /ABC     |
| E     | www.contoso.com | /ABC/    |
| F     | www.contoso.com | /ABC/\*  |
| G     | www.contoso.com | / abc/def |
| H     | www.contoso.com | /PATH/   |

Mając tę konfigurację, spowoduje przykładowej dopasowania tabeli poniżej:

| Żądanie przychodzące    | Dopasowanej trasy |
|---------------------|---------------|
| www.contoso.com/            | A             |
| www.contoso.com/a           | B             |
| www.contoso.com/AB          | C             |
| www.contoso.com/ABC         | D             |
| www.contoso.com/abzzz       | B             |
| www.contoso.com/ABC/        | E             |
| www.contoso.com/ABC/d       | F             |
| www.contoso.com/ABC/DEF     | G             |
| www.contoso.com/ABC/defzzz  | F             |
| www.contoso.com/ABC/DEF/GHI | F             |
| www.contoso.com/Path        | B             |
| www.contoso.com/Path/       | H             |
| www.contoso.com/Path/zzz    | B             |

>[!WARNING]
> </br> Jeśli nie reguły routingu dla hosta dokładnie dopasowanego frontonu z przechwytującą cały trasy ścieżki (`/*`), nie będzie dopasowanie do dowolnej reguły routingu.
>
> Przykładową konfigurację:
>
> | Trasa | Host             | Ścieżka    |
> |-------|------------------|---------|
> | A     | Profile.contoso.com | /API/\* |
>
> Dopasowanej tabeli:
>
> | Żądanie przychodzące       | Dopasowanej trasy |
> |------------------------|---------------|
> | Profile.domain.com/Other | Brak. Błąd 400: Nieprawidłowe żądanie. |

### <a name="routing-decision"></a>Decyzje dotyczące routingu
Po dopasowaliśmy jednej reguły routingu drzwiami frontowymi, następnie należy wybrać sposób przetworzyć żądanie. Jeśli dopasowane reguły routingu, drzwiami frontowymi ma buforowaną odpowiedź dostępne następnie takie same pobiera obsługiwane do klienta. W przeciwnym razie właśnie obliczaniu jest, czy skonfigurowano [(ścieżka przekazywanie niestandardowych) ponowne zapisywanie adresów URL](front-door-url-rewrite.md) dopasowane routingu reguły lub nie. Jeśli nie ma ścieżkę przekazywania niestandardowe zdefiniowane, żądanie zostanie przekazany do odpowiedniego zaplecza w puli zaplecza skonfigurowane, ponieważ jest. W przeciwnym wypadku ścieżki żądania zostanie zaktualizowany zgodnie [ścieżkę przekazywania niestandardowej](front-door-url-rewrite.md) zdefiniowane i następnie dalej do wewnętrznej bazy danych.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).
