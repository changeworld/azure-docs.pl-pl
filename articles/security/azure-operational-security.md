---
title: Azure bezpieczeństwa operacyjnego | Dokumentacja firmy Microsoft
description: Więcej informacji na temat programu Microsoft Operations Management Suite (OMS), jego usług i jak działa.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: d5bea758a0b2a950f51ec443707d9d32dee9cbc4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895684"
---
# <a name="azure-operational-security"></a>Azure bezpieczeństwa operacyjnego
## <a name="introduction"></a>Wprowadzenie

### <a name="overview"></a>Przegląd
Wiemy, że zabezpieczenia są zadania, co w chmurze i jak ważne jest aby znaleźć dokładne i aktualne informacje na temat zabezpieczeń platformy Azure. Najważniejsze przyczyny na potrzeby aplikacji i usług Azure ma korzystać z szerokiej gamy narzędzi zabezpieczeń i możliwości są dostępne. Te narzędzia i funkcje pomocy umożliwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Windows Azure należy podać poufność, integralność i dostępność danych klienta, jednoczesnym accountability przezroczysty.

Aby ułatwić klientom lepiej zrozumieć tablicy kontroli zabezpieczeń zaimplementowana w systemie Microsoft Azure z obu klienta i Microsoft perspektywy operacyjnej, tym oficjalnym dokumencie "Azure bezpieczeństwa operacyjnego", jest zapisywany który zapewnia kompleksowe przyjrzeć się bezpieczeństwa operacyjnego dostępne w systemie Windows Azure.

### <a name="azure-platform"></a>Platformy Azure
Azure to platforma usługi chmury publicznej, która obsługuje szeroki wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń. Kontenery systemu Linux można uruchamiać z integracją rozwiązania Docker; Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js; Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń. Usługa w chmurze Azure obsługuje te same technologie miliony deweloperów i specjalistów IT już zależne i zaufania.

Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług chmury publicznej, są zależne możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktury platformy Azure umożliwiają z funkcji aplikacji do obsługi jednocześnie miliony klientów i zapewnia foundation godne zaufania, na którym firmy mogą spełnić ich wymagań dotyczących zabezpieczeń. Ponadto Azure udostępnia szereg opcji zabezpieczeń można skonfigurować oraz możliwość ich kontroli, dzięki czemu można dostosować zabezpieczeń, aby spełnić unikatowe wymagania danej organizacji wdrożeń. Ten dokument zostanie pomaga w zrozumieniu sposobu Azure zabezpieczeń możliwości mogą pomóc spełnić te wymagania.

### <a name="abstract"></a>Abstrakcyjny
Azure bezpieczeństwa operacyjnego odwołuje się do usług, formanty i funkcje dostępne dla użytkowników do ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. Operacyjne zabezpieczeń platformy Azure w oparciu o strukturę, która zawiera wiedzę za pośrednictwem różnych funkcji, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center i głębokie pogłębianie wiedzy na temat zagrożeń bezpieczeństwa.

Ten dokument zawiera opis podejście firmy Microsoft do usługi Azure Operational zabezpieczeń w ramach platformy Microsoft Azure w chmurze i obejmuje następujące usługi:
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Usługa Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) to rozwiązanie zarządzania IT dla chmur hybrydowych. Użyte bez parametrów lub rozszerzyć Twoje istniejące wdrożenie programu System Center, OMS umożliwia maksymalną elastyczność i kontroli do zarządzania infrastruktury w chmurze.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

Dzięki OMS mogą zarządzać wystąpienie w żadną chmurą, tym lokalnymi, Azure AWS, Windows Server, Linux, VMware i OpenStack, na niższe koszty niż konkurencyjnych rozwiązania. Utworzony na świecie pierwszy chmury, OMS oferuje nowe podejście do zarządzania oznacza to najszybsze i najbardziej ekonomiczny sposób spełniają wyzwania biznesowe i uwzględnić nowe obciążenia, aplikacji i środowisk chmury przedsiębiorstwa.

### <a name="oms-services"></a>Usługi pakietu OMS

Podstawowe funkcje pakietu OMS są oferowane jako zbiór usług działających na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

| Usługa  | Opis|
| :------------- | :-------------|
| Log Analytics | Monitorowanie i analizowanie dostępności oraz wydajności różnych zasobów, łącznie z maszynami fizycznymi i wirtualnymi. |
|Automatyzacja | Automatyzowanie procesów ręcznych oraz wymuszanie konfiguracji maszyn fizycznych i wirtualnych. |
| Backup | Kopie zapasowe i przywracanie danych o kluczowym znaczeniu. |
| Site Recovery | Zapewnianie wysokiej dostępności kluczowych aplikacji. |

### <a name="log-analytics"></a>Log Analytics

Usługa [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) umożliwia monitorowanie pakietu OMS przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.


Ta metoda umożliwia konsolidowanie danych z różnych źródeł, więc można połączyć dane z usługami Azure z istniejącą lokalnego środowiska. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

Usługi analizy dzienników bezpiecznie zarządza danych oparte na chmurze za pomocą następujących metod:
-   Podział danych
-   przechowywanie danych
-   Zabezpieczenia fizyczne
-   Zarządzanie zdarzeniami
-   Zgodność
-   Certyfikaty standardów zabezpieczeń

### <a name="azure-backup"></a>Azure Backup

[Kopia zapasowa Azure](http://azure.microsoft.com/documentation/services/backup) dostarcza dane kopii zapasowej i przywracania usługi i jest częścią pakietu OMS produktów i usług.
Chroni ona dane aplikacji i przechowuje je przez wiele lat bez konieczności ponoszenia jakichkolwiek inwestycji kapitałowych i przy minimalnych kosztach operacyjnych. Jego kopię zapasową danych z fizycznymi i wirtualnymi systemów Windows Server, oprócz obciążeń aplikacji, takich jak SQL Server i programu SharePoint. Może także służyć przez [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) do replikacji danych chronionych na platformie Azure nadmiarowości i długoterminowego przechowywania.


Chronione dane w usłudze Azure Backup są przechowywane w magazynie kopii zapasowych, znajdującym się w określonym regionie geograficznym. Dane są replikowane w ramach tego samego regionu i, w zależności od typu magazynu, również mogą być replikowane do innego regionu dla dalszego odporności.

### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) firmy Microsoft w chmurze IT rozwiązanie do zarządzania ułatwiające zarządzanie i ochrona lokalnej infrastruktury w chmurze.


[Rozwiązania do zarządzania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) opakowaniach jednostkowych zestawy warunki logiczne implementujących scenariusza określonego zarządzania przy użyciu co najmniej jedną usługę OMS. Dostępne są różne rozwiązania oferowane przez firmę Microsoft i partnerów, które można łatwo dodać do subskrypcji platformy Azure w celu zwiększenia wartości inwestycji w pakiet OMS. Przez partnera możesz utworzyć rozwiązań do obsługi aplikacji i usług i udostępniania ich użytkownikom za pośrednictwem portalu Azure Marketplace lub Szybki Start szablonów.


![Rozwiązania do zarządzania](./media/azure-operational-security/azure-operational-security-fig4.png)

Dobrym przykładem rozwiązania obejmującego wiele usług oferowanie dodatkowych funkcji jest [rozwiązania do zarządzania aktualizacji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). To rozwiązanie wymaga [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agenta dla systemu Windows i Linux zebrać informacje o wymaganych aktualizacji na każdego agenta. Te dane są zapisywane w repozytorium usługi Log Analytics, w którym można je przeanalizować za pomocą dołączonego pulpitu nawigacyjnego.

Podczas tworzenia wdrożenia, elementy runbook w [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) są używane w celu zainstalowania wymaganych aktualizacji. Możesz zarządzać całym procesem w portalu, dzięki czemu nie musisz przejmować się szczegółami.

## <a name="azure-security-center"></a>Azure Security Center

Centrum zabezpieczeń Azure ułatwia ochronę zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. W ramach usługi, to można zdefiniować zasady nie tylko z subskrypcjami platformy Azure, ale także przed [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), dlatego może być bardziej szczegółowego.

### <a name="security-policies-and-recommendations"></a>Zasady zabezpieczeń i zalecenia w tym zakresie

Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w określonej subskrypcji lub grupie zasobów.

W usłudze Security Center możesz zdefiniować zasady zgodnie z wymaganiami w zakresie zabezpieczeń firmy oraz typem aplikacji lub stopniem poufności danych.

![Zasady zabezpieczeń i zalecenia w tym zakresie](./media/azure-operational-security/azure-operational-security-fig5.png)


Zasady, które są włączone na poziomie subskrypcji automatycznie propagowane do wszystkich grup zasobów w ramach subskrypcji, jak pokazano na rysunku po prawej stronie:


### <a name="data-collection"></a>Zbieranie danych

Usługa Security Center zbiera dane z maszyn wirtualnych w celu oceny ich stanu zabezpieczeń, przekazywania zaleceń dotyczących zabezpieczeń oraz alertów dotyczących zagrożeń. Po pierwszym dostęp do Centrum zabezpieczeń, zbierania danych jest włączona na wszystkich maszynach wirtualnych w ramach subskrypcji. Zaleca się zbieranie danych, ale możesz zrezygnować z niego, wyłączając zbieranie danych w zasadach usługi Security Center.

### <a name="data-sources"></a>Źródła danych

- Usługa Azure Security Center analizuje dane z następujących źródeł, aby zapewnić wgląd w stan zabezpieczeń, zidentyfikować luki w zabezpieczeniach i polecić rozwiązania oraz wykryć aktywne zagrożenia:

-   Usługi platformy Azure: używa informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.

- Ruch sieciowy: używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy i docelowy adres IP, źródłowy i docelowy port, rozmiar pakietu i protokół sieciowy.

-   Rozwiązania partnerów: używa alertów zabezpieczeń z rozwiązań zintegrowanych partnerów, takich jak zapory i rozwiązania do ochrony przed złośliwym oprogramowaniem.

-   Maszyny wirtualne: używa informacji dotyczących konfiguracji oraz zdarzeń związanych z zabezpieczeniami, takich jak zdarzenia systemu Windows i dzienniki inspekcji, dzienniki programu IIS, komunikaty dziennika systemu i pliki zrzutu awaryjnego, z maszyn wirtualnych.

### <a name="data-protection"></a>Ochrona danych

Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, usługa Azure Security Center zbiera i przetwarza dane dotyczące zabezpieczeń, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i inne. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

-   **Podział danych**: dane są logicznie oddzielone dla każdego składnika w całej usłudze. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

-   **Dostęp do danych**: Podaj zalecenia dotyczące zabezpieczeń i badanie możliwe zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskać dostęp do informacji zbieranych lub przeanalizowane przez usługi Azure, w tym pliki zrzutu awaryjnego, przetworzyć zdarzenia tworzenia dysku maszyny Wirtualnej migawki i artefaktów, które mogą przypadkowo obejmować dane klienta lub dane osobowe z maszyn wirtualnych. Firma Microsoft jest zgodna [warunki dotyczące usług Online firmy Microsoft i zasady zachowania poufności informacji](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), których stan, który firma Microsoft nie ma używa danych klienta lub interpretowania go do celów reklamowych lub podobne komercyjnych.

-   **Użycie danych**: firma Microsoft używa wzorców i analizy zagrożeń obecnych w wielu dzierżawach, aby zwiększyć możliwości wykrywania zagrożeń i zapobiegania im — robimy to zgodnie ze zobowiązaniami co do prywatności opisanymi w [zasadach zachowania poufności informacji](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

### <a name="data-location"></a>Lokalizacja danych

Usługa Azure Security Center zbiera efemeryczne kopie plików zrzutu awaryjnego i analizuje je pod kątem dowodów na próby ich naruszenia i pomyślnie przeprowadzonych ataków. Usługa Azure Security Center dokonuje tej analizy w ramach tego samego obszaru geograficznego, co obszar roboczy, i usuwa efemeryczne kopie po zakończeniu analizy. Artefakty maszyny są przechowywane centralnie w tym samym regionie, co maszyna wirtualna.

-   **Kont magazynu**: określono konto magazynu dla każdego regionu, w którym są uruchomione maszyny wirtualne. Dzięki temu można przechowywać dane w tym samym regionie co maszyna wirtualna, z której zbierane są dane.

-   **Magazyn usługi Azure Security Center Storage**: informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, zalecenia oraz stan kondycji zabezpieczeń, są przechowywane centralnie, obecnie na terenie Stanów Zjednoczonych. Do informacji zebranych z maszyn wirtualnych mogą należeć informacje dotyczące konfiguracji oraz zdarzeń związanych z zabezpieczeniami, dzięki którym możliwe jest przekazanie użytkownikowi danych na temat alertu zabezpieczeń, ewentualnych zaleceń postępowania oraz stanu kondycji zabezpieczeń.


## <a name="azure-monitor"></a>Azure Monitor

[Zabezpieczeń OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) i inspekcji rozwiązania umożliwiają działowi IT aktywnie monitoruje wszystkie zasoby, które można zminimalizować wpływ zdarzeń zabezpieczeń. Zabezpieczenia OMS i inspekcji ma domen zabezpieczeń, które mogą służyć do monitorowania zasobów. Domena zabezpieczeń zapewnia szybki dostęp do opcji, monitorowania zabezpieczeń następujące domeny są objęte więcej szczegółów:

-   oceny złośliwego oprogramowania
-   Ocena aktualizacji
-   Tożsamościami i dostępem.

Azure Monitor udostępnia wskaźniki do informacji dla określonych typów zasobów. Zapewnia ona wizualizacji, zapytania, routingu, alerty, automatyczne skalowanie i automatyzacji na dane zarówno z infrastrukturą systemu Azure (dziennik) i każdego pojedynczego zasobu platformy Azure (dzienników diagnostycznych).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Aplikacje w chmurze są złożonych z wielu części ruchu. Monitorowanie zawiera danych, aby upewnić się, że aplikacja pozostaje w górę i działa w dobrej kondycji. Pomaga również umożliwia stave potencjalne problemy i rozwiązywanie problemów w przeszłości te.

Ponadto można użyć danych monitorowania w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure


Jest dziennika, który zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Dziennik aktywności była wcześniej znana jako "Dzienników inspekcji" lub "Operacyjne dzienniki", ponieważ zgłasza płaszczyzny kontroli zdarzeń dla subskrypcji.

![Dziennik aktywności platformy Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Korzystając z dziennika aktywności, można określić ", co, która i kiedy" dla żadnego zapisu (PUT, POST, DELETE) podejmowaną w odniesieniu do zasobów w ramach subskrypcji. Można także zrozumienie stanu operacji i inne odpowiednie właściwości. Dziennik nie zawiera operacje odczytu (GET) lub operacje dla zasobów, które używają modelu Klasyczny.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Te dzienniki są emitowane przez zasób i podaj rozbudowane, często danych o działaniu tego zasobu. Zawartość tych dzienników zależy od typu zasobu.

Na przykład dzienniki systemu zdarzeń systemu Windows są jedną kategorię dzienników diagnostycznych dla maszyn wirtualnych i obiektów blob, tabeli, a dzienniki kolejki są kategorii dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennik aktywności (wcześniej znane jako dziennik inspekcji lub dziennik operacyjny)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Dziennik aktywności zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez sam zasób.

### <a name="metrics"></a>Metryki

Azure Monitor umożliwia korzystanie z telemetrię, aby uzyskać wgląd w wydajności i kondycji obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywanych również liczniki wydajności) emitowane przez zasoby najbardziej platformy Azure. Azure Monitor udostępnia kilka sposobów konfigurowania i korzystania z tych [metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) do monitorowania i rozwiązywania problemów. Metryki są cenne źródła danych telemetrycznych i umożliwiają wykonywanie następujących zadań:

-   **Śledzenie wyników** zasobu (np. maszyna wirtualna, witryny sieci Web lub logiki aplikacji) kreślenia jego metryki na wykresie portalu i przypinanie wykres do pulpitu nawigacyjnego.

-   **Otrzymuj powiadomienia o problemie** który wpływa na wydajność zasobu, gdy metryki przecina pewnej wartości progowej.

-   **Konfigurowanie automatycznych akcji**, takie jak automatyczne skalowanie zasobu lub wyzwalania elementu runbook, gdy metryki przecina pewnej wartości progowej.

-   **Wykonywać zaawansowane analizy** lub zgłaszanie trendów wydajności lub użycia zasobu.

-   **Archiwum** historii wydajności lub kondycji zasobu zgodności i inspekcji.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Istnieje możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych na wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych różnych źródeł. Obecnie obsługiwane są [Azure Cloud Service w sieci Web i proces roboczy](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [maszyny wirtualne Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) systemu Microsoft Windows i [sieci szkieletowej usług](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Innymi usługami Azure mają własne oddzielne diagnostyki.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Inspekcja zabezpieczeń sieci jest niezbędne do wykrycia luk w zabezpieczeniach sieci i zapewniania zgodności z przepisami ładu modelu i zabezpieczeń IT. Widok grupy zabezpieczeń można pobrać skonfigurowanej grupy zabezpieczeń sieci i zasady zabezpieczeń i zasady efektywnym elementem systemu zabezpieczeń. Z listą zasady zastosowane należy określić porty, które są otwarte i oceny luk w zabezpieczeniach sieci.

[Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) to regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie sieci w, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure. Ta usługa obejmuje przechwytywania pakietów, następnego przeskoku, przepływ IP Sprawdź widok grupy zabezpieczeń, dzienniki przepływu NSG. Scenariusz poziomu monitorowania udostępnia widok pełnego zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Obserwatora sieciowego ma obecnie następujące możliwości:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Dzienniki inspekcji</a>**-operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Dzienniki te mogą być wyświetlane w portalu Azure lub pobrany przy użyciu narzędzi firmy Microsoft, takich jak usługi Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API Rest. Aby uzyskać więcej informacji na dziennikach inspekcji Zobacz operacje inspekcji za pomocą Menedżera zasobów. Dzienniki inspekcji są dostępne dla operacje wykonywane na wszystkich zasobów sieciowych.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Przepływ IP weryfikuje </a>**  — sprawdza, czy pakiet jest dozwolony lub niedozwolony na podstawie przepływ informacji 5-elementowej pakietu parametrów (docelowy adres IP, źródłowy adres IP, Port docelowy, Port źródłowy i Protocol). Jeśli pakiet odmówił sieciowej grupy zabezpieczeń, zwracany jest reguła i grupy zabezpieczeń sieci, z którego pakiet.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Następny przeskok</a>**  — określa następnego skoku dla pakietów przesyłane w sieci szkieletowej Azure, dzięki któremu można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Widok grupy zabezpieczeń</a>**  -pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Rejestrowanie przepływu NSG</a>**  — dzienniki przepływu dla grup zabezpieczeń sieci umożliwiają przechwytywanie dzienniki związane z ruchu, który ma być dozwolony lub odrzucany przez zasady zabezpieczeń w grupie. Przepływ został zdefiniowany przez informacji 5-elementowej — źródłowy adres IP, docelowy adres IP, Port źródłowy, Port docelowy i protokołu.

## <a name="azure-storage-analytics"></a>Analityka usługi Azure Storage

[Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) może przechowywać metryki, które zawierają zagregowane transakcji statystyk i pojemności dane dotyczące żądań do usługi magazynu. Transakcje są raportowane zarówno na poziomie operacji interfejsu API i na poziomie usługi magazynu, a pojemność jest zgłaszany na poziomie usługi magazynu. Dane metryk można analizować wykorzystanie usługi magazynu, diagnozowanie problemów z żądań wysyłanych z usługą magazynu i poprawić wydajność aplikacji, które używają usługi.

[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia metryki danych dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Rejestrowanie analityka magazynu jest dostępne dla [usługi obiektów Blob, kolejki i tabeli](https://docs.microsoft.com/azure/storage/storage-introduction). Analityka magazynu rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi magazynu.

Te informacje może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane w sposób optymalny. Wpisy dziennika są tworzone tylko w przypadku żądań wysyłanych do punktu końcowego usługi. Na przykład jeśli konto magazynu ma działanie punktu końcowego obiektu Blob, ale nie w jej tabel lub kolejek punktów końcowych, tylko dzienniki odnoszące się do usług obiektów Blob jest tworzony.

Aby użyć analityka magazynu, należy włączyć ją osobno dla każdej usługi, które chcesz monitorować. Możesz je włączyć w [portalu Azure](https://portal.azure.com/); Aby uzyskać więcej informacji, zobacz [monitorować konto magazynu w portalu Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Można również włączyć analityka magazynu programowo przy użyciu interfejsu API REST lub biblioteka klienta. Włącz analizy magazynu osobno dla każdej usługi za pomocą operacji ustawić właściwości usługi.

Zagregowane dane są przechowywane w dobrze znany obiekt blob (w przypadku rejestrowania) i dobrze znane tabel (metryk), które mogą uzyskać dostęp za pomocą usługi obiektów Blob i usługa tabel interfejsów API.

Analityka magazynu ma limit 20 TB ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Wszystkie dzienniki są przechowywane w [blokowe obiekty BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) w kontenerze o nazwie $logs, które są automatycznie tworzone podczas analityka magazynu jest włączona dla konta magazynu.

Rozliczeniowy są następujące akcje wykonywane przez analityka magazynu:

-   Żądań w celu utworzenia obiektów blob do rejestrowania
-   Żądania utworzenia jednostek tabeli dla metryki.

> [!Note]
> Aby uzyskać więcej informacji dotyczących rozliczeń i zasad przechowywania danych, zobacz [analizy magazynu i rozliczeń](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Aby uzyskać optymalną wydajność chcesz ograniczyć liczbę wysokiej wykorzystywanych dysków dołączonych do maszyny wirtualnej, aby uniknąć możliwych ograniczania. Jeśli wszystkie dyski nie są jest wysoce używane w tym samym czasie, konto magazynu może obsługiwać większy dysk numer.

> [!Note]
> Aby uzyskać więcej informacji o limity konta magazynu, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Rejestrowane są następujące typy żądań uwierzytelnionych i anonimowe.

| Uwierzytelniony  | Anonimowe|
| :------------- | :-------------|
| Liczba pomyślnych żądań | Liczba pomyślnych żądań |
|Nieudane żądania, w tym limitu czasu, ograniczania przepustowości sieci, autoryzacji i innych błędów | Żądania przy użyciu dostępu sygnatury dostępu Współdzielonego, włącznie z żądaniami nie powiodło się i pomyślne |
| Żądania przy użyciu dostępu sygnatury dostępu Współdzielonego, włącznie z żądaniami nie powiodło się i pomyślne |Błędy limitu czasu dla klienta i serwera |
|   Żądania do analizy danych |    Nieudane żądania GET z kodem błędu 304 (nie jest zmodyfikowany) |
| Żądania wysyłane przez analityka magazynu, takich jak dziennika utworzeniu lub usunięciu, nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) tematów. | Inne nieudanych żądań anonimowych nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Usługa Azure Active Directory

Usługi Azure AD umożliwia także całą gamę możliwości zarządzania tożsamościami, w tym uwierzytelniania wieloskładnikowego, rejestracji urządzenia, zarządzanie hasłami samoobsługi, Samoobsługowe zarządzanie grupami, uprzywilejowane konto zarządzania, kontroli dostępu opartej na rolach, monitorowania użycia aplikacji, sformatowanego inspekcji i zabezpieczeń, monitorowanie i alerty.

-   Poprawy zabezpieczeń aplikacji z usługi Azure AD, uwierzytelnianie wieloskładnikowe i dostępu warunkowego.

-   Monitorowanie użycia aplikacji oraz ochronę firmy przed zagrożeniami zaawansowanych zabezpieczeń, monitorowanie i raportowanie.

W usłudze Azure Active Directory (Azure AD) uwzględniono raporty dotyczące zabezpieczeń, działań i inspekcji dla katalogu. [Azure Active Directory raport dotyczący inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) przydatny do identyfikowania uprzywilejowanych akcji, które wystąpiły w usłudze Azure Active Directory. Uprzywilejowane akcje obejmują zmiany podniesienia uprawnień (na przykład tworzenie roli lub resetowanie haseł), zmiana konfiguracji zasad (na przykład zasady haseł) lub zmiany w konfiguracji katalogu (na przykład zmiany ustawień federacyjnego domeny).

Raporty dostarczają rekordu inspekcji dla nazwy zdarzenia aktora, kto wykonał akcję zasobu docelowego wpływ zmiany oraz Data i godzina (w formacie UTC). Klienci będą mogli pobrać listę zdarzeń inspekcji dla ich Azure Active Directory za pośrednictwem [portalu Azure](https://portal.azure.com/), zgodnie z opisem w [wyświetlanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Oto lista uwzględnionych raportów:

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
| |Działania związane z resetowaniem haseł |   | |



Dane te raporty może być przydatne do aplikacji, takich jak systemów SIEM, inspekcji i narzędzia do analizy biznesowej. Raportów usługi Azure AD [interfejsów API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) programowy dostęp do danych za pomocą zestawu opartego na interfejsie REST API. Te interfejsy API można wywołać z różnych języków programowania i narzędzi.

Zdarzenia w raporcie programu Azure AD inspekcji są zachowywane przez okres 180 dni.

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania w raportach, zobacz [zasady przechowywania raportów Active Directory Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Dla klientów do przechowywania ich [zdarzenia inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) przez dłuższy czas przechowywania, do interfejsu API raportowania może służyć do regularnie ściągnięcia zdarzeń inspekcji w magazynie danych.

## <a name="summary"></a>Podsumowanie

Podsumowania tego artykułu, ochrony prywatności i zabezpieczania danych, dostarczając oprogramowania i usług, które ułatwiają zarządzanie infrastrukturą IT organizacji. Firma Microsoft rozumie, że podczas ich powierzyć swoje dane do innych osób, tej relacji zaufania wymaga rygorystyczne zabezpieczeń. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi. Zabezpieczenia i ochrona danych ma najwyższy priorytet w firmie Microsoft.

W tym artykule opisano

-   Jak dane są zbierane, przetwarzane i zabezpieczone w Operations Management Suite (OMS).

-   Szybko analizuj zdarzenia w wielu źródłach danych. Identyfikuj zagrożenia bezpieczeństwa i uzyskuj wiedzę o zakresie i wpływie zagrożeń i ataków, aby eliminować szkody związane z naruszeniem zabezpieczeń.

-   Identyfikuj wzorce ataków dzięki wizualizowaniu wychodzącego złośliwego ruchu przez adresy IP i typów zagrożeń spowodowanych złośliwymi działaniami. Dowiedz się, stan zabezpieczeń całego środowiska niezależnie od platformy.

-   Przechwyć wszystkie dane dziennika i zdarzenie wymagane dla inspekcji zabezpieczeń i zgodności. Ukośnika czasu i zasobów potrzebnych do dostaw dziennika pełnej, wyszukiwanie i można eksportować i zestaw danych zdarzenia inspekcji zabezpieczeń.

<ul>
<li>Zbieranie zdarzeń zabezpieczeń, inspekcji i analizy naruszenia, aby zachować monitorowaniu zasobów:</li>
<ul>
<li>Stan zabezpieczeń</li>
<li>Godne problem</li>
<li>Zagrożenia podsumowania</li>
</ul>
</ul>

## <a name="next-steps"></a>Następne kroki

- [Projektowanie i bezpieczeństwa operacyjnego](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft opracowywania oprogramowania i usług z zabezpieczeniami, pamiętaj, aby zapewnić, że infrastruktury w chmurze jest odporność i obrony, w odniesieniu przed atakami.

- [Operations Management Suite | Bezpieczeństwo i zgodność](https://www.microsoft.com/cloud-platform/security-and-compliance)

Użyj danych zabezpieczeń firmy Microsoft i analizy przeprowadzić bardziej inteligentne i skuteczne wykrywanie zagrożeń.

- [Planowanie Centrum zabezpieczeń Azure i operacje](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) zestaw kroków i zadań, które można wykonać, aby zoptymalizować korzystanie z Centrum zabezpieczeń, w oparciu o wymagania dotyczące zabezpieczeń oraz modelu zarządzania chmurą w organizacji.

