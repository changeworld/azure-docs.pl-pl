---
title: Azure rejestrowanie i inspekcja | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposób rejestrowania danych można użyć w celu uzyskania szczegółowych informacji o aplikacji.
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
ms.openlocfilehash: e4144ca0d87abda3d9f8de47e56af59d0e4af312
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938370"
---
# <a name="azure-logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji na platformie Azure

Platforma Azure oferuje szeroką gamę zabezpieczeń można skonfigurować inspekcji i rejestrowania opcje ułatwiające identyfikowanie luk w Twojej zasad i mechanizmów zabezpieczeń. W tym artykule omówiono generowania, zbieranie i analizowanie dzienników zabezpieczeń z usługi hostowanej na platformie Azure.

> [!Note]
> Zastosowanie niektórych zaleceń zamieszczonych w tym artykule może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych i zwiększyć koszty licencji lub subskrypcji.

## <a name="types-of-logs-in-azure"></a>Typy dzienników na platformie Azure
Aplikacje w chmurze są złożonych z wielu części ruchu. Dzienniki źródłem danych, aby zapewnić aplikacji do pracy. Dzienniki pomagają rozwiązywać problemy przeszłe problemy lub uniemożliwić potencjalne z nich. I pomaga zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji.

Dzienniki platformy Azure są podzielone na następujące typy:
* **Dzienniki sterowania i zarządzania nimi** zawierają informacje o operacjach tworzenia Menedżera zasobów Azure, UPDATE i DELETE. Aby uzyskać więcej informacji, zobacz [Dzienniki aktywności Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Dane płaszczyzny dzienniki** zawierają informacje o zdarzeniach zgłoszone jako część użycia zasobów platformy Azure. Przykładem tego typu dziennika są zdarzeń systemu Windows, zabezpieczenia i dzienniki aplikacji na maszynie wirtualnej (VM) i [dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) skonfigurowanych za pośrednictwem Monitora Azure.

* **Przetworzonych zdarzeń** zawierają informacje o analizowanych zdarzenia/alerty, które zostały przetworzone w Twoim imieniu. Przykładem tego typu są [alerty Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) gdzie [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) jest przetwarzane i analizowane subskrypcji i zawiera alerty zabezpieczeń zwięzły.

W poniższej tabeli wymieniono najważniejsze typów dzienników dostępnych w programie Azure:

| Kategoria dziennika | Typ dziennika | Sposób użycia | Integracja |
| ------------ | -------- | ------ | ----------- |
|[Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Zdarzenia płaszczyzny kontroli zasobów usługi Azure Resource Manager|   Zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji.|    REST API, [Azure monitora](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Często dane dotyczące działania usługi Azure Resource Manager zasobów w subskrypcji|    Zapewnia wgląd w działania wykonywane przez samego zasobu.| Azure Monitor [strumienia](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Raportowanie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Dzienników i raportów | Raport aktywności logowania użytkowników i informacji o systemie działania dotyczące użytkowników i grupy zarządzania.|[Interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Maszyny wirtualne i usługi w chmurze](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-quick-collect-azurevm)|Usługa Dziennik zdarzeń systemu Windows i systemu Linux|    Przechwytuje dane systemu i rejestrowanie danych na maszynach wirtualnych i przesyła dane do wybranego konta magazynu.|   Systemu Windows (przy użyciu systemu Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] magazynu) i Linux w monitorze Azure|
|[Usługa Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Rejestrowanie magazynu zapewnia danych metryki dla konta magazynu|Zapewnia wgląd w żądań śledzenia, analizuje trendy użycia i diagnozuje problemy z kontem magazynu.|   Interfejs API REST lub [biblioteki klienta](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Dzienniki przepływu grupy zabezpieczeń sieci (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON format zawiera przepływy ruchu wychodzącego i przychodzącego na podstawie reguł|Wyświetla informacje o przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci.|[Monitor sieci platformy Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Szczegółowe informacje o aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Dzienniki, wyjątków i diagnostyki niestandardowej|   Zapewnia wydajność aplikacji (APM) usługi sieci web deweloperów na wielu platformach monitorowania.| Interfejs API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Przetwarzaj dane / alerty zabezpieczeń|    Alerty Centrum zabezpieczeń Azure, Azure Log Analytics alertów|   Zawiera informacje o zabezpieczeniach i alerty.|  Interfejsy API REST, JSON|

### <a name="activity-logs"></a>Dzienniki aktywności
[Dzienniki aktywności Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Dzienniki aktywności zostały wcześniej znana jako "dzienników inspekcji" lub "operacyjne dzienniki,", ponieważ zgłaszają [zdarzeń formantu płaszczyzny](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) dla Twojej subskrypcji. 

Pomoc w określeniu rejestruje działania ", co, która i kiedy" dla operacji zapisu (oznacza to, PUT, POST i usuwania). Działanie rejestruje także ułatwiające zrozumienie, jakie stanu operacji i inne odpowiednie właściwości. Dzienniki aktywności nie dołączaj operacje odczytu (GET).

W tym artykule PUT, POST i DELETE odnosi się do wszystkich operacji zapisu, które zawiera dziennik aktywności na zasobach. Na przykład można użyć Dzienniki aktywności, aby znaleźć błąd przy rozwiązywaniu problemów lub do monitorowania, jak użytkownik w organizacji zmienić zasobu.

![Diagram dziennika aktywności](./media/azure-log-audit/azure-log-audit-fig1.png)

Przy użyciu portalu Azure, można pobrać zdarzenia z dziennika aktywności [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), poleceń cmdlet programu PowerShell, a [interfejsu API REST Monitor Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Dzienniki aktywności mają okres przechowywania danych 19 dnia.

Scenariusze integracji zdarzenia dziennika aktywności:

* [Utwórz alert e-mail lub elementu webhook jest wyzwalany przez zdarzenie dziennika aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Strumienia go do Centrum zdarzeń](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.

* Analizować je w usługi Power BI przy użyciu [pakiet zawartości usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Zapisz go na konto magazynu inspekcji archiwizacji lub ręcznie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Można określić czas przechowywania (w dniach) przy użyciu profilów dziennika.

* Zapytania i wyświetlać go w portalu Azure.

* Zapytanie go za pomocą polecenia cmdlet programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST.

* Eksportuj Dziennik aktywności przy użyciu profilów dziennika do [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Można użyć konta magazynu lub [przestrzeni nazw zdarzenia koncentratora](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) nie jest w tej samej subskrypcji, która jest emitowanie dziennika. Kto Konfiguruje ustawienie musi mieć odpowiednie [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dostęp do obu subskrypcji.

### <a name="azure-diagnostics-logs"></a>Dzienniki diagnostyczne platformy Azure
Dzienniki diagnostyczne platformy Azure są emitowane przez zasób, który zapewnia rozbudowane, często dane o działaniu tego zasobu. Zawartość tych dzienników zależy od typu zasobu. Na przykład [dzienniki systemu zdarzeń systemu Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) są kategorii dzienników diagnostycznych dla maszyn wirtualnych i [obiektów blob, tabel i dzienniki kolejki](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) jest kategorii dzienników diagnostycznych dla kont magazynu. Dzienniki diagnostyczne różnią się od Dzienniki aktywności, które zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji.

![Diagramy dzienników diagnostycznych platformy Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Dzienniki diagnostyczne platformy Azure oferują wiele opcji konfiguracji, takich jak portalu Azure, programu PowerShell, interfejsu wiersza polecenia Azure i interfejsu API REST.

**Scenariusze integracji**

* Zapisywanie ich [konta magazynu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) inspekcji inspekcji lub ręcznie. Można określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych.

* [Strumienia je do usługi event hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analizuj je za pomocą [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Obsługiwane usługi, schematu dla dzienników diagnostyki i kategorie dziennika obsługiwanych na typ zasobu**


| Usługa | Schemat i dokumentacji | Typ zasobu | Kategoria |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Analizy dzienników dla usługi równoważenia obciążenia (wersja zapoznawcza)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Grupy zabezpieczeń sieci|[Analizy dzienników dla grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Rejestrowania diagnostyki bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|W usłudze Azure Key Vault|[Dzienników usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Włączanie i używanie analizy ruchu wyszukiwania](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Dzienniki diagnostyczne dostępu dla usługi Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Inspekcja<br>Żądania|
|Azure Data Lake Analytics|[Dzienniki diagnostyczne dostępu dla usługi Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Inspekcja<br>Żądania|
|Azure Logic Apps|[Logic Apps — niestandardowy schemat śledzenia B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure dzienniki diagnostyczne partii](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Analizy dzienników dla usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Dzienniki diagnostyczne centra zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Usługa Azure Stream Analytics|[Dzienniki diagnostyczne zadania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Wykonanie<br>Tworzenie|
|Azure Service Bus|[Dzienniki diagnostyczne usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Raporty w usłudze Azure Active Directory
Azure Active Directory (Azure AD) zawiera zabezpieczeń, działań i raporty dotyczące inspekcji dla katalogu użytkownika. [Raportów inspekcji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomaga zidentyfikować uprzywilejowanych akcji, które wystąpiły w wystąpieniu usługi Azure AD przez użytkownika. Uprzywilejowane akcje obejmują zmiany podniesienia uprawnień (na przykład tworzenie roli lub resetowanie haseł), zmiana konfiguracji zasad (na przykład zasady haseł) lub zmiany w konfiguracji katalogu (na przykład zmiany domeny federacyjnej ustawień).

Raporty dostarczają rekordu inspekcji zdarzeń nazwę użytkownika, który wykonał akcję zasobu docelowego wpływ zmiany oraz Data i godzina (w formacie UTC). Użytkownicy mogą pobrać listę zdarzeń inspekcji dla usługi Azure AD za pomocą [portalu Azure](https://portal.azure.com/), zgodnie z opisem w [wyświetlanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Raporty uwzględnione są wymienione w poniższej tabeli:

| Raporty dotyczące zabezpieczeń | Raporty dotyczące działań | Raporty dotyczące inspekcji |
| :--------------- | :--------------- | :------------ |
|Logowania z nieznanych źródeł| Podsumowanie użycia aplikacji| Raport dotyczący inspekcji katalogu|
|Logowania po wielokrotnych niepowodzeniach|  Szczegóły użycia aplikacji||
|Logowania z wielu lokalizacji geograficznych|    Pulpit nawigacyjny aplikacji||
|Logowania z adresów IP związanych z podejrzanymi działaniami|   Błędy aprowizacji kont||
|Nieregularne działania związane z logowaniem|    Urządzenia indywidualnych użytkowników||
|Logowania z urządzeń, które mogą być zainfekowane|   Działania indywidualnych użytkowników||
|Nietypowe działania użytkowników związane z logowaniem| Raport dotyczący działań grup||
||Raport aktywności rejestracji resetowania haseł||
||Działania związane z resetowaniem haseł|||

Dane w tych raportach mogą być przydatne do aplikacji, takich jak systemy Security Information and Event Management (SIEM), inspekcji i narzędzia do analizy biznesowej. Interfejsy API raportów usługi Azure AD umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Możesz wywołać te [interfejsów API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) z różnych języków programowania i narzędzi.

Zdarzenia raportów inspekcji usługi Azure AD zostaną zachowane na okres 180 dni.

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania raportów, zobacz [zasady przechowywania raportów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Jeśli interesuje Cię zachowując dłużej zdarzeń inspekcji, za pomocą interfejsu API raportowania regularnie ściągania [zdarzenia inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) do magazynu danych.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Dzienniki maszyny wirtualnej, korzystających z diagnostyki Azure
[Diagnostyka Azure](https://docs.microsoft.com/azure/azure-diagnostics) możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych o wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki za pomocą dowolnego z wielu źródeł. Obecnie obsługiwane są [role sieci web i proces roboczy usługi chmury Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Dzienniki maszyny wirtualnej, korzystających z diagnostyki Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Maszyny wirtualne platformy Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) z systemem Microsoft Windows i [sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Diagnostyki Azure można włączyć na maszynie wirtualnej, wykonując jedną z następujących czynności:

* [Śledzenie maszyn wirtualnych platformy Azure za pomocą programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Zdalnie konfigurować diagnostyki Azure na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Aby skonfigurować diagnostykę na maszynach wirtualnych Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Utwórz maszynę wirtualną systemu Windows z monitorowania i diagnostyki za pomocą szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Analityka magazynu
[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) dzienniki i udostępnia metryki danych dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Rejestrowanie analityka magazynu jest dostępne dla [usługi magazynu obiektów Blob platformy Azure, kolejek platformy Azure i tabel Azure](https://docs.microsoft.com/azure/storage/storage-introduction). Analityka magazynu rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi magazynu.

Te informacje można użyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane w sposób optymalny. Wpisy dziennika są tworzone tylko w przypadku żądań wysyłanych do punktu końcowego usługi. Na przykład jeśli konto magazynu ma działanie punktu końcowego obiektu blob, ale nie w jej tabel lub kolejek punkty końcowe, są tworzone tylko dzienniki, które odnoszą się do usługi magazynu obiektów Blob.

Aby użyć analityka magazynu, włączyć go osobno dla każdej usługi, które chcesz monitorować. Możesz je włączyć w [portalu Azure](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [monitorować konto magazynu w portalu Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Można również włączyć analityka magazynu programowo przy użyciu interfejsu API REST lub biblioteka klienta. Włącz analizy magazynu osobno dla każdej usługi za pomocą operacji ustawić właściwości usługi.

Zagregowane dane są przechowywane w dobrze znany obiekt blob (w przypadku rejestrowania) i dobrze znane tabele (dla metryki), które jest dostępne przy użyciu usługi magazynu obiektów Blob i interfejsów API usług magazynu w tabeli.

Analityka magazynu ma limit 20 terabajtów (TB) ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Wszystkie dzienniki są przechowywane w [blokowe obiekty BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) w kontenerze o nazwie $logs, które zostało automatycznie utworzone po włączeniu analityka magazynu dla konta magazynu.

> [!Note]
> * Aby uzyskać więcej informacji dotyczących rozliczeń i zasad przechowywania danych, zobacz [analizy magazynu i rozliczeń](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [elementy docelowe skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Analityka magazynu rejestruje następujące typy żądań uwierzytelnionych i anonimowy:

| Uwierzytelniony  | Anonimowe|
| :------------- | :-------------|
| Liczba pomyślnych żądań | Liczba pomyślnych żądań |
|Nieudane żądania, w tym limitu czasu, ograniczania przepustowości sieci, autoryzacji i innych błędów | Przy użyciu sygnatury dostępu współdzielonego, włącznie z żądaniami nie powiodło się i pomyślnych żądań |
| Przy użyciu sygnatury dostępu współdzielonego, włącznie z żądaniami nie powiodło się i pomyślnych żądań |Błędy przekroczenia limitu czasu dla klienta i serwera |
|   Żądania do analizy danych |    Nieudane żądania GET z kodem błędu 304 (nie jest zmodyfikowany) |
| Żądania wysyłane przez analityka magazynu, takich jak dziennika utworzeniu lub usunięciu, nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [analityka magazynu rejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [format dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Inne nieudanych żądań anonimowych nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [analityka magazynu rejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [format dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Dzienniki sieci platformy Azure
Sieć rejestrowania i monitorowania na platformie Azure jest kompleksowy i obejmuje dwie szerokie kategorie:

* [Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): monitorowania sieci oparta na scenariuszu jest dostarczana z funkcji obserwatora sieciowego. Ta usługa obejmuje przechwytywania pakietów, następnego przeskoku, przepływ IP Sprawdź widok grupy zabezpieczeń, dzienniki przepływu NSG. Scenariusz poziomu monitorowania udostępnia widok pełnego zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

* [Monitorowanie zasobów](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): poziom monitorowania zasobów składa się z czterech funkcji, dzienników diagnostycznych metryki, rozwiązywanie problemów i kondycji zasobów. Te funkcje są tworzone na poziomie zasobów sieciowych.

![Dzienniki sieci platformy Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Monitor sieci jest regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie sieci scenariusz w, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure.

### <a name="network-security-group-flow-logging"></a>Rejestrowanie przepływu grupy zabezpieczeń sieci

[Dzienniki przepływu NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) są funkcją obserwatora sieciowego, który służy do wyświetlania informacji dotyczących przychodzące i wychodzące ruch IP za pośrednictwem grupy NSG. Te dzienniki przepływu są zapisywane w formacie JSON i wyświetlić:
* Wychodzące i przychodzące przepływem na podstawie reguł.
* Karta sieciowa, która dotyczy przepływu.
* 5-elementowej informacji o przepływie: źródłowy lub docelowy adres IP, port źródłowy lub docelowy i protokół.
* Określa, czy ruch dozwolony lub odmowa dostępu.

Mimo że przepływ rejestruje docelowych grup NSG, nie są wyświetlane w taki sam sposób jak inne dzienniki. Przepływ dzienniki są przechowywane tylko w ramach konta magazynu.

Te same zasady przechowywania, które są widoczne na inne dzienniki dotyczą dzienniki przepływu. Dzienniki ma zasady przechowywania, które można ustawić od 1 dnia do 365 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

**Dzienniki diagnostyczne**

Okresowe i spontanicznych zdarzenia są tworzone przez zasobów sieciowych i zarejestrowane na kontach magazynu i wysyłane do Centrum zdarzeń lub analizy dzienników. Dzienniki wgląd w kondycję zasobu. W menu Narzędzia, takie jak usługi Power BI i analizy dzienników można wyświetlić ich. Aby dowiedzieć się, jak przeglądanie dzienników diagnostycznych, zobacz [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Dzienniki diagnostyczne](./media/azure-log-audit/azure-log-audit-fig5.png)

Dzienniki diagnostyczne są dostępne dla [modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy i [brama aplikacji w](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Zawiera obserwatora sieciowego widoku dzienników diagnostycznych. Ten widok zawiera wszystkie zasoby sieciowe, które obsługuje rejestrowania diagnostyki. Z tego widoku można włączyć i wyłączyć zasobów sieciowych, łatwo i szybko.


Oprócz możliwości rejestrowania wymienione wcześniej obserwatora sieciowego ma obecnie następujące możliwości:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Wyświetla poziom sieci, pokazujący różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.

- [Przechwytywania pakietów zmiennej](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): przechwytuje pakiet danych do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i dostosowawczych formanty, takie jak ustawienia limitu czasu i rozmiaru, podaj wszechstronność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w *CAP* format pliku.

* [Weryfikacja przepływu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): sprawdza, czy pakiet jest dozwolony lub zabroniony na podstawie przepływ informacji 5-elementowej pakietu parametrów (to znaczy, docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli pakiet jest zabroniony przez grupę zabezpieczeń, zwracany jest reguła i grupy, którego pakiet.

* [Następny przeskok](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Określa następnego skoku dla pakietów przesyłane w sieci szkieletowej sieć platformy Azure, dzięki czemu można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika.

* [Widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej.

* [Brama sieci wirtualnej i rozwiązywanie problemów z połączenia](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): pomaga w rozwiązywaniu problemów połączenia i bram sieci wirtualnej.

* [Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): umożliwia wyświetlenie wykorzystania zasobów sieci ograniczeń.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) jest rozszerzalną usługę APM dla deweloperów sieci web na wielu platformach. Używana do monitorowania aplikacji sieci web na żywo. Automatycznie wykrywa anomalii wydajności. Obejmuje ona zaawansowane narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć sposób korzystania z aplikacji przez użytkowników.

Usługa Application Insights zaprojektowano w celu stale poprawić wydajność i użyteczność.

Działania aplikacji w różnych platform, w tym .NET, Node.js i J2EE, czy są one hostowane lokalnie lub w chmurze. Integruje się z procesu opracowywania oprogramowania, a ma punkty połączenia z różnych narzędzi programistycznych.

![Application Insights diagramu](./media/azure-log-audit/azure-log-audit-fig6.png)

Usługa Application Insights jest przeznaczona dla zespołu deweloperów po to, aby pomóc zrozumieć, jak działa Twoja aplikacja i jak jest używana. Monitoruje ona:

* **Żądanie szybkość, czas reakcji i awariami**: stron, które jest najbardziej popularnym o jakich porach dnia, i gdzie znajdują się użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czas reakcji i awariami go wysokiej gdy istnieją więcej żądania, może być resourcing problem.

* **Zależności szybkość, czas reakcji i awariami**: dowiedzieć się, czy usług zewnętrznych są spowolnieniem użytkownik.

* **Wyjątki**: analizowanie zagregowanych danych statystycznych, lub wybierz określone wystąpienia i przejść do szczegółów w powiązanych żądań i ślad stosu. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

* **Liczba wyświetleń strony i wydajność obciążenia**: Pobierz raporty z przeglądarki użytkownika.

* **Wywołania AJAX**: pobieranie stawki strony sieci Web, czas reakcji i awariami.

* **Liczby użytkowników i sesji**.

* **Liczniki wydajności**: pobieranie danych z komputerach serwerów z systemem Windows lub Linux, takie jak procesor CPU, pamięci i użycie sieci.

* **Host diagnostyki**: pobieranie danych z Docker lub Microsoft Azure.

* **Dzienniki śledzenia diagnostyki**: pobieranie danych z aplikacji, dzięki czemu można skorelować zdarzenia śledzenia z żądania.

* **Niestandardowe zdarzenia i metryki**: pobrać dane, które zapisu samodzielnie kodu klienta lub serwera, aby śledzić zdarzenia biznesowe, takie jak towarów sprzedanych lub gry won.

Poniższa tabela zawiera listę oraz opis scenariuszy integracji:

| Scenariusz integracji | Opis |
| --------------------- | :---------- |
|[Mapowanie aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Składniki Twojej aplikacji wraz z kluczowymi metrykami i alertami.||
|[Diagnostyka dla wystąpienia wyszukiwanie danych](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Wyszukiwanie i filtrowanie zdarzeń, takich jak żądania, wyjątki, wywołania zależności, dzienniki śledzenia i wyświetlenia stron.||
|[Eksploratora metryk na zastosowanie zagregowanych danych](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Eksploruj, filtruj i segmentuj zagregowane dane, takie jak liczby żądań, błędów i wyjątków, czasy reakcji, czasy ładowania stron.||
|[Pulpity nawigacyjne](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Połącz dane z wielu zasobów i udostępnij innym osobom. Opcja ta doskonale nadaje się dla aplikacji wieloskładnikowych i ciągłego wyświetlania w pomieszczeniu zespołu.||
|[Metryki strumień na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Podczas wdrażania nowej kompilacji obejrzyj te wskaźniki wydajności prawie w czasie rzeczywistym, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.||
|[Analiza](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Odpowiedz na trudne pytania dotyczące wydajności i użycia Twojej aplikacji za pomocą tego zaawansowanego języka zapytań.||
|[Alerty automatycznej i ręcznej](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Alerty automatycznego dostosowania do normalnej wzorców telemetrii aplikacji i są wyzwalane, gdy coś poza wzorcem zwykle jest. Możesz też ustawić alerty dla konkretnych poziomów metryk niestandardowych lub standardowych.||
|[Program Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Wyświetl dane dotyczące wydajności w kodzie. Przejdź do kodu ze śladów stosu.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integruje metryki użycia z innymi analizami biznesowymi.||
|[Interfejs API REST](https://dev.applicationinsights.io/)|Napisz kod, aby uruchamiać zapytania dla swoich metryk i danych pierwotnych.||
|[Eksport ciągły](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Zbiorcze eksportu nieprzetworzone dane do magazynu po dostarczeniu.||

### <a name="azure-security-center-alerts"></a>Alerty Centrum zabezpieczeń Azure
Wykrywanie zagrożeń Centrum zabezpieczeń Azure działa automatycznie zbieranie informacji o zabezpieczeniach z zasobów platformy Azure, sieci i rozwiązań partnerskich połączonych. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo. Aby uzyskać więcej informacji, zobacz [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagram Centrum zabezpieczeń Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Ma to zastosowanie osiągnięć w dużej ilości danych i [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie, aby ocenić zdarzenia w chmurze całej sieci szkieletowej. W ten sposób wykryciu zagrożenia, które byłoby niemożliwe do identyfikowania przy użyciu metod ręcznych i przewidywanie ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

* **Zintegrowane analizy zagrożeń**: szuka znanych nieupoważnione osoby, stosując globalnej analizy zagrożeń z produktów firmy Microsoft i usług, jednostki ds. przestępstw cyfrowych (DCU) firmy Microsoft, Microsoft Security odpowiedzi Center (MSRC) i zewnętrznych źródeł danych.

* **Analizy behawioralnej**: stosuje znane wzorce do wykrywania złośliwego zachowania.

* **Wykrywanie anomalii**: korzysta statystyczne profilowania w celu skompilowania historycznych linii bazowej. Narzędzie to alarmuje o odchyleniach od ustalonych linii bazowych zgodnych z wektorem potencjalnego ataku.

Wiele operacji zabezpieczeń i zespoły odpowiedzi na zdarzenia korzystają z rozwiązania SIEM, jako punkt początkowy klasyfikowane i badanie alertów zabezpieczeń. Dzięki integracji dziennika Azure można synchronizować alerty Centrum zabezpieczeń i zdarzenia zabezpieczeń maszyny wirtualnej, zebrane przez Azure dzienników diagnostyki i inspekcji, wraz z rozwiązaniem analizy dzienników lub SIEM w najbliższym czasie rzeczywistym.

## <a name="log-analytics"></a>Log Analytics 

Analiza dzienników jest usługą platformy Azure, która ułatwia zbieranie i analizowanie danych, który jest generowany przez zasobów w chmurze i lokalnych środowiskach. Udostępnia wgląd w czasie rzeczywistym za pomocą wyszukiwanie zintegrowane i niestandardowych pulpitów nawigacyjnych można łatwo analizować miliony rekordów we wszystkich obciążeń i serwerów, niezależnie od ich lokalizacji fizycznej.

![Diagram analizy dzienników](./media/azure-log-audit/azure-log-audit-fig8.png)

W Centrum Log Analytics jest obszar roboczy analizy dzienników, która jest hostowana na platformie Azure. Analiza dzienników zbiera dane w obszarze roboczym połączonych źródeł Konfigurowanie źródeł danych i dodawanie rozwiązań do subskrypcji. Źródła danych i tworzenie każdego innego rozwiązania zarejestrować typy, każdy z zestawem właściwości. Ale źródeł i rozwiązań można były analizowane razem w zapytaniach do obszaru roboczego. Ta funkcja umożliwia przy użyciu tego samego narzędzia i metody do pracy z różnymi dane zbierane przez różnych źródeł danych.

Połączone źródła są komputery i inne zasoby, które generują dane zbierane przez analizy dzienników. Źródła może zawierać agentów, które są zainstalowane na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) i [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) komputerów, które łączą się bezpośrednio lub agentów w [podłączonej grupy zarządzania programu System Center Operations Manager](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Analiza dzienników może również zbierać dane z [konto magazynu Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Źródła danych](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) są różnego rodzaju zbieranych z wszystkich połączonych źródeł danych. Źródła obejmują zdarzenia i [dane dotyczące wydajności](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) i agentów systemu Linux, oprócz źródeł, takich jak [dzienniki programu IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) i [dzienniki tekstowe niestandardowych](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Każde źródło danych, które ma być zbierane, jest konfigurowane, a konfiguracja jest automatycznie dostarczana do każdego z nich.

Istnieją cztery metody [zbieranie dzienników i metryki dla usługi Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
* Diagnostyka Azure bezpośrednio do analizy dzienników (**diagnostyki** w tabeli poniżej)

* Diagnostyka Azure do magazynu Azure do analizy dzienników (**magazynu** w tabeli poniżej)

* Łączniki dla usług Azure (**łącznik** w tabeli poniżej)

* Skrypty do zbierania danych do analizy dzienników (puste komórki w poniższej tabeli oraz usług, które nie są wymienione)

| Usługa | Typ zasobu | Dzienniki | Metryki | Rozwiązanie |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostyka|Diagnostyka|    [Aplikacja Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Łącznik|  Łącznik|  [Usługa Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connector (wersja zapoznawcza)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Konta usługi Automatyzacja Azure| Microsoft.Automation/<br>AutomationAccounts|    Diagnostyka||       [Więcej informacji](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Konta usługi partia zadań Azure|  Microsoft.Batch/<br>batchAccounts|  Diagnostyka|    Diagnostyka||
|Usługi w chmurze klasycznego||       Magazyn||       [Więcej informacji](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostyka|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostyka|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostyka|||
|Azure Centrum zdarzeń przestrzeni nazw| Microsoft.EventHub/<br>przestrzenie nazw|  Diagnostyka|    Diagnostyka||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostyka||
|W usłudze Azure Key Vault|   Microsoft.KeyVault/<br>magazynów|  Diagnostyka  || [Analiza usługi Key Vault](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostyka|||
|Azure Logic Apps|  Microsoft.Logic/<br>Przepływy pracy|  Diagnostyka|    Diagnostyka||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupy zabezpieczeń sieci|   Microsoft.Network/<br>networksecuritygroups|Diagnostyka||   [Grupy zabezpieczeń sieci Azure analityka](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Magazyny odzyskiwania|   Microsoft.RecoveryServices/<br>magazynów|||[Usługa Azure Recovery usługi Analytics (wersja zapoznawcza)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Usługi wyszukiwania|   Microsoft.Search/<br>searchServices|    Diagnostyka|    Diagnostyka||
|Przestrzeń nazw magistrali usług| Microsoft.ServiceBus/<br>przestrzenie nazw|    Diagnostyka|Diagnostyka|    [Analiza magistrali usług (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Magazyn||    [Usługa sieci szkieletowej Analytics (wersja zapoznawcza)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>serwery /<br>bazy danych||       Diagnostyka||
||Microsoft.Sql/<br>serwery /<br>elasticPools||||
|Magazyn|||         Skrypt| [Usługa Azure Storage Analytics (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>maszyn wirtualnych|  Wewnętrzny|  Wewnętrzny||
||||Diagnostyka||
|Zestawy skalowania maszyn wirtualnych|    Microsoft.Compute/<br>maszyn wirtualnych    ||Diagnostyka||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>maszyn wirtualnych||||
|Farmach serwerów sieci Web|Microsoft.Web/<br>serverfarms||   Diagnostyka
|Witryny internetowe|  Microsoft.Web/<br>Lokacje ||      Diagnostyka|    [Więcej informacji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>Lokacje /<br>gniazda|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Dziennik integracji z lokalnymi systemów SIEM
Z [integracji dziennika Azure](https://www.microsoft.com/download/details.aspx?id=53324), nowych dzienników z zasobów platformy Azure można zintegrować z lokalnego systemu SIEM.

![Diagram integracji dziennika](./media/azure-log-audit/azure-log-audit-fig9.png)

Integracja dziennika zbiera diagnostycznych platformy Azure z maszyn wirtualnych systemu Windows, dzienniki aktywności platformy Azure, alerty Centrum zabezpieczeń Azure i dzienniki dostawcy zasobów platformy Azure. Integracja ta zapewnia jednolity pulpit nawigacyjny dla wszystkich zasobów, czy zostaną lokalnie lub w chmurze, dzięki czemu można zagregować skorelowania, analizowanie i alertów zdarzeń zabezpieczeń.

Integracja dziennika obecnie obsługuje integrację Dzienniki aktywności platformy Azure, dzienniki inspekcji dzienniki zdarzeń systemu Windows z maszyn wirtualnych systemu Windows z subskrypcji platformy Azure, alerty Centrum zabezpieczeń Azure, dzienników diagnostycznych platformy Azure i usługi Azure AD.

| Typ dziennika | Obsługa formatu JSON (Splunk ArcSight i IBM QRadar) analizy dzienników |
| :------- | :-------------------------------------------------------- |
|Dzienniki inspekcji usługi Azure AD|   Yes|
|Dzienniki aktywności| Yes|
|Alerty Centrum zabezpieczeń |Yes|
|Dzienniki diagnostyczne (Dzienniki zasobów)|  Yes|
|Dzienniki maszyny Wirtualnej|   Tak, za pomocą zdarzenia, a nie za pośrednictwem JSON|

[Wprowadzenie do integracji dziennika Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): w tym samouczku przeprowadzi Cię przez proces instalacji integracji dziennika Azure i integrowanie dzienniki z usługi Azure storage, dzienniki aktywności platformy Azure, alerty Centrum zabezpieczeń Azure i usługi Azure AD dzienniki inspekcji.

Scenariusze integracji SIEM:

* [Czynności konfiguracyjnych partnera](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): ten wpis w blogu przedstawiono sposób konfigurowania integracji dziennika Azure do pracy z rozwiązań partnerskich Splunk HP ArcSight i IBM QRadar.

* [Często zadawane pytania Azure dziennika integracji](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): w tym artykule odpowiedzi na pytania dotyczące integracji dziennika Azure.

* [Integrowanie alerty Centrum zabezpieczeń Azure dziennika w przypadku integracji](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): w tym artykule omówiono sposób synchronizować alerty Centrum zabezpieczeń, zbierane w dziennikach diagnostycznych platformy Azure, zdarzenia zabezpieczeń maszyny wirtualnej i dzienniki inspekcji Azure Log Analytics lub SIEM rozwiązanie.

## <a name="next-steps"></a>Kolejne kroki

- [Inspekcja i rejestrowanie](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): ochrona danych przy zachowaniu widoczności i szybko reagować na alerty zabezpieczeń w odpowiednim czasie.

- [Kolekcja rejestrowania i dziennik inspekcji zabezpieczeń w systemie Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): wprowadzić te ustawienia, aby upewnić się, że Twoje wystąpieniach platformy Azure są zbierane poprawne dzienniki zabezpieczeń i inspekcji.

- [Skonfiguruj ustawienia inspekcji dla zbioru witryn](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Jeśli jesteś administratorem zbioru witryn, pobrać historii czynności poszczególnych użytkowników oraz historię działania podjęte w zakresie określonej daty. 

- [Wyszukaj w dzienniku inspekcji Office 365 zabezpieczeń & Centrum zgodności](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Wyszukaj w dzienniku inspekcji ujednoliconego i wyświetlić aktywności użytkownika i administrator w organizacji usługi Office 365 za pomocą pakietu Office 365 zabezpieczeń i Centrum zgodności.


