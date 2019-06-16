---
title: Rejestrowanie i przeprowadzanie inspekcji platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu rejestrowania danych umożliwia uzyskiwanie szczegółowych informacji o aplikacji.
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
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: edadb369461bb3865dd6894c3329e7079fa9d13f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752557"
---
# <a name="azure-logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji na platformie Azure

System Azure oferuje szeroką gamę konfigurowalne zabezpieczeń, inspekcji i rejestrowania opcji, aby pomóc w identyfikowaniu luk w Twojej zasad i mechanizmów zabezpieczeń. W tym artykule omówiono generowania, zbierania i analizowania dzienników zabezpieczeń z usług hostowanych na platformie Azure.

> [!Note]
> Zastosowanie niektórych zaleceń zamieszczonych w tym artykule może spowodować danych, sieci lub użycia zasobów obliczeniowych i zwiększyć koszty licencji lub subskrypcji.

## <a name="types-of-logs-in-azure"></a>Typy dzienników na platformie Azure

Aplikacje w chmurze są skomplikowane, za pomocą wielu ruchomych elementów. Dzienniki zawierają dane, aby zapewnić działanie aplikacji. Dzienniki pomagają rozwiązywać problemy ostatnie problemy lub uniemożliwić potencjał z nich. I mogą one pomóc zwiększyć wydajność aplikacji lub łatwość konserwacji lub Automatyzuj akcje, które w przeciwnym razie wymagają ręcznej interwencji.

Dzienniki platformy Azure są podzielone na następujące typy:
* **Dzienniki kontroli/zarządzania** zawierają informacje o operacjach usługi Azure Resource Manager utworzenie, UPDATE i DELETE. Aby uzyskać więcej informacji, zobacz [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Dzienniki na płaszczyźnie danych** zawierają informacje dotyczące zdarzenia wygenerowane jako część użycia zasobów platformy Azure. Przykłady tego typu dziennika są system zdarzeń Windows, bezpieczeństwa i dzienniki aplikacji na maszynie wirtualnej (VM) i [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) skonfigurowanych za pomocą usługi Azure Monitor.

* **Przetworzone zdarzenia** Podaj informacje o przeanalizowany zdarzenia/alerty, które zostały przetworzone w Twoim imieniu. Przykłady tego typu są [alerty Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) gdzie [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ma przetworzone i przeanalizowane subskrypcji i zapewnia alerty zabezpieczeń zwięzły.

W poniższej tabeli wymieniono najważniejsze typów dzienników dostępnych na platformie Azure:

| Kategoria dziennika | Typ dziennika | Sposób użycia | Integracja |
| ------------ | -------- | ------ | ----------- |
|[Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Zdarzenia płaszczyznę kontroli dla zasobów usługi Azure Resource Manager|   Zapewnia wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji.|    REST API, [usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Często dane dotyczące działania usługi Azure Resource Manager zasobów w subskrypcji|    Zawiera szczegółowe dane operacji wykonanych przez sam zasób.| Usługa Azure Monitor [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Raportowanie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Dzienniki i raporty | Raporty aktywności logowania użytkowników i informacji o aktywności systemu dotyczące użytkowników i grupy zarządzania.|[Interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Maszyny wirtualne i usługi w chmurze](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Usługa Windows dziennik zdarzeń i dzienników Syslog systemu Linux|  Przechwytuje dane systemu i rejestrowania danych na maszynach wirtualnych i przesyła dane do wybranego konta magazynu.|   Windows (przy użyciu Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] magazynu) i Linux w usłudze Azure Monitor|
|[Analityka usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Rejestrowanie magazynu dostarcza danych metryk dotyczących konta magazynu|Zapewnia wgląd w Śledzenie żądań, analizuje trendy użycia i diagnozowanie problemów z kontem usługi storage.|   Interfejs API REST lub [biblioteki klienckiej](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Dzienniki przepływu sieciowych grup zabezpieczeń (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON format zawiera przepływy wychodzące i przychodzące na podstawie reguł|Wyświetla informacje o przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Dzienniki, wyjątki i Diagnostyka niestandardowa|   Udostępnia usługi (APM) dla deweloperów sieci web na wielu platformach monitorowania wydajności aplikacji.| Interfejs API REST, [usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Przetwarzanie danych / alerty zabezpieczeń|    Alerty usługi Azure Security Center, alertów dzienników usługi Azure Monitor|    Zawiera informacje dotyczące zabezpieczeń i alerty.|  Interfejsy API REST, JSON|

### <a name="activity-logs"></a>Dzienniki aktywności

[Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji. Dzienniki aktywności były wcześniej nazywane "dzienniki inspekcji" lub "operacyjne dzienniki", ponieważ zgłaszają [zdarzenia płaszczyznę kontroli](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) dla subskrypcji. 

Dzienniki aktywności pomocy, możesz określić ", co, kto i kiedy" za operacje zapisu (oznacza to, PUT, POST lub usunąć). Dzienniki aktywności również pomagają zrozumieć stanu operacji i inne odpowiednie właściwości. Dzienniki aktywności nie obejmują operacji odczytu (GET).

W tym artykule PUT, POST i DELETE dotyczą wszystkich operacji zapisu, które dziennik aktywności zawiera na zasobach. Na przykład dzienników aktywności można użyć do znalezienia błędu przy rozwiązywaniu problemów lub do monitorowania, jak użytkownik w organizacji zasób został zmodyfikowany.

![Diagram dziennika aktywności](./media/azure-log-audit/azure-log-audit-fig1.png)

Możesz pobrać zdarzenia z dziennika aktywności przy użyciu witryny Azure portal [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), poleceń cmdlet programu PowerShell, a [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Dzienniki aktywności mają okres przechowywania danych 90-dniowego.

Scenariusze integracji dla zdarzenia dziennika aktywności:

* [Utwórz alert e-mail lub elementu webhook, wyzwalanego przez zdarzenia dziennika aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Stream go do Centrum zdarzeń](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.

* Analizowanie w usłudze Power BI, za pomocą [pakietu zawartości usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Zapisz go na konto magazynu przeznaczone do archiwizacji lub ręcznej kontroli](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Czas przechowywania (w dniach) można określić przy użyciu profilów dziennika.

* Zapytania i wyświetlić je w witrynie Azure portal.

* Wykonuje zapytania za pomocą polecenia cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

* Eksportuj Dziennik aktywności przy użyciu profilów dziennika do [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Możesz użyć konta magazynu lub [przestrzeń nazw Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) nie jest w tej samej subskrypcji, która jest emitowania dziennika. Spojrzenia Konfiguruje ustawienie, musi mieć odpowiednie [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dostęp do obu subskrypcji.

### <a name="azure-diagnostics-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure są emitowane przez zasób, który zapewnia bogate, często dane o działaniu tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu. Na przykład [dzienniki systemu zdarzeń Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) są kategorii dzienniki diagnostyczne dla maszyn wirtualnych i [obiektów blob, tabel i Dzienniki kolejek](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategorie dzienniki diagnostyczne dla kont magazynu. Dzienniki diagnostyczne różnią się od dzienników aktywności, które zapewniają wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji.

![Diagramy dzienniki diagnostyczne platformy Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Dzienniki diagnostyczne platformy Azure oferuje wiele opcji konfiguracji, takich jak witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.

**Kompleksowe scenariusze integracji**

* Zapisywanie ich [konta magazynu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) do wglądu, inspekcji czy ręcznie. Czas przechowywania (w dniach) można określić za pomocą ustawień diagnostycznych.

* [Stream je do usługi event hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) dla pozyskiwania przez usługi innych firm lub rozwiązanie do analizy niestandardowe, takie jak [usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analizuj je za pomocą [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Obsługiwane usługi, schematu dla dzienników diagnostyki i Rejestruj obsługiwane kategorie według typu zasobów**


| Usługa | Schemat i dokumentacji | Typ zasobu | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Dzienniki platformy Azure Monitor dla modułu równoważenia obciążenia (wersja zapoznawcza)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Grupy zabezpieczeń sieci|[Dzienniki monitora platformy Azure dla sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Rejestrowanie diagnostyczne bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|W usłudze Azure Key Vault|[Dzienników usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Włączanie i korzystanie z analizy ruchu wyszukiwania](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Dostęp do dzienników diagnostycznych usługi Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Inspekcja<br>Żądania|
|Azure Data Lake Analytics|[Uzyskiwanie dostępu do dzienników diagnostycznych usługi Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Inspekcja<br>Żądania|
|Azure Logic Apps|[Logic Apps — niestandardowy schemat śledzenia B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Dzienniki diagnostyczne usługi Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Dzienniki monitora platformy Azure dla usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Dzienniki diagnostyczne usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Usługa Azure Stream Analytics|[Dzienniki diagnostyczne zadania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Wykonanie<br>Tworzenie|
|Azure Service Bus|[Dzienniki diagnostyczne usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Raporty w usłudze Azure Active Directory

Azure Active Directory (Azure AD) obejmuje zabezpieczeń, działań i raporty inspekcji dla katalogu użytkownika. [Raportu inspekcji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomaga w identyfikacji uprzywilejowanych akcji, które wystąpiły w wystąpieniu usługi Azure AD przez użytkownika. Uprzywilejowanych akcji obejmują zmiany podniesienie poziomu (np. Tworzenie roli lub resetowanie haseł), zmiany konfiguracji zasad (na przykład zasady haseł) lub zmiany w konfiguracji katalogu (na przykład zmiany ustawienia Federacji domeny).

Raporty zapewniają rekord inspekcji dla nazwy zdarzeń użytkownika, który wykonał operację, zasób docelowy wpływ zmiany oraz Data i godzina (czas UTC). Użytkownicy mogą pobrać listę zdarzeń inspekcji dla usługi Azure AD za pomocą [witryny Azure portal](https://portal.azure.com/), zgodnie z opisem w [przeglądać dzienniki inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Raporty uwzględnione są wymienione w poniższej tabeli:

| Raporty dotyczące zabezpieczeń | Raporty dotyczące działań | Raporty dotyczące inspekcji |
| :--------------- | :--------------- | :------------ |
|Logowania z nieznanych źródeł| Podsumowanie użycia aplikacji| Raport dotyczący inspekcji katalogu|
|Logowania po wielokrotnych niepowodzeniach|  Szczegóły użycia aplikacji||
|Logowania z wielu lokalizacji geograficznych|    Pulpit nawigacyjny aplikacji||
|Logowania z adresów IP związanych z podejrzanymi działaniami|   Błędy aprowizacji kont||
|Nieregularne działania związane z logowaniem|    Urządzenia indywidualnych użytkowników||
|Logowania z urządzeń, które mogą być zainfekowane|   Działania użytkownika||
|Nietypowe działania użytkowników związane z logowaniem| Raport dotyczący działań grup||
||Raport aktywności rejestracji resetowania hasła||
||Działania związane z resetowaniem haseł||

Dane w tych raportach mogą być przydatne w aplikacjach, takich jak systemów Security Information and Event Management (SIEM), inspekcji i narzędzia do analizy biznesowej. Interfejsy API raportów usługi Azure AD umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Można je wywoływać [interfejsów API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) z różnych języków programowania i narzędzi.

Zdarzenia raportu inspekcji usługi Azure AD są przechowywane przez 180 dni.

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania raportów, zobacz [zasady przechowywania raportów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Jeśli interesuje Cię zachowaniu dłużej zdarzeń inspekcji, użyj interfejsu API raportowania regularnie ściągnąć [zdarzenia inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) w oddzielnym magazynem danych.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Dzienników maszyny wirtualnej, które używają usługi Azure Diagnostics

[Diagnostyka Azure](https://docs.microsoft.com/azure/azure-diagnostics) funkcji w obrębie platformy Azure, która umożliwia zbieranie danych diagnostycznych na rozmieszczonej aplikacji. Rozszerzenie diagnostyki można użyć z dowolnego z wielu źródeł. Obecnie obsługiwane wartości to [role sieć web i procesu roboczego usługi Azure cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Dzienników maszyny wirtualnej, które używają usługi Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Usługa Azure virtual machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/) z systemem Microsoft Windows i [usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Należy włączyć diagnostyki platformy Azure na maszynie wirtualnej, wykonując dowolną z następujących czynności:

* [Śledzenie maszyn wirtualnych platformy Azure przy użyciu programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Konfigurowanie diagnostyki platformy Azure, zdalnie na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Konfigurowanie diagnostyki na maszynach wirtualnych platformy Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Utwórz maszynę wirtualną Windows z funkcjami monitorowania i diagnostyki przy użyciu szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Analityka magazynu

[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) dzienniki i dostarcza danych metryk dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Rejestrowanie danych analizy magazynu jest dostępny dla [usługi magazynu Azure Blob, kolejek platformy Azure i usługi Azure Table](https://docs.microsoft.com/azure/storage/storage-introduction). Usługa Storage Analytics rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi storage.

Te informacje służą do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane na zasadzie największej staranności. Wpisy dziennika są tworzone tylko wtedy, gdy żądania skierowanego do punktu końcowego usługi. Na przykład jeśli konto magazynu ma działania, w jej punkt końcowy usługi blob, ale nie w jego tabel lub kolejek punkty końcowe, są tworzone tylko dzienniki, które odnoszą się do usługi Blob storage.

Aby korzystać z usługi Storage Analytics, aktywować go oddzielnie dla każdej usługi, które chcesz monitorować. Możesz ją włączyć w [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [monitorowania na koncie magazynu w witrynie Azure portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Umożliwiają operacji ustawiania właściwości usługi Storage Analytics osobno dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanych obiektów blob (w przypadku rejestrowania) i dobrze znanych tabelach (w przypadku metryk), które są dostępne przy użyciu usługi Blob storage i usługa Table storage interfejsów API.

Usługa Storage Analytics obowiązuje limit 20 terabajtów (TB) na ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Wszystkie dzienniki są przechowywane w [blokowe obiekty BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) w kontenerze o nazwie $logs, które zostało automatycznie utworzone po włączeniu analityka magazynu dla konta magazynu.

> [!Note]
> * Aby uzyskać więcej informacji na temat rozliczeń i zasadami przechowywania danych, zobacz [analizy magazynu i rozliczeń oraz](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Usługa Storage Analytics rejestruje następujące typy żądań uwierzytelnionych i anonimowy:

| Uwierzytelniony  | Anonimowe|
| :------------- | :-------------|
| Żądania zakończone powodzeniem | Żądania zakończone powodzeniem |
|Żądania zakończone niepowodzeniem oraz tym limit czasu, ograniczanie przepustowości, sieci, autoryzacji i inne błędy | Żądania przy użyciu sygnatury dostępu współdzielonego, w tym realizowania żądań nie powiodło się, jak i pomyślnie |
| Żądania przy użyciu sygnatury dostępu współdzielonego, w tym realizowania żądań nie powiodło się, jak i pomyślnie |Błędy przekroczenia limitu czasu dla klienta i serwera |
|   Żądania do analizy danych |    Żądania GET zakończone niepowodzeniem z kodem błędu 304 (nie zmodyfikowano) |
| Żądania wysyłane przez analityka magazynu, takie jak tworzenie dziennika lub usuwanie, nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [Storage Analytics rejestrowane komunikaty o stanie i operacje](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [format dziennika Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Inne zakończone niepowodzeniem żądania anonimowe nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [Storage Analytics rejestrowane komunikaty o stanie i operacje](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [format dziennika Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Dzienników sieci platformy Azure

Sieci, rejestrowanie i monitorowanie na platformie Azure to kompleksowe i obejmuje dwie szerokie kategorie:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitorowanie sieci opartych na scenariuszach jest dostarczana z funkcji usługi Network Watcher. Ta usługa obejmuje przechwytywanie pakietów, następny przeskok, przepływu dla adresu IP Sprawdź widok grup zabezpieczeń, dzienników przepływu sieciowych grup zabezpieczeń. Scenariusz poziomu monitorowania udostępnia widok typu end to end zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

* [Monitorowanie zasobów](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitorowanie na poziomie zasobów składa się z czterech funkcji, dzienniki diagnostyczne, metryki, rozwiązywanie problemów i kondycji zasobów. Wszystkie te funkcje są tworzone na poziomie zasobów sieciowych.

![Dzienników sieci platformy Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher to regionalna usługa, która pozwala na monitorowanie i diagnozowanie warunków na poziomie sieci, do i z platformy Azure. Diagnostyka sieci i narzędzi do wizualizacji dostępne w usłudze Network Watcher pomagają zrozumieć, diagnozowanie i uzyskiwanie szczegółowych informacji do sieci na platformie Azure.

### <a name="network-security-group-flow-logging"></a>Rejestrowanie przepływu sieciowej grupy zabezpieczeń

[Dzienniki przepływu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) są funkcją usługi Network Watcher można użyć, aby wyświetlić informacje o przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie JSON i wyświetlić:
* Wychodzące i przychodzące przepływy na podstawie reguł.
* Karta sieciowa, która dotyczy przepływ.
* 5-elementowych informacji o przepływie: źródłowy lub docelowy adres IP, port źródłowy lub docelowy i protokołu.
* Czy ruch zezwolenie lub odmowa dostępu.

Mimo że dzienników przepływów sieciowych grup zabezpieczeń docelowy, mogą nie są wyświetlane w taki sam sposób jak inne dzienniki. Przepływ dzienniki są przechowywane tylko w ramach konta magazynu.

Te same zasady przechowywania, do których są widoczne na inne dzienniki dotyczą dzienników przepływów. Dzienniki mają zasady przechowywania, które można ustawić od 1 dnia do 365 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

**Dzienniki diagnostyczne**

Zdarzenia okresowe i spontanicznej są utworzone przez zasoby sieciowe i rejestrowane w ramach kont magazynu i wysyłane do Centrum zdarzeń i dzienników usługi Azure Monitor. Dzienniki zawierają szczegółowe informacje na temat kondycji zasobu. Wyświetlanie ich w narzędziach takich jak dzienniki usługi Power BI i usługi Azure Monitor. Aby dowiedzieć się, jak wyświetlić dzienniki diagnostyczne, zobacz [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Dzienniki diagnostyczne](./media/azure-log-audit/azure-log-audit-fig5.png)

Dzienniki diagnostyczne są dostępne dla [modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), tras i [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Wyświetl dzienniki diagnostyczne udostępnia usługi Network Watcher. Ten widok zawiera wszystkie zasoby sieciowe, które obsługują rejestrowania diagnostyki. W tym widoku można włączać i wyłączać zasobami sieciowymi w przypadku, szybko i wygodnie.


Oprócz możliwości rejestrowania wymienionych wcześniej usługi Network Watcher obecnie ma następujące możliwości:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Zapewnia widok poziomie sieci, pokazujący różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.

- [Przechwytywanie pakietów zmiennych](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Umożliwia przechwytywanie danych pakietów do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i dostosowawczych formanty, takie jak ustawienia limitu czasu i rozmiaru zapewniają uniwersalność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w *.cap* format pliku.

- [Weryfikacja przepływu adresów IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Sprawdza, czy pakiet jest dozwolony lub zablokowany na podstawie przepływ informacji 5-elementowe spójne kolekcje pakietów parametrów (oznacza to, że docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli pakiet zostanie odrzucona przez grupę zabezpieczeń, zwracany jest reguła i grupy, która odrzuciła pakiet.

- [Dla następnego przeskoku](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Określa następny przeskok dla pakiety przesyłane w sieci szkieletowej sieci platformy Azure, dzięki czemu możesz zdiagnozować wszelkie nieprawidłowo trasy zdefiniowane przez użytkownika.

- [Widok grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Pobiera reguły zabezpieczeń efektywny i zastosowane, które są stosowane na maszynie Wirtualnej.

- [Brama sieci wirtualnej i rozwiązywanie problemów z połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Ułatwia rozwiązywanie problemów z bramy sieci wirtualnej i połączenia.

- [Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Umożliwia wyświetlenie użycia zasobów sieciowych limitów.

### <a name="application-insights"></a>Application Insights

[Usługa Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) jest rozszerzalną usługę APM dla deweloperów sieci web na wielu platformach. Umożliwia monitorowanie aplikacji sieci web. Funkcja automatycznie wykrywa anomalie wydajność. Obejmuje ona zaawansowane narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć sposób korzystania z aplikacji przez użytkowników.

Usługa Application Insights jest przeznaczony do pomagają w ciągłym udoskonalaniu wydajności i użyteczności.

Działa ona aplikacje na różnych platformach, w tym .NET, Node.js i Java EE, czy są one hostowane w środowisku lokalnym lub w chmurze. Ona integruje się z procesem DevOps i ma punkty połączenia z różnymi narzędziami programistycznymi.

![Application Insights diagramu](./media/azure-log-audit/azure-log-audit-fig6.png)

Usługa Application Insights jest przeznaczona dla zespołu deweloperów po to, aby pomóc zrozumieć, jak działa Twoja aplikacja i jak jest używana. Monitoruje ona:

* **Żądanie kursów, czasy reakcji i współczynniki błędów**: Dowiedz się, które strony są najbardziej popularne, o jakich porach dnia, i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy reakcji i współczynniki błędów przechodzenia do wysokiej jest więcej żądań, może być problem z zasobami.

* **Współczynniki zależności, czasy reakcji i współczynniki błędów**: Dowiedz się, czy usługi zewnętrzne nie spowalniają.

* **Wyjątki**: Analizowanie zagregowanych danych statystycznych lub wybieranie określonych wystąpień i przechodzenie do szczegółów śladu stosu i powiązanych żądań. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

* **Wydajność ładowania i wyświetleń stron**: Pobierz raporty z przeglądarek użytkowników.

* **Wywołania AJAX**: Uzyskaj stawki strony sieci Web, czasy reakcji i współczynniki błędów.

* **Liczby użytkowników i sesji**.

* **Liczniki wydajności**: Pobieranie danych z maszyn serwera Windows lub Linux, takie jak procesor CPU, pamięci i wykorzystania sieci.

* **Diagnostyka hosta**: Pobieranie danych z platformy Docker lub na platformie Azure.

* **Diagnostyczne dzienniki śledzenia**: Pobieranie danych z aplikacji, dzięki czemu można skorelować zdarzenia śledzenia z żądaniami.

* **Niestandardowe zdarzenia i metryki**: Pobierz dane, które samodzielnie zapisujesz w kodzie klienta lub serwera, do śledzenia zdarzeń biznesowych, takich jak sprzedane towary lub Wygrane gry.

Poniższej tabeli wymieniono i opisano scenariusze integracji:

| Scenariusz integracji | Opis |
| --------------------- | :---------- |
|[Mapa aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Składniki Twojej aplikacji wraz z kluczowymi metrykami i alertami.|
|[Na przykład wyszukiwanie diagnostyki danych](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Wyszukiwanie i filtrowanie zdarzeń, takich jak żądania, wyjątki, wywołania zależności, dzienniki śledzenia i wyświetlenia stron.|
|[Eksplorator metryk dla danych zagregowanych](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Eksploruj, filtruj i segmentuj zagregowane dane, takie jak liczby żądań, błędów i wyjątków, czasy reakcji, czasy ładowania stron.|
|[Pulpity nawigacyjne](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)|Połącz dane z wielu zasobów i udostępnij innym osobom. Opcja ta doskonale nadaje się dla aplikacji wieloskładnikowych i ciągłego wyświetlania w pomieszczeniu zespołu.|
|[Stream metryki na żywo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Podczas wdrażania nowej kompilacji obejrzyj te wskaźniki wydajności prawie w czasie rzeczywistym, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.|
|[Analiza](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Odpowiedz na trudne pytania dotyczące wydajności i użycia Twojej aplikacji za pomocą tego zaawansowanego języka zapytań.|
|[Alerty automatyczne i ręczne](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Alerty automatyczne dostosowanie się do normalnych wzorców telemetrii Twojej aplikacji i są wyzwalane, gdy wystąpi coś poza zwykłym wzorcem. Możesz też ustawić alerty dla konkretnych poziomów metryk niestandardowych lub standardowych.|
|[Program Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Wyświetl dane dotyczące wydajności w kodzie. Przejdź do kodu ze śladów stosu.|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integruje metryki użycia z innymi analizami biznesowymi.|
|[Interfejs API REST](https://dev.applicationinsights.io/)|Napisz kod, aby uruchamiać zapytania dla swoich metryk i danych pierwotnych.|
|[Eksport ciągły](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Zbiorczy Eksport danych pierwotnych do magazynu po odebraniu.|

### <a name="azure-security-center-alerts"></a>Alerty usługi Azure Security Center

Wykrywanie zagrożeń w usłudze Azure Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach z zasobami platformy Azure, sieci oraz połączonych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo. Aby uzyskać więcej informacji, zobacz [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagram usługi Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Ma to zastosowanie przełomów w dużych ilości danych i [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologii do oceny zdarzeń w całej sieci szkieletowej chmury. W ten sposób wykrywa zagrożenia, które byłyby niemożliwe do wykrycia przy użyciu ręcznych metod i prognozowanie ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

* **Zintegrowana analiza zagrożeń**: Sprawdza, czy strony znanych złośliwych podmiotów, stosując globalnych zagrożeń z produktów firmy Microsoft i usługi, jednostka przestępstw cyfrowych (DCU) firmy Microsoft, Microsoft Security Response Center (MSRC) i zewnętrznych źródeł danych.

* **Analiza behawioralna**: Stosuje znane wzorce w celu wykrycia złośliwego zachowania.

* **Wykrywanie anomalii**: Korzysta z programu profilowania statystycznego w celu skompilowania historycznych linii bazowych. Narzędzie to alarmuje o odchyleniach od ustalonych linii bazowych zgodnych z wektorem potencjalnego ataku.

Wiele operacji zabezpieczeń i reagowania na zdarzenia zespołów korzystają rozwiązania SIEM jako punktu wyjścia dla klasyfikowania i badanie alertów zabezpieczeń. Za pomocą integracji dzienników platformy Azure można zsynchronizować alertów usługi Security Center i zdarzenia zabezpieczeń dotyczące maszyny wirtualnej, zbieranych przez usługi Azure diagnostics i dzienniki inspekcji, dzienniki usługi Azure Monitor lub rozwiązania SIEM w czasie zbliżonym do rzeczywistego.

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Dzienniki platformy Azure Monitor to usługa platformy Azure, która ułatwia zbieranie oraz analizowanie danych, który został wygenerowany przez zasoby w środowisku chmurowym i lokalnym. Zapewnia wgląd w czasie rzeczywistym przy użyciu zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym szybkie analizowanie milionów rekordów z wszystkich obciążeń i serwerów, niezależnie od ich fizycznej lokalizacji.

![Usługa Azure Monitor rejestruje diagramu](./media/azure-log-audit/azure-log-audit-fig8.png)

Dzienniki Centrum usługi Azure Monitor jest obszar roboczy usługi Log Analytics, która jest hostowana na platformie Azure. Dzienniki platformy Azure Monitor umożliwia zbieranie informacji o danych w obszarze roboczym z połączonych źródeł przez konfigurowanie źródeł danych i dodawanie rozwiązań do subskrypcji. Źródeł danych i rozwiązań tworzone różnych typach rekordów, każdy z swój własny zestaw właściwości. Ale źródeł i rozwiązań mogą być analizowane razem w zapytaniach do obszaru roboczego. Ta funkcja pozwala używać tych samych narzędzi i metod do pracy z różnymi rodzajami danych zbieranych przez różnych źródeł.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Połączone źródła to komputery i inne zasoby, które generują dane, które są zbierane przez dzienniki usługi Azure Monitor. Źródła może zawierać agentów, które są zainstalowane na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) i [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) komputerów, które łączą się bezpośrednio lub agentów w [podłączonej grupy zarządzania programu System Center Operations Manager](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Dzienniki platformy Azure Monitor może również zbierać dane z [konta usługi Azure storage](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Źródła danych](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) różne rodzaje danych zbieranych ze wszystkich połączonych źródeł. Źródła obejmują zdarzenia i [dane dotyczące wydajności](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) i agenci dla systemu Linux, oprócz źródeł, takich jak [dzienniki usług IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) i [niestandardowe dzienniki tekstowe](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Każde źródło danych, które ma być zbierane, jest konfigurowane, a konfiguracja jest automatycznie dostarczana do każdego z nich.

Istnieją cztery sposoby [gromadzenia dzienników i metryk dla usług platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Narzędzie diagnostyczne systemu Azure bezpośrednio do usługi Azure Monitor dzienniki (**diagnostyki** w poniższej tabeli)

* Dzienniki diagnostyczne platformy Azure do usługi Azure storage do usługi Azure Monitor (**magazynu** w poniższej tabeli)

* Łączniki dla usług platformy Azure (**łącznika** w poniższej tabeli)

* Skrypty można zbierać, a następnie publikują dane w usłudze Azure Monitor dzienniki (puste komórki, w poniższej tabeli, jak i dla usług, które nie są wyświetlane)

| Usługa | Typ zasobu | Dzienniki | Metryki | Rozwiązanie |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostyka|Diagnostyka|    [Aplikacja Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics) [analizy bramy](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Łącznik|  Łącznik|  [Usługa Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [łącznik (wersja zapoznawcza)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Konta usługi Azure Automation| Microsoft.Automation/<br>AutomationAccounts|    Diagnostyka||       [Więcej informacji](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Konta usługi Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostyka|    Diagnostyka||
|Usługi w chmurze klasycznego||       Magazyn||       [Więcej informacji](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostyka|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostyka|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostyka|||
|Przestrzeń nazw Centrum zdarzeń platformy Azure| Microsoft.EventHub/<br>Przestrzenie nazw|  Diagnostyka|    Diagnostyka||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostyka||
|W usłudze Azure Key Vault|   Microsoft.KeyVault/<br>Magazyny|  Diagnostyka  || [Analiza usługi Key Vault](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostyka|||
|Azure Logic Apps|  Microsoft.Logic/<br>Przepływy pracy|  Diagnostyka|    Diagnostyka||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupy zabezpieczeń sieci|   Microsoft.Network/<br>networksecuritygroups|Diagnostyka||   [Analiza sieciowej grupy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-and-network-security-group-analytics)|
|Magazyny usługi Recovery|   Microsoft.RecoveryServices/<br>Magazyny|||[Usługa Azure Recovery Services — analiza (Podgląd)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Usługi wyszukiwania|   Microsoft.Search/<br>searchServices|    Diagnostyka|    Diagnostyka||
|Przestrzeń nazw magistrali usług| Microsoft.ServiceBus/<br>Przestrzenie nazw|    Diagnostyka|Diagnostyka|    [Usługa Service Bus analiza (Podgląd)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Magazyn||    [Analiza usługi Service Fabric (wersja zapoznawcza)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>serwery /<br>bazy danych||       Diagnostyka||
||Microsoft.Sql/<br>serwery /<br>elasticPools||||
|Magazyn|||         Skrypt| [Usługa Azure Storage Analytics (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Usługa Azure Virtual Machines|    Microsoft.Compute/<br>maszyn wirtualnych|  Wewnętrzny|  Wewnętrzny||
||||Diagnostyka||
|Zestawy skalowania maszyn wirtualnych|    Microsoft.Compute/<br>maszyn wirtualnych    ||Diagnostyka||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>maszyn wirtualnych||||
|Farmach serwerów sieci Web|Microsoft.Web/<br>farm serwerów||   Diagnostyka
|Witryny internetowe|  Microsoft.Web/<br>Lokacje ||      Diagnostyka|    [Więcej informacji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>Lokacje /<br>gniazda||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integracja dzienników przy użyciu lokalnych systemów SIEM

Za pomocą usługi Azure Log Integration nieprzetworzonych dzienników z zasobów platformy Azure można zintegrować z lokalnego systemu SIEM (Security informacjami i zdarzeniami systemu zarządzania). Pliki do pobrania AzLog zostały wyłączone w dniu 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu wpis [użycia w usłudze Azure monitor do integracji z narzędziami rozwiązania SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Diagram integracji dzienników](./media/azure-log-audit/azure-log-audit-fig9.png)

Integracja dzienników zbiera diagnostyki platformy Azure z maszyn wirtualnych Windows, dzienniki aktywności platformy Azure, alertów usługi Azure Security Center i dzienniki dostawcy zasobów platformy Azure. Integracja ta zapewnia jednolity pulpit nawigacyjny dla wszystkich zasobów, czy są one w środowisku lokalnym lub w chmurze, dzięki czemu można zagregować korelowanie, analizowanie i alertów dla zdarzeń dotyczących zabezpieczeń.

Integracja dzienników obecnie obsługuje integrację Dzienniki aktywności platformy Azure, dzienniki inspekcji Windows dzienniki zdarzeń z maszyn wirtualnych Windows przy użyciu subskrypcji platformy Azure, alertów usługi Azure Security Center, dzienniki diagnostyczne platformy Azure i usługi Azure AD.

| Typ dziennika | Usługa Azure Monitor rejestruje pomocnicze JSON (Splunk ArcSight i IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Dzienniki inspekcji usługi Azure AD|   Tak|
|Dzienniki aktywności| Yes|
|Alertów usługi Security Center |Tak|
|Dzienniki diagnostyczne (Dzienniki zasobów)|  Yes|
|Dzienniki maszyny Wirtualnej|   Tak, za pomocą zdarzenia przesyłane dalej, a nie za pomocą pliku JSON|

[Rozpoczynanie pracy z usługą Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Ten samouczek przeprowadzi Cię przez zainstalowanie Azure Log Integration i integrowanie dzienników z usługi Azure storage, dzienniki aktywności platformy Azure, alertów usługi Azure Security Center i Azure AD dzienników inspekcji.

Scenariusze integracji rozwiązania SIEM:

* [Partner czynności konfiguracyjnych](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ten wpis w blogu dowiesz się, jak skonfigurować program Azure Log Integration do pracy z rozwiązaniami partnerów Splunk, HP ArcSight i QRadar firmy IBM.

* [Integracja dzienników platformy Azure — często zadawane pytania](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Ten artykuł zawiera odpowiedzi na pytania dotyczące usługi Azure Log Integration.

* [Integrowanie alertów usługi Security Center z usługą Azure Log Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): W tym artykule omówiono sposób synchronizacji alertów usługi Security Center, maszyna wirtualna zabezpieczeń zebranych zdarzeń wg dzienniki diagnostyczne platformy Azure i dzienników inspekcji platformy Azure z dzienników usługi Azure Monitor lub rozwiązania SIEM.

## <a name="next-steps"></a>Kolejne kroki

- [Inspekcja i rejestrowanie](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Ochrona danych przy zachowaniu widoczności i szybko reagować na alerty zabezpieczeń w odpowiednim czasie.

- [Zbierania rejestrowanie i dziennik inspekcji zabezpieczeń w systemie Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Wymuszanie te ustawienia, aby upewnić się, że wystąpień platformy Azure są zbierane dane poprawne dzienniki zabezpieczeń i inspekcji.

- [Skonfiguruj ustawienia inspekcji dla zbioru witryn](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Jeśli jesteś administratorem zbioru witryn, należy pobrać historii akcji dla poszczególnych użytkowników oraz historię akcjach podjętych podczas zakresu określonej daty. 

- [Wyszukiwanie w dzienniku inspekcji w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Użyj Centrum zgodności i zabezpieczeń usługi Office 365 do przeszukania dziennika inspekcji ujednolicone i Wyświetl działania użytkowników i administratorów w Twojej organizacji usługi Office 365.


