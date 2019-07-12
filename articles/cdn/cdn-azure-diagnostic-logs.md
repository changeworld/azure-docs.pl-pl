---
title: Dzienniki diagnostyczne platformy Azure | Dokumentacja firmy Microsoft
description: Klienta można włączyć analizy dzienników dla usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: 86696ed6715b4e43a9d02232c013eb64feb61f67
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594207"
---
# <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure możesz wyświetlić core analytics i zapisać je do jednego lub więcej miejsc docelowych, w tym:

 - Konto usługi Azure Storage
 - Azure Event Hubs
 - [Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ta funkcja jest dostępna na punktów końcowych usługi CDN dla wszystkich warstw cenowych. 

Dzienniki diagnostyczne platformy Azure pozwalają eksportu metryki użycia podstawowego punktu końcowego usługi CDN do różnych źródeł, dzięki czemu będzie można korzystać dostosowany sposób. Na przykład można wykonać następujące rodzaje Eksport danych:

- Eksportuj dane do magazynu obiektów blob, Eksportuj do pliku CSV i Generowanie wykresów w programie Excel.
- Eksportowanie danych do usługi Event Hubs i korelowanie danych z innymi usługami platformy Azure.
- Eksportuj dane do dzienników usługi Azure Monitor i wyświetlanie danych w własnego obszaru roboczego usługi Log Analytics

Na poniższym diagramie przedstawiono typowy CDN core analytics widok danych.

![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Rysunek 1 — widok analytics core sieci CDN*

Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Włączanie rejestrowania przy użyciu witryny Azure portal

Wykonaj te kroki Włącz, rejestrowanie za pomocą sieci CDN core analytics:

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie jest jeszcze mają włączone sieci CDN dla przepływu pracy [tworzenie wysokiej dostępności treści Azure profilu i punktu końcowego](cdn-create-new-endpoint.md) przed kontynuowaniem.

1. W witrynie Azure portal przejdź do **profil CDN**.

2. W witrynie Azure portal Wyszukaj profil usługi CDN, lub wybierz ją z poziomu pulpitu nawigacyjnego. Następnie wybierz punkt końcowy usługi CDN, dla którego chcesz włączyć dzienniki diagnostyczne.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Wybierz **dzienniki diagnostyczne** w sekcji monitorowanie.

   **Dzienniki diagnostyczne** zostanie wyświetlona strona.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Włączanie rejestrowania w usłudze Azure Storage

Aby użyć konta magazynu do przechowywania dzienników, wykonaj następujące kroki:
    
1. Aby uzyskać **nazwa**, wprowadź nazwę dla ustawienia dziennika diagnostycznego.
 
2. Wybierz **Zarchiwizuj na koncie magazynu**, a następnie wybierz **CoreAnalytics**. 

2. Aby uzyskać **przechowywania (dni)** , wybierz liczbę dni przechowywania. Dzienniki na wpisanie wartości zero są przechowywane w nieskończoność. 

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Wybierz **konta magazynu**.

    **Wybierz konto magazynu** zostanie wyświetlona strona.

4. Wybierz konto magazynu z listy rozwijanej, a następnie wybierz **OK**.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po wprowadzeniu ustawień dziennika diagnostycznego, wybierz **Zapisz**.

### <a name="logging-with-azure-monitor"></a>Rejestrowanie za pomocą usługi Azure Monitor

Aby użyć usługi Azure Monitor do przechowywania dzienników, wykonaj następujące kroki:

1. Z **dzienniki diagnostyczne** wybierz opcję **wysyłanie do usługi Log Analytics**. 

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Wybierz **Konfiguruj** Aby skonfigurować rejestrowanie dla usługi Azure Monitor. 

   **Obszarów roboczych usługi Log Analytics** zostanie wyświetlona strona.

    >[!NOTE] 
    >Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Wybierz **Utwórz nowy obszar roboczy**.

    **Obszaru roboczego usługi Log Analytics** zostanie wyświetlona strona.

    >[!NOTE] 
    >Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/07_Create-new.png)

4. Aby uzyskać **obszaru roboczego usługi Log Analytics**, wprowadź nazwę obszaru roboczego usługi Log Analytics. Nazwa obszaru roboczego usługi Log Analytics musi być unikatowa i zawierać tylko litery, cyfry i łączniki; spacje i znaki podkreślenia są niedozwolone. 

5. Aby uzyskać **subskrypcji**, wybierz istniejącą subskrypcję z listy rozwijanej. 

6. Aby uzyskać **grupy zasobów**, Utwórz nową grupę zasobów lub wybierz istniejącą grupę.

7. Aby uzyskać **lokalizacji**, wybierz lokalizację z listy.

8. Wybierz **Przypnij do pulpitu nawigacyjnego** Jeśli chcesz zapisać konfigurację dziennika na pulpicie nawigacyjnym. 

9. Wybierz **OK** aby zakończyć konfigurację.

10. Po utworzeniu obszaru roboczego, następuje powrót do **dzienniki diagnostyczne** strony. Potwierdź nazwę nowego obszaru roboczego usługi Log Analytics.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Wybierz **CoreAnalytics**, a następnie wybierz **Zapisz**.

12. Zaznacz, aby wyświetlić nowy obszar roboczy usługi Log Analytics **analiza podstawowa** ze strony punktu końcowego usługi CDN.

    ![Portal — dzienniki diagnostyczne](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Obszar roboczy usługi Log Analytics jest teraz gotowy do dane dziennika. Aby można było używać tych danych, należy użyć [usługi Azure Monitor dzienniki rozwiązania](#consuming-diagnostics-logs-from-a-log-analytics-workspace), pokryte w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat opóźnienia danych dziennika zobacz [dziennika danych opóźnień](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Poniższy przykład pokazuje, jak włączyć dzienniki diagnostyczne za pośrednictwem poleceń cmdlet programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Włączanie diagnostyki dzienniki na koncie magazynu

1. Zaloguj się i wybierz subskrypcję:

    Połącz AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Aby włączyć dzienniki diagnostyczne na koncie magazynu, wpisz to polecenie:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Aby włączyć dzienniki diagnostyczne, w obszarze roboczym usługi Log Analytics, wprowadź to polecenie:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Korzystanie z dzienników diagnostycznych z usługi Azure Storage
W tej sekcji opisano schemat CDN core analytics, jak jest zorganizowana w ramach konta usługi Azure storage i zawiera przykładowy kod, aby pobrać dzienniki w pliku CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Za pomocą Eksploratora usługi Storage platformy Microsoft Azure
Aby korzystać z danych analizy podstawowych, z konta usługi Azure storage, musisz najpierw narzędzie dostępu do zawartości w ramach konta magazynu. Istnieją różne narzędzia dostępne na rynku, który firma Microsoft zaleca jest Microsoft Azure Storage Explorer. Aby pobrać to narzędzie, zobacz [Eksploratora usługi Azure Storage](https://storageexplorer.com/). Po pobraniu i zainstalowaniu oprogramowania, należy skonfigurować go do używania tego samego konta magazynu platformy Azure, który został skonfigurowany jako miejsce docelowe do dzienników diagnostycznych usługi CDN.

1.  Otwórz **Eksploratora usługi Storage platformy Microsoft Azure**
2.  Znajdź konto magazynu
3.  Rozwiń **kontenery obiektów Blob** węzła w ramach tego konta magazynu.
4.  Wybierz kontener o nazwie *insights — dzienniki coreanalytics*.
5.  Wyniki wskazują się w okienku po prawej stronie, rozpoczynając od pierwszego poziomu jako *resourceId =* . Kontynuuj wybieranie każdy poziom, aż znajdziesz plik *PT1H.json*. Opis ścieżka zawiera [format ścieżki obiektu Blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Każdy obiekt blob *PT1H.json* plik reprezentuje dzienniki analiz za jedną godzinę dla określonego punktu końcowego usługi CDN lub jego domeny niestandardowej.
7.  Schemat zawartości tego pliku JSON jest opisana w sekcji schemat dzienniki analiz core.


#### <a name="blob-path-format"></a>Format ścieżki obiektu blob

Dzienniki analiz Core są generowane co godzinę, a dane są zbierane i przechowywane wewnątrz pojedynczego usługi Azure blob jako ładunek JSON. Ponieważ narzędzie Eksplorator magazynu interpretuje "/" jako separator katalogu i przedstawia hierarchii, ścieżka do obiektów blob platformy Azure pojawi się tak, jakby to hierarchiczna struktura i reprezentuje nazwę obiektu blob. Następująca Konwencja nazewnictwa następuje nazwa obiektu blob:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Opis pola:**

|Value|Opis|
|-------|---------|
|Identyfikator subskrypcji    |Identyfikator subskrypcji platformy Azure, w formacie identyfikatora Guid.|
|Nazwa grupy zasobów |Nazwa grupy zasobów, do której należą zasoby usługi CDN.|
|Nazwa profilu |Nazwa profilu CDN|
|Nazwa punktu końcowego |Nazwa punktu końcowego usługi CDN|
|Rok|  Czterocyfrowy reprezentacja roku, na przykład 2017 r.|
|Miesiąc| Reprezentacja dwucyfrowy numer miesiąca. 01 stycznia =... 12 grudnia =|
|Dzień|   Reprezentacja dwucyfrowy dzień miesiąca|
|PT1H.json| Rzeczywisty plik JSON, w której są przechowywane dane analizy|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Eksportowanie danych analitycznych podstawowych do pliku CSV

Aby ułatwić dostęp core analytics, znajduje się przykładowy kod dla narzędzia. To narzędzie umożliwia pobieranie plików JSON do formatu pliku płaskie rozdzielaną przecinkami, która może służyć do tworzenia wykresów lub innych agregacji.

Poniżej przedstawiono sposób korzystania z narzędzia:

1.  Skorzystaj z linku w usłudze GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Pobierz kod.
3.  Postępuj zgodnie z instrukcjami, aby skompilować i skonfigurować.
4.  Uruchom narzędzie.
5.  Wynikowy plik CSV zawiera dane analizy w prostych płaskiej hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Korzystanie z dzienników diagnostycznych z obszaru roboczego usługi Log Analytics
Usługa Azure Monitor jest usługą platformy Azure, która monitoruje środowiska chmurowe lokalnych i w celu zachowania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł. 

Aby korzystać z usługi Azure Monitor, należy najpierw [włączyć rejestrowanie](#enable-logging-with-azure-storage) do obszaru roboczego usługi Azure Log Analytics, które opisano wcześniej w tym artykule.

### <a name="using-the-log-analytics-workspace"></a>Korzystanie z obszaru roboczego usługi Log Analytics

 Na poniższym diagramie przedstawiono architekturę dane wejściowe i wyjściowe repozytorium:

![Obszar roboczy usługi log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Rysunek 3 - Log Analytics repozytorium*

Aby wyświetlić dane na różne sposoby, za pomocą rozwiązania do zarządzania. Możesz uzyskać rozwiązania do zarządzania z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Rozwiązania do monitorowania można zainstalować z witryny Azure marketplace, wybierając **Pobierz teraz** łącze u dołu każde z tych rozwiązań.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Dodaj rozwiązanie do monitorowania usługi Azure Monitor CDN

Wykonaj następujące kroki, aby dodać usługi Azure Monitor, rozwiązanie do monitorowania:

1.   Zaloguj się do witryny Azure portal przy użyciu subskrypcji platformy Azure i przejdź do pulpitu nawigacyjnego.
    ![Pulpit nawigacyjny platformy Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. W **New** w obszarze **Marketplace**, wybierz opcję **monitorowanie + zarządzanie**.

    ![Portal Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. W **monitorowanie + zarządzanie** wybierz opcję **holograficznych**.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/15_See-all.png)

4. Wyszukaj usługę CDN w polu wyszukiwania.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/16_Search-for.png)

5. Wybierz **sieć CDN systemu Azure Core Analytics**. 

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po wybraniu **Utwórz**, zostanie wyświetlony monit, aby utworzyć nowy obszar roboczy usługi Log Analytics lub użyj istniejącej. 

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Wybierz obszar roboczy, utworzonego wcześniej. Następnie należy dodać konto usługi automation.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Na poniższym ekranie przedstawiono formularz konta usługi automation, które należy wypełnić. 

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/20_Automation.png)

9. Po utworzeniu konta usługi automation można przystąpić do dodawania rozwiązania. Wybierz przycisk **Utwórz**.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/21_Ready.png)

10. Rozwiązania został dodany do obszaru roboczego. Wróć do pulpitu nawigacyjnego witryny Azure portal.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/22_Dashboard.png)

    Wybierz obszar roboczy usługi Log Analytics, który został utworzony, aby przejść do obszaru roboczego. 

11. Wybierz **portalu pakietu OMS** Kafelek, aby wyświetlić nowe rozwiązanie.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/23_workspace.png)

12. Twój portal powinna teraz wyglądać podobnie jak następujący ekran:

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Wybierz jeden z kafelków, aby zobaczyć kilka widoków danych.

    ![Zobacz wszystkie](./media/cdn-diagnostics-log/25_Interior-view.png)

    Można przewiniesz w lewo lub w prawo, aby wyświetlić Kafelki dalsze reprezentujących poszczególnych widoków danych. 

    Wybierz jeden z kafelków, aby zobaczyć więcej szczegółów dotyczących danych.

     ![Zobacz wszystkie](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Oferty i warstw cenowych

Widać, oferty i warstw cenowych dla rozwiązania do zarządzania [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Dostosowywanie widoków

Można dostosować widok w swoje dane za pomocą **Projektant widoków**. Aby rozpocząć projektowanie, przejdź do obszaru roboczego usługi Log Analytics i wybierz **Projektant widoków** kafelka.

![Pokaż projektanta](./media/cdn-diagnostics-log/27_Designer.png)

Przeciągnij i upuść typów wykresów i wypełnij dane szczegółów do przeanalizowania.

![Pokaż projektanta](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Opóźnienia danych dziennika

W poniższej tabeli przedstawiono dziennika danych umieszczono dwusekundowe opóźnienie **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, i **Azure CDN standardowa/Premium from Verizon**.

Opóźnienia danych dziennika firmy Microsoft | Opóźnienia danych dziennika firmy Verizon | Opóźnienia danych dziennika firmy Akamai
--- | --- | ---
Opóźnione przez 1 godzinę. | Opóźnione o godzinie i może potrwać do 2 godzin do uruchomienia, pojawiają się po zakończeniu propagowania punktu końcowego. | Opóźnione o 24 godzin; Jeśli zostały utworzone z więcej niż 24 godziny temu zajmuje się do 2 godzin do uruchomienia, pojawiają się. Jeśli niedawno został utworzony, może upłynąć do 25 godzin dzienniki aby rozpocząć, pojawiają się.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Analiza podstawowa typy dziennik diagnostyczny dla usługi CDN

Obecnie firma Microsoft oferuje podstawowe dzienniki analiz, zawierające metryki przedstawiające statystki odpowiedzi HTTP i statystyki ruch wychodzący widzianych z punktów obecności usługi CDN/krawędzi.

### <a name="core-analytics-metrics-details"></a>Szczegóły metryki analytics Core
W poniższej tabeli przedstawiono listę dostępnych w obszarach podstawowych metryk analizy dzienników dla **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, i **standardowa/Premium dla usługi Azure CDN Verizon**. Nie wszystkie metryki są dostępne od wszystkich dostawców, mimo że różnice są minimalne. W tabeli przedstawiono również, czy dana metryka jest dostępna od dostawcy. Metryki są dostępne dla tylko tych punktów końcowych usługi CDN, które mają ruchu na nich.


|Metryka                     | Opis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Łączna liczba trafień żądania, w tym okresie. | Tak | Yes |Yes |
| RequestCountHttpStatus2xx | Liczba wszystkich żądań, które wpłynęły na kod HTTP 2xx (na przykład 200, 202). | Tak | Yes |Tak |
| RequestCountHttpStatus3xx | Liczba wszystkich żądań, które wpłynęły na kod HTTP 3xx (na przykład, 300, 302). | Yes | Yes |Tak |
| RequestCountHttpStatus4xx | Liczba wszystkich żądań, które wpłynęły na kod HTTP 4xx (na przykład, 400, 404). | Yes | Yes |Tak |
| RequestCountHttpStatus5xx | Liczba wszystkich żądań, które wpłynęły na kod HTTP 5xx (na przykład, 500, 504). | Tak | Yes |Tak |
| RequestCountHttpStatusOthers | Liczba inne kody HTTP (poza 2xx-5xx). | Tak | Yes |Yes |
| RequestCountHttpStatus200 | Liczba wszystkich żądań, które spowodowały 200 kod odpowiedzi HTTP. | Yes | Nie  |Tak |
| RequestCountHttpStatus206 | Liczba wszystkich żądań, które spowodowały 206 kod odpowiedzi HTTP. | Yes | Nie  |Yes |
| RequestCountHttpStatus302 | Liczba wszystkich żądań, które wpłynęły na 302 kod odpowiedzi HTTP. | Tak | Nie  |Tak |
| RequestCountHttpStatus304 | Liczba wszystkich żądań, które spowodowały 304 kod odpowiedzi HTTP. | Tak | Nie  |Tak |
| RequestCountHttpStatus404 | Liczba wszystkich żądań, na które uzyskano odpowiedź kod HTTP 404. | Yes | Nie  |Yes |
| RequestCountCacheHit | Liczba wszystkich żądań, na które uzyskano w pamięci podręcznej odwołań. Zasób był obsługiwany bezpośrednio w punkcie POP do klienta. | Tak | Yes | Nie  |
| RequestCountCacheMiss | Liczba wszystkich żądań, które spowodowały Chybienie pamięci podręcznej. Trafienia pamięci podręcznej oznacza, że zasób nie został znaleziony na najbliższy do klienta punkt POP i dlatego został pobrany z punktu początkowego. | Tak | Yes | Nie |
| RequestCountCacheNoCache | Liczba wszystkich żądań do elementu zawartości, które mają zablokowaną możliwość buforowania z powodu konfiguracji użytkownika na urządzeniach brzegowych. | Yes | Yes | Nie |
| RequestCountCacheUncacheable | Liczba wszystkich żądań do zasobów, które mają zablokowaną możliwość buforowania Cache-Control elementu zawartości i nagłówki wygasa, które wskazują, że go powinien nie być buforowany w menu Podręcznym przez klienta HTTP. | Tak | Yes | Nie |
| RequestCountCacheOthers | Liczba wszystkich żądań ze stanem pamięci podręcznej nie pasuje do żadnego powyżej. | Nie | Yes | Nie  |
| EgressTotal | Dodatkowy wychodzący transfer danych w GB | Tak |Yes |Yes |
| EgressHttpStatus2xx | Dane wychodzące transferu * dla odpowiedzi z kodów stanu HTTP 2xx w GB. | Tak | Yes | Nie  |
| EgressHttpStatus3xx | Transfer danych wychodzących dla odpowiedzi z kodów stanu HTTP 3xx w GB. | Yes | Yes | Nie  |
| EgressHttpStatus4xx | Transfer danych wychodzących dla odpowiedzi z kodów stanu HTTP 4xx w GB. | Tak | Yes | Nie  |
| EgressHttpStatus5xx | Transfer danych wychodzących dla odpowiedzi z kodów stanu HTTP 5xx w GB. | Tak | Yes | Nie |
| EgressHttpStatusOthers | Transfer danych wychodzących dla odpowiedzi z innych kodach stanów HTTP w GB. | Tak | Yes | Nie  |
| EgressCacheHit | Transfer danych wychodzących dla odpowiedzi, które były dostarczane bezpośrednio z pamięci podręcznej usługi CDN CDN POP i krawędzi. | Tak | Yes | Nie |
| EgressCacheMiss. | Transfer danych wychodzących dla odpowiedzi, które nie zostały znalezione na najbliższy serwer protokołu POP i pobierane z serwera pochodzenia. | Yes | Yes | Nie |
| EgressCacheNoCache | Wychodzący transfer danych dla zasobów, które mają zablokowaną możliwość buforowania z powodu konfiguracji użytkownika na urządzeniach brzegowych. | Tak | Yes | Nie |
| EgressCacheUncacheable | Wychodzący transfer danych dla zasobów, które nie będą mogli buforowana przez Cache-Control i/lub Expires nagłówków zawartości. Wskazuje, czy jego powinien nie być buforowany w menu Podręcznym przez klienta HTTP. | Yes | Yes | Nie |
| EgressCacheOthers | Wychodzące transfery danych w innych sytuacjach pamięci podręcznej. | Nie | Yes | Nie |

\* Dodatkowy wychodzący transfer danych odnosi się do ruchu dostarczane z serwerów POP sieci CDN do klienta.


### <a name="schema-of-the-core-analytics-logs"></a>Schemat dzienniki analiz core 

Wszystkie dzienniki są przechowywane w formacie JSON, a każdy wpis ma pola ciągu zgodnie z poniższym schemacie:

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

Gdzie *czasu* reprezentuje czas rozpoczęcia granic godzinę, dla którego jest zgłaszana statystyki. Metryka nie jest obsługiwany przez dostawcę sieci CDN, zamiast wartości double lub liczba całkowita ma wartość null. Ta wartość null wskazuje brak metryki i różni się od wartości 0. Istnieje jeden zestaw te metryki dla domeny skonfigurowanej w punkcie końcowym.

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Podstawowa analiza uzupełniające portalu usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Usługi Azure Log Analytics interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics)







