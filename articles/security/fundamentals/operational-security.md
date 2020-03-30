---
title: Zabezpieczenia operacyjne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o dziennikach monitora platformy Microsoft Azure, jego usługach i o tym, jak działa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 34c0c52945abc6e0ab74b1cb180581c76464bee8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749957"
---
# <a name="azure-operational-security"></a>Zabezpieczenia operacyjne platformy Azure
## <a name="introduction"></a>Wprowadzenie

### <a name="overview"></a>Omówienie
Wiemy, że bezpieczeństwo jest jednym z zadań w chmurze i jak ważne jest, aby znaleźć dokładne i aktualne informacje na temat zabezpieczeń platformy Azure. Jednym z najlepszych powodów, aby korzystać z platformy Azure dla aplikacji i usług jest skorzystanie z szerokiej gamy narzędzi zabezpieczeń i możliwości dostępnych. Te narzędzia i funkcje ułatwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. System Windows Azure musi zapewniać poufność, integralność i dostępność danych klientów, a jednocześnie umożliwiać przejrzystą odpowiedzialność.

Aby pomóc klientom lepiej zrozumieć szereg mechanizmów kontroli zabezpieczeń zaimplementowanych w ramach platformy Microsoft Azure zarówno z perspektyw operacyjnych klienta, jak i firmy Microsoft, w tym opracowaniu tego dokumentu "Azure Operational Security" napisano, że zawiera kompleksowe zobacz zabezpieczenia operacyjne dostępne w systemie Windows Azure.

### <a name="azure-platform"></a>Platforma Azure
Platforma Azure to platforma usług w chmurze publicznej, która obsługuje szeroki wybór systemów operacyjnych, języków programowania, struktur, narzędzi, baz danych i urządzeń. Można uruchomić kontenery systemu Linux z integracji platformy Docker; tworzenie aplikacji z JavaScript, Python, .NET, PHP, Java i Node.js; tworzenie zaplecza dla urządzeń z systemami iOS, Android i Windows. Usługa Azure Cloud obsługuje te same technologie miliony programistów i specjalistów IT już polegać i zaufanie.

Podczas tworzenia lub migracji zasobów IT do dostawcy usług w chmurze publicznej, na którym polegasz, możesz chronić aplikacje i dane za pomocą usług i formantów, które zapewniają w celu zarządzania bezpieczeństwem zasobów chmurowych.

Infrastruktura platformy Azure została zaprojektowana z obiektu do aplikacji do obsługi milionów klientów jednocześnie i zapewnia godną zaufania podstawę, na której firmy mogą spełnić swoje wymagania dotyczące zabezpieczeń. Ponadto platforma Azure zapewnia szeroką gamę konfigurowalnych opcji zabezpieczeń i możliwość kontrolowania ich, dzięki czemu można dostosować zabezpieczenia do unikatowych wymagań wdrożeń organizacji. Ten dokument pomoże Ci zrozumieć, w jaki sposób funkcje zabezpieczeń platformy Azure mogą pomóc w spełnieniu tych wymagań.

### <a name="abstract"></a>Abstract
Usługa Azure Operational Security odnosi się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony ich danych, aplikacji i innych zasobów na platformie Microsoft Azure. Usługa Azure Operational Security jest oparta na platformie, która zawiera wiedzę zdobytą za pośrednictwem różnych funkcji, które są unikatowe dla firmy Microsoft, w tym cyklu microsoft security development (SDL), programu Microsoft Security Response Center, i głębokiej świadomości na temat zagrożenia dla bezpieczeństwa cybernetycznego.

W tym opracowaciu tym opracowam artykule opisano podejście firmy Microsoft do zabezpieczeń operacyjnych platformy Azure w ramach platformy chmurowej platformy Microsoft Azure i opisano następujące usługi:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Obserwator sieci platformy Azure](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Analiza usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Usługa Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Dzienniki Monitora platformy Microsoft Azure

Dzienniki monitora Platformy Microsoft Azure to rozwiązanie do zarządzania it dla chmury hybrydowej. Dzienniki usługi Azure Monitor, używane samodzielnie lub rozszerzające istniejące wdrożenie w usłudze System Center, zapewniają maksymalną elastyczność i kontrolę nad zarządzaniem infrastrukturą w chmurze.

![Dzienniki usługi Azure Monitor](./media/operational-security/azure-operational-security-fig1.png)

Za pomocą dzienników usługi Azure Monitor można zarządzać dowolnym wystąpieniem w dowolnej chmurze, w tym lokalnie, azure, aws, windows server, linux, VMware i OpenStack, po niższych kosztach niż konkurencyjne rozwiązania. Oparte na świecie chmury dzienniki usługi Azure Monitor oferują nowe podejście do zarządzania przedsiębiorstwem, które jest najszybszym i najbardziej opłacalnym sposobem sprostania nowym wyzwaniom biznesowym i uwzględnienia nowych obciążeń, aplikacji i środowisk w chmurze.

### <a name="azure-monitor-services"></a>Usługi Azure Monitor

Podstawowa funkcjonalność dzienników usługi Azure Monitor jest dostarczana przez zestaw usług uruchamianych na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

| Usługa  | Opis|
| :------------- | :-------------|
| Dzienniki usługi Azure Monitor | Monitorowanie i analizowanie dostępności oraz wydajności różnych zasobów, łącznie z maszynami fizycznymi i wirtualnymi. |
|Automatyzacja | Automatyzowanie procesów ręcznych oraz wymuszanie konfiguracji maszyn fizycznych i wirtualnych. |
| Tworzenie kopii zapasowych | Twoij kopię zapasową i przywracaj krytyczne dane. |
| Site Recovery | Zapewnianie wysokiej dostępności kluczowych aplikacji. |

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki usługi Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) zapewniają usługi monitorowania, zbierając dane z zasobów zarządzanych do centralnego repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.


Ta metoda umożliwia konsolidację danych z różnych źródeł, dzięki czemu można łączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.


![Dzienniki usługi Azure Monitor](./media/operational-security/azure-operational-security-fig2.png)

Usługa Azure Monitor bezpiecznie zarządza danymi opartymi na chmurze przy użyciu następujących metod:
-   segregacja danych
-   przechowywanie danych
-   bezpieczeństwo fizyczne
-   zarządzanie incydentami
-   Zgodności
-   certyfikaty norm bezpieczeństwa

### <a name="azure-backup"></a>Azure Backup

[Usługa Azure Backup](https://azure.microsoft.com/documentation/services/backup) zapewnia usługi tworzenia kopii zapasowych i przywracania danych i jest częścią pakietu produktów i usług usługi Usługi Usługi Azure Monitor.
Chroni ona dane aplikacji i przechowuje je przez wiele lat bez konieczności ponoszenia jakichkolwiek inwestycji kapitałowych i przy minimalnych kosztach operacyjnych. Oprócz obciążeń aplikacji, takich jak SQL Server i SharePoint, można nabrać kopii zapasowych danych z fizycznych i wirtualnych serwerów systemu Windows. Może być również używany przez [Menedżera ochrony danych systemu Center (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) do replikowania chronionych danych na platformie Azure w celu zapewnienia nadmiarowości i długoterminowego magazynu.


Chronione dane w usłudze Azure Backup są przechowywane w magazynie kopii zapasowych, znajdującym się w określonym regionie geograficznym. Dane są replikowane w tym samym regionie i, w zależności od typu magazynu, mogą być również replikowane do innego regionu w celu zwiększenia odporności.

### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Usługa Azure Monitor](../../security-center/security-center-intro.md) to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania it, które pomaga zarządzać infrastrukturą lokalną i chmurową oraz chronić ją.


[Rozwiązania do zarządzania](../../monitoring/monitoring-solutions.md) to wstępnie spakowane zestawy logiki, które implementują określony scenariusz zarządzania przy użyciu co najmniej jednej usługi Azure Monitor. Różne rozwiązania są dostępne od firmy Microsoft i od partnerów, które można łatwo dodać do subskrypcji platformy Azure, aby zwiększyć wartość inwestycji w usłudze Azure Monitor. Jako partner możesz tworzyć własne rozwiązania do obsługi aplikacji i usług i dostarczać je użytkownikom za pośrednictwem portalu Azure Marketplace lub szablonów szybkiego startu.


![Rozwiązania do zarządzania](./media/operational-security/azure-operational-security-fig4.png)

Dobrym przykładem rozwiązania, które korzysta z wielu usług w celu zapewnienia dodatkowych funkcji, jest [rozwiązanie do zarządzania aktualizacjami.](../../automation/automation-update-management.md) To rozwiązanie używa agenta [dzienników usługi Azure Monitor](../../log-analytics/log-analytics-queries.md) dla systemów Windows i Linux do zbierania informacji o wymaganych aktualizacjach dla każdego agenta. Zapisuje te dane do repozytorium dzienników usługi Azure Monitor, gdzie można analizować je za pomocą dołączonego pulpitu nawigacyjnego.

Podczas tworzenia wdrożenia, elementy runbook w [usłudze Azure Automation](../../automation/automation-intro.md) są używane do instalowania wymaganych aktualizacji. Możesz zarządzać całym procesem w portalu, dzięki czemu nie musisz przejmować się szczegółami.

## <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center pomaga chronić zasoby platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. W ramach usługi można definiować zasady nie tylko względem subskrypcji platformy Azure, ale także względem [grup zasobów,](../../azure-resource-manager/management/overview.md#resource-groups)dzięki czemu można być bardziej szczegółowe.

### <a name="security-policies-and-recommendations"></a>Zasady zabezpieczeń i zalecenia w tym zakresie

Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w określonej subskrypcji lub grupie zasobów.

W usłudze Security Center możesz zdefiniować zasady zgodnie z wymaganiami w zakresie zabezpieczeń firmy oraz typem aplikacji lub stopniem poufności danych.

![Zasady zabezpieczeń i zalecenia w tym zakresie](./media/operational-security/azure-operational-security-fig5.png)


Zasady, które są włączone na poziomie subskrypcji automatycznie propagują do wszystkich grup zasobów w ramach subskrypcji, jak pokazano na diagramie po prawej stronie:


### <a name="data-collection"></a>Zbieranie danych

Usługa Security Center zbiera dane z maszyn wirtualnych w celu oceny ich stanu zabezpieczeń, przekazywania zaleceń dotyczących zabezpieczeń oraz alertów dotyczących zagrożeń. Gdy pierwszy dostęp Security Center, zbieranie danych jest włączona na wszystkich maszynach wirtualnych w ramach subskrypcji. Zaleca się zbieranie danych, ale możesz zrezygnować z niego, wyłączając zbieranie danych w zasadach usługi Security Center.

### <a name="data-sources"></a>Źródła danych

- Usługa Azure Security Center analizuje dane z następujących źródeł, aby zapewnić wgląd w stan zabezpieczeń, zidentyfikować luki w zabezpieczeniach i polecić rozwiązania oraz wykryć aktywne zagrożenia:

-   Usługi platformy Azure: używa informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.

- Ruch sieciowy: używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy i docelowy adres IP, źródłowy i docelowy port, rozmiar pakietu i protokół sieciowy.

-   Rozwiązania partnerów: używa alertów zabezpieczeń z rozwiązań zintegrowanych partnerów, takich jak zapory i rozwiązania do ochrony przed złośliwym oprogramowaniem.

-   Maszyny wirtualne: używa informacji dotyczących konfiguracji oraz zdarzeń związanych z zabezpieczeniami, takich jak zdarzenia systemu Windows i dzienniki inspekcji, dzienniki programu IIS, komunikaty dziennika systemu i pliki zrzutu awaryjnego, z maszyn wirtualnych.

### <a name="data-protection"></a>Ochrona danych

Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, usługa Azure Security Center zbiera i przetwarza dane dotyczące zabezpieczeń, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i inne. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

-   **Podział danych**: dane są logicznie oddzielone dla każdego składnika w całej usłudze. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

-   **Dostęp do danych:** Aby zapewnić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, pracownicy firmy Microsoft mogą uzyskiwać dostęp do informacji zebranych lub analizowanych przez usługi platformy Azure, w tym plików zrzutu awaryjnego, zdarzeń tworzenia procesów, migawek dysku maszyny Wirtualnej i artefaktów, które mogą przypadkowo zawierać dane klienta lub dane osobowe z maszyn wirtualnych. Przestrzegamy [Warunków korzystania z usług online firmy Microsoft oraz Zasad zachowania poufności informacji,](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)które stanowią, że firma Microsoft nie korzysta z Danych Klienta ani nie czerpie z nich informacji w celach reklamowych lub podobnych celach komercyjnych.

-   **Użycie danych**: firma Microsoft używa wzorców i analizy zagrożeń obecnych w wielu dzierżawach, aby zwiększyć możliwości wykrywania zagrożeń i zapobiegania im — robimy to zgodnie ze zobowiązaniami co do prywatności opisanymi w [zasadach zachowania poufności informacji](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Lokalizacja danych

Usługa Azure Security Center zbiera efemeryczne kopie plików zrzutu awaryjnego i analizuje je pod kątem dowodów na próby ich naruszenia i pomyślnie przeprowadzonych ataków. Usługa Azure Security Center dokonuje tej analizy w ramach tego samego obszaru geograficznego, co obszar roboczy, i usuwa efemeryczne kopie po zakończeniu analizy. Artefakty maszyny są przechowywane centralnie w tym samym regionie, co maszyna wirtualna.

-   **Konta magazynu:** Konto magazynu jest określone dla każdego regionu, w którym są uruchomione maszyny wirtualne. Dzięki temu można przechowywać dane w tym samym regionie co maszyna wirtualna, z której zbierane są dane.

-   **Magazyn usługi Azure Security Center Storage**: informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, zalecenia oraz stan kondycji zabezpieczeń, są przechowywane centralnie, obecnie na terenie Stanów Zjednoczonych. Do informacji zebranych z maszyn wirtualnych mogą należeć informacje dotyczące konfiguracji oraz zdarzeń związanych z zabezpieczeniami, dzięki którym możliwe jest przekazanie użytkownikowi danych na temat alertu zabezpieczeń, ewentualnych zaleceń postępowania oraz stanu kondycji zabezpieczeń.


## <a name="azure-monitor"></a>Azure Monitor

[Usługa Azure Monitor rejestruje](../../security-center/security-center-monitoring.md) rozwiązanie zabezpieczeń i inspekcji umożliwia działowi IT aktywne monitorowanie wszystkich zasobów, co może pomóc zminimalizować wpływ zdarzeń związanych z zabezpieczeniami. Dzienniki usługi Azure Monitor Security i Audit mają domeny zabezpieczeń, które mogą być używane do monitorowania zasobów. Domena zabezpieczeń zapewnia szybki dostęp do opcji, do monitorowania zabezpieczeń następujące domeny są omówione bardziej szczegółowo:

-   Ocena złośliwego oprogramowania
-   Ocena aktualizacji
-   Tożsamość i dostęp.

Usługa Azure Monitor udostępnia wskaźniki do informacji na temat określonych typów zasobów. Oferuje wizualizację, kwerendy, routing, alerty, automatyczne skalowanie i automatyzację na danych zarówno z infrastruktury platformy Azure (dziennik aktywności), jak i każdego pojedynczego zasobu platformy Azure (dzienniki diagnostyczne).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Aplikacje w chmurze są złożone z wieloma ruchomymi częściami. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja pozostaje w stanie dobrej kondycji. Pomaga również zażegnać potencjalne problemy lub rozwiązać poprzednie.

Ponadto można użyć danych monitorowania, aby uzyskać szczegółowe informacje na temat aplikacji. Ta wiedza może pomóc w zwiększce wydajności aplikacji lub łatwości konserwacji lub zautomatyzowaniu działań, które w przeciwnym razie wymagałyby ręcznej interwencji.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure


Jest to dziennik, który zapewnia wgląd w operacje, które zostały wykonane na zasoby w ramach subskrypcji. Dziennik aktywności był wcześniej znany jako "Dzienniki inspekcji" lub "Dzienniki operacyjne", ponieważ raportuje zdarzenia płaszczyzny sterowania dla subskrypcji.

![Dziennik aktywności platformy Azure](./media/operational-security/azure-operational-security-fig7.png)

Za pomocą dziennika aktywności, można określić "co, kto i kiedy" dla wszelkich operacji zapisu (PUT, POST, DELETE) podjęte na zasoby w ramach subskrypcji. Można również zrozumieć stan operacji i inne odpowiednie właściwości. Dziennik aktywności nie zawiera operacji odczytu (GET) ani operacji dla zasobów korzystających z modelu klasycznego.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Te dzienniki są emitowane przez zasób i zapewniają bogate, częste dane dotyczące działania tego zasobu. Zawartość tych dzienników różni się w zależności od typu zasobu.

Na przykład dzienniki systemu zdarzeń systemu Windows są jedną kategorią dziennika diagnostycznego dla maszyn wirtualnych i dzienników obiektów blob, tabeli i kolejek są kategoriami dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennika aktywności (wcześniej znanego jako dziennik inspekcji lub dziennik operacyjny).](../../azure-monitor/platform/platform-logs-overview.md) Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na zasoby w ramach subskrypcji. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez sam zasób.

### <a name="metrics"></a>Metryki

Usługa Azure Monitor umożliwia korzystanie z danych telemetrycznych w celu uzyskania wglądu w wydajność i kondycję obciążeń na platformie Azure. Najważniejszym typem danych telemetrycznych platformy Azure są metryki (nazywane również licznikami wydajności) emitowane przez większość zasobów platformy Azure. Usługa Azure Monitor udostępnia kilka sposobów konfigurowania i korzystania z tych [metryk](../../monitoring/monitoring-data-collection.md) do monitorowania i rozwiązywania problemów. Metryki są cennym źródłem danych telemetrycznych i umożliwiają wykonywanie następujących zadań:

-   **Śledź wydajność** zasobu (na przykład maszyny Wirtualnej, witryny sieci Web lub aplikacji logiki), kreśląc jego metryki na wykresie portalu i przypinając ten wykres do pulpitu nawigacyjnego.

-   **Otrzymuj powiadomienia o problemie,** który wpływa na wydajność zasobu, gdy metryka przekroczy określony próg.

-   **Skonfiguruj akcje automatyczne,** takie jak automatyczne skalowanie zasobu lub uruchamianie kreślić, gdy metryka przekroczy określony próg.

-   **Przeprowadzaj zaawansowane analizy** lub raportowanie trendów wydajności lub użycia zasobów.

-   **Archiwizuj** historię wydajności lub kondycji zasobu w celu zapewnienia zgodności lub inspekcji.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Jest to możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych w wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych źródeł. Obecnie obsługiwane są [role usługi Azure Cloud Service Web i roli procesu roboczego,](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) [maszyny wirtualne platformy Azure](../../virtual-machines/windows/overview.md) z systemem Microsoft Windows i sieci [szkieletowej usług.](../../azure-monitor/platform/diagnostics-extension-overview.md) Inne usługi platformy Azure mają własną oddzielną diagnostykę.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Inspekcja zabezpieczeń sieci ma kluczowe znaczenie dla wykrywania luk w zabezpieczeniach sieci i zapewnienia zgodności z modelem bezpieczeństwa IT i nadzoru regulacyjnego. W widoku Grupa zabezpieczeń można pobrać skonfigurowane sieciowe reguły zabezpieczeń i reguły zabezpieczeń oraz skuteczne reguły zabezpieczeń. Po zastosowaniu listy reguł można określić porty, które są otwarte, i ocenić lukę w zabezpieczeniach sieci.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie sieci w, do i z platformy Azure. Narzędzia diagnostyczne i wizualizacji sieci dostępne za pomocą obserwatora sieci pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. Ta usługa obejmuje przechwytywanie pakietów, następny przeskok, weryfikację przepływu IP, widok grupy zabezpieczeń, dzienniki przepływu sieciowej grupy zabezpieczeń. Monitorowanie na poziomie scenariusza zapewnia widok end-to-end zasobów sieciowych w przeciwieństwie do monitorowania poszczególnych zasobów sieciowych.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Kontrola sieci ma obecnie następujące możliwości:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Dzienniki inspekcji</a>**— operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Te dzienniki można wyświetlać w portalu Azure lub pobierać za pomocą narzędzi firmy Microsoft, takich jak usługa Power BI lub narzędzia innych firm. Dzienniki inspekcji są dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API odpoczynku. Aby uzyskać więcej informacji na temat dzienników inspekcji, zobacz Inspekcja operacji z Menedżerem zasobów. Dzienniki inspekcji są dostępne dla operacji wykonywanych na wszystkich zasobach sieciowych.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Weryfikacja przepływu IP</a>** — sprawdza, czy pakiet jest dozwolony lub odrzucony na podstawie informacji o przepływie 5-krotki parametrów pakietu (adres IP docelowy, adres IP źródła, port docelowy, port źródłowy i protokół). Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń sieci, zwracana jest reguła i sieciowa grupa zabezpieczeń, która odrzuciła pakiet.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Następny przeskok</a>** — określa następny przeskok dla pakietów kierowanych w sieci szkieletowej platformy Azure, umożliwiając diagnozowanie wszelkich nieprawidłowo skonfigurowanych tras zdefiniowanych przez użytkownika.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Widok grupy zabezpieczeń</a>** — pobiera skuteczne i stosowane reguły zabezpieczeń, które są stosowane na maszynie Wirtualnej.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Rejestrowanie przepływu sieciowej —</a>** dzienniki przepływu dla grup zabezpieczeń sieci umożliwiają przechwytywanie dzienników związanych z ruchem, które są dozwolone lub odrzucane przez reguły zabezpieczeń w grupie. Przepływ jest definiowany przez 5-krotka informacji — źródłowy adres IP, docelowy adres IP, port źródłowy, port docelowy i protokół.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Usługa Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) może przechowywać dane, które zawierają zagregowane statystyki transakcji i dane pojemności dotyczące żądań do usługi magazynu. Transakcje są zgłaszane zarówno na poziomie operacji interfejsu API, jak i na poziomie usługi magazynu, a pojemność jest raportowana na poziomie usługi magazynu. Dane metryki mogą służyć do analizowania użycia usługi magazynu, diagnozowania problemów z żądaniami dla usługi magazynu i poprawy wydajności aplikacji korzystających z usługi.

[Usługa Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia dane metryki dla konta magazynu. Te dane można używać do śledzenia żądań, analizowania trendów użycia i diagnozowania problemów z kontem magazynu. Rejestrowanie analizy magazynu jest dostępne dla [usług obiektów Blob, Queue i Table.](../../storage/common/storage-introduction.md) Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem.

Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszych starań. Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania wykonane względem punktu końcowego usługi. Na przykład jeśli konto magazynu ma działanie w jego blob punktu końcowego, ale nie w jego tabeli lub kolejka punktów końcowych, tylko dzienniki odnoszące się do usługi obiektów Blob jest tworzony.

Aby korzystać z analizy magazynu, należy włączyć ją indywidualnie dla każdej usługi, którą chcesz monitorować. Można go włączyć w [witrynie Azure portal;](https://portal.azure.com/) aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w witrynie Azure portal](../../storage/common/storage-monitor-storage-account.md). Można również włączyć usługi Storage Analytics programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji Ustaw właściwości usługi, aby włączyć usługę Storage Analytics indywidualnie dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanym obiekcie blob (do rejestrowania) i w dobrze znanych tabelach (dla metryk), które mogą być dostępne przy użyciu interfejsów API usługi obiektów Blob i usługi tabel.

Usługa Storage Analytics ma limit 20 TB ilości przechowywanych danych, która jest niezależna od całkowitego limitu dla twojego konta magazynu. Wszystkie dzienniki są przechowywane w [blokowych obiektach blob](../../storage/common/storage-analytics.md) w kontenerze o nazwie $logs, które są tworzone automatycznie, gdy usługa Storage Analytics jest włączona dla konta magazynu.

Następujące akcje wykonywane przez storage Analytics podlegają rozliczaniu:

-   Żądania tworzenia obiektów blob do rejestrowania
-   Żądania tworzenia jednostek tabeli dla metryk.

> [!Note]
> Aby uzyskać więcej informacji na temat zasad rozliczeń i przechowywania danych, zobacz [Analiza magazynowania i rozliczenia](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Aby uzyskać optymalną wydajność, należy ograniczyć liczbę wysoce wykorzystywanych dysków dołączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczania przepustowości. Jeśli wszystkie dyski nie są wykorzystywane w tym samym czasie, konto magazynu może obsługiwać większy dysk.

> [!Note]
> Aby uzyskać więcej informacji na temat limitów kont magazynu, zobacz [Cele skalowalności dla standardowych kont magazynu](../../storage/common/scalability-targets-standard-account.md).


Rejestrowane są następujące typy żądań uwierzytelnionych i anonimowych.

| Uwierzytelnione  | Anonimowe|
| :------------- | :-------------|
| Żądania zakończone powodzeniem | Żądania zakończone powodzeniem |
|Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy | Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym nieudane i pomyślne żądania |
| Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym nieudane i pomyślne żądania |Błędy przekroczenia limitu czasu dla klienta i serwera |
|   Żądania dotyczące danych analityki |    Żądania GET zakończone niepowodzeniem z kodem błędu 304 (Nie zmodyfikowano) |
| Żądania wykonane przez samą analizę magazynu, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w tematach [Operacje rejestrowane i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) analizy magazynu oraz Format dziennika analizy [magazynu.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) | Wszystkie inne żądania anonimowe nie powiodły się, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w formacie dziennika [rejestrowanych operacji i komunikatów o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) analizy danych [usługi Storage.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) |

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

Usługa Azure AD obejmuje również pełny zestaw funkcji zarządzania tożsamościami, w tym uwierzytelnianie wieloskładnikowe, rejestrację urządzeń, samoobsługowe zarządzanie hasłami, samoobsługowe zarządzanie grupami, zarządzanie kontami uprzywilejowanymi, dostęp oparty na rolach kontrola, monitorowanie użycia aplikacji, rozbudowane inspekcje oraz monitorowanie i ostrzeganie o bezpieczeństwie.

-   Zwiększ bezpieczeństwo aplikacji dzięki uwierzytelnianiu wieloskładnikowego usługi Azure AD i dostępowi warunkowego.

-   Monitoruj użycie aplikacji i chroń swoją firmę przed zaawansowanymi zagrożeniami dzięki raportowaniu i monitorowaniu zabezpieczeń.

W usłudze Azure Active Directory (Azure AD) uwzględniono raporty dotyczące zabezpieczeń, działań i inspekcji dla katalogu. [Raport inspekcji usługi Azure Active Directory](../../active-directory/active-directory-reporting-azure-portal.md) pomaga klientom identyfikować akcje uprzywilejowane, które wystąpiły w usłudze Azure Active Directory. Akcje uprzywilejowane obejmują zmiany wysokości (na przykład tworzenie ról lub resetowanie hasła), zmianę konfiguracji zasad (na przykład zasady haseł) lub zmiany konfiguracji katalogu (na przykład zmiany ustawień federacji domeny).

Raporty zawierają rekord inspekcji dla nazwy zdarzenia, aktora, który wykonał akcję, zasobu docelowego, którego dotyczy zmiana, oraz daty i godziny (w czasie UTC). Klienci mogą pobrać listę zdarzeń inspekcji dla usługi Azure Active Directory za pośrednictwem [witryny Azure portal,](https://portal.azure.com/)zgodnie z opisem w [widoku dzienników inspekcji.](../../active-directory/reports-monitoring/overview-reports.md) Oto lista uwzględnionych raportów:

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



Dane tych raportów mogą być przydatne dla aplikacji, takich jak systemy SIEM, inspekcji i narzędzi analizy biznesowej. Interfejsy [API](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) raportowania usługi Azure AD zapewniają programowy dostęp do danych za pośrednictwem zestawu interfejsów API opartych na rest. Te interfejsy API można wywołać z różnych języków programowania i narzędzi.

Zdarzenia w raporcie inspekcji usługi Azure AD są przechowywane przez 180 dni.

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania raportów, zobacz [Zasady przechowywania raportów usługi Azure Active Directory](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Dla klientów zainteresowanych przechowywaniem [ich zdarzeń inspekcji](../../active-directory/active-directory-reporting-activity-audit-logs.md) przez dłuższy okres przechowywania interfejsu API raportowania może służyć do regularnego ściągania zdarzeń inspekcji do oddzielnego magazynu danych.

## <a name="summary"></a>Podsumowanie

W tym artykule podsumowane są informacje chroniące prywatność i zabezpieczające dane, a jednocześnie dostarczające oprogramowanie i usługi ułatwiające zarządzanie infrastrukturą IT w organizacji. Firma Microsoft uznaje, że gdy powierzają swoje dane innym osobom, zaufanie to wymaga rygorystycznych zabezpieczeń. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi. Zabezpieczanie i ochrona danych jest priorytetem firmy Microsoft.

W tym artykule wyjaśniono

-   Sposób zbierania, przetwarzania i zabezpieczanych danych w pakiecie Azure Monitor.

-   Szybko analizuj zdarzenia w wielu źródłach danych. Identyfikowanie zagrożeń bezpieczeństwa i zrozumienie zakresu i wpływu zagrożeń i ataków w celu ograniczenia szkód spowodowanych naruszeniem zabezpieczeń.

-   Identyfikuj wzorce ataków, wizualizując wychodzący złośliwy ruch IP i złośliwe typy zagrożeń. Poznaj poziom bezpieczeństwa całego środowiska, niezależnie od platformy.

-   Przechwyć wszystkie dane dziennika i zdarzeń wymagane do inspekcji zabezpieczeń lub zgodności. Skróć czas i zasoby potrzebne do dostarczenia inspekcji zabezpieczeń z kompletnym, przeszukiwalnym i eksportowalnym zestawem danych dziennika i zdarzeń.

<ul>
<li>Zbieraj zdarzenia związane z zabezpieczeniami, inspekcję i analizę naruszeń, aby bacznie obserwować swoje zasoby:</li>
<ul>
<li>Postawa bezpieczeństwa</li>
<li>Znamienny problem</li>
<li>Streszczenia zagrożeń</li>
</ul>
</ul>

## <a name="next-steps"></a>Następne kroki

- [Projektowanie i bezpieczeństwo operacyjne](https://www.microsoft.com/trustcenter/security/designopsecurity)

Firma Microsoft projektuje swoje usługi i oprogramowanie z myślą o zabezpieczeniach, aby zapewnić odporność infrastruktury chmury i ochronę przed atakami.

- [Dzienniki usługi Azure Monitor | Zgodność & bezpieczeństwa](https://www.microsoft.com/cloud-platform/security-and-compliance)

Użyj danych i analiz zabezpieczeń firmy Microsoft, aby przeprowadzić bardziej inteligentne i skuteczne wykrywanie zagrożeń.

- [Planowanie i operacje usługi Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md) Zestaw kroków i zadań, które można wykonać w celu optymalizacji korzystania z usługi Security Center na podstawie wymagań dotyczących zabezpieczeń organizacji i modelu zarządzania chmurą.

