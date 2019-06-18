---
title: Monitorowanie potoków i zarządzanie nimi za pomocą witryny Azure portal i programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą witryny Azure portal i programu Azure PowerShell monitorowanie i zarządzanie nimi fabryki danych platformy Azure i potoki, które zostały utworzone.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 64fae56bfc95b62bd60444d49100689845f64278
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122719"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorowanie potoków i zarządzanie nimi usługi Azure Data Factory przy użyciu witryny Azure portal i programu PowerShell
> [!div class="op_single_selector"]
> * [Przy użyciu portalu Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Za pomocą monitorowania i zarządzania aplikacjami](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [monitorowanie i zarządzanie nimi potoków usługi Data Factory w](../monitor-visually.md).

W tym artykule opisano sposób monitorowania i debugowania potoków przy użyciu witryny Azure portal i programu PowerShell oraz zarządzanie nimi.

> [!IMPORTANT]
> Aplikacja monitorowanie i zarządzanie zapewnia lepszą obsługę monitorowania i zarządzania potoki danych i rozwiązać wszelkie problemy. Aby uzyskać szczegółowe informacje dotyczące korzystania z aplikacji, zobacz [monitorowanie i zarządzanie nimi potoków usługi Data Factory przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory w wersji 1 teraz używa nowego [usługi Azure Monitor alertów infrastruktury](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Stare alerty infrastruktury jest przestarzały. W wyniku istniejące alerty skonfigurowany dla wersji 1 dane, które fabryki przestanie działać. Istniejące alerty dla fabryki danych w wersji 1 nie są migrowane automatycznie. Musisz odtworzyć te alerty dotyczące nowych alertów infrastruktury. Zaloguj się w witrynie Azure portal i wybierz **Monitor** do tworzenia nowych alertów dotyczących metryk (np. nieudane uruchomienia lub pomyślnych uruchomień) dla danej wersji fabryk danych 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Zrozumienie potokach i Stanach aktywności
Za pomocą witryny Azure portal, możesz wykonywać następujące czynności:

* Wyświetlanie fabryki danych jako diagram.
* Wyświetlanie działań w potoku.
* Wyświetlanie danych wejściowych i wyjściowych zestawów danych.

W tej sekcji opisano również sposób przejścia wycinek zestawu danych z jednego stanu do innego stanu.   

### <a name="navigate-to-your-data-factory"></a>Przejdź do usługi data factory
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **fabryk danych** w menu po lewej stronie. Jeśli nie widzisz, kliknij przycisk **więcej usług >** , a następnie kliknij przycisk **fabryk danych** w obszarze **rozwiązania INTELIGENTNE + analiza** kategorii.

   ![Przeglądaj wszystko > fabryki danych](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na **fabryk danych** bloku wybierz fabrykę danych, które interesują Cię.

    ![Wybieranie fabryki danych](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Powinna zostać wyświetlona strona główna, które fabryki danych.

   ![Blok Fabryka danych](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Widok diagramu fabryki danych
**Diagram** widok usługi data factory zapewnia jedną taflę szkła do monitorowania i zarządzania usługi data factory i jej zasobów. Aby wyświetlić **Diagram** wyświetlić fabryki danych, kliknij przycisk **Diagram** na stronie głównej fabryki danych.

![Widok diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Możesz powiększyć, pomniejszyć, Powiększ do dopasowania, Powiększ do 100%, Zablokuj układ diagramu i automatycznie rozmieszczaj potoki i zestawów danych. Możesz również sprawdzić informacje pochodzenie danych (czyli Pokaż elementów nadrzędnych i podrzędnych wybranych elementów).

### <a name="activities-inside-a-pipeline"></a>Działania w potoku
1. Kliknij prawym przyciskiem myszy potok, a następnie kliknij przycisk **Otwórz potok** Aby wyświetlić wszystkie działania w potoku, wraz z danych wejściowych i wyjściowych zestawów danych dla działań. Ta funkcja jest przydatna, gdy potok zawiera więcej niż jedno działanie i chcesz poznać operacyjnej pochodzenie jeden potok.

    ![Menu Otwórz potok](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. W poniższym przykładzie widać działania kopiowania w potoku przy użyciu danych wejściowych i wyjściowych. 

    ![Działania w potoku](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Przejdź na powrót do strony głównej usługi data factory, klikając **usługi Data factory** link w obszarze nawigacji w lewym górnym rogu.

    ![Przejdź z powrotem do usługi data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Wyświetl stan każdego działania w potoku
Możesz wyświetlić bieżący stan działania, wyświetlając stan zestawów danych, które są generowane przez działanie.

Przez dwukrotne kliknięcie **OutputBlobTable** w **Diagram**, można wyświetlić wszystkie wycinki, które są produkowane przez uruchomienia różnych działań w potoku. Możesz zobaczyć, działanie kopiowania został uruchomiony pomyślnie dla ostatnich ośmiu godzin i generowane wycinki **gotowe** stanu.  

![Stan potoku](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Wycinków zestawu danych w usłudze data factory może mieć jedną z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Podstanu</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>ScheduleTime</td><td>Czas nie pochodzą dla uruchomienia wycinka.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Zależności strumienia wychodzącego nie są gotowe.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Zasoby obliczeniowe nie są dostępne.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Wszystkie wystąpienia działania są zajęte uruchamianiem innych wycinków.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Działanie jest wstrzymane i nie może uruchamiać wycinków, dopóki nie zostanie wznowione działanie.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Ponawiane wykonywania działania.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja jeszcze się nie rozpoczął.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Walidacja Trwa oczekiwanie na ponowienie próby.</td>
</tr>
<tr>
<tr>
<td rowspan="2">W toku</td><td>Sprawdzanie poprawności</td><td>Weryfikacja jest w toku.</td>
</tr>
<td>-</td>
<td>Wycinek jest przetwarzany.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>Przekroczono limit czasu</td><td>Wykonanie działanie trwało dłużej niż dozwolona przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Wycinek została anulowana przez użytkownika akcji.</td>
</tr>
<tr>
<td>Walidacja</td><td>Weryfikacja nie powiodła się.</td>
</tr>
<tr>
<td>-</td><td>Wycinek nie powiodło się wygenerowany i/lub zweryfikować.</td>
</tr>
<td>Gotowe</td><td>-</td><td>Wycinek jest gotowy do użycia.</td>
</tr>
<tr>
<td>Pominięte</td><td>Brak</td><td>Wycinek nie jest przetwarzany.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Wycinek miał poprzednio inny stan, ale zostało zresetowane.</td>
</tr>
</table>



Szczegóły wycinka można wyświetlić, klikając wpis wycinek **ostatnio zaktualizowane wycinki** bloku.

![Szczegóły wycinka](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Jeśli wycinek została wykonana wiele razy, zobaczysz wiele wierszy w **uruchomienia działania** listy. Możesz wyświetlić szczegóły dotyczące działania Uruchom, klikając pozycję Uruchom wpisu w **uruchomienia działania** listy. Lista zawiera wszystkie pliki dziennika, oraz komunikat o błędzie, jeśli taka istnieje. Ta funkcja jest przydatna do wyświetlania dzienników i debugowania bez konieczności opuszczania fabryką danych.

![Szczegóły uruchamiania działania](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Jeśli wycinek nie znajduje się w **gotowe** stanu, możesz zobaczyć wycinki strumienia wychodzącego, które nie są gotowe i blokują bieżącego wycinka na wykonywanie **niegotowe wycinki strumienia wychodzącego nie są gotowe** listy. Ta funkcja jest przydatna, gdy Twoje wycinek jest w **oczekiwania** stanu i chcesz zrozumieć zależności strumienia wychodzącego, które wycinek oczekuje na.

![Niegotowe wycinki strumienia wychodzącego nie są gotowe](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stanu zestawu danych
Po wdrażanie fabryki danych, potoki prawidłowe okresu aktywności, zestaw danych dzieli przejścia z jednego stanu do drugiego. Obecnie stan wycinka następuje na poniższym diagramie stanu:

![Diagram stanu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Przepływ przejścia stanu zestawu danych w usłudze data factory jest następująca: Oczekiwania -> w toku/w toku (Sprawdzanie poprawności) -> gotowe lub nie powiodła się.

Wycinek jest uruchamiany w **oczekiwania** stanu oczekiwania na warunki wstępne, które muszą być spełnione przed rozpoczęciem wykonywania. Następnie działanie rozpoczyna wykonywanie i wycinek przechodzi w stan **w toku** stanu. Wykonania działania może powodzenie lub niepowodzenie. Wycinek jest oznaczony jako **gotowe** lub, na podstawie wyniku wykonania.

Można zresetować wycinka tak, aby wrócić do poprzedniej strony z **gotowe** lub **niepowodzenie** stan **oczekiwania** stanu. Można również oznaczyć, że stan wycinka na **Pomiń**, co uniemożliwia działania wykonywania i nie przetwarzania wycinka.

## <a name="pause-and-resume-pipelines"></a>Wstrzymywanie i wznawianie potoków
Potoki można zarządzać za pomocą programu Azure PowerShell. Na przykład można wstrzymywać i wznawiać potoki przez uruchomienie polecenia cmdlet programu Azure PowerShell przez użytkownika. 

> [!NOTE] 
> Widok diagramu nie obsługuje wstrzymywanie i wznawianie potoków. Jeśli chcesz użyć interfejsu użytkownika, za pomocą aplikacji monitorowanie i zarządzanie nimi. Aby uzyskać szczegółowe informacje dotyczące korzystania z aplikacji, zobacz [monitorowanie i zarządzanie nimi potoków usługi Data Factory przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) artykułu. 

Można pause/zawiesić potoków przy użyciu **AzDataFactoryPipeline Wstrzymaj** polecenia cmdlet programu PowerShell. To polecenie cmdlet jest przydatne, gdy nie chcesz uruchomienia potoków, dopóki problem nie zostanie rozwiązany. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Na przykład:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po usunięciu problemu z potokiem, możesz wznowić zawieszone potoku, uruchamiając następujące polecenie programu PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Na przykład:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Debugowanie potoków
Usługa Azure Data Factory zapewnia szerokie możliwości debugowania i rozwiązywanie problemów z potokami przy użyciu witryny Azure portal i programu Azure PowerShell.

> [!NOTE] 
> Jest znacznie łatwiejsze Rozwiązywanie problemów z błędami, przy użyciu aplikacji Zarządzanie i monitorowanie. Aby uzyskać szczegółowe informacje dotyczące korzystania z aplikacji, zobacz [monitorowanie i zarządzanie nimi potoków usługi Data Factory przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) artykułu. 

### <a name="find-errors-in-a-pipeline"></a>Znajdź błędy w potoku
W przypadku niepowodzenia uruchomienia działania w potoku zestawu danych, który jest wytwarzany przez potok jest w stanie Błąd z powodu błędu. Można debugować i rozwiązywanie problemów w usłudze Azure Data Factory przy użyciu następujących metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Debugowanie błędów przy użyciu witryny Azure portal
1. Na **tabeli** bloku, kliknij przycisk wycinek problem, który ma **stan** ustawioną.

   ![Blok tabeli z wycinek problem](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na **wycinka danych** bloku, kliknij przycisk Uruchom działania, który uległ awarii.

   ![Wycinek danych z powodu błędu](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na **szczegóły uruchamiania działania** bloku, możesz pobrać pliki, które są związane z przetwarzaniem HDInsight. Kliknij przycisk **Pobierz** dla stanu/stderr do pobrania pliku dziennika błędów, który zawiera szczegóły dotyczące błędu.

   ![Uruchomienie bloku szczegółów z powodu błędu działania](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Błąd podczas debugowania przy użyciu programu PowerShell
1. Uruchom program **PowerShell**.
2. Uruchom **Get AzDataFactorySlice** polecenie, aby wyświetlić wycinki oraz ich stan. Powinny pojawić się wycinek ze statusem.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Na przykład:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Zastąp **StartDateTime** czas rozpoczęcia potoku. 
3. Teraz uruchom **Get AzDataFactoryRun** polecenia cmdlet w celu uzyskania szczegółowych informacji o działaniu uruchomienia dla wycinka.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Na przykład:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Wartość StartDateTime oznacza czas rozpoczęcia dla wycinka błąd lub problem, który zauważyć w poprzednim kroku. Daty i godziny, powinna zostać ujęta w cudzysłów.
4. Powinny zostać wyświetlone dane wyjściowe zawierające szczegółowe informacje o błędzie, który jest podobny do następującego:

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
5. Możesz uruchomić **AzDataFactoryLog Zapisz** polecenia cmdlet z wartością identyfikatora, zobacz z danych wyjściowych i pobrania plików dziennika przy użyciu **- DownloadLogsoption** polecenia cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Błędów ponownego uruchomienia w potoku

> [!IMPORTANT]
> Jest to łatwiejsze Rozwiązywanie problemów z błędami i ponownie uruchomić wycinki z błędami, korzystając z funkcji monitorowania i zarządzania aplikacji. Aby uzyskać szczegółowe informacje dotyczące korzystania z aplikacji, zobacz [monitorowanie i zarządzanie nimi potoków usługi Data Factory przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Po Rozwiązywanie problemów i debugowanie błędów w potoku, możesz ponownie uruchomić błędów, przechodząc do wycinka błąd i klikając **Uruchom** przycisk na pasku poleceń.

![Ponownie uruchomić wycinek nie powiodło się](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

W przypadku wycinek Weryfikacja nie powiodła się z powodu błędu zasad (na przykład, jeśli dane nie są dostępne), można naprawić błąd i Zweryfikuj ponownie, klikając **weryfikacji** przycisk na pasku poleceń.

![Napraw błędy i sprawdzanie poprawności](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Błędy, można uruchomić program za pomocą **AzDataFactorySliceStatus zestaw** polecenia cmdlet. Zobacz [AzDataFactorySliceStatus zestaw](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) temacie Informacje o składni i inne szczegóły dotyczące polecenia cmdlet.

**Przykład:**

Poniższy przykład ustawia stan wszystkich wycinków dla tabeli "DAWikiAggregatedData" Oczekiwanie w usłudze Azure data factory "WikiADF".

Typ "aktualizacji" jest ustawiona na "Niego parametru UpstreamInPipeline", co oznacza, że stan każdego wycinka do tabeli i wszystkie tabele zależne (nadrzędnego) są ustawione na "Oczekiwanie". Możliwa wartość tego parametru jest "Indywidualny".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Tworzenie alertów w witrynie Azure portal

1.  Zaloguj się w witrynie Azure portal i wybierz **Monitor -> alerty** można otworzyć na stronie alertów.

    ![Otwórz na stronie alertów.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Wybierz **+ Nowa reguła alertu** można utworzyć nowego alertu.

    ![Tworzenie nowego alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Zdefiniuj **warunek alertu**. (Upewnij się, że wybrano **fabryk danych** w **Filtruj według typu zasobu** pola.) Można również określić wartości dla **wymiary**.

    ![Zdefiniuj warunek alertu — Wybieranie lokalizacji docelowej](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Zdefiniuj warunek alertu — Dodaj kryteria alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Zdefiniuj warunek alertu — Dodaj logikę alertów](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Zdefiniuj **szczegóły alertu**.

    ![Zdefiniuj szczegóły alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Zdefiniuj **grupy akcji**.

    ![Określ grupę akcji — Utwórz nową grupę akcji](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Określ grupę akcji — Ustawianie właściwości](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Określ grupę akcji — Nowa grupa akcji utworzone](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Przenieś fabrykę danych do innej grupy zasobów lub subskrypcji
Usługi data factory można przenieść do innej grupy zasobów lub w innej subskrypcji przy użyciu **przenieść** polecenia paska przycisk na stronie głównej fabryki danych.

![Przenieś fabryki danych](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Można również przenosić jakiekolwiek powiązane zasoby (takie jak alerty, które są skojarzone z usługą data factory), wraz z fabryką danych.

![Okno dialogowe przenoszenia zasobów](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
