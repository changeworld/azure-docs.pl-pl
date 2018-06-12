---
title: Dzienniki diagnostyczne platformy Azure | Dokumentacja firmy Microsoft
description: Klienta można włączyć analizy dziennika dla usługi Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.openlocfilehash: 98a7fc5c4607115811e17a7cf6acd4e867663833
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261308"
---
# <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure możesz wyświetlić podstawowa analiza i zapisać je w co najmniej jednego miejsca docelowe, w tym:

 - Konto usługi Azure Storage
 - Azure Event Hubs
 - [Obszar roboczy analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ta funkcja jest dostępna dla punktów końcowych usługi CDN dla wszystkich warstw cenowych. 

Dzienniki diagnostyczne platformy Azure umożliwiają eksportowania metryki użycia podstawowe z punktu końcowego CDN do różnych źródeł, dzięki czemu będzie można korzystać dostosowany sposób. Na przykład można wykonać następujące typy eksportu danych:

- Eksportuj dane do magazynu obiektów blob, Eksportuj do pliku CSV i Generowanie wykresów w programie Excel.
- Eksportuj dane do usługi Event Hubs i skorelowania danych z innymi usługami Azure.
- Eksportuj dane do analizy dzienników i wyświetlanie danych w swojego obszaru roboczego analizy dzienników

Na poniższym diagramie przedstawiono typowe analytics core CDN widoku danych.

![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Rysunek 1 — widok analytics core CDN*

Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Włącz rejestrowanie w portalu Azure

Wykonaj te kroki Włącz, rejestrowanie w sieci CDN w warstwie podstawowa analiza:

Zaloguj się w [Portalu Azure](http://portal.azure.com). Jeśli nie już włączono CDN przepływu pracy, [Tworzenie usługi Azure CDN profilu i punktu końcowego](cdn-create-new-endpoint.md) przed kontynuowaniem.

1. W portalu Azure, przejdź do **profilu CDN**.

2. W portalu Azure Wyszukaj profilu CDN, lub wybierz ją z pulpitu nawigacyjnego. Następnie wybierz punkt końcowy CDN, dla której chcesz włączyć dzienniki diagnostyczne.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Wybierz **dzienników diagnostycznych** w sekcji monitorowanie.

   **Dzienników diagnostycznych** zostanie wyświetlona strona.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Włącz rejestrowanie z usługą Azure Storage

Aby użyć konta magazynu do przechowywania dzienników, wykonaj następujące kroki:
    
1. Aby uzyskać **nazwa**, wprowadź nazwę dla ustawienia dziennika diagnostycznego.
 
2. Wybierz **archiwum na konto magazynu**, a następnie wybierz pozycję **CoreAnalytics**. 

2. Aby uzyskać **przechowywania (dni)**, wybierz liczbę dni przechowywania. Przechowywanie zero dni są przechowywane dzienniki nieskończoność. 

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Wybierz **konta magazynu**.

    **Wybierz konto magazynu** zostanie wyświetlona strona.

4. Wybierz konto magazynu z listy rozwijanej, a następnie wybierz **OK**.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po dokonaniu ustawień dziennika diagnostycznego wybierz **zapisać**.

### <a name="logging-with-log-analytics"></a>Rejestrowanie z analizy dzienników

Aby użyć analizy dzienników do przechowywania dzienników, wykonaj następujące kroki:

1. Z **dzienników diagnostycznych** wybierz pozycję **wysyłać do analizy dzienników**. 

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Wybierz **Konfiguruj** Aby skonfigurować rejestrowanie analizy dzienników. 

   **Obszarów roboczych OMS** zostanie wyświetlona strona.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Wybierz **Utwórz nowy obszar roboczy**.

    **Obszarem roboczym pakietu OMS** zostanie wyświetlona strona.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/07_Create-new.png)

4. Aby uzyskać **obszarem roboczym pakietu OMS**, wprowadź nazwę obszarem roboczym pakietu OMS. Nazwa obszarem roboczym pakietu OMS musi być unikatowa i zawierać tylko litery, cyfry i łączniki; spacje i znaki podkreślenia są niedozwolone. 

5. Aby uzyskać **subskrypcji**, wybierz istniejącą subskrypcję z listy rozwijanej. 

6. Aby uzyskać **grupy zasobów**, Utwórz nową grupę zasobów lub wybierz istniejący.

7. Aby uzyskać **lokalizacji**, wybierz lokalizację z listy.

8. Wybierz **Przypnij do pulpitu nawigacyjnego** Jeśli chcesz zapisać konfigurację dziennika do pulpitu nawigacyjnego. 

9. Wybierz **OK** w celu ukończenia konfiguracji.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/08_Workspace-resource.png)

10. Po utworzeniu obszaru roboczego, nastąpi powrót do **dzienniki diagnostyczne** strony. Upewnij się, nazwę nowego obszaru roboczego analizy dzienników.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Wybierz **CoreAnalytics**, a następnie wybierz pozycję **zapisać**.

12. Aby wyświetlić nowy obszar roboczy analizy dzienników, wybierz **podstawowe analytics** ze strony punktu końcowego CDN.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Obszar roboczy analizy dzienników jest teraz gotowy do rejestrowania danych. Aby można było korzystać z tych danych, należy użyć [rozwiązania analizy dziennika](#consuming-diagnostics-logs-from-a-log-analytics-workspace), wymienionych w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat opóźnienia danych dziennika, zobacz [dziennika opóźnienia danych](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Włącz rejestrowanie przy użyciu programu PowerShell

Poniższy przykład przedstawia Włączanie dzienników diagnostycznych za pomocą poleceń cmdlet programu Azure PowerShell.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Włączanie diagnostyki logowania na koncie magazynu

1. Zaloguj się i wybierz subskrypcję:

    Connect-AzureRmAccount 

    Wybierz AzureSubscription — identyfikator subskrypcji 

2. Aby włączyć dzienniki diagnostyczne na koncie magazynu, wprowadź polecenie:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Aby włączyć dzienniki diagnostyki w obszarze roboczym analizy dzienników, wprowadź polecenie:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Korzystanie z dzienników diagnostycznych z usługi Azure Storage
W tej sekcji opisano schematu sieci CDN w warstwie podstawowa analiza, jak jest zorganizowana w ramach konta magazynu platformy Azure i udostępnia przykładowy kod, aby pobrać dzienniki w pliku CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Za pomocą Eksploratora usługi Storage platformy Microsoft Azure
Przed uzyskujesz dostęp do podstawowych danych analytics z kontem magazynu platformy Azure, musisz najpierw narzędzia dostępu do zawartości na koncie magazynu. Gdy brak kilka narzędzi dostępnych na rynku, zaleca się jest Eksploratora magazynu Microsoft Azure. Aby pobrać to narzędzie, zobacz [Eksploratora usługi Storage Azure](http://storageexplorer.com/). Po pobraniu i zainstalowaniu oprogramowania, należy skonfigurować go do używania tego samego konta magazynu platformy Azure, który został skonfigurowany jako miejsce docelowe w dziennikach Diagnostyka sieci CDN.

1.  Otwórz **Eksploratora usługi Storage platformy Microsoft Azure**
2.  Zlokalizuj konto magazynu
3.  Rozwiń węzeł **kontenerów obiektów Blob** węzła przy użyciu tego konta magazynu.
4.  Wybierz kontener o nazwie *insights dzienniki coreanalytics*.
5.  Powoduje Pokaż się w okienku po prawej stronie, rozpoczynając od pierwszego poziomu jako *resourceId =*. Kontynuuj wybieranie każdy poziom, dopóki nie można odnaleźć pliku *PT1H.json*. Zobacz następujące tematy *format ścieżki obiektu Blob* należy pamiętać, aby uzyskać informacje o ścieżce.
6.  Każdy obiekt blob *PT1H.json* plik reprezentuje dzienniki analizy przez godzinę dla określonego punktu końcowego CDN lub jego domeny niestandardowej.
7.  Schemat zawartość tego pliku JSON jest opisany w schemacie sekcji dzienników analytics core.


> [!NOTE]
> **Format ścieżki obiektu blob**
> 
> Dzienniki analytics Core są generowane co godzinę i dane są zbierane i przechowywane wewnątrz jednej Azure blob jako ładunek JSON. Ponieważ narzędzie Eksploratora magazynu interpretuje "/" jako separator katalogu i pokazuje hierarchii, ścieżka do obiektów blob platformy Azure jest wyświetlana tak, jakby to hierarchiczna struktura i reprezentuje nazwę obiektu blob. Nazwa obiektu blob obejmuje następującą konwencją nazewnictwa: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Opis pola:**

|Wartość|Opis|
|-------|---------|
|Identyfikator subskrypcji    |Identyfikator subskrypcji platformy Azure w formacie Guid.|
|Nazwa grupy zasobów |Nazwa grupy zasobów, do której należą zasoby sieci CDN.|
|Nazwa profilu |Nazwa profilu CDN|
|Nazwa punktu końcowego |Nazwa punktu końcowego CDN|
|Rok|  Reprezentacja czterocyfrowy rok, na przykład 2017|
|Miesiąc| Reprezentacja dwucyfrowe numer miesiąca. 01 stycznia =... 12 grudnia =|
|Dzień|   Reprezentacja dwucyfrowe dnia miesiąca|
|PT1H.json| Rzeczywisty plik JSON, gdzie są przechowywane dane analityczne|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Eksportowanie danych analytics core do pliku CSV

Aby ułatwić dostęp podstawowa analiza podano przykładowy kod dla narzędzia. To narzędzie umożliwia pobieranie plików JSON na format pliku płaskim przecinkami, która może służyć do tworzenia schematów i innych agregacji.

Oto, jak można użyć narzędzia:

1.  Skorzystaj z łącza w witrynie github: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Pobierz kod.
3.  Postępuj zgodnie z instrukcjami, aby skompilować i skonfigurować.
4.  Uruchom narzędzie.
5.  Wynikowy plik CSV zawiera dane analityczne w prosty płaskiej hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Korzystanie z dzienników diagnostycznych z obszaru roboczego analizy dzienników
Analiza dzienników jest usługą platformy Azure, który monitoruje chmurze i lokalnych środowiskach utrzymywać ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł. 

Aby korzystać z analizy dzienników, należy najpierw [włączyć rejestrowanie](#enable-logging-with-azure-storage) do obszaru roboczego analizy dzienników Azure, które omówione w tym artykule.

### <a name="using-the-log-analytics-workspace"></a>Korzystanie z obszaru roboczego analizy dzienników

 Na poniższym diagramie przedstawiono architekturę danych wejściowych i wyjściowych repozytorium:

![Obszar roboczy usługi Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Rysunek 3 – repozytorium analizy dzienników*

Dane można wyświetlić w na różne sposoby, za pomocą rozwiązania do zarządzania. Można uzyskać z rozwiązania do zarządzania [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Rozwiązania do zarządzania można zainstalować z portalu Azure marketplace, wybierając **Pobierz teraz** łącze u dołu każdego z rozwiązań.

### <a name="add-a-log-analytics-cdn-management-solution"></a>Dodaj rozwiązanie do zarządzania CDN analizy dzienników

Wykonaj następujące kroki, aby dodać rozwiązanie do zarządzania analizy dziennika:

1.   Zaloguj się do portalu Azure przy użyciu subskrypcji platformy Azure i przejdź do pulpitu nawigacyjnego.
    ![Pulpit nawigacyjny platformy Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. W **nowy** w obszarze **Marketplace**, wybierz pozycję **monitorowanie i zarządzanie**.

    ![Portal Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. W **monitorowanie i zarządzanie** wybierz pozycję **zobaczyć wszystkie**.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/15_See-all.png)

4. Wyszukiwanie sieci CDN w polu wyszukiwania.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/16_Search-for.png)

5. Wybierz **usługi Azure CDN podstawowa analiza**. 

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po wybraniu **Utwórz**, zostanie wyświetlona prośba o utworzenie nowego obszaru roboczego analizy dzienników lub użyć istniejącego. 

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Wybierz obszar roboczy utworzony przed. Następnie należy dodać konto usługi Automatyzacja.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Następujący ekran pokazuje musisz wypełnić formularz konta automatyzacji. 

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/20_Automation.png)

9. Po utworzeniu konta automatyzacji, można przystąpić do dodawania rozwiązania. Wybierz przycisk **Utwórz**.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/21_Ready.png)

10. Teraz dodano rozwiązania do swojego obszaru roboczego. Wróć do pulpitu nawigacyjnego portalu Azure.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/22_Dashboard.png)

    Wybierz obszar roboczy analizy dzienników, do którego został utworzony, aby przejść do obszaru roboczego. 

11. Wybierz **portalu OMS** Kafelek, aby zobaczyć nowe rozwiązania.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/23_workspace.png)

12. Portalem powinna wyglądać tak jak następujący ekran:

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Wybierz jeden z fragmentów, aby wyświetlić kilka widoków danych.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/25_Interior-view.png)

    Po lewej lub prawej strony, aby wyświetlić dalsze Kafelki reprezentujących poszczególnych widoków do danych mogą być przewijane. 

    Wybierz jedną z Kafelki, aby dowiedzieć się więcej o danych.

     ![Zobacz wszystkie](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Oferty i warstw cenowych

Możesz wyświetlać oferty i warstw cenowych dla rozwiązań do zarządzania [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Dostosowywanie widoków

Widok danych można dostosować za pomocą **Widok projektanta**. Aby rozpocząć projektowanie, przejdź do obszaru roboczego analizy dzienników i wybierz **Widok projektanta** kafelka.

![Projektant widoków](./media/cdn-diagnostics-log/27_Designer.png)

Przeciągnij i upuść typów wykresów i wypełnij dane szczegółów do przeanalizowania.

![Projektant widoków](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Opóźnienia dane dziennika

W poniższej tabeli przedstawiono dziennika opóźnień danych **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, i **Azure CDN Standard/Premium from Verizon**.

Opóźnienia danych dziennika firmy Microsoft | Opóźnienia danych dziennika Verizon | Opóźnienia danych dziennika Akamai
--- | --- | ---
Opóźnione o 1 godzinę. | Opóźnione przez godzinę i może potrwać maksymalnie 2 godziny, aby uruchomić pojawiające się po zakończeniu propagowania punktu końcowego. | Opóźnione przez 24 godziny; Jeśli został on utworzony więcej niż 24 godziny temu, może potrwać maksymalnie 2 godziny Rozpocznij wyświetlaniu. Jeśli niedawno został utworzony, może upłynąć do 25 godzin dzienniki, aby uruchomić wyświetlaniu.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy dzienników diagnostycznych do sieci CDN w warstwie podstawowa analiza

Firma Microsoft oferuje obecnie core analytics dzienniki, zawierające metryki wyświetlane statystki odpowiedzi HTTP i statystyki wyjście widziany od obecności CDN/krawędzi.

### <a name="core-analytics-metrics-details"></a>Szczegóły metryki analytics Core
W poniższej tabeli przedstawiono listę dostępnych w rdzeniu metryk analizy dzienników dla **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, i **Azure CDN Standard/Premium FROM Verizon**. Nie wszystkie metryki są dostępne z wszystkich dostawców, choć różnice są minimalne. Tabela także wskazuje, czy dana metryka jest dostępna od dostawcy. Metryki są dostępne dla tylko tych punktów końcowych usługi CDN których ruchu.


|Metryka                     | Opis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Całkowita liczba trafień żądania, w tym okresie. | Yes | Yes |Yes |
| RequestCountHttpStatus2xx | Liczba wszystkich żądań, które wywołały kod HTTP 2xx (na przykład 200, 202). | Yes | Yes |Yes |
| RequestCountHttpStatus3xx | Liczba wszystkich żądań, które wywołały kod HTTP 3xx (na przykład, 300, 302). | Yes | Yes |Yes |
| RequestCountHttpStatus4xx | Liczba wszystkich żądań, które wywołały kod HTTP 4xx (na przykład, 400, 404). | Yes | Yes |Yes |
| RequestCountHttpStatus5xx | Liczba wszystkich żądań, które wywołały kod HTTP 5xx (na przykład, 500, 504). | Yes | Yes |Yes |
| RequestCountHttpStatusOthers | Liczba innych kodów HTTP (poza 2xx 5xx). | Yes | Yes |Yes |
| RequestCountHttpStatus200 | Liczba wszystkich żądań, które spowodowało 200 kod odpowiedzi HTTP. | Yes | Nie  |Yes |
| RequestCountHttpStatus206 | Liczba wszystkich żądań, które wywołały kod odpowiedź HTTP 206. | Yes | Nie  |Yes |
| RequestCountHttpStatus302 | Liczba wszystkich żądań, które spowodowało 302 kod odpowiedzi HTTP. | Yes | Nie  |Yes |
| RequestCountHttpStatus304 | Liczba wszystkich żądań, które zakończyły się odpowiedź 304 kodu HTTP. | Yes | Nie  |Yes |
| RequestCountHttpStatus404 | Liczba wszystkich żądań, które wywołały kod odpowiedzi HTTP 404. | Yes | Nie  |Yes |
| RequestCountCacheHit | Liczba wszystkich żądań, które spowodowało pamięci podręcznej odwołań. Element zawartości zostało obsłużone bezpośrednio z punktu obecności do klienta. | Yes | Yes | Nie  |
| RequestCountCacheMiss | Liczba wszystkich żądań, które spowodowało Chybienie pamięci podręcznej. Chybienia pamięci podręcznej oznacza, że zasób nie został znaleziony na POP najbliżej klienta i w związku z tym nie została pobrana z punktu początkowego. | Yes | Yes | Nie |
| RequestCountCacheNoCache | Liczba wszystkich żądań do zasobu, uniemożliwiających w pamięci podręcznej z powodu konfiguracji użytkownika na krawędzi. | Yes | Yes | Nie |
| RequestCountCacheUncacheable | Liczba wszystkich żądań do zasobów, które uniemożliwiały buforowana przez Cache-Control elementu zawartości i nagłówków wygasa, wskazujące, że go mają nie być buforowane punktu obecności lub przez klienta HTTP. | Yes | Yes | Nie |
| RequestCountCacheOthers | Liczba wszystkich żądań o stanie pamięci podręcznej nie jest objęty przez powyżej. | Nie | Yes | Nie  |
| EgressTotal | Transfer danych wychodzących w GB | Yes |Yes |Yes |
| EgressHttpStatus2xx | Danych wychodzących transfer * odpowiedzi z kodów stanu HTTP 2xx w GB. | Yes | Yes | Nie  |
| EgressHttpStatus3xx | Transfer danych wychodzących dla odpowiedzi z kodów stanu HTTP 3xx w GB. | Yes | Yes | Nie  |
| EgressHttpStatus4xx | Transfer danych wychodzących dla odpowiedzi z kodów stanu HTTP 4xx w GB. | Yes | Yes | Nie  |
| EgressHttpStatus5xx | Transfer danych wychodzących dla odpowiedzi z kodów stanu HTTP 5xx w GB. | Yes | Yes | Nie |
| EgressHttpStatusOthers | Transfer danych wychodzących dla odpowiedzi o innych kodach stanów HTTP w GB. | Yes | Yes | Nie  |
| EgressCacheHit | Wychodzący transfer danych odpowiedzi, które zostały dostarczone bezpośrednio z pamięci podręcznej CDN CDN POP/krawędzi. | Yes | Yes | Nie |
| EgressCacheMiss. | Transfer danych wychodzących dla odpowiedzi, które nie zostały znalezione na najbliższy serwer protokołu POP i pobrać z serwera pochodzenia. | Yes | Yes | Nie |
| EgressCacheNoCache | Transfer danych wychodzących dla zasobów, które uniemożliwiały pamięci podręcznej z powodu konfiguracji użytkownika na krawędzi. | Yes | Yes | Nie |
| EgressCacheUncacheable | Transfer danych wychodzących dla zasobów, które uniemożliwiały buforowana przez element zawartości Cache-Control lub Expires headers. Wskazuje, czy jego mają nie być buforowane punktu obecności lub przez klienta HTTP. | Yes | Yes | Nie |
| EgressCacheOthers | Transfery danych wychodzących w innych sytuacjach pamięci podręcznej. | Nie | Yes | Nie |

* Transfer danych wychodzących odwołuje się do ruchu dostarczane z serwerów POP w sieci CDN do klienta.


### <a name="schema-of-the-core-analytics-logs"></a>Schemat core analizy dzienników 

Wszystkie dzienniki są przechowywane w formacie JSON i każdy wpis ma pól ciągów według następującego schematu:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Gdzie *czasu* reprezentuje czas rozpoczęcia granic godzinę, dla którego jest raportowane dane statystyczne. Gdy metryki nie jest obsługiwany przez dostawcę sieci CDN w warstwie, a nie wartość o podwójnej precyzji lub liczba całkowita jest wartością null. Ta wartość null wskazuje brak metryki i różni się od wartości 0. Istnieje jeden zestaw te metryki dla domeny skonfigurowany w punkcie końcowym.

Przykład właściwości:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Podstawowa analiza uzupełniające portalu usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Program Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Analiza dzienników Azure interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics)







