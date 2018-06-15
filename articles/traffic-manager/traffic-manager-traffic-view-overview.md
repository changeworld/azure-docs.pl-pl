---
title: Ruch widoku w usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Wprowadzenie do widoku ruchu Menedżera ruchu
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 7ce51017fdee92e5589c06b398c9650930d5436d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179841"
---
# <a name="traffic-manager-traffic-view"></a>Widok ruchu Menedżera ruchu

Zawiera Menedżera ruchu z poziomu routingu DNS tak, aby użytkownicy końcowi są kierowane do dobrej kondycji punktów końcowych na podstawie metody routingu określone podczas tworzenia profilu. Widok ruchu udostępnia Menedżera ruchu z widokiem Twojej bazy użytkowników (na poziomie szczegółowości programu rozpoznawania nazw DNS) oraz ich wzorzec ruchu. Po włączeniu widoku ruchu, te informacje są przetwarzane dostarczają przydatnych wyników analiz. 

Przy użyciu widoku ruchu sieciowego, można:
- Dowiedz się, których Twojej bazy użytkowników znajdują się (do lokalnego DNS rozpoznawania poziomu szczegółowości).
- Wyświetl wielkość ruchu sieciowego (przestrzegane jako zapytania DNS obsługiwane przez usługę Azure Traffic Manager) pochodzące z tych obszarów.
- Uzyskaj wgląd w nowości reprezentatywny czas oczekiwania wystąpił przez tych użytkowników.
- Nowości w wzorce ruchu z każdej z tych zasad użytkownika do regiony platformy Azure, gdzie masz punktów końcowych. 

Na przykład służy widok ruchu do zrozumienia regiony dużą liczbą ruchu, ale boryka się z większych opóźnień. Następnie służą tych informacji do planowania użytkownika została zrozumiana. dzięki rozszerzania nowe regiony platformy Azure, aby te użytkownicy mogą mieć niższe środowisko opóźnienia.

## <a name="how-traffic-view-works"></a>Jak działa widok ruchu

Widok ruchu działa przez Menedżera ruchu przyjrzeć się zapytania przychodzące otrzymanych w ciągu ostatnich siedmiu dni przed profil, który ma ta funkcja jest włączona. Przychodzące informacji zapytania widok ruchu wyodrębnia źródłowy adres IP program rozpoznawania nazw DNS, która jest używana jako reprezentacja lokalizacji użytkowników. Te są następnie grupowane podsumowanie poziomu programu rozpoznawania nazw DNS do utworzenia użytkownika podstawowego regionów, korzystając z informacji geograficzne obsługiwane przez usługę Traffic Manager adresów IP. Menedżer ruchu następnie przegląda regiony platformy Azure, do których został skierowany zapytania i tworzy mapę przepływu ruchu sieciowego dla użytkowników z tych regionów.  
W następnym kroku Traffic Manager są powiązane z regionu podstawowego użytkownika do mapowania region platformy Azure z tabelami opóźnienia analizy sieci, które przechowuje dla różnych użytkowników końcowych sieci zrozumienie Średni czas oczekiwania u użytkowników z tych regionów po Łączenie z usługą regiony platformy Azure. Te obliczenia są następnie łączone w dla lokalnych DNS rozpoznawania adresu IP poziomu przed jego są prezentowane. Będzie można korzystać z informacji w różny sposób.

>[!NOTE]
>Opóźnienia opisanego w widoku ruchu jest reprezentatywny opóźnienia między użytkowników końcowych i regiony platformy Azure, do których uprzednio łączyli się i nie jest opóźnień wyszukiwania DNS. Ruch widoku sprawia, że zwrócony oszacowanie nakładu najlepsze opóźnienia między lokalnego rozpoznawania nazw DNS i regionu Azure, które zapytania został skierowany do, jeśli dostępnych jest za mało danych, a następnie opóźnienie będzie równy null. 

## <a name="visual-overview"></a>Omówienie Visual

Po przejściu do **widoku ruchu** sekcji na stronie usługi Traffic Manager prezentowany mapie geograficznej z nakładką ruchu wyświetlanie szczegółowych informacji. Mapy zawiera informacje dotyczące użytkowników i punktów końcowych dla profilu Menedżera ruchu.

### <a name="user-base-information"></a>Podstawowe informacje o użytkowniku

Dla tych lokalnego rozpoznawania nazw DNS dla lokalizacji, które informacje są dostępne są wyświetlane na mapie. Kolor program rozpoznawania nazw DNS oznacza średni czas oczekiwania doświadczonym przez użytkowników końcowych, którzy korzystali z tym program rozpoznawania nazw DNS dla ich zapytań usługi Traffic Manager.

Jeśli aktywuj lokalizację programu rozpoznawania nazw DNS na mapie, zawiera:
- adres IP program rozpoznawania nazw DNS
- wielkość ruchu sieciowego zapytania DNS zobaczyć przez Menedżera ruchu
- punkty końcowe, do których ruch z DNS rozpoznawania został skierowany, jako linii między punktem końcowym i program rozpoznawania nazw DNS 
- Średni czas oczekiwania z tej lokalizacji do punktu końcowego reprezentowane jako koloru linii, łącząc je

### <a name="endpoint-information"></a>Informacje o punkcie końcowym

Regiony platformy Azure, w których znajdują się punkty końcowe są wyświetlane jako niebieskie kropki na mapie. Jeśli punkt końcowy jest zewnętrzna i nie ma region platformy Azure do niej przyporządkowany, jest wyświetlany w górnej części mapy. Polecenie dowolnego punktu końcowego, aby wyświetlić różne lokalizacje (oparte na używany program rozpoznawania nazw DNS) z który ruch został skierowany do określonego punktu końcowego. Połączenia są wyświetlane w formie linii między punktem końcowym i lokalizację programu rozpoznawania nazw DNS i mają kolor zgodnie z reprezentatywny opóźnienia między tym pary. Ponadto można zobaczyć nazwę tego punktu końcowego, region platformy Azure, w której jest uruchamiana i całkowitej liczby żądań, które były kierowane do niej przez ten profil Menedżera ruchu.


## <a name="tabular-listing-and-raw-data-download"></a>Lista tabelarycznych i pobierania danych pierwotnych

Dane widoku ruchu można wyświetlić w formacie tabelarycznym w portalu Azure. Istnieje wpis dla każdego adresu IP programu rozpoznawania nazw DNS / pary punktu końcowego, który zawiera adres IP program rozpoznawania nazw DNS, nazwy i lokalizacji geograficznej regionu Azure, w którym znajduje się punkt końcowy, (jeśli jest dostępny), odebranej liczby żądań skojarzone z tym programem rozpoznawania nazw DNS do tego punktu końcowego, a reprezentatywny opóźnienia związanego z użytkownikami końcowymi za pomocą tej usługi DNS (jeśli jest dostępna). Możesz również pobrać dane widoku ruchu jako pliku CSV, który może służyć jako część przepływu pracy analytics wybranych przez użytkownika.

## <a name="billing"></a>Rozliczenia

Korzystając z widoku ruch, są rozliczane na podstawie liczby punktów danych używany do tworzenia analiz przedstawiony. Obecnie typ punktu tylko danych używany jest zapytań odebranych z profilu Menedżera ruchu. Aby uzyskać więcej informacji o cenach, odwiedź stronę [cennikiem usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się [działania Menedżera ruchu](traffic-manager-overview.md)
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez Menedżera ruchu
- Dowiedz się, jak [Tworzenie profilu Menedżera ruchu](traffic-manager-create-profile.md)

