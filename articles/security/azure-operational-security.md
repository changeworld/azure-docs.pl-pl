---
title: Bezpieczeństwo działania platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat dzienników Microsoft Azure Monitor, usługach i jak to działa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: bb0726fdddf6d20a9c8c5f20d6cec0cae68322ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67119257"
---
# <a name="azure-operational-security"></a>Bezpieczeństwo działania platformy Azure
## <a name="introduction"></a>Wprowadzenie

### <a name="overview"></a>Omówienie
Wiemy, że bezpieczeństwo to zadanie, jeden w chmurze i jak ważne jest, że możesz znaleźć dokładne i aktualne informacje o zabezpieczeniach platformy Azure. Jedną z najważniejszych powodów, dla aplikacji i usług za pomocą platformy Azure jest aby móc korzystać z szerokiej gamy możliwości są dostępne i narzędziom zabezpieczającym. Te narzędzia i możliwości pomocy, umożliwiają tworzenie bezpiecznych rozwiązań na platformie Azure jest bezpieczne. Windows Azure, musisz podać poufność, integralność i dostępność danych klientów, pozwalając również przezroczyste odpowiedzialność.

Aby ułatwić klientom lepiej zrozumieć tablicy środki kontroli bezpieczeństwa zaimplementowane w systemie Microsoft Azure z obu klienta i Microsoft perspektywy operacyjnej, ten oficjalny dokument "Azure Operational Security", są zapisywane, która zapewnia kompleksowy Spójrz na bezpieczeństwo działania dostępne w systemie Windows Azure.

### <a name="azure-platform"></a>Platforma Azure
Platforma Azure to platforma usługi w chmurze publicznej obsługuje szeroki zakres systemów operacyjnych, języków programowania, struktur, narzędzia, baz danych i urządzeń. Jej uruchamianie kontenerów systemu Linux z integracją Docker; Tworzenie aplikacji w językach JavaScript, Python, .NET, PHP, Java i Node.js; Tworzenie zapleczy dla systemów iOS, Android i Windows urządzenia. Usługa w chmurze Azure obsługuje te same technologie przez miliony deweloperów i specjalistów IT, już używane i wypróbowane.

Podczas tworzenia na lub migracji zasobów informatycznych do dostawcy usług w chmurze publicznej polegasz na możliwości w organizacji można chronić aplikacje i dane za pomocą usług i formanty zapewniają do zarządzania zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od aplikacje hostujące jednocześnie miliony klientów, a jego zapewnia wiarygodną podstawę firm mogą spełnienie rządowych wymagań dotyczących zabezpieczeń. Ponadto platforma Azure udostępnia szeroki zakres konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, dzięki czemu możesz dostosować zabezpieczenia, aby spełniały unikatowe wymagania wdrożenia w organizacji. Ten dokument zostanie pomoże Ci zrozumieć, jak usługa Azure security możliwości pomaga spełnić te wymagania.

### <a name="abstract"></a>Abstrakcyjny
Zabezpieczenia usługi Azure Operational odnosi się do usługi, formanty i funkcje dostępne dla użytkowników na potrzeby ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. Bezpieczeństwa platformy Azure działa w oparciu o strukturę, która zawiera wiedzy uzyskanej dzięki różnych funkcji, które są unikatowe w firmie Microsoft, w tym Microsoft cykl projektowania zabezpieczeń (SDL), program Microsoft Security Response Center oraz głębokiej świadomości krajobraz zagrożeń cyberbezpieczeństwa.

Ten dokument przedstawia podejście firmy Microsoft do kwestii bezpieczeństwa platformy Azure w ramach platformy Microsoft Azure w chmurze i obejmuje następujące usługi:
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-monitor-logs"></a>Dzienniki systemu Microsoft Azure Monitor

Dzienniki systemu Microsoft Azure Monitor to rozwiązanie zarządzania IT dla chmury hybrydowej. Użyte bez parametrów lub rozszerzyć Twoje istniejące wdrożenie programu System Center, dzienniki usługi Azure Monitor zapewnia maksymalną elastyczność i kontrolę dla zarządzania infrastrukturą w chmurze.

![Dzienniki usługi Azure Monitor](./media/azure-operational-security/azure-operational-security-fig1.png)

Przy użyciu dzienników usługi Azure Monitor możesz zarządzać dowolne wystąpienie w chmurze, w tym w środowisku lokalnym, Azure, AWS, systemu Windows Server, Linux, VMware i OpenStack przy niskich kosztach niż konkurencyjne rozwiązania. Oparta na świecie chmury, dzienniki usługi Azure Monitor udostępnia nowe podejście do zarządzania w przedsiębiorstwie, który jest najszybszym i najbardziej ekonomiczny sposób spełniają wyzwania biznesowe i nowych obciążeń, aplikacji i środowisk w chmurze.

### <a name="azure-monitor-services"></a>Usługi Azure Monitor

Podstawowe funkcje dzienniki usługi Azure Monitor jest dostarczana przez zbiór usług działających na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

| Usługa  | Opis|
| :------------- | :-------------|
| Dzienniki usługi Azure Monitor | Monitorowanie i analizowanie dostępności oraz wydajności różnych zasobów, łącznie z maszynami fizycznymi i wirtualnymi. |
|Automatyzacja | Automatyzowanie procesów ręcznych oraz wymuszanie konfiguracji maszyn fizycznych i wirtualnych. |
| Backup | Tworzenie kopii zapasowej i przywracanie kluczowych danych. |
| Site Recovery | Zapewnianie wysokiej dostępności kluczowych aplikacji. |

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki platformy Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) umożliwia monitorowanie przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.


Ta metoda umożliwia konsolidowanie danych z różnych źródeł, dzięki czemu można połączyć dane z usług platformy Azure przy użyciu istniejących lokalnych środowiska. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.


![Dzienniki usługi Azure Monitor](./media/azure-operational-security/azure-operational-security-fig2.png)

Usługa Azure Monitor bezpiecznie zarządza dane oparte na chmurze przy użyciu następujących metod:
-   Podział danych
-   przechowywanie danych
-   zabezpieczenia fizyczne
-   Zarządzanie zdarzeniami
-   zgodność
-   Certyfikaty standardów zabezpieczeń

### <a name="azure-backup"></a>Azure Backup

[Usługa Azure Backup](https://azure.microsoft.com/documentation/services/backup) udostępnia dane kopii zapasowej i przywracania usługi i jest częścią zestawu usługi Azure Monitor, produktów i usług.
Chroni ona dane aplikacji i przechowuje je przez wiele lat bez konieczności ponoszenia jakichkolwiek inwestycji kapitałowych i przy minimalnych kosztach operacyjnych. Jego kopię zapasową danych z fizycznych i wirtualnych serwerów Windows, także obciążeń aplikacji, takich jak SQL Server i SharePoint. Może również służyć za [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) do replikacji chronionych danych na platformę Azure dla zapewnienia nadmiarowości i długoterminowego przechowywania.


Chronione dane w usłudze Azure Backup są przechowywane w magazynie kopii zapasowych, znajdującym się w określonym regionie geograficznym. Dane są replikowane w obrębie tego samego regionu, a następnie, w zależności od typu magazynu mogą być również replikowane do innego regionu w celu osiągnięcia większej odporności.

### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Usługa Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) jest firmy Microsoft oparte na chmurze rozwiązanie zarządzania IT, która ułatwia zarządzanie i chronić lokalne i infrastruktury chmury.


[Rozwiązania do zarządzania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) to wstępnie spakowane zestawy warunki logiczne, które implementują określony scenariusz zarządzania przy użyciu co najmniej jednej usługi Azure Monitor. Różne są dostępne rozwiązania firmy Microsoft i partnerów, które można łatwo można dodać do subskrypcji platformy Azure w celu zwiększenia wartości inwestycji w usłudze Azure Monitor. Jako partner można utworzyć własne rozwiązania do obsługi danych aplikacji i usług oraz udostępniać je użytkownikom za pośrednictwem witryny Azure Marketplace lub szablonów Szybki Start.


![Rozwiązania do zarządzania](./media/azure-operational-security/azure-operational-security-fig4.png)

Dobrym przykładem rozwiązania korzystającego z wielu usług oferowanie dodatkowych funkcji jest [rozwiązanie do zarządzania aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). To rozwiązanie używa [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agent for Windows i Linux zebrać informacje o wymaganych aktualizacji na każdym agencie. Te dane są zapisywane w repozytorium dzienniki usługi Azure Monitor, gdzie je przeanalizować za pomocą dołączonego pulpitu nawigacyjnego.

Podczas tworzenia wdrożenia elementy runbook w programie [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) są używane w celu zainstalowania wymaganych aktualizacji. Możesz zarządzać całym procesem w portalu, dzięki czemu nie musisz przejmować się szczegółami.

## <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center pomaga w ochronie Twoich zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. W ramach usługi, to można zdefiniować zasady nie wyłącznie w odniesieniu do subskrypcji platformy Azure, ale także względem [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), więc może być bardziej szczegółowego.

### <a name="security-policies-and-recommendations"></a>Zasady zabezpieczeń i zalecenia w tym zakresie

Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w określonej subskrypcji lub grupie zasobów.

W usłudze Security Center możesz zdefiniować zasady zgodnie z wymaganiami w zakresie zabezpieczeń firmy oraz typem aplikacji lub stopniem poufności danych.

![Zasady zabezpieczeń i zalecenia w tym zakresie](./media/azure-operational-security/azure-operational-security-fig5.png)


Zasady, które są włączone na poziomie subskrypcji automatycznie propagowane wśród wszystkich grup zasobów w ramach subskrypcji, jak pokazano na diagramie, po prawej stronie:


### <a name="data-collection"></a>Zbieranie danych

Usługa Security Center zbiera dane z maszyn wirtualnych w celu oceny ich stanu zabezpieczeń, przekazywania zaleceń dotyczących zabezpieczeń oraz alertów dotyczących zagrożeń. Podczas pierwszego dostępu do usługi Security Center, zbieranie danych jest włączona na wszystkich maszynach wirtualnych w ramach subskrypcji. Zaleca się zbieranie danych, ale możesz zrezygnować z niego, wyłączając zbieranie danych w zasadach usługi Security Center.

### <a name="data-sources"></a>Źródła danych

- Usługa Azure Security Center analizuje dane z następujących źródeł, aby zapewnić wgląd w stan zabezpieczeń, zidentyfikować luki w zabezpieczeniach i polecić rozwiązania oraz wykryć aktywne zagrożenia:

-   Usługi platformy Azure: Używa informacji o konfiguracji usługi platformy Azure została wdrożona, komunikując się z dostawcą zasobów tej usługi.

- Ruch sieciowy: Używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak lokalizacja źródłowa/docelowa IP i port, rozmiar pakietu i protokół sieciowy.

-   Rozwiązania partnerów: Używa alertów zabezpieczeń z rozwiązań zintegrowanych partnerów, takich jak zapory i rozwiązania do ochrony przed złośliwym oprogramowaniem.

-   Maszyny wirtualne: Używa informacji o konfiguracji i informacji na temat zdarzeń zabezpieczeń, takich jak Windows dzienniki inspekcji i zdarzeń, dzienniki programu IIS, komunikaty dziennika systemu i pliki zrzutu awaryjnego, z maszyn wirtualnych.

### <a name="data-protection"></a>Ochrona danych

Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, usługa Azure Security Center zbiera i przetwarza dane dotyczące zabezpieczeń, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i inne. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

-   **Podział danych**: Dane są logicznie oddzielone dla każdego składnika w całej usłudze. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

-   **Dostęp do danych**: Aby przedstawić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskiwać dostęp do informacji zbieranych lub analizowanych przez usługi platformy Azure, w tym pliki zrzutu awaryjnego, procesu tworzenia zdarzeń, migawki dysków maszyny Wirtualnej i artefaktów, które przypadkowo mogą zawierać dane klientów lub dane osobowe z maszyn wirtualnych. Stosujemy się do [warunki Microsoft Online Services i zasady zachowania poufności](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), które firma Microsoft nie ma stan używa danych klienta lub informacji do żadnych celów reklamowych ani zbliżonych celów komercyjnych pochodnych.

-   **Użycie danych**: Firma Microsoft używa wzorców i analizy zagrożeń obecnych w wielu dzierżawach, aby zwiększyć nasze możliwości zapobiegania i wykrywania; robimy to zgodnie ze zobowiązaniami zachowania opisanego w naszym [zasady zachowania poufności informacji](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Lokalizacja danych

Usługa Azure Security Center zbiera efemeryczne kopie plików zrzutu awaryjnego i analizuje je pod kątem dowodów na próby ich naruszenia i pomyślnie przeprowadzonych ataków. Usługa Azure Security Center dokonuje tej analizy w ramach tego samego obszaru geograficznego, co obszar roboczy, i usuwa efemeryczne kopie po zakończeniu analizy. Artefakty maszyny są przechowywane centralnie w tym samym regionie, co maszyna wirtualna.

-   **Konta magazynu**: Konto magazynu jest określone dla każdego regionu, w którym działają maszyny wirtualne. Dzięki temu można przechowywać dane w tym samym regionie co maszyna wirtualna, z której zbierane są dane.

-   **Magazyn usługi Azure Security Center**: Informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, zalecenia oraz stan kondycji zabezpieczeń są przechowywane centralnie, obecnie, w Stanach Zjednoczonych. Do informacji zebranych z maszyn wirtualnych mogą należeć informacje dotyczące konfiguracji oraz zdarzeń związanych z zabezpieczeniami, dzięki którym możliwe jest przekazanie użytkownikowi danych na temat alertu zabezpieczeń, ewentualnych zaleceń postępowania oraz stanu kondycji zabezpieczeń.


## <a name="azure-monitor"></a>Azure Monitor

[Usługi Azure Monitor dzienniki zabezpieczeń](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) i przeprowadzanie inspekcji umożliwiają rozwiązania IT, aktywne monitorowanie wszystkich zasobów, które mogą pomóc zminimalizować wpływ incydentów zabezpieczeń. Dzienniki platformy Azure Monitor, zabezpieczenia i inspekcja mają domeny zabezpieczeń, które mogą służyć do monitorowania zasobów. Domeny zabezpieczeń zapewnia szybki dostęp do opcji, dotyczące monitorowania zabezpieczeń następujących domen zostały omówione bardziej szczegółowo:

-   Ocena złośliwego oprogramowania
-   Ocena aktualizacji
-   Tożsamość i dostęp.

Usługa Azure Monitor zapewnia łącza do informacji o określonych typów zasobów. Oferuje ona wizualizacji, zapytania, routingu, alerty, automatyczne skalowanie i automatyzacja na danych, zarówno z infrastrukturą platformy Azure (Dziennik aktywności) i każdego pojedynczego zasobu platformy Azure (dzienników diagnostycznych).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Aplikacje w chmurze są złożone z wielu ruchomych elementów. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja stale się i działa w dobrej kondycji. Pomaga również stave potencjalnych problemów lub Rozwiązywanie problemów z przeszłości te.

Ponadto można użyć danych monitorowania do uzyskania szczegółowych informacji o aplikacji. Tę wiedzę można pomóc zwiększyć wydajność aplikacji lub łatwość konserwacji lub Automatyzuj akcje, które w przeciwnym razie wymagają ręcznej interwencji.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure


Jest dziennika, który zapewnia wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji. Dziennik aktywności była wcześniej znana jako "Dzienniki inspekcji" lub "Operacyjne dzienniki", ponieważ wysyła raporty zdarzeń płaszczyznę kontroli dla subskrypcji.

![Dziennik aktywności platformy Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Przy użyciu dziennika aktywności, można określić "co, kto i kiedy" dla dowolnego zapisu (PUT, POST, DELETE) wykonywanych na zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. Dziennik aktywności nie obejmują operacji odczytu (GET) ani operacji dotyczących zasobów, które używają klasycznego modelu.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Te dzienniki są emitowane przez zasób i zapewniają bogate, często dane o działaniu tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu.

Na przykład dzienniki systemu zdarzeń Windows są jedną z kategorii dziennik diagnostyczny dla maszyn wirtualnych i obiektów blob, tabel i Dzienniki kolejek są kategorie dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennika aktywności (wcześniej znana pod nazwą dziennik inspekcji dzienników operacyjnych)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez sam zasób.

### <a name="metrics"></a>Metryki

Usługa Azure Monitor umożliwia korzystanie z telemetrii, aby uzyskać wgląd w wydajność i kondycja obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywane również liczników wydajności) wyemitowane przez zasoby najbardziej platformy Azure. Usługa Azure Monitor zapewnia kilka sposobów konfigurowania i korzystania z tych [metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) do monitorowania i rozwiązywania problemów. Metryki są cennym źródłem danych telemetrycznych i pozwalają na wykonywanie następujących zadań:

-   **Śledzenie wyników** zasobu (np. maszyn wirtualnych, witryny sieci Web lub logiki aplikacji), kreślenia jego metryk na wykresie portalu i przypinanie ten wykres do pulpitu nawigacyjnego.

-   **Otrzymuj powiadomienia o problemie** który wpływa na wydajność zasobu gdy Metryka przekracza określony próg.

-   **Skonfiguruj automatyczne akcje**, takich jak automatyczne skalowanie zasobów lub wyzwalania elementu runbook, gdy Metryka przekracza określony próg.

-   **Przeprowadzać zaawansowane analizy** lub zgłaszanie trendów wydajności lub użycia zasobu.

-   **Archiwum** historii wydajności i kondycji zasobu związanych ze zgodnością lub inspekcji.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Istnieje możliwość w obrębie platformy Azure, która umożliwia zbieranie danych diagnostycznych na rozmieszczonej aplikacji. Rozszerzenie diagnostyki można użyć z różnych innych źródeł. Obecnie obsługiwane wartości to [Azure usługi sieci Web i chmury ról procesów roboczych](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) systemem Microsoft Windows, a [usługi Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Inne usługi platformy Azure mają własne oddzielne diagnostyki.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Inspekcja zabezpieczeń sieci jest wykrywanie luk w zabezpieczeniach sieci przy zapewnieniu zgodności z zabezpieczeniami IT i modelu przepisami nadzoru. Widok grupy zabezpieczeń można pobrać skonfigurowanego sieciowej grupy zabezpieczeń i reguły zabezpieczeń i efektywne reguły zabezpieczeń. Z listą reguł zastosowane należy określić, że porty, które są otwarte i oceny luk w zabezpieczeniach sieci.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) to regionalna usługa, która pozwala na monitorowanie i diagnozowanie warunków na poziomie sieci w, do i z platformy Azure. Diagnostyka sieci i narzędzi do wizualizacji dostępne w usłudze Network Watcher pomagają zrozumieć, diagnozowanie i uzyskiwanie szczegółowych informacji do sieci na platformie Azure. Ta usługa obejmuje przechwytywanie pakietów, następny przeskok, przepływu dla adresu IP Sprawdź widok grup zabezpieczeń, dzienników przepływu sieciowych grup zabezpieczeń. Scenariusz poziomu monitorowania udostępnia widok typu end to end zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Usługa Network Watcher jest obecnie ma następujące możliwości:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Dzienniki inspekcji</a>** — operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Te dzienniki mogą być wyświetlane w witrynie Azure portal lub pobrać przy użyciu narzędzi firmy Microsoft, takich jak usługa Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API Rest. Aby uzyskać więcej informacji na temat dzienników inspekcji Zobacz operacje inspekcji w usłudze Resource Manager. Dzienniki inspekcji są dostępne dla operacje wykonywane na wszystkich zasobów sieciowych.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Weryfikuje przepływu dla adresu IP </a>**  — sprawdza, czy pakiet jest dozwolony lub zablokowany na podstawie przepływ informacji 5-elementowe spójne kolekcje pakietów parametrów (docelowy adres IP, źródłowy adres IP, Port docelowy, Port źródłowy i protokół). Jeśli pakiet zostanie odrzucona przez sieciową grupę zabezpieczeń, zwracany jest reguła i sieciowej grupy zabezpieczeń, która odrzuciła pakiet.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Następny przeskok</a>**  -określa następny przeskok dla pakiety przesyłane w sieci szkieletowej platformy Azure, umożliwiając diagnozowanie dowolne błędnie skonfigurowane trasy zdefiniowane przez użytkownika.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Widok grup zabezpieczeń</a>**  — pobiera reguł zabezpieczeń efektywny i zastosowane, które są stosowane na maszynie Wirtualnej.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Rejestrowanie przepływu sieciowych grup zabezpieczeń</a>**  -dzienników przepływu dla sieciowych grup zabezpieczeń pozwalają przechwytywać dzienniki dotyczące ruchu, które są dozwolone lub odmowa dostępu przez zasady zabezpieczeń w grupie. Przepływ jest definiowany przez informacji krotki 5: źródłowy adres IP, docelowy adres IP, portu źródłowego, Port docelowy i protokołu.

## <a name="azure-storage-analytics"></a>Analityka usługi Azure Storage

[Usługa Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) może przechowywać metryki, które obejmują zagregowane transakcji statystyki oraz dane dyspozycyjności o żądaniach do usługi storage. Transakcje są zgłaszane zarówno na poziomie operacji interfejsu API i na poziomie usługi storage, a pojemność jest zgłaszany na poziomie usługi magazynu. Dane metryk można analizować użycie usługi magazynu, diagnozowanie problemów z żądania skierowanego do usługi storage i poprawić wydajność aplikacji, które używają usługi.

[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Rejestrowanie danych analizy magazynu jest dostępny dla [usługi obiektów Blob, kolejek i tabel](https://docs.microsoft.com/azure/storage/storage-introduction). Usługa Storage Analytics rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi storage.

Informacja ta może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane na zasadzie największej staranności. Wpisy dziennika są tworzone tylko wtedy, gdy żądania skierowanego do punktu końcowego usługi. Na przykład jeśli konto magazynu ma działania, w jej punkt końcowy usługi Blob, ale nie w jego tabel lub kolejek punkty końcowe, tylko dzienniki odnoszących się do usługi obiektów Blob jest tworzony.

Aby korzystać z usługi Storage Analytics, należy włączyć je osobno dla każdej usługi, które chcesz monitorować. Możesz ją włączyć w [witryny Azure portal](https://portal.azure.com/); Aby uzyskać więcej informacji, zobacz [monitorowania na koncie magazynu w witrynie Azure portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Umożliwiają operacji ustawiania właściwości usługi Storage Analytics osobno dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanych obiektów blob (w przypadku rejestrowania) i dobrze znanych tabelach (w przypadku metryk), które mogą być dostępne za pomocą usługi obiektów Blob i Table service API.

Usługa Storage Analytics obowiązuje limit 20 TB na ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Wszystkie dzienniki są przechowywane w [blokowe obiekty BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) w kontenerze o nazwie $logs, które są tworzone automatycznie podczas analityka magazynu jest włączona dla konta magazynu.

Następujące akcje wykonywane przez analityka magazynu są płatne:

-   Utwórz obiekty BLOB do rejestrowania żądań
-   Żądań w celu utworzenia tabeli jednostki dla metryki.

> [!Note]
> Aby uzyskać więcej informacji dotyczących rozliczeń i zasadami przechowywania danych, zobacz [analizy magazynu i rozliczeń oraz](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Aby uzyskać optymalną wydajność chcesz ograniczyć liczbę intensywnie używanych dysków dołączonych do maszyny wirtualnej w celu uniknięcia potencjalnego ograniczania. Jeśli wszystkie dyski nie są wysoce wykorzystywane w tym samym czasie, konto magazynu może obsługiwać większy dysk liczb.

> [!Note]
> Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Rejestrowane są następujące typy żądań uwierzytelnionych i anonimowych.

| Uwierzytelniony  | Anonimowe|
| :------------- | :-------------|
| Żądania zakończone powodzeniem | Żądania zakończone powodzeniem |
|Żądania zakończone niepowodzeniem oraz tym limit czasu, ograniczanie przepustowości, sieci, autoryzacji i inne błędy | Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądania nie powiodło się, jak i pomyślnie |
| Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądania nie powiodło się, jak i pomyślnie |Błędy limitu czasu dla klienta i serwera |
|   Żądania do analizy danych |    Żądania GET zakończone niepowodzeniem z kodem błędu 304 (nie zmodyfikowano) |
| Żądania wysyłane przez analityka magazynu, takie jak tworzenie dziennika lub usuwanie, nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [operacji rejestrowane analizy magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analizy magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) tematów. | Inne zakończone niepowodzeniem żądania anonimowe nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [operacji rejestrowane analizy magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analizy magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

Usługa Azure AD zawiera również pełnego zestawu możliwości zarządzania tożsamościami, w tym uwierzytelnianie wieloskładnikowe, rejestracja urządzeń, Samoobsługowe zarządzanie hasłami, Samoobsługowe zarządzanie grupami, uprzywilejowanego konta zarządzania, opartej na rolach dostęp kontroli, monitorowania użycia aplikacji, rozbudowane inspekcji i zabezpieczeń monitorowania i alertów.

-   Zwiększ bezpieczeństwo aplikacji za pomocą uwierzytelniania wieloskładnikowego w usłudze Azure AD i dostępu warunkowego.

-   Monitorowanie użycia aplikacji i Chroń swoją firmę przed zaawansowanymi zagrożeniami Dzięki zabezpieczeniom raportowania i monitorowania.

W usłudze Azure Active Directory (Azure AD) uwzględniono raporty dotyczące zabezpieczeń, działań i inspekcji dla katalogu. [Raport inspekcji Active Directory systemu Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomaga klientom w celu identyfikacji uprzywilejowanych akcji, które wystąpiły w usługą Azure Active Directory. Uprzywilejowanych akcji obejmują zmiany podniesienie poziomu (np. Tworzenie roli lub resetowanie haseł), zmiany konfiguracji zasad (na przykład zasady haseł) lub zmiany w konfiguracji katalogu (na przykład zmiany ustawienia Federacji domeny).

Raporty zapewniają rekord inspekcji dla nazwy zdarzenia aktora, który wykonał operację, zasób docelowy wpływ zmiany oraz Data i godzina (czas UTC). Klienci będą mogli pobrać listy zdarzeń inspekcji dla swojej usługi Azure Active Directory za pośrednictwem [witryny Azure portal](https://portal.azure.com/), zgodnie z opisem w [wyświetlanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Oto lista uwzględnionych raportów:

| Raporty dotyczące zabezpieczeń  | Raporty dotyczące działań| Raporty dotyczące inspekcji |
| :------------- | :-------------| :-------------|
|Logowania z nieznanych źródeł | Podsumowanie użycia aplikacji | Raport dotyczący inspekcji katalogu |
|Logowania po wielokrotnych niepowodzeniach | Szczegóły użycia aplikacji |   |
|Logowania z wielu lokalizacji geograficznych | Pulpit nawigacyjny aplikacji |  |
|Logowania z adresów IP związanych z podejrzanymi działaniami |Błędy aprowizacji kont |  |
|Nieregularne działania związane z logowaniem |Urządzenia indywidualnych użytkowników |  |
|Logowania z urządzeń, które mogą być zainfekowane |Działania indywidualnych użytkowników |   |
|Nietypowe działania użytkowników związane z logowaniem |Raport dotyczący działań grup |   |
| |Raport dotyczący działań związanych z rejestracją resetowania haseł |   |
| |Działania związane z resetowaniem haseł |   |



Dane z tych raportów mogą być przydatne w aplikacjach, takich jak systemy SIEM, inspekcji i narzędzia do analizy biznesowej. Raportowanie usługi Azure AD [interfejsów API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) programowy dostęp do danych za pomocą zestawu interfejsów API, oparte na protokole REST. Te interfejsy API można wywołać z różnych języków programowania i narzędzi.

Zdarzenia raportu inspekcji usługi AD platformy Azure są przechowywane przez 180 dni.

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania w raportach zobacz [zasady przechowywania raportów Active Directory Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Dla klientów zainteresowanych przechowywania ich [zdarzenia inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) przez dłuższy czas przechowywania, interfejsu API raportowania pozwala regularnie ściąganie zdarzeń inspekcji do oddzielnego magazynu danych.

## <a name="summary"></a>Podsumowanie

Ta podsumowania artykułu, ochrony prywatności i zabezpieczanie danych, dostarczając oprogramowanie i usługi, które ułatwiają zarządzanie infrastruktury IT w organizacji. Firma Microsoft uznaje, że gdy powierzają swoje dane do innych osób, tej relacji zaufania wymaga rygorystyczne. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi. Zabezpieczanie i ochrona danych należy traktować priorytetowo w firmie Microsoft.

W tym artykule opisano

-   Jak dane są zbierane, przetwarzane i zabezpieczane w zestawie Azure Monitor.

-   Szybko analizuj zdarzenia w wielu źródłach danych. Identyfikuj zagrożenia bezpieczeństwa i uzyskuj wiedzę o zakresie i wpływie zagrożeń i ataków, aby eliminować szkody związane z naruszeniem zabezpieczeń.

-   Identyfikuj wzorce ataków dzięki wizualizowaniu wychodzącego złośliwego ruchu przez adresy IP i typów zagrożeń spowodowanych złośliwymi działaniami. Poznaj poziom zabezpieczeń całego środowiska, niezależnie od platformy.

-   Przechwyć wszystkie dane dzienników i zdarzeń wymagane na potrzeby inspekcji zabezpieczeń i zgodności. Slash — czas i zasoby potrzebne do dostarczania inspekcji zabezpieczeń dzięki kompletne, którą można przeszukiwać i eksportowalny dzienników i zestaw danych zdarzenia.

<ul>
<li>Zbieranie zdarzeń zabezpieczeń, inspekcji i analizy naruszenia obserwowanie zasobów:</li>
<ul>
<li>Poziom zabezpieczeń</li>
<li>Istotny problem</li>
<li>Podsumowanie zagrożeń</li>
</ul>
</ul>

## <a name="next-steps"></a>Następne kroki

- [Bezpieczeństwo projektowania i działania](https://www.microsoft.com/trustcenter/security/designopsecurity)

Firma Microsoft projektuje swoje usługi i oprogramowania z myślą o bezpieczeństwie, aby mieć pewność, że infrastruktura chmury jest odporne na błędy i obrony, w odniesieniu przed atakami.

- [Dzienniki platformy Azure Monitor | Bezpieczeństwo i zgodność](https://www.microsoft.com/cloud-platform/security-and-compliance)

Dane zabezpieczeń firmy Microsoft i analiza można używać do wykonywania bardziej inteligentne i efektywne wykrywania zagrożeń.

- [Usługa Azure Security Center planowania i obsługi](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) zestaw kroków i zadań, które można wykonać, aby zoptymalizować korzystanie z usługi Security Center w oparciu o wymagania dotyczące zabezpieczeń oraz modelu zarządzania chmurą w organizacji.

