---
title: Monitorowanie i zarządzanie nimi zadania usługi analiza strumienia Azure przy użyciu programu PowerShell
description: W tym artykule opisano sposób użycia programu Azure PowerShell i polecenia cmdlet można monitorować i zarządzać nimi zadania usługi analiza strumienia Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ed47141c46259007227ed391c1e114932153de38
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorowanie i zarządzanie nimi zadania usługi analiza strumienia za pomocą poleceń cmdlet programu Azure PowerShell
Dowiedz się, jak monitorowanie i zarządzanie zasobami usługi Stream Analytics za pomocą poleceń cmdlet programu Azure PowerShell i skryptów programu powershell, które wykonywać podstawowe zadania usługi analiza strumienia.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Wymagania wstępne dotyczące uruchamiania poleceń cmdlet programu Azure PowerShell dla usługi analiza strumienia
* Utwórz grupy zasobów platformy Azure w ramach subskrypcji. Poniżej przedstawiono przykładowy skrypt programu PowerShell systemu Azure. Informacje programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview);  

Program Azure PowerShell 0.9.8:  

         # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Program Azure PowerShell 1.0:  

         # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Zadania usługi analiza strumienia utworzone programowo monitorowania, domyślnie nie jest konieczne.  Można ręcznie włączyć monitorowanie w portalu Azure nawigowania do strony Monitor zadania, a następnie klikając przycisk włączania lub programowo to zrobić, wykonując kroki opisane w lokalizacji [Azure Stream Analytics — Monitor Stream Analytics zadania programowo](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure poleceń cmdlet programu PowerShell dla usługi analiza strumienia
Następujące polecenia cmdlet programu Azure PowerShell umożliwia monitorowanie i zarządzanie nimi zadania usługi analiza strumienia Azure. Należy pamiętać, że programu Azure PowerShell ma różne wersje. 
**W przykładach wymienione pierwsze polecenie dotyczy programu Azure PowerShell 0.9.8, drugie polecenie jest przeznaczony dla usługi Azure PowerShell 1.0.** Polecenia programu Azure PowerShell 1.0 zawsze będą mieć "AzureRM" w poleceniu.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Wyświetla listę wszystkich zadań usługi Stream Analytics zdefiniowanej w subskrypcji platformy Azure lub określonej grupy zasobów lub pobiera informacje o zadaniu o konkretnym zadaniu w grupie zasobów.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

To polecenie programu PowerShell zwraca informacje o wszystkich zadań usługi Stream Analytics w subskrypcji platformy Azure.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

To polecenie programu PowerShell zwraca informacje o wszystkich zadań usługi Stream Analytics w grupie zasobów StreamAnalytics domyślne-centralnego-US.

**Przykład 3**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

To polecenie programu PowerShell zwraca informacje o zadaniu Stream Analytics StreamingJob w grupie zasobów StreamAnalytics domyślne-centralnego-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Wyświetla listę wszystkich danych wejściowych, które są zdefiniowane w określonym zadaniu Stream Analytics lub pobiera informacje o określone dane wejściowe.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

To polecenie programu PowerShell zwraca informacje o wszystkich danych wejściowych zdefiniowane w zadaniu StreamingJob.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

To polecenie programu PowerShell zwraca informacje o danych wejściowych o nazwie EntryStream zdefiniowane w zadaniu StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Wyświetla wszystkie dane wyjściowe, które są zdefiniowane w określonym zadaniu Stream Analytics lub pobiera informacje o określonych danych wyjściowych.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

To polecenie programu PowerShell zwraca informacje o zdefiniowane w zadaniu StreamingJob dane wyjściowe.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

To polecenie programu PowerShell zwraca informacje o danych wyjściowych, o nazwie zdefiniowane w zadaniu StreamingJob danych wyjściowych.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Pobiera informacje na temat przydział jednostki w określonym regionie przesyłania strumieniowego.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Tego polecenia programu PowerShell zwraca informacje dotyczące limitu przydziału i użycia jednostek przesyłania strumieniowego w regionie środkowe stany USA.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Pobiera informacje o określonych przekształcenia zdefiniowane w zadaniu Stream Analytics.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Program Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

To polecenie programu PowerShell zwraca informacje o transformacji o nazwie StreamingJob w zadaniu StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
Tworzy nowe dane wejściowe w zadaniu Stream Analytics, lub aktualizuje istniejące określone dane wejściowe.

W pliku JSON lub w wierszu polecenia można określić nazwę danych wejściowych. Jeśli określono oba nazwy w wierszu polecenia musi być taka sama jak w pliku.

Jeśli Określ dane wejściowe, która już istnieje i nie zostanie określony parametr-Force, polecenia cmdlet zapyta, czy chcesz zastąpić istniejące dane wejściowe.

Jeśli określisz Force parametru i określ istniejącą wprowadź nazwę, danych wejściowych zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje o zawartości i struktury pliku JSON, zapoznaj się [Tworzenie danych wejściowych (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] sekcji [strumienia Analytics Management REST API odwołania biblioteki][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

To polecenie programu PowerShell tworzy nowe dane wejściowe z pliku Input.json. Jeśli zdefiniowano już istniejące dane wejściowe o podanej nazwie w pliku definicji wejściowy, polecenie cmdlet zapyta, czy chcesz go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

To polecenie programu PowerShell tworzy nowe dane wejściowe w zadaniu o nazwie EntryStream. Jeśli istniejące dane wejściowe o tej nazwie jest już zdefiniowana, polecenia cmdlet zapyta, czy chcesz go zastąpić.

**Przykład 3**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

To polecenie programu PowerShell zastępuje istniejące źródło danych wejściowych o nazwie EntryStream z definicją z pliku definicji.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Tworzy nowe zadanie usługi analiza strumienia w Microsoft Azure lub aktualizacji definicji istniejących określonego zadania.

Nazwa zadania można określić w pliku JSON lub w wierszu polecenia. Jeśli określono oba nazwy w wierszu polecenia musi być taka sama jak w pliku.

Jeśli Określ nazwę zadania, która już istnieje i nie zostanie określony parametr-Force, polecenia cmdlet zapyta, czy chcesz zastąpić istniejące zadanie.

Jeśli określisz Force parametru i określ istniejącą nazwę zadania, definicji zadania zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje o zawartości i struktury pliku JSON, zapoznaj się [utworzyć zadania usługi analiza strumienia] [ msdn-rest-api-create-stream-analytics-job] sekcji [strumienia Analytics Management REST API odwołania biblioteki][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

To polecenie programu PowerShell tworzy nowe zadanie z definicją w JobDefinition.json. Jeśli zdefiniowano już istniejące zadanie o podanej nazwie w pliku definicji zadania, polecenia cmdlet zapyta, czy chcesz go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

To polecenie programu PowerShell zastępuje StreamingJob definicji zadania.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
Tworzy nowe dane wyjściowe w zadaniu Stream Analytics, lub aktualizuje istniejące dane wyjściowe.  

Nazwa danych wyjściowych można określić w pliku JSON lub w wierszu polecenia. Jeśli określono oba nazwy w wierszu polecenia musi być taka sama jak w pliku.

Jeśli Określ wyjścia, który już istnieje i nie zostanie określony parametr-Force, polecenia cmdlet zapyta, czy chcesz zastąpić istniejące dane wyjściowe.

Jeśli określisz wymusić parametru i określ istniejącą output nazwy, dane wyjściowe zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje o zawartości i struktury pliku JSON, zapoznaj się [Tworzenie danych wyjściowych (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] sekcji [strumienia Analytics Management REST API odwołania biblioteki][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

To polecenie programu PowerShell tworzy nowe dane wyjściowe zadania StreamingJob o nazwie "wyjściowej". Jeśli istniejące dane wyjściowe o tej nazwie jest już zdefiniowana, polecenia cmdlet zapyta, czy chcesz go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

To polecenie programu PowerShell zastępuje definicji "wyjściowej" w zadaniu StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
Tworzy nowy transformacji w zadaniu Stream Analytics lub aktualizuje istniejące transformacji.

Można określić nazwę transformacji w pliku JSON lub w wierszu polecenia. Jeśli określono oba nazwy w wierszu polecenia musi być taka sama jak w pliku.

Możesz określić transformację, która już istnieje i nie zostanie określony parametr-Force, polecenia cmdlet zapyta, czy chcesz zastąpić istniejące transformacji.

Jeśli określisz wymusić parametru i określenia nazwy istniejącej przekształcania, transformacja zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje o zawartości i struktury pliku JSON, zapoznaj się [utworzyć transformacji (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] sekcji [strumienia Analytics Management REST API odwołania biblioteki][stream.analytics.rest.api.reference].

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

To polecenie programu PowerShell tworzy nowy przekształcenia o nazwie StreamingJobTransform w zadaniu StreamingJob. Jeśli istniejące transformacji jest już zdefiniowany o tej nazwie, polecenia cmdlet zapyta, czy chcesz go zastąpić.

**Przykład 2**

Program Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Program Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 To polecenie programu PowerShell zastępuje definicji StreamingJobTransform w zadaniu StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Asynchronicznie usuwa określone dane wejściowe z zadania usługi analiza strumienia w Microsoft Azure.  
Jeśli określono parametr-Force, zostaną usunięte dane wejściowe bez potwierdzenia.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Program Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

To polecenie programu PowerShell usuwa EventStream wejściowego w zadaniu StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Asynchronicznie usuwa określonego zadania usługi analiza strumienia w Microsoft Azure.  
Jeśli określono parametr-Force, zadania zostaną usunięte bez potwierdzenia.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Program Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

To polecenie programu PowerShell usuwa zadanie StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Asynchronicznie usuwa określone dane wyjściowe z zadania usługi analiza strumienia w Microsoft Azure.  
Jeśli określono parametr-Force, dane wyjściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Program Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Tego polecenia programu PowerShell usuwa dane wyjściowe dane wyjściowe zadania StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Asynchronicznie wdraża i uruchamia zadanie usługi analiza strumienia w Microsoft Azure.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Program Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

To polecenie programu PowerShell uruchomi zadanie StreamingJob danych wyjściowych niestandardowego czas rozpoczęcia ustawioną 12 grudnia 2012 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Asynchronicznie Zatrzymuje zadanie usługi analiza strumienia, w programie Microsoft Azure i zwalnia zasoby, które były, które były używane. Definicji zadania i metadanych pozostaną dostępne w ramach subskrypcji za pośrednictwem portalu Azure i zarządzanie interfejsów API, tak, aby edytować i ponownie uruchomić zadanie. Nie zostanie obciążona dla zadania w stanie zatrzymania.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Program Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

To polecenie programu PowerShell Zatrzymuje zadanie StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Testy Stream Analytics możliwość łączenia z określonych danych wejściowych.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Program Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

To polecenie programu PowerShell testów stan połączenia EntryStream wejściowego w StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Testy Stream Analytics możliwość łączenia z określonym produktem wyjściowym.

**Przykład 1**

Program Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Program Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Ten stan połączenia danych wyjściowych danych wyjściowych w StreamingJob testy polecenia programu PowerShell.  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

