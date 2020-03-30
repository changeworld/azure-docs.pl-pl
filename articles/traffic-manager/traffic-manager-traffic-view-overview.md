---
title: Widok ruchu w usłudze Azure Traffic Manager
description: W tym wstępie dowiedz się, jak działa widok Ruchu menedżera.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938354"
---
# <a name="traffic-manager-traffic-view"></a>Widok ruchu menedżera ruchu

Menedżer ruchu zapewnia routing na poziomie DNS, dzięki czemu użytkownicy końcowi są kierowani do zdrowych punktów końcowych na podstawie metody routingu określonej podczas tworzenia profilu. Widok ruchu zapewnia usługa Traffic Manager z widokiem baz użytkowników (na poziomie szczegółowości rozpoznawania nazw DNS) i ich wzorzec ruchu. Po włączeniu widoku ruchu te informacje są przetwarzane w celu zapewnienia szczegółowych informacji z zasłyniętymi. 

Korzystając z widoku ruchu, można:
- zrozumieć, gdzie znajdują się bazy użytkowników (do lokalnego poziomu rozpoznawania nazw DNS ziarnistość).
- wyświetlanie natężenia ruchu (obserwowanego jako kwerendy DNS obsługiwane przez usługę Azure Traffic Manager) pochodzącego z tych regionów.
- uzyskać wgląd w to, co jest reprezentatywne opóźnienie doświadczane przez tych użytkowników.
- głębokie zagłębienie się w wzorce określonego ruchu z każdej z tych baz użytkowników do regionów platformy Azure, w których masz punkty końcowe. 

Na przykład można użyć widoku ruchu, aby zrozumieć, które regiony mają dużą liczbę ruchu, ale cierpią z powodu większych opóźnień. Następnie można użyć tych informacji, aby zaplanować rozszerzenie footprint do nowych regionów platformy Azure, dzięki czemu ci użytkownicy mogą mieć mniejsze opóźnienia.

## <a name="how-traffic-view-works"></a>Jak działa widok ruchu

Widok ruchu działa poprzez usługa Traffic Manager przyjrzyj się przychodzącym kwerendom odebranym w ciągu ostatnich siedmiu dni względem profilu, w który ma włączoną tę funkcję. Z informacji o kwerendach przychodzących widok ruchu wyodrębnia źródłowy adres IP programu rozpoznawania nazw DNS, który jest używany jako reprezentacja lokalizacji użytkowników. Są one następnie grupowane razem na poziomie rozpoznawania nazw DNS w celu utworzenia regionów podstawowych użytkowników przy użyciu informacji geograficznych adresów IP obsługiwanych przez menedżera ruchu. Usługa Traffic Manager następnie analizuje regiony platformy Azure, do których zapytanie zostało skierowane, i konstruuje mapę przepływu ruchu dla użytkowników z tych regionów.  
W następnym kroku usługa Traffic Manager koreluje region podstawowy użytkownika z mapowaniem regionu platformy Azure z tabelami opóźnienia analizy sieci, które przechowuje dla różnych sieci użytkowników końcowych, aby zrozumieć średnie opóźnienie, którego doświadczają użytkownicy z tych regionów, gdy łączenie się z regionami platformy Azure. Wszystkie te obliczenia są następnie łączone na poziomie lokalnego rozpoznawania nazw DNS, zanim zostaną ci przedstawione. Informacje można wykorzystywać na różne sposoby.

Częstotliwość aktualizacji danych widoku ruchu zależy od wielu wewnętrznych zmiennych usługi. Jednak dane są zwykle aktualizowane raz na 24 godziny.

>[!NOTE]
>Opóźnienie opisane w widoku ruchu jest reprezentatywne opóźnienie między użytkownikiem końcowym i regionów platformy Azure, do których zostały połączone i nie jest opóźnienie wyszukiwania DNS. Widok ruchu sprawia, że najlepszy wysiłek oszacować opóźnienie między lokalnym programem rozpoznawania nazw DNS i regionu platformy Azure kwerenda została skierowana do, jeśli nie ma wystarczających danych, a następnie opóźnienie zwrócone będzie null. 

## <a name="visual-overview"></a>Przegląd wizualny

Po przejściu do sekcji **Widok ruchu na** stronie Usługi Traffic Manager zostanie wyświetlona mapa geograficzna z nakładką na statystyki widoku ruchu. Mapa zawiera informacje o bazie użytkownika i punktach końcowych profilu usługi Traffic Manager.

![Widok geograficzny widoku ruchu menedżera ruchu][1]

### <a name="user-base-information"></a>Informacje podstawowe użytkownika

Dla tych lokalnych programów rozpoznawania nazw DNS, dla których informacje o lokalizacji są dostępne, są one wyświetlane na mapie. Kolor programu rozpoznawania nazw DNS oznacza średnie opóźnienie, jakie doświadczają użytkownicy końcowi, którzy używali tego programu rozpoznawania nazw DNS dla swoich zapytań usługi Traffic Manager.

Jeśli najedziesz kursorem DNS na lokalizację rozpoznawania nazw DNS na mapie, zostanie wyświetlenie:
- adres IP programu rozpoznawania nazw DNS
- wolumin ruchu zapytań DNS widoczny przez Menedżera ruchu z niego
- punkty końcowe, do których kierowano ruch z programu rozpoznawania nazw DNS, jako linię między punktem końcowym a programem rozpoznawania nazw DNS 
- średnie opóźnienie od tej lokalizacji do punktu końcowego, reprezentowane jako kolor linii łączącej je

### <a name="endpoint-information"></a>Informacje o punkcie końcowym

Regiony platformy Azure, w których znajdują się punkty końcowe są wyświetlane jako niebieskie kropki na mapie. Jeśli punkt końcowy jest zewnętrzny i nie ma zamapowany na niego regionu platformy Azure, jest on wyświetlany w górnej części mapy. Kliknij dowolny punkt końcowy, aby wyświetlić różne lokalizacje (na podstawie używanego programu rozpoznawania nazw DNS), z którego ruch został skierowany do tego punktu końcowego. Połączenia są wyświetlane jako linia między punktem końcowym a lokalizacją rozpoznawania nazw DNS i są kolorowe zgodnie z reprezentatywnym opóźnieniem między tą parą. Ponadto można wyświetlić nazwę punktu końcowego, regionu platformy Azure, w którym jest uruchamiany, i całkowitą liczbę żądań, które zostały skierowane do niego przez ten profil usługi Traffic Manager.


## <a name="tabular-listing-and-raw-data-download"></a>Lista tabelaryczny i pobieranie surowych danych

Dane widoku ruchu można wyświetlić w formacie tabelarycznym w witrynie Azure portal. Istnieje wpis dla każdej pary adresów rozpoznawania nazw DNS / punktu końcowego, który pokazuje adres IP programu rozpoznawania nazw DNS, nazwę i lokalizację geograficzną regionu platformy Azure, w którym znajduje się punkt końcowy (jeśli jest dostępny), ilość żądań skojarzonych z tym programem rozpoznawania nazw DNS do tego punktu końcowego oraz reprezentatywne opóźnienie skojarzone z użytkownikami końcowymi korzystającymi z tego systemu DNS (jeśli jest dostępne). Można również pobrać dane widoku ruchu jako plik CSV, który może być używany jako część wybranego przepływu pracy analizy.

## <a name="billing"></a>Rozliczenia

Korzystając z widoku ruchu, są naliczane na podstawie liczby punktów danych używanych do tworzenia prezentowanych statystyk. Obecnie jedynym typem punktu danych jest kwerendy odebrane w profilu usługi Traffic Manager. Aby uzyskać więcej informacji na temat cen, odwiedź [stronę z cennikiem Menedżera ruchu](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Często zadawane pytania

* [Do czego służy widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Jak mogę korzystać z usługi Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Czym różni się widok ruchu od metryk usługi Traffic Manager dostępnych za pośrednictwem monitora platformy Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Czy widok ruchu korzysta z informacji o podsieci klienta EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Ile dni danych używa widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Jak usługa Traffic View obsługuje zewnętrzne punkty końcowe?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Czy muszę włączyć widok ruchu dla każdego profilu w mojej subskrypcji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Jak wyłączyć widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Jak działa rozliczenia traffic view?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak działa Usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez usługę Traffic Manager
- Dowiedz się, jak [utworzyć profil usługi Traffic Manager](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png