---
title: Monitorowanie i zarządzanie nimi potoki przy użyciu portalu Azure i programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać portalu Azure i programu Azure PowerShell do monitorowania i zarządzania fabryki danych Azure i potoki, które zostały utworzone.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 94b3c1e812bdf3345d5fb1f7308fb7a55be8f922
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorowanie i zarządzanie nimi potoki fabryki danych Azure przy użyciu portalu Azure i programu PowerShell
> [!div class="op_single_selector"]
> * [Przy użyciu portalu Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Przy użyciu monitorowania i zarządzania aplikacjami](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [monitorować i zarządzać nimi potoki fabryki danych w wersji 2](../monitor-visually.md).

> [!IMPORTANT]
> Aplikacja monitorowania i zarządzania zapewnia lepszą obsługę do monitorowania i zarządzania potoki Twoje dane i rozwiązywania problemów. Aby uzyskać informacje dotyczące korzystania z aplikacji, zobacz [monitorować i zarządzać nimi potoki fabryki danych przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md). 


W tym artykule opisano sposób monitorowania, zarządzania i debugowania z potoków przy użyciu portalu Azure i programu PowerShell.

## <a name="understand-pipelines-and-activity-states"></a>Potoki i stanów działania
Za pomocą portalu Azure, możesz:

* Wyświetl fabrykę danych jako diagram.
* Przeglądanie działań w potoku.
* Umożliwia wyświetlanie zestawów danych wejściowych i wyjściowych.

W tej sekcji opisano również sposób przejścia wycinek zestawu danych z jednego stanu do innego stanu.   

### <a name="navigate-to-your-data-factory"></a>Przejdź z fabryką danych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **fabryki danych** w menu po lewej stronie. Jeśli nie widzisz, kliknij przycisk **więcej usług >**, a następnie kliknij przycisk **fabryki danych** w obszarze **analizy i analiza** kategorii.

   ![Przeglądaj wszystkie > fabryki danych](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na **fabryki danych** bloku, wybierz fabryki danych, która Cię interesują.

    ![Wybieranie fabryki danych](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Powinna zostać wyświetlona strona główna dla fabryki danych.

   ![Blok Fabryka danych](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Widok diagramu w fabryce danych
**Diagram** widoku fabryki danych zapewnia jeden szkła monitorować i zarządzać fabryki danych i jego zasoby. Aby wyświetlić **Diagram** wyświetlić w fabryce danych, kliknij przycisk **Diagram** na stronie głównej dla fabryki danych.

![Widok diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Można powiększać, pomniejszyć, Powiększ do dopasowania, powiększenie do 100%, Zablokuj układ diagramu i automatycznie rozmieszczaj potoki i zestawów danych. Możesz również sprawdzić informacje elementy powiązane dane (to znaczy Pokaż elementy nadrzędne i podrzędne wybranego elementu).

### <a name="activities-inside-a-pipeline"></a>Działania w potoku
1. Kliknij prawym przyciskiem myszy potok, a następnie kliknij przycisk **Otwórz potoku** aby zobaczyć wszystkie działania w potoku, wraz z zestawów danych wejściowych i wyjściowych dla działania. Ta funkcja jest przydatne, gdy potoku sieci zawiera więcej niż jedno działanie i chcesz poznać operacyjne elementy powiązane z jednym potoku.

    ![Menu Otwórz potok](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. Działanie kopiowania w potoku z danych wejściowych i wyjściowych widać w poniższym przykładzie. 

    ![Działania w potoku](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Przejdź na powrót do strony głównej fabryki danych, klikając **fabryki danych** łącza w łączach w lewym górnym rogu.

    ![Przejdź z powrotem do fabryki danych](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Wyświetl stan każdego działania w potoku
Można wyświetlić bieżący stan działania, wyświetlając stan zestawów danych, które są tworzone podczas działania.

Klikając dwukrotnie **OutputBlobTable** w **Diagram**, zobaczysz wycinków, które są tworzone przez uruchamia innej aktywności w potoku. Można zobaczyć, czy działanie kopiowania został uruchomiony pomyślnie w ciągu ostatnich ośmiu godzin, a wyprodukowanych wycinki **gotowe** stanu.  

![Stan potoku](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Wycinków zestaw danych w fabryce danych może mieć jeden z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Podstan</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>ScheduleTime</td><td>Czas nie pochodzą dla uruchomienia wycinka.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Zależności strumienia wychodzącego nie są gotowe.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Zasoby obliczeniowe są niedostępne.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Wszystkie wystąpienia działania są zajęte uruchamianiem innych wycinków.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Działanie jest wstrzymane i nie może uruchamiać wycinków, dopóki nie zostanie wznowione działania.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Ponawiane wykonania działania.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja jeszcze się nie rozpoczął.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Sprawdzanie poprawności oczekuje na ponowienie próby.</td>
</tr>
<tr>
<tr>
<td rowspan="2">W toku</td><td>Sprawdzanie poprawności</td><td>Trwa sprawdzanie poprawności.</td>
</tr>
<td>-</td>
<td>Wycinek jest przetwarzany.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>Upłynął limit czasu</td><td>Wykonywanie działania trwało dłużej niż jest dozwolonych przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Wycinek zostało anulowane przez akcję użytkownika.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja nie powiodła się.</td>
</tr>
<tr>
<td>-</td><td>Nie można wygenerować i/lub sprawdzić poprawności wycinka.</td>
</tr>
<td>Gotowe</td><td>-</td><td>Wycinek jest gotowy do użycia.</td>
</tr>
<tr>
<td>Pominięto</td><td>Brak</td><td>Wycinek nie jest przetwarzany.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Wycinek miał poprzednio inny stan, ale został zresetowany.</td>
</tr>
</table>



Szczegółowe informacje o wycinek można wyświetlić, klikając polecenie wpisu wycinka w **ostatnio zaktualizowano wycinków** bloku.

![Szczegóły wycinka](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Jeśli wykonano wycinka wiele razy, zobacz wielu wierszy **odbywa się działanie** listy. Można wyświetlić szczegółowe informacje o działaniu uruchomić, klikając przycisk Uruchom wpisu w **odbywa się działanie** listy. Lista zawiera wszystkie pliki dziennika, oraz komunikat o błędzie, jeśli istnieje. Ta funkcja jest przydatna do wyświetlania dzienników i debugowania bez konieczności opuszczania fabryką danych.

![Szczegóły uruchamiania działania](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Jeśli go nie ma **gotowe** stanu, widać wycinków strumienia wychodzącego, które nie są gotowe i blokuje bieżący fragment wykonywanie w **wycinków strumienia wychodzącego, które nie są gotowe** listy. Ta funkcja jest przydatna, gdy Twoje wycinka **oczekiwania** stanu i chcesz poznać nadrzędnego zależności wycinka oczekuje na.

![Niegotowe wycinki strumienia wychodzącego](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stanu zestawu danych
Po wdrożeniu fabryki danych i potoki ma nieprawidłowy okres aktywności, zestaw danych wycinków przejście z jednego stanu do innego. Obecnie stanu wycinka następujące na poniższym diagramie stanu:

![Diagram stanu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Przepływ zestawu danych stanu przejścia w fabryce danych jest następująca: oczekiwania -> w toku/w toku (sprawdzania) -> gotowe/nie powiodło się.

Wycinek jest uruchamiany w **oczekiwania** stanu oczekiwania na warunki wstępne, które muszą być spełnione, przed rozpoczęciem wykonywania. Następnie działanie rozpoczyna wykonywanie i wycinka przechodzi w stan **w toku** stanu. Wykonanie działania może powodzenie lub niepowodzenie. Wycinek jest oznaczony jako **gotowe** lub, na podstawie wyniku wykonania.

Można zresetować wycinka tak, aby wrócić do poprzedniej strony z **gotowe** lub **niepowodzenie** stan **oczekiwania** stanu. Można również zaznaczyć stanu wycinka do **Pomiń**, które uniemożliwiają działanie z wykonywania i nie przetwarzania wycinka.

## <a name="pause-and-resume-pipelines"></a>Wstrzymywanie i wznawianie potoki
Twoje potoki można zarządzać za pomocą programu Azure PowerShell. Na przykład można wstrzymywać i wznawiać potoki przez uruchomienie polecenia cmdlet programu Azure PowerShell przez użytkownika. 

> [!NOTE] 
> Widok diagramu nie obsługuje wstrzymywanie i wznawianie potoków. Jeśli chcesz użyć interfejsu użytkownika, za pomocą aplikacji monitorowania i zarządzanie nimi. Aby uzyskać informacje dotyczące korzystania z aplikacji, zobacz [monitorować i zarządzać nimi potoki fabryki danych przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) artykułu. 

Można Wstrzymaj/zawiesić potoki przy użyciu **Suspend-AzureRmDataFactoryPipeline** polecenia cmdlet programu PowerShell. To polecenie cmdlet jest przydatne, gdy nie chcesz uruchomić z potoków, dopóki problem nie zostanie rozwiązany. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Na przykład:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po usunięciu problemu z potoku, można wznowić zawieszonego procesu za pomocą następującego polecenia programu PowerShell:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Na przykład:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Debugowanie potoki
Fabryka danych Azure zapewnia zaawansowane możliwości debugowania i rozwiązywania problemów z potoków, za pomocą portalu Azure i programu Azure PowerShell.

> [! Uwaga} jest znacznie łatwiejsze Rozwiązywanie problemów z błędami, za pomocą aplikacji do zarządzania i monitorowania. Aby uzyskać informacje dotyczące korzystania z aplikacji, zobacz [monitorować i zarządzać nimi potoki fabryki danych przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) artykułu. 

### <a name="find-errors-in-a-pipeline"></a>Znaleziono błędów w potoku
W przypadku niepowodzenia uruchomienia działania w potoku zestawu danych, który jest generowany przez potok jest w stanie błędu z powodu błędu. Można debugować i rozwiązywanie problemów z fabryki danych Azure przy użyciu następujących metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Debugowanie błędów przy użyciu portalu Azure
1. Na **tabeli** bloku, kliknij przycisk wycinek problem, który ma **stan** ustawioną.

   ![Blok tabeli z wycinka problem](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na **wycinka danych** bloku, kliknij przycisk uruchamiania działania, która nie powiodła się.

   ![Wycinek danych z powodu błędu](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na **szczegóły uruchomienia działania** bloku, możesz pobrać pliki, które są powiązane z przetwarzaniem HDInsight. Kliknij przycisk **Pobierz** dla stanu/stderr można pobrać pliku dziennika błędów, który zawiera szczegóły dotyczące błędu.

   ![Działanie Uruchom szczegóły blok z powodu błędu](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Debugowanie wystąpił błąd przy użyciu programu PowerShell
1. Uruchom program **PowerShell**.
2. Uruchom **Get AzureRmDataFactorySlice** polecenie, aby wyświetlić wycinków i ich Stany. Powinny pojawić się wycinek ze statusem.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Na przykład:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Zastąp **StartDateTime** czas rozpoczęcia z potoku. 
3. Teraz uruchom **Get AzureRmDataFactoryRun** Uruchom polecenie cmdlet, aby uzyskać szczegółowe informacje o działaniach dla wycinka.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Na przykład:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Wartość StartDateTime jest czas rozpoczęcia wycinka błąd/problem zanotowaną w poprzednim kroku. Daty i godziny należy ująć w podwójny cudzysłów.
4. Powinny pojawić się dane wyjściowe zawierające szczegółowe informacje o błędzie, który jest podobny do następującego:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Można uruchomić **AzureRmDataFactoryLog Zapisz** polecenia cmdlet z identyfikatorem Zobacz z danych wyjściowych i pobierania plików dziennika za pomocą **- DownloadLogsoption** polecenia cmdlet.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Uruchom ponownie błędów w potoku

> [!IMPORTANT]
> Możliwe jest łatwiejsze Rozwiązywanie problemów i ponownie uruchom wycinków nie powiodło się przy użyciu monitorowania & aplikacji do zarządzania. Aby uzyskać informacje dotyczące korzystania z aplikacji, zobacz [monitorować i zarządzać nimi potoki fabryki danych przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Po Rozwiązywanie problemów i debugowanie błędów w potoku, przechodząc do wycinek błąd i klikając, można uruchomić błędów **Uruchom** przycisk paska poleceń.

![Uruchom ponownie wycinek nie powiodło się](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

W przypadku wycinka Weryfikacja nie powiodła się z powodu błędu zasad (na przykład, jeśli dane są niedostępne), można naprawić błędu i ponownie zweryfikować, klikając **weryfikacji** przycisk paska poleceń.

![Usuń błędy i sprawdzania poprawności](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Uruchom ponownie błędów za pomocą **AzureRmDataFactorySliceStatus zestaw** polecenia cmdlet. Zobacz [AzureRmDataFactorySliceStatus zestaw](https://msdn.microsoft.com/library/mt603522.aspx) temat o składni i inne szczegóły dotyczące polecenia cmdlet.

**Przykład:**

Poniższy przykład ustawia stan wszystkich wycinki dla tabeli "DAWikiAggregatedData" Oczekiwanie w fabryce danych Azure "WikiADF".

Typ "aktualizacji" ma ustawioną wartość "Parametru UpstreamInPipeline", co oznacza, że stanów każdy wycinek dla tabeli i wszystkie tabele zależne (nadrzędnego) są ustawione na "Oczekiwanie". Możliwe wartości tego parametru jest "Indywidualny".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Przenieś fabryki danych do innej grupy zasobów lub subskrypcji
Fabryka danych można przenieść do innej grupie zasobów lub różnych subskrypcji przy użyciu **Przenieś** polecenia paska przycisk na stronie głównej w fabryce danych.

![Przenieś fabryki danych](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Można również przenosić powiązanych zasobów (takich jak alerty, które są skojarzone z fabryki danych), wraz z fabryki danych.

![Przenoszenie zasobów, okno dialogowe](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
