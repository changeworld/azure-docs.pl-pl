---
title: Dzienniki diagnostyczne platformy Azure | Dokumenty firmy Microsoft
description: Klient może włączyć analizę dziennika dla usługi Azure CDN.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594207"
---
# <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Za pomocą dzienników diagnostycznych platformy Azure można wyświetlać podstawowe analizy i zapisywać je w co najmniej jednym miejscu docelowym, w tym:

 - Konto usługi Azure Storage
 - Azure Event Hubs
 - [Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ta funkcja jest dostępna w punktach końcowych sieci CDN dla wszystkich warstw cenowych. 

Dzienniki diagnostyki platformy Azure umożliwiają eksportowanie metryk użycia podstawowego z punktu końcowego usługi CDN do różnych źródeł, dzięki czemu można je używać w sposób dostosowany. Na przykład można wykonać następujące typy eksportu danych:

- Eksportowanie danych do magazynu obiektów blob, eksportowanie do pliku CSV i generowanie wykresów w programie Excel.
- Eksportuj dane do centrów zdarzeń i skoreluj z danymi z innych usług platformy Azure.
- Eksportowanie danych do dzienników usługi Azure Monitor i wyświetlanie danych we własnym obszarze roboczym usługi Log Analytics

Na poniższym diagramie przedstawiono typowy widok analizy rdzenia CDN danych.

![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Rysunek 1 — widok analizy rdzenia CDN*

Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Włączanie rejestrowania w witrynie Azure Portal

Wykonaj następujące kroki, aby umożliwić rejestrowanie za pomocą analizy rdzenia CDN:

Zaloguj się do [Portalu Azure](https://portal.azure.com). Jeśli nie masz jeszcze włączonej sieci CDN dla przepływu pracy, [utwórz profil i punkt końcowy usługi Azure CDN](cdn-create-new-endpoint.md) przed kontynuowaniem.

1. W witrynie Azure portal przejdź do **profilu usługi CDN**.

2. W witrynie Azure portal wyszukaj profil usługi CDN lub wybierz go z pulpitu nawigacyjnego. Następnie wybierz punkt końcowy sieci CDN, dla którego chcesz włączyć dzienniki diagnostyczne.

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Wybierz **dzienniki diagnostyczne** w sekcji MONITOROWANIE.

   Zostanie wyświetlona strona **Dzienniki diagnostyki.**

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Włączanie rejestrowania za pomocą usługi Azure Storage

Aby użyć konta magazynu do przechowywania dzienników, wykonaj następujące kroki:
    
1. W obszarze **Nazwa**wprowadź nazwę ustawień dziennika diagnostycznego.
 
2. Wybierz **pozycję Archiwizuj na konto magazynu**, a następnie wybierz pozycję **CoreAnalytics**. 

2. W polu **Retencja (dni)** wybierz liczbę dni przechowywania. Przechowywanie zero dni przechowuje dzienniki przez czas nieokreślony. 

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Wybierz **konto magazynu**.

    Zostanie wyświetlona strona **Wybierz konto magazynu.**

4. Wybierz konto magazynu z listy rozwijanej, a następnie wybierz **przycisk OK**.

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po zakończeniu tworzenia ustawień dziennika diagnostycznego wybierz pozycję **Zapisz**.

### <a name="logging-with-azure-monitor"></a>Rejestrowanie za pomocą usługi Azure Monitor

Aby używać usługi Azure Monitor do przechowywania dzienników, wykonaj następujące kroki:

1. Na stronie **Dzienniki diagnostyki** wybierz pozycję **Wyślij do usługi Log Analytics**. 

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Wybierz **pozycję Konfiguruj,** aby skonfigurować rejestrowanie usługi Azure Monitor. 

   Zostanie wyświetlona strona **Obszary robocze usługi Log Analytics.**

    >[!NOTE] 
    >Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Wybierz **pozycję Utwórz nowy obszar roboczy**.

    Zostanie wyświetlona strona **obszaru roboczego usługi Log Analytics.**

    >[!NOTE] 
    >Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/07_Create-new.png)

4. W **obszarze roboczym usługi Log Analytics**wprowadź nazwę obszaru roboczego usługi Log Analytics. Nazwa obszaru roboczego usługi Log Analytics musi być unikatowa i zawierać tylko litery, cyfry i łączniki; spacji i podkreśleń nie są dozwolone. 

5. W przypadku **subskrypcji**wybierz istniejącą subskrypcję z listy rozwijanej. 

6. W przypadku **grupy zasobów**utwórz nową grupę zasobów lub wybierz istniejącą.

7. W obszarze **Lokalizacja**wybierz lokalizację z listy.

8. Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** jeśli chcesz zapisać konfigurację dziennika na pulpicie nawigacyjnym. 

9. Wybierz **przycisk OK,** aby zakończyć konfigurację.

10. Po utworzeniu obszaru roboczego zostaniesz przywrócony do strony **Dzienniki diagnostyczne.** Potwierdź nazwę nowego obszaru roboczego usługi Log Analytics.

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Wybierz **Opcję CoreAnalytics**, a następnie wybierz pozycję **Zapisz**.

12. Aby wyświetlić nowy obszar roboczy usługi Log Analytics, wybierz **analizę rdzenia** ze strony punktu końcowego usługi CDN.

    ![portal - Dzienniki diagnostyczne](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Obszar roboczy usługi Log Analytics jest teraz gotowy do rejestrowania danych. Aby korzystać z tych danych, należy użyć [rozwiązania dzienników usługi Azure Monitor,](#consuming-diagnostics-logs-from-a-log-analytics-workspace)omówione w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat opóźnień danych dziennika, zobacz [Rejestrowanie opóźnień danych](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

W poniższym przykładzie pokazano, jak włączyć dzienniki diagnostyczne za pośrednictwem poleceń cmdlet programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Włączanie dzienników diagnostycznych na koncie magazynu

1. Zaloguj się i wybierz subskrypcję:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Aby włączyć dzienniki diagnostyczne na koncie magazynu, wprowadź to polecenie:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Aby włączyć dzienniki diagnostyczne w obszarze roboczym usługi Log Analytics, wprowadź to polecenie:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Korzystanie z dzienników diagnostycznych z usługi Azure Storage
W tej sekcji opisano schemat analizy rdzenia usługi CDN, sposób organizowania go wewnątrz konta magazynu platformy Azure i zawiera przykładowy kod do pobrania dzienników w pliku CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Korzystanie z Eksploratora magazynu platformy Microsoft Azure
Aby uzyskać dostęp do podstawowych danych analitycznych z konta magazynu platformy Azure, najpierw potrzebujesz narzędzia do uzyskiwania dostępu do zawartości konta magazynu. Chociaż na rynku dostępnych jest kilka narzędzi, ten, który zaleca się, to Eksplorator magazynu platformy Microsoft Azure. Aby pobrać narzędzie, zobacz [Eksplorator usług Azure Storage](https://storageexplorer.com/). Po pobraniu i zainstalowaniu oprogramowania skonfiguruj je tak, aby używało tego samego konta magazynu platformy Azure, które zostało skonfigurowane jako miejsce docelowe dzienników diagnostyki usługi CDN.

1.  Otwórz **Eksploratora magazynu platformy Microsoft Azure**
2.  Lokalizowanie konta magazynu
3.  Rozwiń węzeł **Kontenery obiektów blob** w ramach tego konta magazynu.
4.  Wybierz kontener o nazwie *insights-logs-coreanalytics*.
5.  Wyniki są wyświetlane w okienku po prawej stronie, zaczynając od pierwszego poziomu, jako *resourceId=*. Kontynuuj wybieranie każdego poziomu, aż znajdziesz plik *PT1H.json*. Aby uzyskać wyjaśnienie ścieżki, zobacz [format ścieżki obiektu Blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Każdy plik *PT1H.json* obiektu blob reprezentuje dzienniki analizy przez jedną godzinę dla określonego punktu końcowego sieci CDN lub jego domeny niestandardowej.
7.  Schemat zawartości tego pliku JSON jest opisany w schemacie sekcji dzienników analizy podstawowych.


#### <a name="blob-path-format"></a>Format ścieżki obiektu blob

Dzienniki analizy podstawowej są generowane co godzinę, a dane są zbierane i przechowywane wewnątrz pojedynczego obiektu blob platformy Azure jako ładunek JSON. Ponieważ narzędzie Eksploratora magazynu interpretuje "/" jako separator katalogów i pokazuje hierarchię, ścieżka do obiektu blob platformy Azure jest wyświetlana tak, jakby istniała struktura hierarchiczna i reprezentuje nazwę obiektu blob. Nazwa obiektu blob jest zgodna z następującą konwencją nazewnictwa:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Opis pól:**

|Wartość|Opis|
|-------|---------|
|Identyfikator subskrypcji    |Identyfikator subskrypcji platformy Azure w formacie Guid.|
|Nazwa grupy zasobów |Nazwa grupy zasobów, do której należą zasoby sieci CDN.|
|Profile Name (Nazwa profilu) |Nazwa profilu sieci CDN|
|Nazwa punktu końcowego |Nazwa punktu końcowego sieci CDN|
|Year|  Czterocyfrowa reprezentacja roku, na przykład 2017|
|Month| Dwucyfrowa reprezentacja numeru miesiąca. 01=styczeń ... 12=Grudzień|
|Day|   Dwucyfrowa reprezentacja dnia miesiąca|
|PT1H.json| Rzeczywisty plik JSON, w którym przechowywane są dane analityczne|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Eksportowanie podstawowych danych analitycznych do pliku CSV

Aby ułatwić dostęp do analizy podstawowych, dostępny jest przykładowy kod narzędzia. To narzędzie umożliwia pobieranie plików JSON do płaskiego formatu pliku oddzielonego przecinkami, który może być używany do tworzenia wykresów lub innych agregacji.

Oto jak możesz użyć tego narzędzia:

1.  Odwiedź link GitHub:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Pobierz kod.
3.  Postępuj zgodnie z instrukcjami, aby skompilować i skonfigurować.
4.  Uruchom narzędzie.
5.  Wynikowy plik CSV pokazuje dane analityczne w prostej płaskiej hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Korzystanie z dzienników diagnostycznych z obszaru roboczego usługi Log Analytics
Usługa Azure Monitor to usługa platformy Azure, która monitoruje środowisko w chmurze i środowiskach lokalnych w celu utrzymania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł. 

Aby korzystać z usługi Azure Monitor, należy [włączyć rejestrowanie](#enable-logging-with-azure-storage) do obszaru roboczego usługi Azure Log Analytics, który został omówiony wcześniej w tym artykule.

### <a name="using-the-log-analytics-workspace"></a>Korzystanie z obszaru roboczego usługi Log Analytics

 Na poniższym diagramie przedstawiono architekturę wejść i wyjść repozytorium:

![Obszar roboczy usługi Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Rysunek 3 — Repozytorium analizy dzienników*

Dane można wyświetlać na różne sposoby za pomocą rozwiązań zarządzania. Rozwiązania do zarządzania można uzyskać z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Rozwiązania do monitorowania można zainstalować z witryny Azure Marketplace, wybierając łącze **Pobierz teraz** u dołu każdego rozwiązania.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Dodawanie rozwiązania do monitorowania usługi Azure Monitor CDN

Wykonaj następujące kroki, aby dodać rozwiązanie do monitorowania usługi Azure Monitor:

1.   Zaloguj się do witryny Azure portal przy użyciu subskrypcji platformy Azure i przejdź do pulpitu nawigacyjnego.
    ![Pulpit nawigacyjny platformy Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na stronie **Nowy** w obszarze **Marketplace**wybierz pozycję **Monitorowanie + zarządzanie**.

    ![Portal Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na stronie **Monitorowanie + zarządzanie** wybierz pozycję Zobacz **wszystkie**.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/15_See-all.png)

4. Wyszukaj sieć CDN w polu wyszukiwania.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/16_Search-for.png)

5. Wybierz **pozycję Azure CDN Core Analytics**. 

    ![Zobacz wszystko](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po wybraniu opcji **Utwórz**zostaniesz poproszony o utworzenie nowego obszaru roboczego usługi Log Analytics lub użycie istniejącego. 

    ![Zobacz wszystko](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Wybierz utworzony wcześniej obszar roboczy. Następnie należy dodać konto automatyzacji.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Na poniższym ekranie przedstawiono formularz konta automatyzacji, który należy wypełnić. 

    ![Zobacz wszystko](./media/cdn-diagnostics-log/20_Automation.png)

9. Po utworzeniu konta automatyzacji można przystąpić do dodania rozwiązania. Wybierz przycisk **Utwórz**.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/21_Ready.png)

10. Rozwiązanie zostało dodane do obszaru roboczego. Wróć do pulpitu nawigacyjnego portalu azure.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/22_Dashboard.png)

    Wybierz utworzony obszar roboczy usługi Log Analytics, aby przejść do obszaru roboczego. 

11. Wybierz kafelek **Portal OMS,** aby wyświetlić nowe rozwiązanie.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/23_workspace.png)

12. Portal powinien teraz wyglądać następująco:

    ![Zobacz wszystko](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Wybierz jeden z kafelków, aby wyświetlić kilka widoków w danych.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/25_Interior-view.png)

    Możesz przewijać w lewo lub w prawo, aby wyświetlić kolejne kafelki reprezentujące poszczególne widoki w danych. 

    Wybierz jeden z kafelków, aby wyświetlić więcej szczegółów dotyczących danych.

     ![Zobacz wszystko](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Oferty i warstwy cenowe

Tutaj możesz zobaczyć oferty i warstwy cenowe dla [rozwiązań](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)do zarządzania.

### <a name="customizing-views"></a>Dostosowywanie widoków

Widok można dostosować do danych za pomocą **projektanta widoku**. Aby rozpocząć projektowanie, przejdź do obszaru roboczego usługi Log Analytics i wybierz kafelek **Projektanta widoku.**

![Projektant widoków](./media/cdn-diagnostics-log/27_Designer.png)

Przeciągnij i upuść typy wykresów i wprowadź szczegóły danych, które chcesz przeanalizować.

![Projektant widoków](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Rejestrowanie opóźnień danych

W poniższej tabeli przedstawiono opóźnienia danych dziennika dla **usługi Azure CDN Standard firmy Microsoft,** **standard azure CDN standard firmy Akamai**i **usługę Azure CDN Standard/Premium firmy Verizon.**

Opóźnienia danych dziennika firmy Microsoft | Opóźnienia danych dziennika Verizon | Opóźnienia danych dziennika Akamai
--- | --- | ---
Opóźnione o 1 godzinę. | Opóźnione o 1 godzinę i może potrwać do 2 godzin, aby rozpocząć pojawianie się po zakończeniu propagacji punktu końcowego. | Opóźnione o 24 godziny; jeśli został utworzony ponad 24 godziny temu, to trwa do 2 godzin, aby rozpocząć pojawianie się. Jeśli został niedawno utworzony, może upłynąć do 25 godzin, aby dzienniki zaczęły się pojawiać.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy dzienników diagnostycznych dla analizy rdzeni CDN

Firma Microsoft oferuje obecnie tylko dzienniki analizy podstawowej, które zawierają metryki przedstawiające statystyki odpowiedzi HTTP i statystyki wychodzące widziane z po brzegach/krawędziach sieci CDN.

### <a name="core-analytics-metrics-details"></a>Szczegóły metryk analizy podstawowej
W poniższej tabeli przedstawiono listę metryk dostępnych w dziennikach analizy podstawowej dla **usługi Azure CDN Standard firmy Microsoft,** **standardu azure CDN standard firmy Akamai**i **usłudze Azure CDN Standard/Premium firmy Verizon.** Nie wszystkie metryki są dostępne od wszystkich dostawców, chociaż takie różnice są minimalne. W tabeli jest również wyświetlany, czy dana metryka jest dostępna od dostawcy. Metryki są dostępne tylko dla tych punktów końcowych sieci CDN, które mają ruch na nich.


|Metryka                     | Opis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal (Liczba żądań)         | Całkowita liczba trafień żądań w tym okresie. | Tak | Tak |Tak |
| RequestCountHttpStatus2xx | Liczba wszystkich żądań, które spowodowały 2xx kod HTTP (na przykład 200, 202). | Tak | Tak |Tak |
| RequestCountHttpStatus3xx | Liczba wszystkich żądań, które spowodowały 3xx kod HTTP (na przykład 300, 302). | Tak | Tak |Tak |
| RequestCountHttpStatus4xx | Liczba wszystkich żądań, które spowodowały 4xx kod HTTP (na przykład 400, 404). | Tak | Tak |Tak |
| RequestCountHttpStatus5xx | Liczba wszystkich żądań, które spowodowały 5xx kod HTTP (na przykład 500, 504). | Tak | Tak |Tak |
| RequestCountHttpStatusOthers | Liczba wszystkich innych kodów HTTP (poza 2xx-5xx). | Tak | Tak |Tak |
| RequestCountHttpStatus200 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 200. | Tak | Nie  |Tak |
| RequestCountHttpStatus206 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 206. | Tak | Nie  |Tak |
| RequestCountHttpStatus302 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 302. | Tak | Nie  |Tak |
| RequestCountHttpStatus304 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 304. | Tak | Nie  |Tak |
| RequestCountHttpStatus404 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 404. | Tak | Nie  |Tak |
| RequestCountCacheHit (Liczba żądań) | Liczba wszystkich żądań, które spowodowały trafienie pamięci podręcznej. Zasób został doręczony bezpośrednio z punktu obecności do klienta. | Tak | Tak | Nie  |
| ŻądanieCountCacheMiss | Liczba wszystkich żądań, które spowodowały pominięcie pamięci podręcznej. Brak pamięci podręcznej oznacza, że zasób nie został znaleziony w przyp. | Tak | Tak | Nie |
| RequestCountCacheNoCache | Liczba wszystkich żądań do zasobu, które nie mogą być buforowane z powodu konfiguracji użytkownika na krawędzi. | Tak | Tak | Nie |
| RequestCountCacheUncacheable | Liczba wszystkich żądań do zasobów, które nie mogą być buforowane przez cache-control zasobu i wygasa nagłówki, które wskazują, że nie powinny być buforowane na pop lub przez klienta HTTP. | Tak | Tak | Nie |
| RequestCountCacheOthers | Liczba wszystkich żądań ze stanem pamięci podręcznej, który nie został objęty powyższym. | Nie | Tak | Nie  |
| Wyjście Dotłaczku | Transfer danych wychodzących w GB | Tak |Tak |Tak |
| WyjściehttpStatus2xx | Transfer danych wychodzących* dla odpowiedzi z 2xx kodami stanu HTTP w GB. | Tak | Tak | Nie  |
| WyjściehttpStatus3xx | Transfer danych wychodzących dla odpowiedzi z 3xx kodami stanu HTTP w GB. | Tak | Tak | Nie  |
| WyjściehttpStatus4xx | Transfer danych wychodzących dla odpowiedzi z 4xx kodami stanu HTTP w GB. | Tak | Tak | Nie  |
| WyjściehttpStatus5xx | Transfer danych wychodzących dla odpowiedzi z kodami stanu HTTP 5xx w GB. | Tak | Tak | Nie |
| WyjścieHttpStatusOthers | Transfer danych wychodzących dla odpowiedzi z innymi kodami stanu HTTP w GB. | Tak | Tak | Nie  |
| WyjścieCacheHit | Transfer danych wychodzących dla odpowiedzi, które zostały dostarczone bezpośrednio z pamięci podręcznej sieci CDN w po brzegach/krawędziach sieci CDN. | Tak | Tak | Nie |
| WychodzącyCacheMiss. | Transfer danych wychodzących dla odpowiedzi, które nie zostały znalezione na najbliższym serwerze POP i pobrane z serwera pochodzenia. | Tak | Tak | Nie |
| WyjścieCacheNoCache | Transfer danych wychodzących dla zasobów, które nie mogą być buforowane z powodu konfiguracji użytkownika na krawędzi. | Tak | Tak | Nie |
| WyjścieCacheNiesłażalne | Transfer danych wychodzących dla zasobów, które nie mogą być buforowane przez cache-control zasobu i/lub wygasa nagłówki. Wskazuje, że nie powinny być buforowane na pop lub przez klienta HTTP. | Tak | Tak | Nie |
| EgressCacheOthers | Wychodzące transfery danych dla innych scenariuszy pamięci podręcznej. | Nie | Tak | Nie |

*Wychodzący transfer danych odnosi się do ruchu dostarczanego z serwerów CDN POP do klienta.


### <a name="schema-of-the-core-analytics-logs"></a>Schemat podstawowych dzienników analizy 

Wszystkie dzienniki są przechowywane w formacie JSON, a każdy wpis ma pola ciągu zgodnie z następującym schematem:

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

Gdzie *czas* reprezentuje godzinę rozpoczęcia granicy godziny, dla której są zgłaszane statystyki. Jeśli metryka nie jest obsługiwana przez dostawcę usługi CDN zamiast wartości podwójnej lub całkowitej, istnieje wartość null. Ta wartość null wskazuje brak metryki i różni się od wartości 0. Istnieje jeden zestaw tych metryk dla domeny skonfigurowanych w punkcie końcowym.

Przykładowe właściwości:

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

* [Dzienniki diagnostyki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Podstawowa analiza za pośrednictwem dodatkowego portalu usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







