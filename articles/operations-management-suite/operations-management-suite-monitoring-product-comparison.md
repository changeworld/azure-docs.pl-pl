---
title: Monitorowanie porównanie produktów firmy Microsoft | Dokumentacja firmy Microsoft
description: Microsoft Operations Management Suite (OMS) to rozwiązanie do zarządzania IT, które pomaga zarządzać i chronić lokalnej i w chmurze infrastruktury opartej na chmurze firmy Microsoft.  W tym artykule opisano różne usługi w ramach pakietu OMS i umieszczono linki do szczegółowych treści na ich temat.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: 6da876a0e6c1e98683caa864a4a2bcf85195cd10
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753323"
---
# <a name="microsoft-monitoring-product-comparison"></a>Monitorowania porównanie produktów firmy Microsoft
W tym artykule przedstawiono porównanie ich architektury, logiki jak monitorować ich zasobów i jak są analizy danych, które pobierają programu System Center Operations Manager (SCOM) i analizy dzienników w Operations Management Suite (OMS) .  To zapewniają podstawowe zrozumienie ich różnice i siły względnej.  

## <a name="basic-architecture"></a>Podstawowa architektura
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Wszystkie składniki programu SCOM są zainstalowane w centrum danych.  [Agenci są zainstalowani](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-console) na maszynach z systemem Windows i Linux, które są zarządzane przez SCOM.  Nawiązać agenci [serwerów zarządzania](https://technet.microsoft.com/library/hh301922.aspx) którego komunikować się z magazynem danych i bazy danych SCOM.  Agenci korzystają z uwierzytelniania domeny łączyć się z serwerami zarządzania.  Te poza zaufanej domenie uwierzytelniania certyfikatów, czyli nawiązywanie [serwera bramy](https://technet.microsoft.com/library/hh212823.aspx).

SCOM wymaga dwóch baz danych, dla danych operacyjnych i inny magazyn danych do obsługi raportowanie i analiza danych.  A [Reporting Server](https://technet.microsoft.com/library/hh298611.aspx) uruchamia usługi SQL Reporting Services do raportowania danych z magazynu danych. 

SCOM można monitorować zasobów w chmurze przy użyciu pakietów administracyjnych dla produktów takich jak [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [usługi Office 365](https://www.microsoft.com/download/details.aspx?id=43708), i [usług AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Te pakiety administracyjne Użyj co najmniej jeden agent lokalny jako serwery proxy do odnajdowania zasobów w chmurze i uruchomionych przepływów pracy do pomiaru ich wydajności i dostępności.  Agenci proxy są również używane do [monitorować urządzenia sieciowe](https://technet.microsoft.com/library/hh212935.aspx) i innych zasobów zewnętrznych.

Konsola operacje jest aplikacji systemu Windows, który jest podłączany do jednego z serwerów zarządzania i pozwala administratorowi na wyświetlanie i analizowanie danych zebranych i konfigurowanie środowiska SCOM.  Konsola sieci web może znajdować się na dowolnym serwerze usług IIS i zapewnia analizy danych za pośrednictwem przeglądarki.

![Architektura programu SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
Większość składników pakietu OMS są w chmurze Azure, aby można było wdrożyć i zarządzania nim za pomocą minimalne kosztów i prac administracyjnych związanych.  Wszystkie dane zebrane przez analizy dzienników są przechowywane w repozytorium OMS.

Analiza dzienników może zbierać dane z jednego z trzech źródeł:

* Fizycznych i maszyn wirtualnych z systemem Windows i [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) lub Linux i [Operations Management Suite agenta dla systemu Linux](https://technet.microsoft.com/library/mt622052.aspx).  Te maszyny można lokalnymi lub maszyn wirtualnych w Azure lub innej chmurze.
* Konto magazynu Azure z [diagnostyki Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) dane zebrane przez roli procesu roboczego platformy Azure, roli sieci web lub maszyny wirtualnej.
* [Połączenie z grupą zarządzania programu SCOM](https://technet.microsoft.com/library/mt484104.aspx).  W tej konfiguracji agentów komunikują się z serwerami zarządzania programu SCOM, które dostarczają dane do bazy danych SCOM, gdzie następnie jest dostarczany do magazynu danych OMS.
  Administratorzy analizowanie zebranych danych i konfigurowanie analizy dzienników przy użyciu portalu OMS, na którym jest hostowany na platformie Azure i są dostępne z dowolnej przeglądarki.  Aplikacje mobilne, aby dostęp do tych danych są dostępne dla standardowych platform.

![Architektura analizy dzienników](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrowanie programu SCOM i analizy dzienników
Kiedy SCOM jest używany jako źródło danych do analizy dzienników można korzystać z funkcji obu produktów w hybrydowego monitorowania środowiska.  Można skonfigurować istniejącą agentów SCOM za pomocą konsoli operacje, które mają być zarządzane przez OMS, oprócz kontynuowani e uruchamiania pakietów administracyjnych z programu SCOM.  
Dane z podłączonej grupy zarządzania SCOM są dostarczane do analizy dzienników, przy użyciu jednej z czterech metod:

* Zdarzenia i dane wydajności są zbierane przez agenta i dostarczane do SCOM.  Serwery zarządzania w SCOM dostarcza dane do analizy dzienników.
* Niektóre zdarzenia, takie jak dzienniki programu IIS i zdarzenia zabezpieczeń są nadal mają być dostarczane bezpośrednio do analizy dzienników od agenta.
* Niektóre rozwiązania będzie dostarczać dodatkowe oprogramowanie do agenta lub wymagają zainstalowania oprogramowania do zbierania dodatkowych danych.  Te dane będą zazwyczaj wysłana bezpośrednio do analizy dzienników.
* Niektóre rozwiązania będzie zbierać dane bezpośrednio z serwerów zarządzania programu SCOM, które nie pochodzą z agenta.  Na przykład [rozwiązania zarządzania alertami](https://technet.microsoft.com/library/mt484092.aspx) gromadzi alerty z programu SCOM, po ich utworzeniu.

## <a name="monitoring-logic"></a>Logika monitorowania
SCOM i analizy dzienników współpracować z podobne dane zbierane przez agentów, ale mają podstawowe różnice w sposób definiowania i wdrożyć ich logikę zbierania danych i sposobu ich analizowania zbieranych danych.

### <a name="operations-manager"></a>Operations Manager
Logika monitorowania dla programu SCOM jest zaimplementowana w [pakietów administracyjnych](https://technet.microsoft.com/library/hh457558.aspx) zawierających logikę wykrywania składniki do monitorowania, pomiaru kondycji tych składników, a także do zbierania danych do analizy.  Dane monitorowania może być prosty jak zbieranie licznik zdarzeń lub wydajności lub można użyć złożonej logiki zaimplementowana w skrypcie.  Pakiety administracyjne, które obejmują pełne monitorowania są dostępne dla różnych [aplikacji firmy Microsoft i innych firm](http://go.microsoft.com/fwlink/?LinkId=82105) oprócz urządzeń sprzętowych i sieciowych.  Możesz [tworzenie własnych pakietów administracyjnych](http://aka.ms/mpauthor) dla niestandardowych aplikacji.

Pakiety administracyjne zawierają wiele przepływów pracy pełniących niektórych odrębne funkcje monitorowania takich jak próbkowania licznika wydajności, sprawdzanie stanu usługi lub uruchamianie skryptu.  Każdy przepływ pracy działa niezależnie i definiuje własny wyniki, takie jak bazę danych, która będzie zapisywała do i czy będzie generować alert. 

Szczegółowe informacje o przepływie pracy, takich jak częstotliwość, które uruchamiać, próg, gdzie uznają błąd i ważność alertu, które generują można zastąpić.  Można też podać dodatkowe funkcje, dodając własne przepływów pracy.

![Zastąpienia](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Pakiety administracyjne zainstalowane w bazie danych programu Operations Manager i automatycznie dystrybuowana do agentów serwerów zarządzania.  Każdy agent automatycznie pobierze pakietów administracyjnych i załadować przepływy pracy dotyczące aplikacji, które zostały one zainstalowane.  Danych zbieranych przez agenta jest dostarczana z powrotem do serwera zarządzania w celu wstawienia go do magazynu danych i bazy danych programu SCOM.  Konsoli operacje można przeglądać i analizować te dane przy użyciu niestandardowych widoków, pulpity nawigacyjne i raporty zawarte w pakiecie administracyjnym.

Na poniższym diagramie przedstawiono dystrybucji pakietów administracyjnych.

![Przepływ pakiet zarządzania](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Zdarzenia i zbieranie danych wydajności
Analiza dzienników gromadzi zdarzenia i liczniki wydajności z systemów agenta przy użyciu źródeł, takich jak dziennika zdarzeń systemu Windows, dzienniki programu IIS i Syslog.  Można określić kryteria, dla których dane są zbierane za pomocą portalu usługi Analiza dzienników, a następnie utwórz umożliwiają analizowanie zebranych danych zapytań dziennika.  Standardowe kryteria określone jest definiowany podczas tworzenia obszaru roboczego OMS i można zdefiniować dodatkowe dane dla określonej aplikacji. 

![Definiowanie dzienników zdarzeń w analizy dzienników](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Kiedy SCOM ma wiele szczegółowe przepływów pracy, które zazwyczaj określić kryteria dla danych i akcji, która powinna być wykonywana w odpowiedzi, Log Analytics ma bardziej ogólne kryteria zbierania danych.  Dziennika zapytań i rozwiązań zapewnia bardziej docelowe kryteria do analizowania i działające na określonych danych w chmurze po zostały zebrane.

#### <a name="solutions"></a>Rozwiązania
Rozwiązań zapewnia dodatkową logikę do zbierania danych i analiz.  Możesz wybrać rozwiązania, aby dodać do subskrypcji OMS z galerii rozwiązania.

![Galeria rozwiązań](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Rozwiązania głównie uruchomienia w chmurze, umożliwiając analizę zdarzenia i liczniki wydajności zebrane w repozytorium OMS.  Mogą również określić dodatkowe dane mają zostać zebrane można analizować z zapytaniami dziennika lub przy użyciu interfejsu użytkownika dodatkowe podane przez rozwiązanie na pulpicie nawigacyjnym OMS. 

Na przykład [śledzenia zmian rozwiązania](https://technet.microsoft.com/library/mt484099.aspx) wykrywa konfiguracji zmiany w systemach agenta i zapisuje zdarzenia do repozytorium OMS, które mogą być analizowane za pomocą kilku graficznego widoki, które zawierają podsumowanie wykrył zmiany.  Użytkownik może przejść do szczegółów w widoku podsumowania do dziennika zapytań, które wyświetlić szczegółowe dane zebrane przez rozwiązanie.

Gdy wybierzesz rozwiązania, które można dodać do subskrypcji, obecnie nie ma możliwość tworzenia własnych rozwiązań.  Możesz wybrać zdarzenia i liczniki wydajności do zbierania i tworzenie niestandardowych widoków opartych na kwerendach dziennika.

Na poniższym diagramie podsumowanie logikę monitorowania analizy dzienników.

![Przepływ rozwiązania analizy dzienników](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Monitorowanie kondycji
### <a name="operations-manager"></a>Operations Manager
SCOM można modelu różne składniki aplikacji i zapewniają kondycji w czasie rzeczywistym dla każdego.  Dzięki temu można nie tylko błędy wykryte widoku i wydajności w czasie, ale także do sprawdzania poprawności rzeczywiste kondycji aplikacji lub systemu i wszystkich jego składników w danym momencie.  Ponieważ sam okresów, które aplikacja jest dostępna, aparat kondycji w SCOM obsługuje również poziomu usług umów (SLA), którego analiza i raport o dostępności aplikacji wraz z upływem czasu.

Na przykład poniższa ilustracja przedstawia kondycję w czasie rzeczywistym monitorowane przez SCOM aparatów bazy danych SQL.  Kondycję wszystkich baz danych dla jednej z baz danych przedstawiono w dolnej połowie widoku.

![Widok stanu](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Poniżej przedstawiono Eksploratora kondycji dla jednej z baz danych z monitorami, które są używane do ustalenia jego ogólna kondycja.  Te monitory są zdefiniowane w pakiecie administracyjnym SQL, a następnie uruchom na aparatów bazy danych SQL wszystkie odnalezione przez SCOM.

![Eksplorator kondycji](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Kondycję aplikacji rozproszonej można łączyć składników w wielu systemach.  Może to być szczególnie przydatne w przypadku aplikacji LOB, obejmujących wiele składników rozproszonych.  Można utworzyć modelu, który mierzy kondycję każdego składnika czy pakiet zbiorczy aktualizacji do dostępności dla aplikacji.

Usługa Active Directory jest przykładem jeden pakiet administracyjny, który udostępnia model do analizowania jego składników rozproszonych.  Na poniższym diagramie przykładowych pokazano kondycji ogólnej środowiska i relacji między lasami, domenami i kontrolerów domeny.  Każdy z tych składników obejmuje podskładniki i wiele monitorów, podobnie jak w powyższym przykładzie SQL.

![Widok diagramu programu SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS nie zawierają wspólne engine do aplikacji modelu lub miary, ich kondycję w czasie rzeczywistym.  Poszczególne rozwiązania może ocenić ogólną kondycję konkretnych usług na podstawie zebranych danych i może instalacji niestandardowej logiki dla agenta, aby przeprowadzić analizę w czasie rzeczywistym.  Ponieważ rozwiązania są uruchamiane w chmurze za pomocą dostępu do repozytorium OMS, często zapewniają dokładniejszej analizy nie jest zazwyczaj wykonywane przez pakiety administracyjne. 

Na przykład [oceny usługi AD i oceny SQL rozwiązania](https://technet.microsoft.com/library/mt484102.aspx) analizowanie zebranych danych i podaj klasyfikacji dla różnych aspektów środowiska.  Zawiera zalecenia dotyczące ulepszeń, które można podjąć w celu zwiększenia dostępności i wydajności środowiska.

![Rozwiązanie do oceny usługi AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Analiza danych
SCOM, jak i analizy dzienników Podaj różnych funkcji do analizowania zebranych danych.  SCOM ma widoków i pulpitów nawigacyjnych w konsoli operacje na potrzeby analizowania najnowszych danych w różnych formatach i raportów, prezentacji danych z magazynu danych w formie tabelarycznej.  Analiza dzienników zapewnia pełny dziennik język zapytań i interfejsu analizowania danych w repozytorium OMS.  Gdy SCOM jest używany jako źródło danych analizy dzienników, repozytorium zawiera dane zebrane przez SCOM dzięki narzędzia analizy dzienników mogą być używane do analizowania danych z tymi dwoma systemami.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Widoki
Widoki w konsoli operacje pozwalają na wyświetlanie różnych typów danych zebranych przez SCOM w różnych formatach, zwykle tabelarycznym zdarzeń, alertów i dane o stanie i wiersz wykresy danych wydajności.  Widoki wykonywania analizy minimalnego lub konsolidacji danych, ale umożliwiają filtrowanie zgodnie z kryteriami. 

![Widoki](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Pakiety administracyjne zwykle zawierają wiele widoków obsługi aplikacji lub systemu, którą monitoruje.  Może to obejmować widoki stanu dla różnych obiektów, które pakiet administracyjny umożliwia odnajdywanie, widoki alertów wykrytych problemów i widokach wydajności dla liczników.

Widoki są szczególnie przydatny w przypadku analizowania bieżący stan środowiska w tym otwartych alertów i kondycję monitorowanych systemów i obiektów.  Użytkownik może przejść do szczegółów szczegółowe zdarzenia lub dane dotyczące wydajności obsługi danego alertu w celu zdiagnozowania jego główną przyczynę. Podobnie można wyświetlić, wydajności i kondycji poszczególnych składników aplikacji w celu oszacowania jego bieżącego stanu.

#### <a name="dashboards"></a>Pulpity nawigacyjne
Pulpity nawigacyjne w konsoli operacje głównie współpracować z tych samych danych widoków, ale więcej można dostosowywać i może zawierać wizualizacje bardziej zaawansowane funkcje.  Zbiór standardowych pulpity nawigacyjne są dostępne, że można łatwo dostosować do własnych celów.  Umożliwia także widget programu PowerShell, który można wyświetlić dane zwrócone w wyniku zapytania programu PowerShell.

![Pulpit nawigacyjny](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Deweloperzy mają możliwość dodawania niestandardowych składników do pulpitów nawigacyjnych, które są umieszczane w pakietach administracyjnych, ich.  Te mogą być bardzo specjalizowany do konkretnej aplikacji, takich jak pulpit nawigacyjny w pakiecie administracyjnym SQL, pokazano poniżej.  Ten pulpit nawigacyjny mogą służyć jako szablon dla wersji niestandardowych.

![Pulpit nawigacyjny SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Raporty
Raportów w programie SCOM analizę danych z magazynu danych w formie tabelarycznej.  Mogą być drukowane i zaplanowane w celu automatycznego dostarczania w różnych formatach plików tym PDF, CSV i Word.  Raporty pracować z danymi z magazynu danych, tak, aby były szczególnie odpowiednie do analizy trendów w perspektywie długoterminowej.

Pakiety administracyjne zwykle zapewni niestandardowych raportów dla określonej aplikacji.  Możesz również wybrać z biblioteki raportów ogólnych, które można dostosować do własnych aplikacji lub do wykonywania analizy ad hoc.

Poniżej przedstawiono przykładowy raport wydajności, dane zebrane przez pakietu administracyjnego usługi Active Directory są wyświetlane.

![Raport](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Analiza dzienników ma [języka zapytań](https://technet.microsoft.com/library/mt484120.aspx) której można wykonywać analizy w danych z wielu aplikacji bez konieczności tworzenia niestandardowych widoku lub raportu.  Ponieważ OMS jest wdrażane w chmurze, wydajność zapytań i analizy danych nie są ograniczenia sprzętowe i szybko analizować kwerend w tym miliony rekordów. 

Zapytania w analizy dzienników również stanowią podstawę innych funkcji.  Możesz zapisać kwerendę, wyeksportować wyniki do programu Excel lub go uruchomić w regularnych odstępach czasu i automatycznie Wygeneruj alerty, gdy jego wyników spełniających kryteria określonego.  

![Dziennik zapytań przepływu](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Poniżej znajduje się przykład kwerendy analizy dzienników.  W tym przykładzie wszystkie zdarzenia z "uruchomiona" w nazwie są zwracane i grupować zdarzenia identyfikatora.  Użytkownik musi tylko podać zapytania i analizy dzienników dynamicznie generuje interfejsu użytkownika do przeprowadzenia analizy.  Wybranie dowolnego elementu na liście zwróci dane szczegółowe zdarzenia.

![Dziennik zapytań](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Oprócz zapewnienia analizy ad hoc, zapytania w analizy dzienników można zapisać do użytku w przyszłości i również został dodany do Twojego [pulpitu nawigacyjnego OMS](http://technet.microsoft.com/library/mt484090.aspx) jak pokazano w poniższym przykładzie.

![Pulpit nawigacyjny OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Następne kroki
* Wdrażanie [programu System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Zaloguj się do [analizy dzienników](https://azure.microsoft.com/documentation/services/log-analytics).  

