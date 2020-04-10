---
title: Monitorowanie zadań usługi Azure Stream Analytics i zarządzanie nimi za pomocą programu PowerShell
description: W tym artykule opisano sposób używania programu Azure PowerShell i poleceń cmdlet do monitorowania zadań usługi Azure Stream Analytics i zarządzania nimi.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 295141dfd9b84428e2ee69354ab0c249fa46d1b6
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998881"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorowanie zadań usługi Stream Analytics i zarządzanie nimi za pomocą poleceń cmdlet usługi Azure PowerShell
Dowiedz się, jak monitorować zasoby usługi Stream Analytics i zarządzać nimi za pomocą poleceń cmdlet programu Azure PowerShell i skryptów programu PowerShell, które wykonują podstawowe zadania usługi Stream Analytics.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Wymagania wstępne dotyczące uruchamiania poleceń cmdlet programu Azure PowerShell dla usługi Stream Analytics
* Utwórz grupę zasobów platformy Azure w ramach subskrypcji. Poniżej przedstawiono przykładowy skrypt programu Azure PowerShell. Aby uzyskać informacje o programie Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview);  

Program Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Program Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Zadania usługi Stream Analytics utworzone programowo nie mają domyślnie włączonego monitorowania.  Monitorowanie można ręcznie włączyć w witrynie Azure Portal, przechodząc do strony Monitor zadania i klikając przycisk Włącz, lub można to zrobić programowo, wykonując kroki znajdujące się w [usłudze Azure Stream Analytics — programowo monitoruj zadania analizy strumienia.](stream-analytics-monitor-jobs.md)
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Polecenia cmdlet programu Azure PowerShell dla usługi Stream Analytics
Następujące polecenia cmdlet usługi Azure PowerShell mogą służyć do monitorowania zadań usługi Azure Stream Analytics i zarządzania nimi. Należy zauważyć, że program Azure PowerShell ma różne wersje. 
**W przykładach wymienionych pierwsze polecenie jest dla programu Azure PowerShell 0.9.8, drugie polecenie jest dla programu Azure PowerShell 1.0.** Polecenia programu Azure PowerShell 1.0 zawsze będą miały "Az" w poleceniu.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsOb | Get-AzStreamAnalyticsOb
Wyświetla listę wszystkich zadań usługi Stream Analytics zdefiniowanych w subskrypcji platformy Azure lub określonej grupie zasobów lub pobiera informacje o zadaniu o określonym zadaniu w grupie zasobów.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

To polecenie programu PowerShell zwraca informacje o wszystkich zadaniach usługi Stream Analytics w ramach subskrypcji platformy Azure.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

To polecenie programu PowerShell zwraca informacje o wszystkich zadaniach usługi Stream Analytics w grupie zasobów StreamAnalytics-Default-Central-US.

**Przykład 3**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

To polecenie programu PowerShell zwraca informacje o zadaniu usługi Stream Analytics StreamingJob w grupie zasobów StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Wyświetla listę wszystkich danych wejściowych, które są zdefiniowane w określonym zadaniu usługi Stream Analytics lub pobiera informacje o określonych danych wejściowych.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

To polecenie programu PowerShell zwraca informacje o wszystkich danych wejściowych zdefiniowanych w zadaniu StreamingJob.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

To polecenie programu PowerShell zwraca informacje o danych wejściowych o nazwie EntryStream zdefiniowanych w zadaniu StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Wyświetla listę wszystkich wyjść, które są zdefiniowane w określonym zadaniu usługi Stream Analytics lub pobiera informacje o określonych danych wyjściowych.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

To polecenie programu PowerShell zwraca informacje o wyjściach zdefiniowanych w zadaniu StreamingJob.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell zwraca informacje o danych wyjściowych zdefiniowanych w zadaniu StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Pobiera informacje o przydział jednostkach przesyłania strumieniowego w określonym regionie.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

To polecenie programu PowerShell zwraca informacje o przydziałze i użyciu jednostek przesyłania strumieniowego w regionie Środkowe stany USA.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformacja
Pobiera informacje o określonej transformacji zdefiniowane w zadaniu usługi Stream Analytics.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Program Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

To polecenie programu PowerShell zwraca informacje o transformacji o nazwie StreamingJob w zadaniu StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>Nowa usługa AzureStreamAnalyticsInput | Nowa-AzStreamAnalyticsInput
Tworzy nowe dane wejściowe w zadaniu usługi Stream Analytics lub aktualizuje istniejące określone dane wejściowe.

Nazwę danych wejściowych można określić w pliku .json lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz dane wejściowe, które już istnieje i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące dane wejściowe.

Jeśli określisz parametr -Force i określisz istniejącą nazwę danych wejściowych, dane wejściowe zostaną zastąpione bez potwierdzenia.

Szczegółowe informacje na temat struktury i zawartości pliku JSON można znaleźć w sekcji [Tworzenie danych wejściowych (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] [biblioteki referencyjnej interfejsu API REST][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

To polecenie programu PowerShell tworzy nowe dane wejściowe z pliku Input.json. Jeśli istniejące dane wejściowe o nazwie określonej w pliku definicji danych wejściowych są już zdefiniowane, polecenie cmdlet zapyta, czy go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

To polecenie programu PowerShell tworzy nowe dane wejściowe w zadaniu o nazwie EntryStream. Jeśli istniejące dane wejściowe o tej nazwie są już zdefiniowane, polecenie cmdlet zapyta, czy go zastąpić.

**Przykład 3**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

To polecenie programu PowerShell zastępuje definicję istniejącego źródła wejściowego o nazwie EntryStream definicją z pliku.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>Nowa usługa AzureStreamAnalyticsJob | Nowy-AzStreamAnalyticsOb
Tworzy nowe zadanie usługi Stream Analytics na platformie Microsoft Azure lub aktualizuje definicję istniejącego określonego zadania.

Nazwę zadania można określić w pliku .json lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz nazwę zadania, która już istnieje i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące zadanie.

Jeśli określisz parametr -Force i określisz istniejącą nazwę zadania, definicja zadania zostanie zastąpiona bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zobacz sekcję [Tworzenie zadania analizy strumienia][msdn-rest-api-create-stream-analytics-job] [w bibliotece referencyjnej interfejsu API REST usługi Stream Analytics][stream.analytics.rest.api.reference]Management .

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

To polecenie programu PowerShell tworzy nowe zadanie z definicji w pliku JobDefinition.json. Jeśli istniejące zadanie o nazwie określonej w pliku definicji zadania jest już zdefiniowane, polecenie cmdlet zapyta, czy go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

To polecenie programu PowerShell zastępuje definicję zadania dla streamingjob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>Nowa usługa AzureStreamAnalyticsOutput | Nowa-AzStreamAnalyticsOutput
Tworzy nowe dane wyjściowe w zadaniu usługi Stream Analytics lub aktualizuje istniejące dane wyjściowe.  

Nazwę danych wyjściowych można określić w pliku .json lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz dane wyjściowe, które już istnieje i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące dane wyjściowe.

Jeśli określisz parametr -Force i określisz istniejącą nazwę danych wyjściowych, dane wyjściowe zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zobacz sekcję [Tworzenie danych wyjściowych (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] [biblioteki referencyjnej interfejsu API REST][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

To polecenie programu PowerShell tworzy nowe dane wyjściowe o nazwie "dane wyjściowe" w zadaniu StreamingJob. Jeśli istniejące dane wyjściowe o tej nazwie są już zdefiniowane, polecenie cmdlet zapyta, czy go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

To polecenie programu PowerShell zastępuje definicję "dane wyjściowe" w zadaniu StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>Nowa-AzureStreamAnalyticsTransformation | Nowa-AzStreamAnalyticsTransformation
Tworzy nową transformację w zadaniu usługi Stream Analytics lub aktualizuje istniejącą transformację.

Nazwę transformacji można określić w pliku .json lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz transformację, która już istnieje i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejącą transformację.

Jeśli określisz parametr -Force i określisz istniejącą nazwę transformacji, transformacja zostanie zastąpiona bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zobacz sekcję [Tworzenie transformacji (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] [biblioteki referencyjnej interfejsu API REST][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

To polecenie programu PowerShell tworzy nową transformację o nazwie StreamingJobTransform w zadaniu StreamingJob. Jeśli istniejąca transformacja jest już zdefiniowana przy tej nazwie, polecenie cmdlet zapyta, czy ją zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Program Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 To polecenie programu PowerShell zastępuje definicję StreamingJobTransform w zadaniu StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Usuń-AzureStreamAnalyticsInput | Usuń-AzStreamAnalyticsInput
Asynchronicznie usuwa określone dane wejściowe z zadania usługi Stream Analytics na platformie Microsoft Azure.  
Jeśli określisz parametr -Force, dane wejściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Program Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

To polecenie programu PowerShell usuwa wejście EventStream w zadaniu StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Usuń-AzureStreamAnalyticsOb | Usuń-AzStreamAnalyticsOb
Asynchronicznie usuwa określone zadanie usługi Stream Analytics na platformie Microsoft Azure.  
Jeśli określisz parametr -Force, zadanie zostanie usunięte bez potwierdzenia.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Program Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

To polecenie programu PowerShell usuwa zadanie StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Usuń-AzureStreamAnalyticsOutput | Usuń-AzStreamAnalyticsOutput
Asynchronicznie usuwa określone dane wyjściowe z zadania usługi Stream Analytics na platformie Microsoft Azure.  
Jeśli określisz parametr -Force, dane wyjściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Program Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell usuwa dane wyjściowe w zadaniu StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsOb | Start-AzStreamAnalyticsOb
Asynchronicznie wdraża i uruchamia zadanie usługi Stream Analytics na platformie Microsoft Azure.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Program Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

To polecenie programu PowerShell uruchamia zadanie StreamingJob z niestandardowym czasem rozpoczęcia danych wyjściowych ustawionym na 12 grudnia 2012 r., 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsOb | Stop-AzStreamAnalyticsOb
Asynchronicznie zatrzymuje zadanie usługi Stream Analytics z systemem na platformie Microsoft Azure i de-przydziela zasoby, które były używane. Definicja zadania i metadane pozostaną dostępne w ramach subskrypcji za pośrednictwem witryny Azure portal i interfejsów API zarządzania, dzięki czemu zadanie może być edytowane i uruchamiane ponownie. Opłata za pracę w stanie zatrzymania nie zostanie naliczona.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Program Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

To polecenie programu PowerShell zatrzymuje zadanie StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testuje możliwość stream analytics połączyć się z określonym wejściem.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Program Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

To polecenie programu PowerShell testuje stan połączenia wejściowego entrystream w streamingjob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testuje możliwość stream analytics, aby połączyć się z określonym wyjściem.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Program Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell testuje stan połączenia danych wyjściowych w pliku StreamingJob.  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

