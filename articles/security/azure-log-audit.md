---
title: Rejestrowanie i inspekcja na platformie Azure | Microsoft Docs
description: Dowiedz się, jak korzystać z danych rejestrowania w celu uzyskania szczegółowych informacji o aplikacji.
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
ms.openlocfilehash: 6634b674037b48ff9ef9690144ef5274f31a6ea8
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227961"
---
# <a name="azure-logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji na platformie Azure

System Azure oferuje szeroką gamę konfigurowalnych opcji inspekcji zabezpieczeń i rejestrowania, które ułatwiają identyfikację luk w zabezpieczeniach i mechanizmach zabezpieczeń. W tym artykule omówiono generowanie, zbieranie i analizowanie dzienników zabezpieczeń z usług hostowanych na platformie Azure.

> [!Note]
> Niektóre zalecenia przedstawione w tym artykule mogą spowodować zwiększenie użycia zasobów danych, sieci lub obliczeń oraz zwiększyć koszty licencji lub subskrypcji.

## <a name="types-of-logs-in-azure"></a>Typy dzienników na platformie Azure

Aplikacje w chmurze są złożone, a wiele ruchomych części. Dzienniki zapewniają dane, aby pomóc w zachowaniu i uruchomieniu aplikacji. Dzienniki pomagają w rozwiązywaniu problemów z poprzednimi problemami lub zapobieganiu potencjalnym. Mogą one pomóc w zwiększeniu wydajności aplikacji lub utrzymaniu lub zautomatyzowaniu działań, które w przeciwnym razie wymagają ręcznej interwencji.

Dzienniki platformy Azure są podzielone na następujące typy:
* **Dzienniki kontroli i zarządzania** zawierają informacje dotyczące Azure Resource Manager operacji tworzenia, aktualizowania i usuwania. Aby uzyskać więcej informacji, zobacz [dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Dzienniki płaszczyzny danych** zawierają informacje o zdarzeniach wywoływanych jako część użycia zasobów platformy Azure. Przykładami tego typu dziennika są dzienniki systemu zdarzeń systemu Windows, zabezpieczeń i aplikacji na maszynie wirtualnej (VM) oraz [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) skonfigurowane za pomocą Azure monitor.

* **Przetwarzane zdarzenia** zawierają informacje o przeanalizowanych zdarzeniach/alertach, które zostały przetworzone w Twoim imieniu. Przykłady tego typu są [Azure Security Center alertów](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) , w których [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) przetworzył i przeanalizować subskrypcję i oferuje zwięzłe alerty zabezpieczeń.

Poniższa tabela zawiera listę najważniejszych typów dzienników dostępnych na platformie Azure:

| Kategoria dziennika | Typ dziennika | Użycie | Integracja |
| ------------ | -------- | ------ | ----------- |
|[Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Zdarzenia płaszczyzny kontroli dla zasobów Azure Resource Manager|   Zapewnia wgląd w operacje wykonywane względem zasobów w ramach subskrypcji.|    Interfejs API REST, [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Częste dane dotyczące operacji Azure Resource Manager zasobów w subskrypcji|    Zapewnia wgląd w operacje wykonywane przez sam zasób.| Azure Monitor, [strumień](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Raportowanie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Dzienniki i raporty | Zgłasza działania związane z logowaniem użytkowników i informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami.|[Interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Maszyny wirtualne i usługi w chmurze](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Usługa Dziennik zdarzeń systemu Windows i dziennik systemowy Linux|  Przechwytuje dane systemowe i rejestruje dane na maszynach wirtualnych i przesyła je do wybranego konta magazynu.|   Windows (z systemem Windows Diagnostyka Azure [[funkcji wad](https://docs.microsoft.com/azure/azure-diagnostics)] Storage) i Linux w systemie Azure monitor|
|[analityka magazynu platformy Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Rejestrowanie magazynu udostępnia dane metryk dla konta magazynu|Zapewnia wgląd w żądania śledzenia, analizuje trendy użycia i diagnozuje problemy związane z kontem magazynu.|   Interfejs API REST lub [Biblioteka kliencka](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Format JSON, pokazuje przepływy wychodzące i przychodzące dla każdej reguły|Wyświetla informacje dotyczące ruchu przychodzącego i wychodzącego IP za pomocą sieciowej grupy zabezpieczeń.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Dzienniki, wyjątki i Diagnostyka niestandardowa|   Zapewnia usługę programu Application Performance Monitoring (APM) dla deweloperów sieci Web na wielu platformach.| Interfejs API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Przetwarzanie alertów dotyczących danych/zabezpieczeń|    Alerty dotyczące Azure Security Center, alerty dzienników Azure Monitor|    Zawiera informacje o zabezpieczeniach i alertach.|  Interfejsy API REST, JSON|

### <a name="activity-logs"></a>Dzienniki aktywności

[Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane względem zasobów w ramach subskrypcji. Dzienniki aktywności były wcześniej znane jako "dzienniki inspekcji" lub "dzienniki operacyjne", ponieważ zgłaszają one [zdarzenia płaszczyzny kontroli](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) dla subskrypcji. 

Dzienniki aktywności ułatwiają określenie "co, kto i kiedy" operacji zapisu (to znaczy, PUT, POST lub DELETE). Dzienniki aktywności pomagają również zrozumieć stan operacji i inne istotne właściwości. Dzienniki aktywności nie obejmują operacji odczytu (GET).

W tym artykule pozycje PUT, POST i DELETE odnoszą się do wszystkich operacji zapisu, które znajdują się w dzienniku aktywności w zasobach. Można na przykład użyć dzienników aktywności, aby znaleźć błąd w przypadku rozwiązywania problemów lub aby monitorować sposób, w jaki użytkownik w organizacji zmodyfikował zasób.

![Diagram dziennika aktywności](./media/azure-log-audit/azure-log-audit-fig1.png)

Zdarzenia z dziennika aktywności można pobrać przy użyciu Azure Portal, interfejsu wiersza polecenia [platformy Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), poleceń cmdlet programu PowerShell i usługi [Azure Monitor API REST](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Dzienniki aktywności mają okres przechowywania danych na 90 dni.

Scenariusze integracji dla zdarzenia dziennika aktywności:

* [Utwórz alert wiadomości e-mail lub elementu webhook, który jest wyzwalany przez zdarzenie dziennika aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Przesyłaj strumieniowo do centrum zdarzeń](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie do analizy, takie jak PowerBI.

* Analizuj ją w usłudze PowerBI przy użyciu [pakietu zawartości usługi PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Zapisz go na koncie magazynu, aby przeprowadzić archiwizację lub inspekcję ręczną](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Czas przechowywania (w dniach) można określić za pomocą profilów dzienników.

* Wykonaj zapytanie i Wyświetl je w Azure Portal.

* Wykonaj zapytania za pośrednictwem polecenia cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.

* Wyeksportuj dziennik aktywności z profilami dzienników do [Azure monitor dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Możesz użyć konta magazynu lub [przestrzeni nazw centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) , która nie znajduje się w tej samej subskrypcji, co ta, która emituje dziennik. Osoba, która konfiguruje ustawienie, musi mieć dostęp do obu subskrypcji przy użyciu odpowiedniej [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) .

### <a name="azure-diagnostics-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure są emitowane przez zasób, który zapewnia rozbudowane, częste dane dotyczące operacji tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu. Na przykład [Dzienniki systemu zdarzeń systemu Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) są kategoriami dzienników diagnostycznych dla maszyn wirtualnych, a [dzienniki obiektów blob, tabel i kolejek](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) są kategoriami dzienników diagnostyki dla kont magazynu. Dzienniki diagnostyczne różnią się od dzienników aktywności, które zapewniają wgląd w operacje wykonywane względem zasobów w ramach subskrypcji.

![Diagramy dzienników diagnostyki platformy Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Dzienniki diagnostyczne platformy Azure oferują wiele opcji konfiguracji, takich jak Azure Portal, PowerShell, interfejs wiersza polecenia platformy Azure i interfejs API REST.

**Scenariusze integracji**

* Zapisz je na [koncie magazynu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) , aby przeprowadzić inspekcję lub inspekcję ręczną. Czas przechowywania (w dniach) można określić za pomocą ustawień diagnostycznych.

* [Przesyłaj strumieniowo do centrów zdarzeń](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) na potrzeby pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej, takie jak [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analizuj je za pomocą [dzienników Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Obsługiwane usługi, schematy dla dzienników diagnostycznych i obsługiwanych kategorii dzienników dla każdego typu zasobu**


| Usługa | Schemat i dokumentacja | Typ zasobu | Kategoria |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Dzienniki Azure Monitor dla Load Balancer (wersja zapoznawcza)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Grupy zabezpieczeń sieci|[Dzienniki Azure Monitor dla sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Rejestrowanie diagnostyczne dla Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|W usłudze Azure Key Vault|[Dzienniki Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Włączanie i używanie Analiza ruchu wyszukiwania](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Dostęp do dzienników diagnostyki dla Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Inspekcja<br>Żądania|
|Azure Data Lake Analytics|[Dostęp do dzienników diagnostyki dla Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Inspekcja<br>Żądania|
|Azure Logic Apps|[Logic Apps — niestandardowy schemat śledzenia B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Dzienniki diagnostyki Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Monitor dzienników dla Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Dzienniki diagnostyki Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Usługa Azure Stream Analytics|[Dzienniki diagnostyki zadań](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Wykonanie<br>Tworzenie|
|Azure Service Bus|[Dzienniki diagnostyczne usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Raporty w usłudze Azure Active Directory

Azure Active Directory (Azure AD) obejmuje raporty dotyczące zabezpieczeń, działań i inspekcji dla katalogu użytkownika. [Raport inspekcji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomaga identyfikować uprzywilejowane akcje, które wystąpiły w wystąpieniu usługi Azure AD użytkownika. Akcje uprzywilejowane obejmują zmiany podniesienia uprawnień (na przykład tworzenie ról lub resetowanie haseł), Zmienianie konfiguracji zasad (na przykład zasad haseł) lub zmiany konfiguracji katalogu (na przykład zmiany w ustawieniach federacyjnych domen).

Raporty zawierają rekord inspekcji dla nazwy zdarzenia, użytkownika, który wykonał akcję, zasób docelowy, na który ma wpływ zmiana, oraz datę i godzinę (w formacie UTC). Użytkownicy mogą pobrać listę zdarzeń inspekcji dla usługi Azure AD za pośrednictwem [Azure Portal](https://portal.azure.com/), zgodnie z opisem w temacie [Wyświetlanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Uwzględnione raporty są wymienione w poniższej tabeli:

| Raporty dotyczące zabezpieczeń | Raporty dotyczące działań | Raporty dotyczące inspekcji |
| :--------------- | :--------------- | :------------ |
|Logowania z nieznanych źródeł| Podsumowanie użycia aplikacji| Raport dotyczący inspekcji katalogu|
|Logowania po wielokrotnych niepowodzeniach|  Szczegóły użycia aplikacji||
|Logowania z wielu lokalizacji geograficznych|    Pulpit nawigacyjny aplikacji||
|Logowania z adresów IP związanych z podejrzanymi działaniami|   Błędy aprowizacji kont||
|Nieregularne działania związane z logowaniem|    Urządzenia indywidualnych użytkowników||
|Logowania z urządzeń, które mogą być zainfekowane|   Aktywność poszczególnych użytkowników||
|Nietypowe działania użytkowników związane z logowaniem| Raport dotyczący działań grup||
||Raport dotyczący aktywności rejestracji resetowania hasła||
||Działania związane z resetowaniem haseł||

Dane w tych raportach mogą być przydatne dla aplikacji, takich jak systemy zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), inspekcje i narzędzia analizy biznesowej. Interfejsy API raportów usługi Azure AD umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te [interfejsy API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) można wywoływać za pomocą różnych języków programowania i narzędzi.

Przechowywanie zdarzeń w raporcie inspekcji usługi Azure AD różni się od 7-90 dni w zależności od typu licencji skojarzonej z dzierżawcą. 

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania raportów, zobacz [zasady przechowywania raportów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Jeśli interesuje się zachowanie zdarzeń inspekcji dłużej, należy użyć interfejsu API raportowania do regularnego ściągania [zdarzeń inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) do oddzielnego magazynu danych.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Dzienniki maszyn wirtualnych używające Diagnostyka Azure

[Diagnostyka Azure](https://docs.microsoft.com/azure/azure-diagnostics) jest możliwością platformy Azure, która umożliwia zbieranie danych diagnostycznych we wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z dowolnego z kilku źródeł. Obecnie obsługiwane są [role sieci Web i procesu roboczego usługi w chmurze Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Dzienniki maszyn wirtualnych używające Diagnostyka Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Maszyny wirtualne platformy Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) z systemem Microsoft Windows i [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Diagnostyka Azure można włączyć na maszynie wirtualnej, wykonując jedną z następujących czynności:

* [Śledzenie maszyn wirtualnych platformy Azure za pomocą programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Skonfiguruj zdalnie Diagnostyka Azure na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Konfigurowanie diagnostyki w usłudze Azure Virtual Machines przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Tworzenie maszyny wirtualnej z systemem Windows z funkcją monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Analityka magazynu

[Analityka magazynu platformy Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) dzienników i udostępnia dane metryk dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Rejestrowanie analityka magazynu jest dostępne dla [usług Azure Blob, Azure Queue i Azure Table Storage](https://docs.microsoft.com/azure/storage/storage-introduction). Analityka magazynu rejestruje szczegółowe informacje o żądaniach zakończonych powodzeniem i zakończonych niepowodzeniem do usługi magazynu.

Te informacje służą do monitorowania indywidualnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku. Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywność w swoim punkcie końcowym obiektu BLOB, ale nie znajduje się w jego punktach końcowych tabeli lub kolejki, tworzone są tylko dzienniki odnoszące się do usługi BLOB Storage.

Aby użyć analityka magazynu, włącz je indywidualnie dla każdej usługi, którą chcesz monitorować. Można ją włączyć w [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Monitorowanie konta magazynu w Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji ustaw właściwości usługi, aby włączyć analityka magazynu indywidualnie dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanym obiekcie BLOB (na potrzeby rejestrowania) i w dobrze znanych tabelach (dla metryk), do których można uzyskać dostęp przy użyciu interfejsów API usługi BLOB Storage i tabel usługi Storage.

Analityka magazynu ma limit 20 terabajtów (TB) przechowywania danych, które są niezależne od łącznego limitu dla konta magazynu. Wszystkie dzienniki są przechowywane w blokowych obiektach [BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) w kontenerze o nazwie $logs, który jest tworzony automatycznie po włączeniu analityka magazynu dla konta magazynu.

> [!Note]
> * Aby uzyskać więcej informacji na temat rozliczeń i zasad przechowywania danych, zobacz [analityka magazynu i](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)rozliczanie.
> * Aby uzyskać więcej informacji na temat limitów kont magazynu, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Analityka magazynu rejestruje następujące typy żądań uwierzytelnionych i anonimowych:

| Uwierzytelnia  | Anonimowe|
| :------------- | :-------------|
| Żądania zakończone powodzeniem | Żądania zakończone powodzeniem |
|Żądania zakończone niepowodzeniem, w tym limit czasu, ograniczanie przepustowości, Sieć, autoryzacja i inne błędy | Żądania przy użyciu sygnatury dostępu współdzielonego, w tym żądań zakończonych niepowodzeniem i zakończonych pomyślnie |
| Żądania przy użyciu sygnatury dostępu współdzielonego, w tym żądań zakończonych niepowodzeniem i zakończonych pomyślnie |Błędy limitu czasu dla klienta i serwera |
|   Żądania danych analitycznych |    Żądania GET zakończone niepowodzeniem z kodem błędu 304 (niemodyfikowane) |
| Żądania wykonywane przez analityka magazynu samego siebie, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w [analityka magazynu zarejestrowanych operacji i komunikatów o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) oraz w [formacie dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w [analityka magazynu zarejestrowanych operacji i komunikatów o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) oraz w [formacie dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Dzienniki sieci platformy Azure

Rejestrowanie i monitorowanie sieci na platformie Azure jest kompleksowe i obejmuje dwie szerokie Kategorie:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitorowanie sieci oparte na scenariuszu jest dostarczane z funkcjami w Network Watcher. Ta usługa obejmuje funkcję przechwytywania pakietów, następny przeskok, sprawdzenie przepływu IP, widok grupy zabezpieczeń, dzienniki przepływu sieciowej grupy zabezpieczeń. Monitorowanie poziomu scenariusza zapewnia kompleksowy wgląd w zasoby sieciowe w przeciwieństwie do monitorowania poszczególnych zasobów sieciowych.

* [Monitorowanie zasobów](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitorowanie poziomu zasobów obejmuje cztery funkcje, dzienniki diagnostyczne, metryki, rozwiązywanie problemów i kondycję zasobów. Wszystkie te funkcje są tworzone na poziomie zasobów sieciowych.

![Dzienniki sieci platformy Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Narzędzia do diagnostyki sieci i wizualizacji dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure.

### <a name="network-security-group-flow-logging"></a>Rejestrowanie przepływu sieciowych grup zabezpieczeń

[Dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) są funkcją Network Watcher, która służy do wyświetlania informacji dotyczących ruchu przychodzącego i wychodzącego IP przez sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie JSON i pokazują:
* Przepływy wychodzące i przychodzące dla każdej reguły.
* Karta sieciowa, do której odnosi się przepływ.
* 5-informacje o spójnej kolekcji przepływu: źródłowy lub docelowy adres IP, port źródłowy lub docelowy oraz protokół.
* Czy ruch jest dozwolony, czy odrzucony.

Mimo że dzienniki przepływów są docelowe sieciowych grup zabezpieczeń, nie są wyświetlane w taki sam sposób jak w przypadku innych dzienników. Dzienniki przepływów są przechowywane tylko w ramach konta magazynu.

Te same zasady przechowywania, które są widoczne dla innych dzienników, mają zastosowanie do dzienników przepływów. Dzienniki mają zasady przechowywania, które można ustawić od 1 dnia do 365 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

**Dzienniki diagnostyczne**

Zdarzenia okresowe i spontaniczne są tworzone przez zasoby sieciowe i rejestrowane na kontach magazynu i wysyłane do centrum zdarzeń lub dzienników Azure Monitor. Dzienniki zapewniają wgląd w kondycję zasobu. Mogą one być wyświetlane w narzędziach, takich jak dzienniki Power BI i Azure Monitor. Aby dowiedzieć się, jak wyświetlać dzienniki diagnostyczne, zobacz [dzienniki Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Dzienniki diagnostyczne](./media/azure-log-audit/azure-log-audit-fig5.png)

Dzienniki diagnostyczne są dostępne dla [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), tras i [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher udostępnia widok dzienników diagnostycznych. Ten widok zawiera wszystkie zasoby sieciowe, które obsługują rejestrowanie diagnostyki. Z tego widoku możesz wygodnie i szybko włączać i wyłączać zasoby sieciowe.


Oprócz wyżej wymienionych funkcji rejestrowania Network Watcher obecnie ma następujące możliwości:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Zapewnia widok poziomu sieci, który pokazuje różne powiązania i skojarzenia między zasobami sieci w grupie zasobów.

- [Przechwytywanie pakietów zmiennych](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Przechwytuje dane pakietów do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i kontrolki dostrajania, takie jak ustawienia czasu i ograniczenia rozmiaru, zapewniają uniwersalność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie *. Cap* .

- [Weryfikacja przepływu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Sprawdza, czy pakiet jest dozwolony, czy odrzucany w oparciu o informacje o przepływie 5-parametry pakietów krotki (to jest, docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń, zwracana jest zasada i Grupa, która odrzuciła pakiet.

- [Następny przeskok](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Określa następny przeskok dla pakietów przesyłanych w sieci szkieletowej platformy Azure, dzięki czemu można zdiagnozować wszelkie nieprawidłowo skonfigurowane trasy zdefiniowane przez użytkownika.

- [Widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Pobiera obowiązujące i stosowane reguły zabezpieczeń stosowane na maszynie wirtualnej.

- [Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Pomaga rozwiązywać problemy z bramami i połączeniami sieci wirtualnej.

- [Limity dotyczące subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Umożliwia wyświetlenie użycia zasobów sieciowych względem limitów.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna usługa APM dla deweloperów sieci Web na wielu platformach. Służy do monitorowania aktywnych aplikacji sieci Web. Automatycznie wykrywa anomalie wydajności. Obejmuje ona zaawansowane narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć sposób korzystania z aplikacji przez użytkowników.

Application Insights zaprojektowano, aby pomóc w ciągłym ulepszaniu wydajności i użyteczności.

Działa w przypadku aplikacji na wielu różnych platformach, w tym .NET, Node. js i Java EE, niezależnie od tego, czy są hostowane lokalnie, czy w chmurze. Integruje się ona z procesem DevOps i ma punkty połączenia z różnymi narzędziami programistycznymi.

![Diagram Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Usługa Application Insights jest przeznaczona dla zespołu deweloperów po to, aby pomóc zrozumieć, jak działa Twoja aplikacja i jak jest używana. Monitoruje ona:

* **Stawki żądania, czasy odpowiedzi i wskaźniki niepowodzeń**: Dowiedz się, które strony są najbardziej popularne, o godzinach i miejscach, w których znajdują się użytkownicy. Zobacz, które strony działają najlepiej. Jeśli czasy reakcji i wskaźniki niepowodzeń są wysokie, gdy występuje więcej żądań, może wystąpić problem z ponownym pozyskiwaniem.

* **Stawki zależności, czasy odpowiedzi i wskaźniki niepowodzeń**: Dowiedz się, czy usługi zewnętrzne spowalniają pracę.

* **Wyjątki**: Analizuj zagregowane dane statystyczne lub wybieraj określone wystąpienia i przechodzenie do szczegółów w śladach stosu i powiązanych żądaniach. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

* **Wyświetlanie stron i wydajność ładowania**: Pobierz raporty z przeglądarek użytkowników.

* **Wywołania AJAX**: Uzyskaj stawki stron sieci Web, czasy odpowiedzi i stawki niepowodzeń.

* **Liczby użytkowników i sesji**.

* **Liczniki wydajności**: Pobieranie danych z komputerów z systemem Windows lub Linux, takich jak procesor CPU, pamięć i użycie sieci.

* **Diagnostyka hosta**: Pobierz dane z platformy Docker lub Azure.

* **Dzienniki śledzenia diagnostyki**: Pobierz dane z aplikacji, aby można było skorelować zdarzenia śledzenia z żądaniami.

* **Niestandardowe zdarzenia i metryki**: Pobierz dane, które można napisać samodzielnie w kodzie klienta lub serwera, aby śledzić zdarzenia biznesowe, takie jak sprzedane elementy lub gry.

W poniższej tabeli przedstawiono i opisano scenariusze integracji:

| Scenariusz integracji | Opis |
| --------------------- | :---------- |
|[Mapa aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Składniki Twojej aplikacji wraz z kluczowymi metrykami i alertami.|
|[Wyszukiwanie danych wystąpienia przez diagnostykę](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Wyszukiwanie i filtrowanie zdarzeń, takich jak żądania, wyjątki, wywołania zależności, dzienniki śledzenia i wyświetlenia stron.|
|[Eksplorator metryk danych agregowanych](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Eksploruj, filtruj i segmentuj zagregowane dane, takie jak liczby żądań, błędów i wyjątków, czasy reakcji, czasy ładowania stron.|
|[Pulpity nawigacyjne](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)|Połącz dane z wielu zasobów i udostępnij innym osobom. Opcja ta doskonale nadaje się dla aplikacji wieloskładnikowych i ciągłego wyświetlania w pomieszczeniu zespołu.|
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Podczas wdrażania nowej kompilacji obejrzyj te wskaźniki wydajności prawie w czasie rzeczywistym, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.|
|[Analiza](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Odpowiedz na trudne pytania dotyczące wydajności i użycia Twojej aplikacji za pomocą tego zaawansowanego języka zapytań.|
|[Alerty automatyczne i ręczne](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatyczne alerty dostosowują się do normalnych wzorców danych telemetrycznych aplikacji i są wyzwalane, gdy istnieje coś poza zwykłym wzorcem. Możesz też ustawić alerty dla konkretnych poziomów metryk niestandardowych lub standardowych.|
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Wyświetl dane wydajności w kodzie. Przejdź do kodu ze śladów stosu.|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integruje metryki użycia z innymi analizami biznesowymi.|
|[REST API](https://dev.applicationinsights.io/)|Napisz kod, aby uruchamiać zapytania dla swoich metryk i danych pierwotnych.|
|[Eksport ciągły](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Zbiorcze eksportowanie danych pierwotnych do magazynu po odebraniu.|

### <a name="azure-security-center-alerts"></a>Alerty Azure Security Center

Azure Security Center wykrywanie zagrożeń polega na automatycznym zbieraniu informacji o zabezpieczeniach z zasobów platformy Azure, sieci i podłączonych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo. Aby uzyskać więcej informacji, zobacz [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagram Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Ma ona zastosowanie do przełomowych technologii dużych [](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) ilości danych i uczenia maszynowego w celu szacowania zdarzeń w całej sieci szkieletowej w chmurze. W ten sposób wykrywa zagrożenia, które byłyby niemożliwe do zidentyfikowania przy użyciu ręcznych podejścia i przewidywania ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

* **Zintegrowana analiza zagrożeń**: Szuka znanych nieprawidłowych aktorów przez zastosowanie globalnej analizy zagrożeń z produktów i usług firmy Microsoft, jednostki Microsoft Digital Zbrodnis (DCU), centrum Microsoft Security Response Center (MSRC) i zewnętrznych źródeł danych.

* **Analiza behawioralna**: Stosuje znane wzorce w celu odnalezienia złośliwego zachowania.

* **Wykrywanie anomalii**: Używa profilowania statystycznego do tworzenia historycznej linii bazowej. Narzędzie to alarmuje o odchyleniach od ustalonych linii bazowych zgodnych z wektorem potencjalnego ataku.

Wiele operacji zabezpieczeń i zespołów reagowania na zdarzenia polega na rozwiązaniu SIEM jako punktu wyjścia do segregowania i badania alertów zabezpieczeń. Za pomocą Azure Log Integration można synchronizować alerty Security Center i zdarzenia zabezpieczeń maszyn wirtualnych, zbierane przez diagnostykę i dzienniki inspekcji platformy Azure, a także dzienniki Azure Monitor lub rozwiązania SIEM niemal w czasie rzeczywistym.

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Azure Monitor logs to usługa platformy Azure, która ułatwia zbieranie i analizowanie danych generowanych przez zasoby w środowiskach w chmurze i lokalnych. Zapewnia wgląd w dane w czasie rzeczywistym dzięki zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym szybkie analizowanie milionów rekordów w ramach wszystkich obciążeń i serwerów, niezależnie od ich fizycznej lokalizacji.

![Diagram dzienników Azure Monitor](./media/azure-log-audit/azure-log-audit-fig8.png)

W centrum dzienników Azure Monitor jest obszar roboczy Log Analytics, który jest hostowany na platformie Azure. Azure Monitor Logs zbiera dane z połączonych źródeł przez skonfigurowanie źródeł danych i Dodawanie rozwiązań do subskrypcji. Wszystkie źródła danych i rozwiązania tworzą różne typy rekordów, z których każdy ma własny zestaw właściwości. Jednak źródła i rozwiązania nadal mogą być analizowane razem w zapytaniach do obszaru roboczego. Ta funkcja umożliwia korzystanie z tych samych narzędzi i metod do pracy z różnymi danymi zbieranymi przez różne źródła.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Połączone źródła to komputery i inne zasoby, które generują dane zbierane przez Azure Monitor dzienników. Źródła mogą obejmować agentów zainstalowanych na komputerach z [systemem Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) i [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) , które łączą się bezpośrednio lub agenci w [połączonej System Center Operations Manager grupie zarządzania](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Dzienniki Azure Monitor mogą również zbierać dane z [konta usługi Azure Storage](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Źródła danych](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) to różne rodzaje danych, które są zbierane z każdego połączonego źródła. Źródła obejmują zdarzenia i [dane wydajności](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z agentów [systemu Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) i Linux, a także źródła, takie jak [dzienniki usług IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) i [niestandardowe Dzienniki tekstowe](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Każde źródło danych, które ma być zbierane, jest konfigurowane, a konfiguracja jest automatycznie dostarczana do każdego z nich.

Istnieją cztery sposoby [zbierania dzienników i metryk dla usług platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Diagnostyka Azure bezpośrednio do dzienników Azure Monitor (**Diagnostyka** w poniższej tabeli)

* Diagnostyka Azure do usługi Azure Storage w celu Azure Monitor dzienników (**Magazyn** w poniższej tabeli)

* Łączniki dla usług platformy Azure (**Łącznik** w poniższej tabeli)

* Skrypty do zbierania i publikowania danych w dziennikach Azure Monitor (puste komórki w poniższej tabeli oraz dla usług, które nie znajdują się na liście)

| Usługa | Typ zasobu | Dzienniki | Metryki | Rozwiązanie |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostyka|Diagnostyka|    [Aplikacja platformy Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics) [Analiza bramy](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Łącznik|  Łącznik|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Łącznik (wersja zapoznawcza)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Konta Azure Automation| Microsoft.Automation/<br>automationAccounts|    Diagnostyka||       [Więcej informacji](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Konta Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostyka|    Diagnostyka||
|Usługi w chmurze klasycznego||       Magazyn||       [Więcej informacji](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostyka|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostyka|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostyka|||
|Przestrzeń nazw centrum zdarzeń platformy Azure| Microsoft.EventHub/<br>Przestrzeni|  Diagnostyka|    Diagnostyka||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostyka||
|W usłudze Azure Key Vault|   Microsoft.KeyVault/<br>magazynów|  Diagnostyka  || [Analiza usługi Key Vault](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostyka|||
|Azure Logic Apps|  Microsoft.Logic/<br>przebieg|  Diagnostyka|    Diagnostyka||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupy zabezpieczeń sieci|   Microsoft.Network/<br>networksecuritygroups|Diagnostyka||   [Analiza grup zabezpieczeń sieci platformy Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-and-network-security-group-analytics)|
|Magazyny usługi Recovery|   Microsoft.RecoveryServices/<br>magazynów|||[Usługa Azure Recovery Services — analiza (Podgląd)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Usługi wyszukiwania|   Microsoft.Search/<br>searchServices|    Diagnostyka|    Diagnostyka||
|Przestrzeń nazw magistrali usług| Microsoft.ServiceBus/<br>Przestrzeni|    Diagnostyka|Diagnostyka|    [Usługa Service Bus analiza (Podgląd)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Magazyn||    [Analiza usługi Service Fabric (wersja zapoznawcza)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>serwerem<br>databases||       Diagnostyka||
||Microsoft.Sql/<br>serwerem<br>elasticPools||||
|Magazyn|||         Skrypt| [Usługa Azure Storage Analytics (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Usługa Azure Virtual Machines|    Microsoft.Compute/<br>VirtualMachines|  Wewnętrzny|  Wewnętrzny||
||||Diagnostyka||
|Zestawy skalowania maszyn wirtualnych|    Microsoft.Compute/<br>VirtualMachines    ||Diagnostyka||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>VirtualMachines||||
|Farmy serwerów sieci Web|Microsoft.Web/<br>dopuszczalna||   Diagnostyka
|Witryny internetowe|  Microsoft.Web/<br>teren ||      Diagnostyka|    [Więcej informacji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>teren<br>czasów||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integracja dzienników z lokalnymi systemami SIEM

Za pomocą Azure Log Integration można zintegrować nieprzetworzone dzienniki z zasobów platformy Azure przy użyciu lokalnego systemu SIEM (informacje o zabezpieczeniach i system zarządzania zdarzeniami). Pliki do pobrania AzLog zostały wyłączone w dniu 27 czerwca 2018. Aby uzyskać wskazówki na temat tego, co należy zrobić, przejrzyj temat publikowanie [za pomocą usługi Azure monitor w celu integracji z narzędziami Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Diagram integracji dzienników](./media/azure-log-audit/azure-log-audit-fig9.png)

Integracja dzienników zbiera dane diagnostyczne platformy Azure z maszyn wirtualnych z systemem Windows, dzienników aktywności platformy Azure, alertów Azure Security Center i dzienników dostawcy zasobów platformy Azure. Ta integracja zapewnia ujednolicony pulpit nawigacyjny dla wszystkich zasobów, niezależnie od tego, czy są one lokalne, czy w chmurze, dzięki czemu można agregować, skorelować, analizować i generować alerty dotyczące zdarzeń zabezpieczeń.

Integracja dzienników obecnie obsługuje integrację dzienników aktywności platformy Azure, dzienników zdarzeń systemu Windows z maszyn wirtualnych z systemem Windows z subskrypcją platformy Azure, Azure Security Center alertów, dzienników diagnostyki platformy Azure i dzienników inspekcji usługi Azure AD.

| Typ dziennika | Dzienniki Azure Monitor obsługujące kod JSON (Splunk, ArcSight i IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Dzienniki inspekcji usługi Azure AD|   Yes|
|Dzienniki aktywności| Tak|
|Alerty Security Center |Tak|
|Dzienniki diagnostyczne (dzienniki zasobów)|  Tak|
|Dzienniki maszyn wirtualnych|   Tak, za pośrednictwem zdarzeń przekazywanych, a nie za pośrednictwem JSON|

[Rozpocznij pracę z Azure log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Ten samouczek przeprowadzi Cię przez proces instalowania Azure Log Integration i integrowania dzienników z usługi Azure Storage, dzienników aktywności platformy Azure, alertów Azure Security Center i dzienników inspekcji usługi Azure AD.

Scenariusze integracji dla SIEM:

* [Kroki konfiguracji partnera](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ten wpis w blogu przedstawia sposób konfigurowania Azure Log Integration do pracy z rozwiązaniami partnerskimi Splunk, HP ArcSight i IBM QRadar.

* [Azure log Integration często zadawane pytania](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): W tym artykule znajdują się odpowiedzi na pytania dotyczące Azure Log Integration.

* [Integrowanie alertów Security Center z Azure log Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): W tym artykule omówiono sposób synchronizowania alertów Security Center, zdarzeń zabezpieczeń maszyn wirtualnych zbieranych przez dzienniki diagnostyki platformy Azure oraz dzienników inspekcji platformy Azure z dziennikami Azure Monitor lub rozwiązaniem SIEM.

## <a name="next-steps"></a>Następne kroki

- [Inspekcja i rejestrowanie](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Ochrona danych przez utrzymywanie wglądu i szybkie reagowanie na alerty zabezpieczeń.

- [Rejestrowanie zabezpieczeń i zbieranie dzienników na platformie Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Wymuś te ustawienia, aby upewnić się, że wystąpienia platformy Azure będą zbierać poprawne dzienniki zabezpieczeń i inspekcji.

- [Skonfiguruj ustawienia inspekcji dla zbioru witryn](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Jeśli jesteś administratorem zbioru witryn, Pobierz historię działań poszczególnych użytkowników i historię działań podjętych w określonym zakresie dat. 

- [Przeszukaj dziennik inspekcji w centrum zabezpieczeń i zgodności pakietu Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Użyj Centrum zabezpieczeń i zgodności pakietu Office 365, aby przeszukać ujednolicony dziennik inspekcji i wyświetlić aktywność użytkowników i administratorów w organizacji pakietu Office 365.


