---
title: Widok ruchu w usłudze Azure Traffic Manager
description: Wprowadzenie do usługi Traffic Manager funkcję Widok ruchu
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 70ac4319e2ea0081f7805c2fb936af1310d57d8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771604"
---
# <a name="traffic-manager-traffic-view"></a>Widok ruchu usługi Traffic Manager

Usługa Traffic Manager udostępnia z poziomu routingu DNS tak, aby użytkownicy końcowi są kierowane do dobrej kondycji punktów końcowych, na podstawie metody routingu określone podczas tworzenia profilu. Widok ruchu udostępnia usługi Traffic Manager pod kątem swojej bazy użytkowników (na poziomie szczegółowości rozpoznawania nazw DNS) i ich wzorzec ruchu. Jeśli włączysz funkcję Widok ruchu, te informacje są przetwarzane do przedstawienia uzyskiwanie przydatnych wyników analiz. 

Korzystając z funkcji Widok ruchu, możesz wykonywać następujące czynności:
- Dowiedz się, gdzie znajdują się (aż do lokalnego rozpoznawania nazw poziomu szczegółowości DNS) swojej bazy użytkowników.
- Wyświetl wielkość ruchu sieciowego (przestrzegane jako zapytania DNS, obsługiwane przez usługę Azure Traffic Manager) pochodzące z tych regionów.
- Uzyskaj szczegółowe informacje dotyczące nowości występujące opóźnienie reprezentatywne przez tych użytkowników.
- szczegółowe informacje na temat konkretne wzorce ruchu z każdej z tych baz użytkowników do regionów platformy Azure, w którym masz punktów końcowych. 

Na przykład można użyć widok ruchu, aby zrozumieć, które regiony z dużą liczbą ruchu, ale borykają się z większych opóźnień. Następnie można użyć tych informacji do zaplanowania Twojego rozszerzenia zużycie nowych regionów systemu Azure, dzięki czemu użytkownicy mają środowisku niższe opóźnienia.

## <a name="how-traffic-view-works"></a>Jak działa widok ruchu

Widok ruchu działa przez usługi Traffic Manager, spójrz na przychodzące zapytania odebrane w ciągu ostatnich siedmiu dni względem profilu, który ma włączenia tej funkcji. Z przychodzącego informacji zapytań widok ruchu wyodrębnia źródłowy adres IP programu rozpoznawania nazw DNS, używanego jako reprezentacja lokalizację użytkowników. Te są następnie grupowane na do rozpoznawania nazw poziomu szczegółowości DNS utworzyć podstawowy regionów użytkownika przy użyciu informacji geograficznych adresów IP, przechowywane przez usługę Traffic Manager. Usługa Traffic Manager sprawdza następnie regiony platformy Azure, do których zapytania został rozesłany i tworzy mapę przepływu ruchu sieciowego dla użytkowników z tych regionów.  
W następnym kroku usługi Traffic Manager koreluje region podstawowy użytkownika do mapowania region platformy Azure z tabelami opóźnienia sieci analizy, które utrzymuje dla różnych użytkowników końcowych sieci w celu zrozumienia średnie opóźnienie napotykanych przez użytkowników z tych regionów po Łączenie z regionów platformy Azure. Te obliczenia są następnie łączone w poszczególnych lokalnego rozpoznawania nazw IP poziomie usługi DNS przed są prezentowane użytkownikowi. Mogą wykorzystywać dane na różne sposoby.

Częstotliwość aktualizacji danych widoku ruchu zależy od wielu zmiennych wewnętrznego usługi. Jednak dane są zazwyczaj aktualizowane co 24 godziny.

>[!NOTE]
>Czas oczekiwania opisane w widoku ruchu jest występujące opóźnienie między użytkownika końcowego i regiony platformy Azure, do których uprzednio łączyli się i nie jest opóźnienie wyszukiwania DNS. Sprawia, że widok ruchu najlepsze możliwe oszacowanie nakład pracy opóźnienia między lokalnego rozpoznawania nazw DNS i regionu platformy Azure, które zapytania został rozesłany, jeśli dostępnych jest za mało danych, a następnie opóźnienie zwracana będzie mieć wartości null. 

## <a name="visual-overview"></a>Graficzne

Po przejściu do **widok ruchu** sekcji na stronie usługi Traffic Manager, są prezentowane za pomocą mapy geograficzne z nakładką ruchu Wyświetl szczegółowe dane. Mapa zawiera informacje dotyczące użytkowników i punktów końcowych dla profilu usługi Traffic Manager.

### <a name="user-base-information"></a>Podstawowe informacje użytkownika

Dla tych lokalnego rozpoznawania nazw DNS dla lokalizacji, do której informacje są dostępne są wyświetlane na mapie. Kolor program rozpoznawania nazw DNS wskazuje, że średni czas oczekiwania doświadczonym przez użytkowników końcowych, którzy korzystali z tego programu rozpoznawania nazw DNS dla ich zapytań usługi Traffic Manager.

Po umieszczeniu wskaźnika myszy nad lokalizacji programu rozpoznawania nazw DNS na mapie, które pokazuje:
- adres IP programu rozpoznawania nazw DNS
- wielkość ruchu sieciowego zapytania DNS zaobserwować przez usługę Traffic Manager
- punkty końcowe, do której ruchu przy użyciu serwera DNS rozpoznawania nazw został rozesłany, jako linię między punktem końcowym i program rozpoznawania nazw DNS 
- średnie opóźnienie z tej lokalizacji do punktu końcowego, reprezentowane jako koloru linii, łącząc je

### <a name="endpoint-information"></a>Informacje o punkcie końcowym

Regiony platformy Azure, w których znajdują się punkty końcowe są wyświetlane jako niebieskie kropki na mapie. Jeśli punkt końcowy usługi jest zewnętrzna i nie ma do niej przyporządkowany region platformy Azure, jest wyświetlany w górnej części mapy. Kliknij w dowolnym punkcie końcowym, aby wyświetlić różne lokalizacje (oparte na program rozpoznawania nazw DNS używane) z którym był przekierowywany ruch do tego punktu końcowego. Połączenia są wyświetlane jako linię między punktem końcowym i lokalizację programu rozpoznawania nazw DNS i mają kolor zgodnie z językiem opóźnienia między tym pary. Ponadto możesz zobaczyć nazwę punktu końcowego, region platformy Azure, w której jest uruchamiany i całkowita ilość żądań, które były kierowane do niego przez ten profil usługi Traffic Manager.


## <a name="tabular-listing-and-raw-data-download"></a>Tabelarycznej liście i pobierania danych pierwotnych

Można wyświetlić widok ruchu danych w formacie tabelarycznym w witrynie Azure portal. Istnieje wpis dla każdego adresu IP programu rozpoznawania nazw DNS / Sparuj punktu końcowego, który zawiera adres IP programu rozpoznawania nazw DNS, nazwy i lokalizacji geograficznej, regionu platformy Azure w umiejscowiony punktu końcowego (jeśli jest dostępny), liczba żądań skojarzone z tym rozpoznawania DNS punkt końcowy oraz występujące opóźnienie, które są skojarzone z użytkownikami końcowymi, za pomocą tej usługi DNS (jeśli dostępne). Widok ruchu danych można również pobrać jako plik CSV, który może służyć jako część przepływu pracy analizy wybranych przez użytkownika.

## <a name="billing"></a>Rozliczenia

Korzystając z widoku ruchu, są rozliczane na podstawie liczby punktów danych używanych do tworzenia przedstawione szczegółowe informacje. Obecnie jedynym typem punktu danych używane są zapytania odebrane względem profilu usługi Traffic Manager. Aby uzyskać więcej informacji na temat cen, odwiedź stronę [usługi Traffic Manager stronę z cennikiem](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez usługę Traffic Manager
- Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-create-profile.md)

