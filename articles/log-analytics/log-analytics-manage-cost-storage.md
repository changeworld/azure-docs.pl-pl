---
title: Zarządzanie kosztami danych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić plan taryfowy i zarządzanie zasadami ilości i przechowywania danych obszaru roboczego usługi Log Analytics na platformie Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 8343c27693538480757a879e477ec77fba08de3d
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958342"
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Zarządzanie kosztami przez kontrolowanie ilości danych i przechowywania w usłudze Log Analytics

> [!NOTE]
> W tym artykule opisano sposób kontrolowania kosztów w usłudze Log Analytics, ustawiając okres przechowywania danych.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Analizowanie użycia danych w usłudze Log Analytics](log-analytics-manage-cost-storage.md) w tym artykule opisano sposób analizowanie i alerty dotyczące użycia danych.
> - [Monitorowanie użycia i szacowanych kosztów](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

Usługa log Analytics została zaprojektowana do skalowania i zbieranie pomocy technicznej, indeksowanie i przechowywanie dużych ilości danych na dzień z dowolnego źródła w przedsiębiorstwie lub wdrożonych na platformie Azure.  Chociaż może to być sterownik podstawowy dla Twojej organizacji, niskich kosztów ostatecznie to klient jest podstawowy sterownik. W tym celu, istotne jest dowiedzieć się, że koszt obszaru roboczego usługi Log Analytics nie jest po prostu oparty na temat ilości zebranych danych, również jest zależny od wybranym planem i jak długo został wybrany do przechowywania danych generowanych przez połączone źródła.  

W tym artykule omówimy, jak można proaktywnie monitorować wzrostu wielkości i magazynu danych i definiowania ograniczeń do kontrolowania tych powiązanych z nimi kosztów. 

Koszt dane mogą być znaczące w zależności od następujących czynników: 

- Liczba systemów, składników infrastruktury, zasobów w chmurze, itp., które są zbierane z 
- Typ danych utworzone przez źródła, takich jak kolejki komunikatów, dzienniki, zdarzenia, dane związane z zabezpieczeniami i metryki wydajności 
- Ilość danych generowanych przez te źródła i pozyskane do obszaru roboczego 
- Okresu dane są przechowywane w obszarze roboczym  
- Liczba rozwiązań do zarządzania włączone, źródła danych i częstotliwość zbierania 

> [!NOTE]
> Zapoznaj się z dokumentacją dla każdego rozwiązania ponieważ umożliwia szacowanie ilości danych, które są zbierane.   

Klienci z umową Enterprise Agreement podpisaną przed 1 lipca 2018 r. lub twórcę już obszar roboczy usługi Log Analytics w ramach subskrypcji, możesz nadal mieć dostęp do *bezpłatna* planu. Jeśli Twoja subskrypcja nie jest związany z istniejącą rejestracją umowy EA *bezpłatna* warstwa nie jest dostępna podczas tworzenia obszaru roboczego w nowej subskrypcji po 2 kwietnia 2018 r.  Dane są ograniczone do 7 dni przechowywania dla *bezpłatna* warstwy.  Dla *autonomiczny* lub *płatnych* warstwy, zebrane dane są dostępne w ciągu ostatnich 31 dni. *Bezpłatna* warstwa ma dziennego limitu pozyskiwania 500 MB, a jeśli okaże się stale przekraczać kwoty dozwolone woluminu, można zmienić obszaru roboczego do płatnego planu, aby zbierać dane powyżej tego limitu. 

> [!NOTE]
> Opłaty są naliczane, jeśli zdecydujesz wybrać dłuższy okres przechowywania danych w warstwie płatnej. Możesz zmienić typ planu w dowolnym momencie i aby uzyskać więcej informacji na temat cen, zobacz [cennik](https://azure.microsoft.com/pricing/details/log-analytics/). 

Istnieją dwa sposoby, w których ilości danych może być ograniczona i pomocy kontrolowanie kosztów, są one dzienny limit i przechowywania danych.  

## <a name="review-estimated-cost"></a>Przejrzyj szacowany koszt
Log Analytics sprawia, że ułatwiają zrozumienie, co prawdopodobnie koszty oparte na najnowszych wzorców użycia.  Aby to zrobić, wykonaj następujące czynności.  

1. Zaloguj się do [Azure Portal](http://portal.azure.com). 
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-manage-cost-storage/azure-portal-01.png)<br><br>  
3. W okienku subskrypcji usługi Log Analytics wybierz swój obszar roboczy, a następnie kliknij przycisk **użycie i szacunkowe koszty** z okienka po lewej stronie.<br><br> ![Użycie i szacowane koszty strony](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

W tym miejscu możesz przejrzeć ilość danych na miesiąc. Obejmuje to wszystkie dane odebrane i przechowywane w obszarze roboczym usługi Log Analytics.  Kliknij przycisk **szczegóły użycia** w górnej części strony Aby wyświetlić pulpit nawigacyjny użycia z informacjami na temat tendencji woluminu danych źródła, komputerów i oferty. Aby wyświetlić i określić dzienny limit lub zmodyfikować okres przechowywania, kliknij przycisk **zarządzanie ilością danych**.
 
Log Analytics opłaty są dodawane na rachunku dotyczącym platformy Azure. Aby wyświetlić szczegóły subskrypcji platformy Azure, w sekcji rozliczeń w witrynie Azure Portal lub w przypadku płatności [portalu rozliczeń systemu Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Dzienny limit
Podczas tworzenia obszaru roboczego usługi Log Analytics z witryny Azure portal i wybierz *bezpłatna* planu, ustawiana jest na 500 MB dziennie, limit na. Nie ma limitu dla innych planów cenowych. Możesz skonfigurować dzienny limit i ograniczyć dziennie pozyskiwanie informacji do obszaru roboczego, ale zachować ostrożność, ponieważ cel nie powinno być osiągnięty dzienny limit.  W przeciwnym razie możesz utracić dane na pozostałą część dnia, w którym mogą mieć wpływ na inne usługi platformy Azure i rozwiązania, którego funkcja może zależeć od aktualnych danych dostępnych w obszarze roboczym.  W rezultacie możliwość obserwowania i otrzymywać powiadamia, gdy warunki zdrowia w zasobach obsługujących usługi IT ma wpływ.  Dzienny limit jest przeznaczona do użycia jako sposób na zarządzanie nieoczekiwany wzrost ilości danych z zarządzanych zasobów i pozostają w zakupionym limicie lub gdy chcesz po prostu ograniczaniu nieplanowanych opłat za obszar roboczy.  

Po osiągnięciu dziennego limitu kolekcję typów danych płatnych zatrzymuje się do końca dnia. Transparent ostrzeżenie pojawia się w górnej części strony dla wybranego obszaru roboczego usługi Log Analytics i zdarzenie operacji są wysyłane do *operacji* tabeli w ramach **LogManagement** kategorii. Zbieranie danych wznawia działanie po podczas resetowania zdefiniowane w obszarze *dzienny limit jest ustawiony na*. Firma Microsoft zaleca, definiując reguły alertu na podstawie zdarzeń tej operacji, skonfigurowana do wysyłania powiadomień, gdy został osiągnięty dzienny limit danych. 

### <a name="identify-what-daily-data-limit-to-define"></a>Zidentyfikować jakie dzienny limit danych, aby zdefiniować 
Przegląd [użycie usługi Log Analytics i szacowane koszty](log-analytics-usage.md) , aby zrozumieć trend pozyskiwania danych i co to jest dziennego limitu woluminu w celu zdefiniowania. Należy rozważyć ostrożnie, ponieważ nie będzie można monitorować swoje zasoby, po osiągnięciu limitu. 

### <a name="manage-the-maximum-daily-data-volume"></a>Zarządzanie Maksymalna dzienna ilość danych 
Poniżej opisano sposób konfigurowania limit zarządzanie ilością danych, która będzie obsługiwać usługi Log Analytics, dziennie.  

1. W obszarze roboczym wybierz **użycie i szacunkowe koszty** z okienka po lewej stronie.
2. Na **użycie i szacunkowe koszty** dla wybranego obszaru roboczego strony, kliknij przycisk **zarządzanie ilością danych** w górnej części strony. 
5. Dzienny limit jest **OFF** domyślnie — kliknij przycisk **ON** można ją włączyć, a następnie ustaw limit objętość danych w GB/dzień.<br><br> ![Usługa log Analytics skonfigurować limit danych](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Zgłoś alert, gdy osiągnięto limit
Gdy możemy przedstawić wizualnie w witrynie Azure portal po spełnieniu próg limitu danych tego zachowania nie musi być wyrównane do, w jaki sposób zarządzasz problemów operacyjnych, które wymaga natychmiastowej uwagi.  Aby otrzymywać powiadomień o alertach, można utworzyć nową regułę alertu w usłudze Azure Monitor.  Aby dowiedzieć się więcej, zobacz [jak tworzyć, wyświetlać alerty i zarządzaj nimi](../monitoring-and-diagnostics/alert-metric.md).      

Aby ułatwić rozpoczęcie pracy, Oto zalecane ustawienia dla alertu:

* Cel: Wybierz zasób usługi Log Analytics
* Kryteria: 
   * Nazwa sygnału: przeszukiwanie dzienników niestandardowych
   * Zapytanie wyszukiwania: operacja | których szczegóły zawiera "Przekroczenie"
   * Na podstawie: liczba wyników
   * Warunek: Większa niż
   * Próg: 0
   * Okres: 5 (w minutach)
   * Częstotliwość: 5 (w minutach)
* Nazwa reguły alertu: osiągnięto dzienny limit danych
* Ważność: Ostrzegawczy (ważność 1)

Gdy alert jest zdefiniowana i limit zostanie osiągnięty, alert zostanie wyzwolony i wykonuje odpowiedzi zdefiniowany w grupy akcji. Powiadamiaj zespół za pośrednictwem wiadomości e-mail i SMS lub Automatyzuj akcje przy użyciu elementów webhook i runbook usługi Automation lub [integrację z zewnętrznego rozwiązaniem ITSM](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Zmienić okres przechowywania danych 
Poniżej opisano sposób konfigurowania dziennika jak długo dane są przechowywane w obszarze roboczym.
 
1. W obszarze roboczym wybierz **użycie i szacunkowe koszty** z okienka po lewej stronie.
2. Na **użycie i szacunkowe koszty** kliknij **zarządzanie ilością danych** w górnej części strony.
5. W okienku, przesuń suwak, aby zwiększyć lub zmniejszyć liczbę dni, a następnie kliknij przycisk **OK**.  Jeśli użytkownik pracuje na *bezpłatne* warstwy, nie można zmodyfikować okres przechowywania danych i należy uaktualnić do warstwy płatnej w celu kontrolowania tego ustawienia.<br><br> ![Zmiana ustawienia przechowywania danych w obszarze roboczym](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
**Pytanie**: jak rozwiązać, jeśli nie jest już usługi Log Analytics zbiera dane? 
**Odpowiedź**: Jeśli są na bezpłatną warstwę cenową i wysłane w ciągu dnia więcej niż 500 MB danych, zbieranie danych nie będzie możliwy do końca dnia. Osiągnięcia dziennego limitu jest typową przyczyną, usługi Log Analytics zatrzymuje proces zbierania danych lub danych prawdopodobnie brakuje.  
Usługa log Analytics tworzy zdarzenie typu operacji podczas zbierania danych uruchamia i zatrzymuje.  
Uruchom następujące zapytanie w polu wyszukiwania, aby sprawdzić, jeśli osiągnięcia dziennego limitu i Brak danych: operacja | gdzie OperationCategory == "Stan zbierania danych"   
Po zatrzymaniu zbierania danych OperationStatus jest ostrzeżenie. Podczas uruchamiania zbierania danych OperationStatus jest zakończyło się pomyślnie.  
W poniższej tabeli opisano powody, dla których zatrzymuje zbieranie danych i zalecaną akcję, aby wznowić zbieranie danych:  

|Zatrzymuje kolekcję Przyczyna| Rozwiązanie| 
|-----------------------|---------|
|Osiągnięto dzienny limit bezpłatnych danych<sup>1</sup>|Poczekaj, aż do następnego dnia, aby Kolekcjonowanie mogło być automatycznie uruchomiony ponownie lub Zmień na płatną warstwę cenową.|
|Osiągnięto dzienny limit, które zostały zdefiniowane w zarządzanie ilością danych|Poczekaj, aż do następnego dnia, aby Kolekcjonowanie mogło być automatycznie uruchomiony ponownie, lub zwiększ dziennego limitu woluminu danych opisanych w [Zarządzanie Maksymalna dzienna ilość danych](#manage-the-maximum-daily-volume)|
|Subskrypcja platformy Azure jest w stanie wstrzymania ze względu na:<br> Bezpłatny okres próbny zakończył się<br> Azure — dostęp próbny wygasł<br> Co miesiąc limit wydatków osiągnięto (na przykład w ramach subskrypcji MSDN lub Visual Studio)|Konwertuj na płatną subskrypcję<br> Usuń limit lub poczekaj na zresetowanie limitu|

<sup>1</sup> przypadku obszaru roboczego w ramach warstwy cenowej bezpłatna jest ograniczona do wysyłanie 500 MB danych dziennie do usługi. Po osiągnięciu dziennego limitu gromadzenie danych zatrzymuje się od następnego dnia. Dane wysłane podczas zatrzymania zbierania danych nie jest indeksowana i nie jest dostępna dla wyszukiwania. Po wznowieniu pracy zbierania danych, przetwarzanie odbywa się tylko w przypadku nowych przesłanych danych. 

Usługa log Analytics korzysta z czasu UTC. Podczas resetowania waha się między obszarami roboczymi, aby uniemożliwić wszystkie zaczynają ograniczona obszary robocze, które dane są wprowadzane w tym samym czasie. Jeśli obszar roboczy osiągnie limit dzienny, przetwarzanie wznawia działanie po czas resetowania zdefiniowany w **dzienny limit jest ustawiony na**.<br><br> ![Usługa log Analytics ograniczyć strefą czasową UTC](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Pytanie**: jak mogę otrzymywać powiadomienia po zatrzymaniu zbierania danych? 
**Odpowiedź**: wykonaj czynności opisane w *dziennego limitu danych utwórz* alert, aby otrzymywać powiadomienia, po zatrzymaniu zbierania danych i postępuj zgodnie z instrukcjami, wykonaj czynności opisane w Dodawanie akcji do reguły alertów skonfigurować wiadomości e-mail, element webhook lub runbook Akcja reguły alertu. 

## <a name="next-steps"></a>Kolejne kroki  

Aby określić, ile dane są zbierane, jakie źródła są wysyłania, a także różne rodzaje danych wysyłanych ułatwiające zarządzanie użycia i kosztów, zobacz [Analizowanie użycia danych w usłudze Log Analytics](log-analytics-usage.md).
