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
ms.openlocfilehash: c0ca120da27daa7a498f73b9c62530e3af26e539
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-logging-and-auditing"></a>Rejestrowanie platformy Azure i inspekcji
## <a name="introduction"></a>Wprowadzenie
### <a name="overview"></a>Przegląd
Pomoc aktualnych i potencjalnych klientów platformy Azure w opis i przy użyciu różnych funkcjach zabezpieczeń dostępnych w i otaczającego platformy Azure, firma Microsoft opracowała szereg oficjalne dokumenty, omówienie zabezpieczeń najlepsze rozwiązania i listy kontrolne. Tematy zakresu pod względem szerokości i głębokość i są okresowo aktualizowane. Ten dokument jest częścią tej serii, zgodnie z opisem w poniższej sekcji abstrakcyjny.
### <a name="azure-platform"></a>Platformy Azure
Azure to platforma usługi chmury Otwórz i elastyczne, która obsługuje najszerszych wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń.

Można na przykład:
-   Uruchom kontenery Linux z integracją rozwiązania Docker.

-   Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js

-   Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń.

Usługi w chmurze publicznej Azure obsługuje te same technologie miliony deweloperów i informatyków już zależne i zaufania.

Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług w chmurze, możesz używają możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Ponadto platforma Azure oferuje szeroki zakres konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, co pozwala dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania realizowanych wdrożeń. Ten dokument zostanie pomaga spełnić te wymagania.

### <a name="abstract"></a>Abstrakcyjny
Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami i powiązanych alertów, to ważne składniki danych skutecznej strategii ochrony. Dzienniki zabezpieczeń i raporty umożliwiają elektronicznych rekordu podejrzanych działań i wykrywać wzorce wskazujące i nieudane próby zewnętrznych penetrację sieci, a także atakami wewnętrznymi pomocy. Inspekcja służy do monitorowania aktywności użytkownika, zgodność z przepisami dokumentu, dokonać analizy śledczej i inne. Alerty udostępniają natychmiastowego wysłania powiadomienia w przypadku wystąpienia zdarzeń zabezpieczeń.

Usługi Microsoft Azure i produktów umożliwiają zabezpieczeń można skonfigurować inspekcji i rejestrowania opcje ułatwiające identyfikowanie luk w zasadach zabezpieczeń i mechanizmów i rozwiązać te luki, aby uniknąć naruszenia. Oferty usług firmy Microsoft, niektóre (i w niektórych przypadkach wszystkie) z następujących opcji: scentralizowane monitorowanie, rejestrowanie i analizy systemów, aby zapewnić ciągłe widoczność; alerty odpowiednim; i raporty ułatwiające zarządzanie dużej ilości danych wygenerowanych przez urządzeń i usług.

Dane dziennika Microsoft Azure mogą być eksportowane do systemów zdarzenia zabezpieczeń i zarządzania zdarzenia (SIEM) do analizy i integruje się z rozwiązaniami inspekcji innych firm.

Ten dokument zawiera wprowadzenie do generowania, zbierania i analizowania dzienniki zabezpieczeń z usługi hostowanej na platformie Azure, a może pomóc wgląd zabezpieczeń Azure wdrożeń. Zakres ten dokument jest ograniczony do aplikacji i usług wbudowanych i wdrożonych na platformie Azure.

> [!Note]
> Zastosowanie niektórych zaleceń zamieszczonych w niniejszym dokumencie może spowodować danych, sieci i użycia zasobów obliczeniowych i zwiększyć koszty licencji lub subskrypcji.

## <a name="types-of-logs-in-azure"></a>Typy dzienników na platformie Azure
Aplikacje w chmurze są złożonych z wielu części ruchu. Dzienniki dostarcza dane, aby upewnić się, że aplikacja pozostaje w górę i działa w dobrej kondycji. Pomaga również umożliwia stave potencjalne problemy i rozwiązywanie problemów w przeszłości te. Ponadto można użyć rejestrowania danych w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji.

Azure tworzy szczegółowe rejestrowanie dla każdej usługi Azure. Te dzienniki są podzielone według rodzaju główne:
-   **Dzienniki sterowania i zarządzania nimi** zapewniają wgląd w operacji tworzenia Menedżera zasobów Azure, UPDATE i DELETE. [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) przykładem tego typu dziennika.

-   **Dane płaszczyzny dzienniki** zapewniają wgląd w zdarzenia wygenerowane jako część użycia zasobów platformy Azure. Przykładem tego typu dziennika są zdarzeń systemu Windows systemu zabezpieczeń, i dzienniki aplikacji na maszynie wirtualnej i [dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) skonfigurowane za pośrednictwem Monitora Azure


-   **Przetworzonych zdarzeń** informacje o analizowanych zdarzenia/alerty, które zostały przetworzone w Twoim imieniu. Przykładem tego typu są [alerty Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) gdzie [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) jest przetwarzane i analizowane subskrypcji i zawiera alerty zabezpieczeń zwięzły

Następująca tabela zawiera listę najważniejszych typów dzienników dostępnych w usłudze Azure.

| Kategoria dziennika | Typ dziennika | Użycia | Integracja |
| ------------ | -------- | ------ | ----------- |
|[Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Zdarzenia płaszczyzny kontroli zasobów usługi Azure Resource Manager|   Zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji.| Interfejs API REST & [Azure monitora](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|często dane dotyczące działania usługi Azure Resource Manager zasobów w subskrypcji| Zapewniają wgląd w działania, zasobu wykonanie samego| Azure Monitor [strumienia](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Raportowanie usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Dzienników i raportów|Działania logowania użytkowników & informacje o systemie działania dotyczące użytkowników i grupy zarządzania|[Interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Maszyny wirtualne i usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Dziennik zdarzeń systemu Windows i systemu Linux|    Przechwytuje dane systemu i rejestrowanie danych na maszynach wirtualnych i przesyła dane do wybranego konta magazynu.|   Przy użyciu systemu Windows [WAD](https://docs.microsoft.com/azure/azure-diagnostics) (magazynu diagnostyki Windows Azure) i Linux w Monitorze systemu Azure|
|[Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Rejestrowanie magazynu i udostępnia metryki danych dla konta magazynu|Zapewnia wgląd w żądań śledzenia analizować trendy użycia i diagnozowanie problemów z konta magazynu.|    Interfejs API REST lub [biblioteki klienta](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Dzienniki przepływu NSG (sieciowej grupy zabezpieczeń)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON format i zawiera przepływy ruchu wychodzącego i przychodzącego na podstawie reguł na|Wyświetl informacje o przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci|[Obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Szczegółowe informacje o aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Dzienniki, wyjątków i diagnostyki niestandardowej|    Usługa zarządzania wydajności aplikacji przeznaczonych dla deweloperów sieci web na wielu platformach.| Interfejs API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Przetwarzaj dane / Alert zabezpieczeń| Centrum zabezpieczeń Azure Alert, Alert analizy dzienników|   Informacje o zabezpieczeniach i alerty.|   Interfejsy API REST, JSON|

### <a name="activity-log"></a>Dziennik aktywności
[Dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Dziennik aktywności była wcześniej znana jako "Dzienników inspekcji" lub "Operacyjne dzienniki", ponieważ zgłasza [zdarzeń formantu płaszczyzny](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) dla Twojej subskrypcji. Korzystając z dziennika aktywności, można określić ", co, która i kiedy" dla żadnego zapisu (PUT, POST, DELETE) podejmowaną w odniesieniu do zasobów w ramach subskrypcji. Można także zrozumienie stanu operacji i inne odpowiednie właściwości. Dziennik nie zawiera operacje odczytu (GET).

W tym miejscu PUT, POST, DELETE odwołuje się do wszystkich zawiera dziennik aktywności operacji zapisu na zasobach. Na przykład można użyć Dzienniki aktywności można znaleźć wystąpił błąd podczas rozwiązywania problemu, lub do monitorowania, jak użytkownik w organizacji zmienić zasobu.

![Dziennik aktywności](./media/azure-log-audit/azure-log-audit-fig1.png)


Można pobrać zdarzenia z dziennika aktywności przy użyciu portalu Azure [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), poleceń cmdlet programu PowerShell, a [interfejsu API REST Monitor Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Dzienniki aktywności ma 19-dniowy okres przechowywania danych.

Scenariusze integracji
-   [Utwórz alert e-mail lub elementu webhook wyzwala poza zdarzenie dziennika aktywności.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Strumienia go do Centrum zdarzeń](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.

-   Przeanalizuj go za pomocą usługi Power BI [pakiet zawartości usługi Power BI.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)

-   [Zapisz go na konto magazynu inspekcji archiwizacji lub ręcznie.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) Można określić czas przechowywania (w dniach) przy użyciu profilów dziennika.

-   Zapytania i wyświetlać go w portalu Azure.

-   Zapytanie go za pomocą polecenia Cmdlet programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST.

-   Eksportuj Dziennik aktywności przy użyciu profilów dziennika do [dziennika analizy](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Można użyć konta magazynu lub [przestrzeni nazw zdarzenia koncentratora](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) nie jest w tej samej subskrypcji co jeden dziennik emisji. Użytkownik, który konfiguruje ustawienie musi mieć odpowiednie [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dostęp do obu subskrypcji
### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure
Azure dzienników diagnostycznych są emitowane przez zasób zawierają rozbudowane, często dane dotyczące operacji tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu (na przykład [dzienniki systemu zdarzeń systemu Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)są jedną kategorię dzienników diagnostycznych dla maszyn wirtualnych i [obiektów blob, tabel i dzienniki kolejki](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategorii dzienników diagnostycznych dla kont magazynu) i różnią się w dzienniku aktywności zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji.

![Dzienniki diagnostyczne platformy Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Dzienniki diagnostyczne Azure oferują wiele opcji konfiguracji, które jest, portalu Azure, przy użyciu programu PowerShell, interfejsu wiersza polecenia (CLI) i interfejsu API REST.

**Scenariusze integracji**
-   Zapisywanie ich [konta magazynu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) inspekcji inspekcji lub ręcznie. Można określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych.

-   [Strumienia je do usługi Event Hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak [usługi Power BI.](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

-   Analizuj je za pomocą [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Obsługiwane usługi schematu dla dzienników diagnostycznych i kategorie dziennika obsługiwanych na typ zasobu**


| Usługa | Schemat & dokumentów | Typ zasobu | Kategoria |
| ------- | ------------- | ------------- | -------- |
|Moduł równoważenia obciążenia| [Analizy dzienników dla usługi równoważenia obciążenia Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Grupy zabezpieczeń sieci|[Usługa Log Analytics dla sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Bramy Application Gateway|[Rejestrowania diagnostyki bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Usługa Key Vault|[Funkcja rejestrowania usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Włączanie i używanie analizy ruchu wyszukiwania](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Inspekcja|
|Data Lake Analytics|[Accessing diagnostic logs for Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)|Microsoft.DataLakeAnalytics/accounts|Inspekcja|
|||Microsoft.DataLakeAnalytics/accounts|Żądania|
|||Microsoft.DataLakeStore/accounts|Żądania|
|Logic Apps|[Logic Apps — niestandardowy schemat śledzenia B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Azure rejestrowania diagnostycznego partii](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Analizy dzienników dla usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Azure Event Hubs dzienników diagnostycznych](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Dzienniki diagnostyczne zadania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Wykonanie|
|||Microsoft.StreamAnalytics/streamingjobs|Tworzenie|
|Service Bus|[Azure Service Bus dzienników diagnostycznych](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Raportowanie usługi Azure Active Directory
W usłudze Azure Active Directory (Azure AD) uwzględniono raporty dotyczące zabezpieczeń, działań i inspekcji dla katalogu. [Raport dotyczący usługi Azure Active Directory inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) przydatny do identyfikowania uprzywilejowanych akcji, które wystąpiły w usłudze Azure Active Directory. Uprzywilejowane akcje obejmują zmiany podniesienia uprawnień (na przykład tworzenie roli lub resetowanie haseł), zmiana konfiguracji zasad (na przykład zasady haseł) lub zmiany w konfiguracji katalogu (na przykład zmiany ustawień federacyjnego domeny).

Raporty dostarczają rekordu inspekcji dla nazwy zdarzenia aktora, kto wykonał akcję zasobu docelowego wpływ zmiany oraz Data i godzina (w formacie UTC). Klienci będą mogli pobrać listę zdarzeń inspekcji dla ich Azure Active Directory za pośrednictwem [portalu Azure](https://portal.azure.com/), zgodnie z opisem w [wyświetlanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Oto lista uwzględnionych raportów:

| Raporty dotyczące zabezpieczeń | Raporty dotyczące działań | Raporty dotyczące inspekcji |
| :--------------- | :--------------- | :------------ |
|Logowania z nieznanych źródeł| Podsumowanie użycia aplikacji| Raport dotyczący inspekcji katalogu|
|Logowania po wielokrotnych niepowodzeniach|  Szczegóły użycia aplikacji||
|Logowania z wielu lokalizacji geograficznych|    Pulpit nawigacyjny aplikacji||
|Logowania z adresów IP związanych z podejrzanymi działaniami|   Błędy aprowizacji kont||
|Nieregularne działania związane z logowaniem|    Urządzenia indywidualnych użytkowników||
|Logowania z urządzeń, które mogą być zainfekowane|   Działania indywidualnych użytkowników||
|Nietypowe działania użytkowników związane z logowaniem| Raport dotyczący działań grup||
||Raport dotyczący działań związanych z rejestracją resetowania haseł||
||Działania związane z resetowaniem haseł|||

Dane te raporty może być przydatne do aplikacji, takich jak systemów SIEM, inspekcji i narzędzia do analizy biznesowej. Interfejsy API raportów usługi Azure AD umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Możesz wywołać te [interfejsów API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) z różnych języków programowania i narzędzi.

Zdarzenia w raporcie programu Azure AD inspekcji są zachowywane przez okres 180 dni.

> [!Note]
> Aby uzyskać więcej informacji na temat przechowywania w raportach, zobacz [zasady przechowywania raportów Active Directory platformy Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)

W przypadku klientów do przechowywania ich zdarzeń inspekcji przez dłuższy czas przechowywania, do interfejsu API raportowania może służyć do ściągnięcia regularnie [zdarzenia inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) do magazynu danych.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Dzienniki maszyny wirtualnej za pomocą diagnostyki Azure
[Diagnostyka Azure](https://docs.microsoft.com/azure/azure-diagnostics) możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych na wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych źródeł. Obecnie obsługiwane są [Azure Cloud Service w sieci Web i proces roboczy](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me),

![Dzienniki maszyny wirtualnej za pomocą diagnostyki Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

[Maszyny wirtualne platformy Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) systemu Microsoft Windows i [sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Można włączyć diagnostyki Azure na maszynę wirtualną przy użyciu następujących:

-   Przy użyciu programu Visual Studio, zobacz [program Visual Studio do śledzenia maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Konfigurowanie diagnostyki Azure w usłudze Azure Virtual Machine zdalnie](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Aby skonfigurować diagnostykę na maszynach wirtualnych platformy Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Utwórz maszynę wirtualną systemu Windows z monitorowania i diagnostyki za pomocą szablonu usługi Resource Manager Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Analityka magazynu
[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia metryki danych dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Rejestrowanie analityka magazynu jest dostępne dla [usług obiektów Blob, kolejki i tabeli.](https://docs.microsoft.com/azure/storage/storage-introduction) Analityka magazynu rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi magazynu.

Te informacje może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane w sposób optymalny. Wpisy dziennika są tworzone tylko w przypadku żądań wysyłanych do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywności w punktu końcowego obiektu Blob, ale nie w jej tabel lub kolejek punktów końcowych, tylko dzienniki odnoszące się do usług obiektów Blob jest tworzona.

Aby użyć analityka magazynu, należy włączyć ją osobno dla każdej usługi, które chcesz monitorować. Możesz je włączyć w [portalu Azure](https://portal.azure.com/); Aby uzyskać więcej informacji, zobacz [monitorować konto magazynu w portalu Azure.](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) Można również włączyć analityka magazynu programowo przy użyciu interfejsu API REST lub biblioteka klienta. Włącz analizy magazynu osobno dla każdej usługi za pomocą operacji ustawić właściwości usługi.

Zagregowane dane są przechowywane w dobrze znany obiekt blob (w przypadku rejestrowania) i dobrze znane tabel (metryk), które mogą uzyskać dostęp za pomocą usługi obiektów Blob i usługa tabel interfejsów API.

Analityka magazynu ma limit 20 TB ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Wszystkie dzienniki są przechowywane w [blokowe obiekty BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) w kontenerze o nazwie $logs, które są automatycznie tworzone podczas analityka magazynu jest włączona dla konta magazynu.

> [!Note]
> Aby uzyskać więcej informacji dotyczących rozliczeń i zasad przechowywania danych, zobacz [analizy magazynu i rozliczeń.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)
>
> [!Note]
> Aby uzyskać więcej informacji o limity konta magazynu, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure.](https://docs.microsoft.com/azure/storage/storage-scalability-targets)

Rejestrowane są następujące typy żądań uwierzytelnionych i anonimowe.



| Uwierzytelniony  | Anonimowe|
| :------------- | :-------------|
| Liczba pomyślnych żądań | Liczba pomyślnych żądań |
|Nieudane żądania, w tym limitu czasu, ograniczania przepustowości sieci, autoryzacji i innych błędów | Żądania przy użyciu dostępu sygnatury dostępu Współdzielonego, włącznie z żądaniami nie powiodło się i pomyślne |
| Żądania przy użyciu dostępu sygnatury dostępu Współdzielonego, włącznie z żądaniami nie powiodło się i pomyślne |Błędy limitu czasu dla klienta i serwera |
|   Żądania do analizy danych |    Nieudane żądania GET z kodem błędu 304 (nie jest zmodyfikowany) |
| Żądania wysyłane przez analityka magazynu, takich jak dziennika utworzeniu lub usunięciu, nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) tematów. | Inne nieudanych żądań anonimowych nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Dzienniki sieci platformy Azure
Sieć rejestrowania i monitorowania na platformie Azure jest kompleksowy i obejmuje dwie szerokie kategorie:

-   [Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) -monitorowania sieci oparta na scenariuszu jest dostarczana z funkcji obserwatora sieciowego. Ta usługa obejmuje przechwytywania pakietów, następnego przeskoku, przepływ IP Sprawdź widok grupy zabezpieczeń, dzienniki przepływu NSG. Scenariusz poziomu monitorowania udostępnia widok pełnego zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

-   [Monitorowanie zasobów](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) -poziomu monitorowania zasobów składa się z czterech funkcji, dzienników diagnostycznych metryki, rozwiązywanie problemów i kondycji zasobów. Te funkcje są tworzone na poziomie zasobów sieciowych.

![Dzienniki sieci platformy Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Monitor sieci jest regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie sieci scenariusz w, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure.

**Rejestrowanie przepływu NSG** — dzienniki przepływu dla grup zabezpieczeń sieci umożliwiają przechwytywanie dzienniki związane z ruchu, który ma być dozwolony lub odrzucany przez zasady zabezpieczeń w grupie. Te dzienniki przepływu są zapisywane w formacie JSON i Pokaż przepływów wychodzącego i przychodzącego na podstawie reguły w poszczególnych kart przepływ dotyczy 5-elementowej informacji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego Protocol), i jeśli ruch został dozwolony lub niedozwolony.

### <a name="network-security-group-flow-logging"></a>Rejestrowanie przepływu grupy zabezpieczeń sieci

[Grupy zabezpieczeń sieci przepływu dzienniki](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) są funkcją obserwatora sieciowego, który służy do wyświetlania informacji na temat przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci. Te dzienniki przepływu są zapisywane w formacie JSON i Pokaż przepływów wychodzącego i przychodzącego na podstawie reguły w poszczególnych kart przepływ dotyczy 5-elementowej informacji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego Protocol), i jeśli ruch został dozwolony lub niedozwolony.

Podczas przepływu rejestruje grup zabezpieczeń sieci docelowej, nie są wyświetlane takie same jak inne dzienniki. Przepływ dzienniki są przechowywane tylko w ramach konta magazynu.

Te same zasady przechowywania wyświetlanego na inne dzienniki dotyczą dzienniki przepływu. Dzienniki ma zasady przechowywania, które można ustawić od dnia 1 do 365 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

**Dzienniki diagnostyczne**

Okresowe i spontanicznych zdarzenia są tworzone przez zasobów sieciowych i zarejestrowane na kontach magazynu, wysyłane do Centrum zdarzeń lub analizy dzienników. Te dzienniki wgląd w kondycję zasobu. Te dzienniki można wyświetlać w narzędzi, takich jak Power BI i analizy dzienników. Aby dowiedzieć się wyświetlić dzienniki diagnostyczne, odwiedź stronę [analizy dzienników.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)

![Dzienniki diagnostyczne](./media/azure-log-audit/azure-log-audit-fig5.png)

Dzienniki diagnostyczne są dostępne dla [modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy i [bramy aplikacji.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

Wyświetl dzienniki diagnostyczne zawiera obserwatora sieciowego. Ten widok zawiera wszystkich zasobów sieciowych, które obsługują rejestrowania diagnostycznego. Z tego widoku można włączyć i wyłączyć zasobów sieciowych, łatwo i szybko.


Oprócz powyższego możliwości rejestrowania zdarzeń, obserwatora sieciowego ma obecnie następujące możliwości:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -Wyświetla sieci poziomu pokazywanie różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.

- [Zmienna przechwytywania pakietów](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -przechwytuje pakiet danych do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i dostosować formanty, takie jak ustawianie czasu i rozmiaru ograniczenia zapewniają wszechstronność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie CAP.

-   [Przepływ IP weryfikuje](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) — sprawdza, czy pakiet jest dozwolony lub niedozwolony na podstawie przepływ informacji 5-elementowej pakietu parametrów (docelowy adres IP, źródłowy adres IP, Port docelowy, Port źródłowy i Protocol). Jeśli pakiet jest zabroniony przez grupę zabezpieczeń, zwracany jest reguła i grupy, którego pakiet.

-   [Następny przeskok](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) — określa następnego skoku dla pakietów przesyłane w sieci szkieletowej Azure, dzięki któremu można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika.

-   [Widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej.

-   [Brama sieci wirtualnej i rozwiązywanie problemów z połączenia](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) — umożliwia rozwiązywanie problemów z bramy sieci wirtualnej i połączenia.

-   [Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) — umożliwia wyświetlenie wykorzystania zasobów sieci ograniczeń.

### <a name="application-insight"></a>Szczegółowe informacje o aplikacji

[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) jest rozszerzalną usługę zarządzania wydajności aplikacji (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji sieci Web na żywo. Jest automatycznie wykrywania anomalii wydajności. Obejmuje ona zaawansowane narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć sposób korzystania z aplikacji przez użytkowników.

 Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

 Działa w przypadku aplikacji na różnych platformach, w tym .NET, Node.js i J2EE, hostowanych lokalnie lub w chmurze. Integruje się z procesu opracowywania oprogramowania, a ma punkty połączenia do różnych narzędzi programistycznych.

![Szczegółowe informacje o aplikacji](./media/azure-log-audit/azure-log-audit-fig6.png)

Usługa Application Insights jest przeznaczona dla zespołu deweloperów po to, aby pomóc zrozumieć, jak działa Twoja aplikacja i jak jest używana. Monitoruje ona:

-   **Liczby żądań, czasy reakcji i współczynniki błędów** — dowiedz się, które strony są najbardziej popularne, o jakiej porze dnia i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy odpowiedzi i częstotliwości awarii są duże, gdy jest więcej żądań, być może masz problem z zasobami.

-   **Współczynniki zależności, czasy reakcji i współczynniki błędów** — dowiedz się, czy usługi zewnętrzne nie spowalniają pracy.

-   **Wyjątki** — analizy zagregowanych danych statystycznych, lub wybierz określone wystąpienia i przejść do szczegółów w powiązanych żądań i ślad stosu. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

-   **Wydajność ładowania i wyświetleń stron** — zgłoszona przez przeglądarki użytkowników.

-   **Wywołania AJAX** ze stron sieci Web — liczba, czasy reakcji i współczynniki błędów.

-   **Liczby użytkowników i sesji**.

-   **Liczniki wydajności** z serwerów systemu Windows lub Linux, takie jak użycie procesora CPU, pamięci i sieci.

-   **Diagnostyka hosta** z platformy Docker lub Azure.

-   **Diagnostyczne dzienniki śledzenia** z Twojej aplikacji — dzięki temu możesz skorelować zdarzenia śledzenia z żądaniami.

-   **Niestandardowe zdarzenia i metryki**, które samodzielnie zapisujesz w kodzie klienta lub serwera, do śledzenia zdarzeń biznesowych, takich jak sprzedane towary lub wygrane gry.

**Listę scenariuszy integracji i opis:**

| Scenariusze integracji | Opis |
| --------------------- | :---------- |
|[Mapowanie aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Składniki Twojej aplikacji wraz z kluczowymi metrykami i alertami.||
|[Diagnostycznych wyszukiwania danych dla wystąpienia](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Wyszukiwanie i filtrowanie zdarzeń, takich jak żądania, wyjątki, wywołania zależności, dzienniki śledzenia i wyświetlenia stron.||
|[Eksploratora metryk na zastosowanie zagregowanych danych](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Eksploruj, filtruj i segmentuj zagregowane dane, takie jak liczby żądań, błędów i wyjątków, czasy reakcji, czasy ładowania stron.||
|[Pulpity nawigacyjne](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Połącz dane z wielu zasobów i udostępnij innym osobom. Opcja ta doskonale nadaje się dla aplikacji wieloskładnikowych i ciągłego wyświetlania w pomieszczeniu zespołu.||
|[Metryki strumień na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Podczas wdrażania nowej kompilacji obejrzyj te wskaźniki wydajności prawie w czasie rzeczywistym, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.||
|[Analiza](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Odpowiedz na trudne pytania dotyczące wydajności i użycia Twojej aplikacji za pomocą tego zaawansowanego języka zapytań.||
|[Alerty automatycznej i ręcznej](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Alerty automatyczne dostosowują się do normalnych wzorców telemetrii Twojej aplikacji i są wyzwalane, gdy wystąpi coś poza zwykłym wzorcem. Możesz też ustawić alerty dla konkretnych poziomów metryk niestandardowych lub standardowych.||
|[Program Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Zobacz dane dotyczące wydajności w kodzie. Przejdź do kodu ze śladów stosu.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integruje metryki użycia z innymi analizami biznesowymi.||
|[Interfejs API REST](https://dev.applicationinsights.io/)|Napisz kod, aby uruchamiać zapytania dla swoich metryk i danych pierwotnych.||
|[Eksport ciągły](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Zbiorcze eksportu nieprzetworzone dane do magazynu po dostarczeniu.||

### <a name="azure-security-center-alerts"></a>Alerty Centrum zabezpieczeń Azure
[Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci i połączonych rozwiązań partnerskich, takich jak zapory i punktu końcowego rozwiązań do ochrony, aby wykrywać prawdziwe zagrożenia i zmniejszyć wartość false alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

Wykrywanie zagrożeń za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych z zasobów platformy Azure, sieci i powiązanych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Osiągnięć w dużej ilości danych i [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologii są stosowane do oceny zdarzeń w sieci szkieletowej chmury całego — wykrywanie zagrożenia, które nie można wysyłać do identyfikacji przy użyciu metod ręcznych i prognozowanie ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

-   **Zintegrowane analizy zagrożeń:** szuka znanych nieupoważnione osoby, stosując globalnej analizy zagrożeń z produktów firmy Microsoft i usług, jednostki ds. przestępstw cyfrowych (DCU) firmy Microsoft, Microsoft Security odpowiedzi Center (MSRC) i zewnętrznych źródeł danych.

-   **Analizy behawioralnej:** stosuje znane wzorce do wykrywania złośliwego zachowania.

-   **Wykrywanie anomalii:** korzysta statystyczne profilowania w celu skompilowania historycznych linii bazowej. Narzędzie to alarmuje o odchyleniach od ustalonych linii bazowych zgodnych z wektorem potencjalnego ataku.


Wiele operacji zabezpieczeń i odpowiedzi na zdarzenia zespołów zależne rozwiązania Security Information and Event Management (SIEM) jako punkt początkowy klasyfikowane i badanie alertów zabezpieczeń. Dzięki integracji dzienników Azure klienci mogą synchronizować alerty Centrum zabezpieczeń i zdarzenia zabezpieczeń maszyny wirtualnej, zebrane przez diagnostyki Azure i dzienników inspekcji platformy Azure, z ich analizy dzienników lub rozwiązania SIEM w najbliższym czasie rzeczywistym.


## <a name="log-analytics"></a>Log Analytics

Analiza dzienników jest usługa na platformie Azure, która ułatwia zbieranie i analizowanie danych wygenerowanych przez zasobów w chmurze i lokalnych środowiskach. Udostępnia wgląd w czasie rzeczywistym przy użyciu wyszukiwanie zintegrowane i niestandardowe pulpity nawigacyjne, aby łatwo analizować miliony rekordów we wszystkich obciążeń i serwerów, niezależnie od ich lokalizacji fizycznej.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

W Centrum Log Analytics jest obszar roboczy analizy dzienników, która jest hostowana w chmurze Azure. Dane są zbierane w obszarze roboczym z połączonych źródeł przez konfigurowanie źródeł danych i Dodawanie rozwiązania do subskrypcji. Źródła danych i rozwiązań każdego utworzy różnych typów rekordów własnych zbiór właściwości, które mogą być analizowane razem w zapytaniach do obszaru roboczego. Pozwala to korzystać z tych samych narzędzi i metod do pracy z różnymi rodzajami danych zbieranych przez różne źródła.

Połączone źródła to komputery i inne zasoby, które generują dane zbierane przez usługę Log Analytics. Może to obejmować agentów zainstalowanych na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) i [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) komputerów, które łączą się bezpośrednio lub agentów w [podłączonej grupy zarządzania programu System Center Operations Manager.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Analiza dzienników może również zbierać dane z [magazynu Azure.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)

[Źródła danych](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) to różne rodzaje danych zbieranych ze wszystkich połączonych źródeł. Obejmuje to zdarzenia i [dane dotyczące wydajności](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) z [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) i agentów systemu Linux oprócz źródeł, takich jak [dzienniki programu IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs), i [dzienniki tekstowe niestandardowych.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs) Każde źródło danych, które ma być zbierane, jest konfigurowane, a konfiguracja jest automatycznie dostarczana do każdego z nich.

Istnieją cztery różne sposoby [zbieranie dzienników i metryki dla usług Azure:](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Diagnostyka Azure bezpośrednio do analizy dzienników (Diagnostyka w tabeli poniżej)

2.  Diagnostyka Azure do magazynu Azure do analizy dzienników (magazynu w tabeli poniżej)

3.  Łączniki dla usług Azure (łączników w tabeli poniżej)

4.  Skrypty do zbierania danych do analizy dzienników (puste wartości w poniższej tabeli oraz usług, które nie są wymienione)

| Usługa | Typ zasobu | Dzienniki | Metryki | Rozwiązanie |
| :------ | :------------ | :--- | :------ | :------- |
|Bramy aplikacji|  Microsoft.Network/<br>applicationGateways|  Diagnostyka|Diagnostyka|    [Aplikacja Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Łącznik|  Łącznik|  [Usługa Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connector (wersja zapoznawcza)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Konta usługi Automation|   Microsoft.Automation/<br>AutomationAccounts|    Diagnostyka||       [Więcej informacji](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Konta usługi Batch|    Microsoft.Batch/<br>batchAccounts|  Diagnostyka|    Diagnostyka||
|Usługi w chmurze klasycznego||       Magazyn||       [Więcej informacji](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Usługi poznawcze|    Microsoft.CognitiveServices/<br>accounts|       Diagnostyka|||
|Data Lake analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostyka|||
|Data Lake store|   Microsoft.DataLakeStore/<br>accounts|   Diagnostyka|||
|Przestrzeń nazw centrum zdarzeń|   Microsoft.EventHub/<br>Przestrzenie nazw|  Diagnostyka|    Diagnostyka||
|Centra IoT Hub|  Microsoft.Devices/<br>IotHubs||     Diagnostyka||
|Usługa Key Vault| Microsoft.KeyVault/<br>magazynów|  Diagnostyka  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Moduły równoważenia obciążenia|    Microsoft.Network/<br>loadBalancers|    Diagnostyka|||
|Logic Apps|    Microsoft.Logic/<br>Przepływy pracy|  Diagnostyka|    Diagnostyka||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupy zabezpieczeń sieci|   Microsoft.Network/<br>networksecuritygroups|Diagnostyka||   [Grupy zabezpieczeń sieci Azure analityka](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Magazyny odzyskiwania|   Microsoft.RecoveryServices/<br>magazynów|||[Usługa Azure Recovery usługi Analytics (wersja zapoznawcza)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Usługi wyszukiwania|   Microsoft.Search/<br>searchServices|    Diagnostyka|    Diagnostyka||
|Przestrzeń nazw magistrali usług| Microsoft.ServiceBus/<br>Przestrzenie nazw|    Diagnostyka|Diagnostyka|    [Analiza magistrali usług (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Magazyn||    [Usługa sieci szkieletowej Analytics (wersja zapoznawcza)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>serwery /<br>bazy danych||       Diagnostyka||
||Microsoft.Sql/<br>serwery /<br>elasticPools||||
|Magazyn|||         Skrypt| [Usługa Azure Storage Analytics (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Maszyny wirtualne|  Microsoft.Compute/<br>maszyn wirtualnych|  Wewnętrzny|  Wewnętrzny||
||||Diagnostyka||
|Zestawy skalowania maszyn wirtualnych|   Microsoft.Compute/<br>maszyn wirtualnych    ||Diagnostyka||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>maszyn wirtualnych||||
|Farmach serwerów sieci Web|Microsoft.Web/<br>serverfarms||   Diagnostyka
|Witryny sieci Web| Microsoft.Web/<br>Lokacje ||      Diagnostyka|    [Więcej informacji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>Lokacje /<br>gniazda|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Dziennik integracji z lokalnymi systemów SIEM
[Integracja z dzienników Azure](https://www.microsoft.com/download/details.aspx?id=53324) pozwala na integrowanie nowych dzienników z zasobów platformy Azure w do lokalnej **systemów Security Information and Event Management (SIEM)**.

![Integracja dziennika](./media/azure-log-audit/azure-log-audit-fig9.png)

Integracja z dzienników Azure zbiera diagnostyki Azure z systemem Windows (WAD) maszyn wirtualnych Azure Dzienniki aktywności, alerty Centrum zabezpieczeń Azure i dzienniki dostawcy zasobów platformy Azure. Integracja ta zapewnia jednolity pulpit nawigacyjny dla wszystkich zasobów, lokalnie lub w chmurze, dzięki czemu może agregować, skorelowania, analizowanie i alertów zdarzeń zabezpieczeń.



Integracja dzienników Azure obsługuje obecnie integracji Dzienniki aktywności platformy Azure, dziennika zdarzeń systemu Windows z maszyny wirtualnej systemu Windows w Twojej subskrypcji platformy Azure, dzienniki inspekcji alerty Centrum zabezpieczeń Azure, dzienników diagnostycznych platformy Azure i usługi Azure Active Directory.

| Typ dziennika | Obsługa formatu JSON (Splunk, ArcSight, Qradar) analizy dzienników |
| :------- | :-------------------------------------------------------- |
|Dzienniki inspekcji usługi AAD|    tak|
|Dzienniki aktywności| Yes|
|Alerty ASC |Yes|
|Dzienniki diagnostyczne (Dzienniki zasobów)|  Yes|
|Dzienniki maszyny Wirtualnej|   Tak, za pośrednictwem przekazane zdarzenia, a nie za pośrednictwem JSON|


W poniższej tabeli opisano dziennika kategorii i szczegóły integracji SIEM.

[Wprowadzenie do integracji dzienników Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) — samouczek przeprowadzi Cię przez kolejne instalacji integracji dzienników Azure i integrowanie dzienniki z magazynu Azure WAD, dzienniki aktywności platformy Azure, alerty Centrum zabezpieczeń Azure i usługi Azure Active Directory dzienniki inspekcji.

Scenariusze integracji

-   [Czynności konfiguracyjnych partnera](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) — ten wpis w blogu przedstawiono sposób konfigurowania integracji dzienników Azure do pracy z rozwiązań partnerskich Splunk HP ArcSight i IBM QRadar.

-   [Dzienników Azure — często zadawane pytania (FAQ) integracji](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) — to często zadawane pytania dotyczące odpowiedzi na pytania dotyczące integracji dzienników Azure.

-   [Integrowanie Centrum zabezpieczeń alertów z usługi Azure dziennika integracji](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) — tym dokumencie przedstawiono sposób synchronizować alerty Centrum zabezpieczeń, wraz z zebrane przez diagnostyki Azure i dzienników inspekcji platformy Azure, z Twojego analizy dzienników zdarzeń zabezpieczeń maszyny wirtualnej lub Rozwiązania SIEM.

## <a name="next-steps"></a>Następne kroki

- [Inspekcja i rejestrowanie](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Ochrona danych przy zachowaniu widoczności i szybko reagować na alerty zabezpieczeń odpowiednim

- [Rejestrowanie zabezpieczeń i kolekcji dziennika inspekcji w systemie Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Które ustawienia należy wymusić, aby upewnić się, że Twoje wystąpieniach platformy Azure są zbierane poprawnych zabezpieczeń i dzienniki inspekcji.

- [Skonfiguruj ustawienia inspekcji dla zbioru witryn](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Jako administrator zbioru witryn jeden można pobrać historii akcje wykonywane przez określonego użytkownika i może również pobierać historię działania podjęte w zakresie określonej daty. 

- [Wyszukaj w dzienniku inspekcji Office 365 zabezpieczeń & Centrum zgodności](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Jeden służy Office 365 zabezpieczeń & Centrum zgodności do wyszukiwania dziennik inspekcji ujednoliconego, aby wyświetlić aktywności użytkownika i administrator w organizacji usługi Office 365.


