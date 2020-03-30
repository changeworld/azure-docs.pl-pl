---
title: Monitorowanie potoków i zarządzanie nimi przy użyciu portalu Azure i programu PowerShell
description: Dowiedz się, jak korzystać z witryny Azure portal i programu Azure PowerShell do monitorowania utworzonych fabryk danych i potoków platformy Azure oraz zarządzania nimi.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73666979"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorowanie potoków usługi Azure Data Factory i zarządzanie nimi przy użyciu portalu Azure i programu PowerShell
> [!div class="op_single_selector"]
> * [Korzystanie z portalu Azure/usługi Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Korzystanie z aplikacji Monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [monitorowanie potoków fabryki danych i zarządzanie nimi w pliku](../monitor-visually.md).

W tym artykule opisano sposób monitorowania, zarządzania i debugowania potoków przy użyciu witryny Azure Portal i programu PowerShell.

> [!IMPORTANT]
> Aplikacja do monitorowania & zarządzania zapewnia lepszą obsługę monitorowania i zarządzania potokami danych i rozwiązywania problemów. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie potoków fabryki danych i zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Usługa Azure Data Factory w wersji 1 korzysta teraz z nowej [infrastruktury alertów usługi Azure Monitor.](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) Stara infrastruktura alertów jest przestarzała. W rezultacie istniejące alerty skonfigurowane dla fabryk danych w wersji 1 nie działają. Istniejące alerty dla fabryk danych w wersji 1 nie są migrowane automatycznie. Należy ponownie utworzyć te alerty w nowej infrastrukturze alertów. Zaloguj się do witryny Azure portal i wybierz **monitor,** aby utworzyć nowe alerty dotyczące metryk (takich jak nieudane przebiegi lub pomyślne przebiegi) dla fabryk danych w wersji 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Opis potoków i stanów aktywności
Za pomocą witryny Azure portal, można:

* Wyświetl fabrykę danych jako diagram.
* Wyświetlanie działań w potoku.
* Wyświetlanie wejściowych i wyjściowych zestawów danych.

W tej sekcji opisano również, jak plasterek zestawu danych przechodzi z jednego stanu do innego stanu.   

### <a name="navigate-to-your-data-factory"></a>Przejdź do fabryki danych
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Kliknij **polecenie Fabryki danych** w menu po lewej stronie. Jeśli go nie widzisz, kliknij pozycję **Więcej usług >**, a następnie kliknij pozycję Fabryki **danych** w kategorii ANALIZY **+ ANALIZY.**

   ![Przeglądaj wszystkie fabryki danych >](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. W bloku **Fabryki danych** wybierz fabrykę danych, która Cię interesuje.

    ![Wybieranie fabryki danych](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Powinna zostać wyświetlona strona główna fabryki danych.

   ![Blok Fabryka danych](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Widok diagramu fabryki danych
Widok **diagramu** fabryki danych udostępnia pojedynczą szybę do monitorowania fabryki danych i zarządzania nią oraz zarządzania nią. Aby wyświetlić widok **diagramu** fabryki danych, kliknij pozycję **Diagram** na stronie głównej fabryki danych.

![Widok diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Możesz powiększać, pomniejszać, powiększać, aby dopasować, powiększać do 100%, blokować układ diagramu i automatycznie umieszczać potoki i zestawy danych. Można również wyświetlić informacje o rodowód danych (czyli pokaż upstream i downstream elementy wybranych elementów).

### <a name="activities-inside-a-pipeline"></a>Działania wewnątrz rurociągu
1. Kliknij prawym przyciskiem myszy potok, a następnie kliknij polecenie **Otwórz potok,** aby wyświetlić wszystkie działania w potoku, wraz z wejściowymi i wyjściowymi zestawami danych dla działań. Ta funkcja jest przydatna, gdy potok zawiera więcej niż jedno działanie i chcesz zrozumieć linię operacyjną pojedynczego potoku.

    ![Menu Otwórz potok](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. W poniższym przykładzie zostanie wyświetlony działanie kopiowania w potoku z danych wejściowych i danych wyjściowych. 

    ![Działania wewnątrz rurociągu](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Możesz wrócić do strony głównej fabryki danych, klikając łącze **Fabryka danych** w przycisku nawiga w lewym górnym rogu.

    ![Przechodzenie z powrotem do fabryki danych](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Wyświetlanie stanu każdego działania wewnątrz potoku
Można wyświetlić bieżący stan działania, wyświetlając stan dowolnego zestawu danych, które są tworzone przez działanie.

Klikając dwukrotnie **OutputBlobTable** na **diagramie,** można wyświetlić wszystkie wycinki, które są produkowane przez różne działania przebiega wewnątrz potoku. Widać, że działanie kopiowania działało pomyślnie przez ostatnie osiem godzin i wyprodukowało wycinki w stanie **Gotowe.**  

![Stan rurociągu](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Wycinki zestawu danych w fabryce danych mogą mieć jeden z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Podstanek</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>Czas harmonogramu</td><td>Nie nadszedł czas, aby kawałek do uruchomienia.</td>
</tr>
<tr>
<td>Zestaw danychZależności</td><td>Zależności nadrzędne nie są gotowe.</td>
</tr>
<tr>
<td>Zasoby obliczeniowe</td><td>Zasoby obliczeniowe nie są dostępne.</td>
</tr>
<tr>
<td>WspółbieżnośćLiczek</td> <td>Wszystkie wystąpienia działania są zajęte uruchamianiem innych plasterków.</td>
</tr>
<tr>
<td>AktywnośćResume</td><td>Działanie jest wstrzymane i nie można uruchomić plasterki, dopóki działanie zostanie wznowione.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Wykonanie działania jest ponowione.</td>
</tr>
<tr>
<td>Sprawdzanie poprawności</td><td>Sprawdzanie poprawności jeszcze się nie rozpoczęło.</td>
</tr>
<tr>
<td>Sprawdzanie poprawności</td><td>Sprawdzanie poprawności czeka na ponowne ponowione.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress (Ruch przychodzący)</td><td>Sprawdzanie poprawności</td><td>Trwa sprawdzanie poprawności.</td>
</tr>
<td>-</td>
<td>Plasterek jest przetwarzany.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>Limit czasu</td><td>Wykonanie działania trwało dłużej niż to, co jest dozwolone przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Plasterek został anulowany przez akcję użytkownika.</td>
</tr>
<tr>
<td>Sprawdzanie poprawności</td><td>Sprawdzanie poprawności nie powiodło się.</td>
</tr>
<tr>
<td>-</td><td>Nie można wygenerować i/lub zweryfikować plasterka.</td>
</tr>
<td>Gotowy</td><td>-</td><td>Plasterek jest gotowy do spożycia.</td>
</tr>
<tr>
<td>Pominięto</td><td>Brak</td><td>Plasterek nie jest przetwarzany.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Plasterek istniał z innym stanem, ale został zresetowany.</td>
</tr>
</table>



Szczegóły dotyczące plasterka można wyświetlić, klikając wpis plasterka w bloku **Ostatnio zaktualizowane plasterki.**

![Szczegóły plasterka](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Jeśli plasterek został wykonany wiele razy, na liście **Przebiegi działania** jest widoczna wiele wierszy. Można wyświetlić szczegółowe informacje o działaniu uruchamianym, klikając wpis uruchom na liście **Przebiegi działania.** Lista zawiera wszystkie pliki dziennika, wraz z komunikatem o błędzie, jeśli istnieje. Ta funkcja jest przydatna do wyświetlania i debugowania dzienników bez konieczności opuszczania fabryki danych.

![Szczegóły uruchamiania działania](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Jeśli plasterek nie jest w stanie **Gotowy,** można zobaczyć wycinki nadrzędne, które nie są gotowe i blokują bieżący plasterek z wykonywania na liście **Upstream wycinków, które nie są gotowe.** Ta funkcja jest przydatna, gdy plasterek jest w stanie **oczekiwania** i chcesz zrozumieć zależności nadrzędne, na których oczekuje plasterek.

![Wycinki nadrzędne, które nie są gotowe](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stanu zestawu danych
Po wdrożeniu fabryki danych i potoki mają prawidłowy aktywny okres, zestaw danych plasterki przejścia z jednego stanu do drugiego. Obecnie stan plasterka jest zgodny z następującym diagramem stanu:

![Diagram stanu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Przepływ przejścia stanu zestawu danych w fabryce danych jest następujący: Oczekiwanie -> w toku/w toku (sprawdzanie poprawności) -> gotowe/nie powiodło się.

Plasterek rozpoczyna się w stanie **oczekiwania,** czekając na warunki wstępne, które mają być spełnione przed jego wykonaniem. Następnie działanie rozpoczyna wykonywanie, a plasterek przechodzi do stanu **w toku.** Wykonanie działania może zakończyć się pomyślnie lub zakończyć się niepowodzeniem. Plasterek jest oznaczony jako **Gotowy** lub **Nie powiodło się**, na podstawie wyniku wykonania.

Można zresetować plasterek, aby wrócić ze stanu **Gotowe** lub **Nie powiodło się** do stanu **oczekiwania.** Można również oznaczyć stan plasterka, aby **pominąć**, co uniemożliwia wykonywanie działania i nie przetwarzanie plasterka.

## <a name="pause-and-resume-pipelines"></a>Wstrzymywanie i wznawianie potoków
Potoki można zarządzać przy użyciu programu Azure PowerShell. Na przykład można wstrzymać i wznowić potoki, uruchamiając polecenia cmdlet programu Azure PowerShell. 

> [!NOTE] 
> Widok diagramu nie obsługuje wstrzymywania i wznawiania potoków. Jeśli chcesz użyć interfejsu użytkownika, użyj monitorowania i zarządzania aplikacją. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie i zarządzanie potokami fabryki danych przy użyciu](data-factory-monitor-manage-app.md) aplikacji Monitorowanie i zarządzanie artykułu. 

Potoki można wstrzymać/zawiesić przy użyciu polecenia cmdlet **programu PowerShell w zawieszeniu AzDataFactoryPipeline.** To polecenie cmdlet jest przydatne, gdy nie chcesz uruchamiać potoków, dopóki problem nie zostanie rozwiązany. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Przykład:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po naprawieniu problemu z potokiem można wznowić zawieszony potok, uruchamiając następujące polecenie programu PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Przykład:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Potoki debugowania
Usługa Azure Data Factory zapewnia zaawansowane możliwości debugowania i rozwiązywania problemów z potokami przy użyciu witryny Azure portal i programu Azure PowerShell.

> [!NOTE] 
> Znacznie łatwiej jest błędy problematyki za pomocą monitorowania & zarządzania aplikacji. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie i zarządzanie potokami fabryki danych przy użyciu](data-factory-monitor-manage-app.md) aplikacji Monitorowanie i zarządzanie artykułu. 

### <a name="find-errors-in-a-pipeline"></a>Znajdowanie błędów w potoku
Jeśli uruchomienie działania zakończy się niepowodzeniem w potoku, zestaw danych, który jest produkowany przez potok jest w stanie błędu z powodu błędu. Można debugować i rozwiązywać problemy z błędami w usłudze Azure Data Factory przy użyciu następujących metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Używanie portalu Azure do debugowania błędu
1. Na bloku **Tabela** kliknij plasterek problemu, który ma **stan** ustawiony na **Niepowodzenie**.

   ![Ostrze stołu z plasterkiem problemowym](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. W bloku **Plasterek danych** kliknij przebieg działania, który nie powiódł się.

   ![Plasterek danych z błędem](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. W **bloku szczegóły przebiegu działania** można pobrać pliki skojarzone z przetwarzaniem HDInsight. Kliknij **przycisk Pobierz** dla stanu/stderr, aby pobrać plik dziennika błędów, który zawiera szczegółowe informacje o błędzie.

   ![Blok szczegółów przebiegu działania z błędem](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Debugowanie błędu za pomocą programu PowerShell
1. Uruchom program **PowerShell**.
2. Uruchom polecenie **Get-AzDataFactorySlice,** aby wyświetlić wycinki i ich stany. Powinien zostać wyświetlony plasterek o stanie **Niepowodzenie**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Przykład:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Zamień **StartDateTime** z czasem rozpoczęcia potoku. 
3. Teraz uruchom polecenie cmdlet **Get-AzDataFactoryRun,** aby uzyskać szczegółowe informacje na temat działania uruchamianego dla plasterka.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Przykład:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Wartość StartDateTime jest czas rozpoczęcia dla błędu/problem plasterek, który został odnotowany z poprzedniego kroku. Data-godzina powinna być ujęta w cudzysłowie.
4. Powinny być widoczne dane wyjściowe ze szczegółami dotyczącymi błędu, który jest podobny do następującego:

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
5. Polecenie cmdlet **Save-AzDataFactoryLog** można uruchomić z wartością identyfikatora widoczna z danych wyjściowych, a następnie pobrać pliki dziennika przy użyciu **polecenia cmdlet -DownloadLogsoption.**

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Ponowne uruchomienie błędów w potoku

> [!IMPORTANT]
> Łatwiej jest rozwiązywać błędy i ponownie uruchomić nieudane plasterki za pomocą aplikacji Monitorowania & Zarządzania. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie potoków fabryki danych i zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Po rozwiązywaniu problemów i debugowaniu błędów w potoku można ponownie uruchomić błędy, przechodząc do plasterka błędu i klikając przycisk **Uruchom** na pasku poleceń.

![Ponowne uruchomienie fragmentu po awarii](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

W przypadku, gdy wycinka nie powiodło się sprawdzanie poprawności z powodu błędu zasad (na przykład, jeśli dane nie są dostępne), można naprawić błąd i sprawdzić poprawność ponownie, klikając przycisk **Sprawdź poprawność** na pasku poleceń.

![Naprawianie błędów i sprawdzanie poprawności](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Błędy można ponownie uruchomić przy użyciu polecenia cmdlet **Set-AzDataFactorySliceStatus.** Zobacz [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) temat składni i inne szczegóły dotyczące polecenia cmdlet.

**Przykład:**

W poniższym przykładzie ustawia stan wszystkich wycinków dla tabeli "DAWikiAggregatedData" na "Oczekiwanie" w fabryce danych platformy Azure "WikiADF".

"UpdateType" jest ustawiona na "UpstreamInPipeline", co oznacza, że stany każdego plasterka dla tabeli i wszystkich tabel zależnych (nadrzędnych) są ustawione na "Oczekiwanie". Inną możliwą wartością dla tego parametru jest "Individual".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Tworzenie alertów w witrynie Azure portal

1.  Zaloguj się do witryny Azure portal i wybierz **pozycję Monitor -> Alerty,** aby otworzyć stronę Alerty.

    ![Otwórz stronę Alerty.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Wybierz **+ Nowa reguła alertu,** aby utworzyć nowy alert.

    ![Tworzenie nowego alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Zdefiniuj **warunek alertu**. (Upewnij się, że w polu **Filtruj według typu zasobu** wybierz **pozycję Fabryki danych).** Można również określić wartości **dla wymiarów**.

    ![Zdefiniuj warunek alertu — wybierz obiekt docelowy](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definiowanie warunku alertu — dodawanie kryteriów alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definiuj warunek alertu — dodawanie logiki alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Zdefiniuj **szczegóły alertu**.

    ![Definiowanie szczegółów alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Zdefiniuj **grupę Akcja**.

    ![Zdefiniuj grupę akcji — utwórz nową grupę akcji](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Zdefiniuj właściwości grupy akcji](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definiuj grupę akcji — utworzono nową grupę akcji](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Przenoszenie fabryki danych do innej grupy zasobów lub subskrypcji
Fabrykę danych można przenieść do innej grupy zasobów lub innej subskrypcji za pomocą przycisku **Przenieś** pasek poleceń na stronie głównej fabryki danych.

![Przenoszenie fabryki danych](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Można również przenieść wszystkie powiązane zasoby (takie jak alerty skojarzone z fabryką danych) wraz z fabryką danych.

![Okno dialogowe Przenoszenie zasobów](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
