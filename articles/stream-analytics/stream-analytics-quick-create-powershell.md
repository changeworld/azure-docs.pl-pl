---
title: Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell
description: W tym przewodniku Szybki start zawarto szczegółowe instrukcje korzystania z modułu Azure PowerShell w celu wdrożenia i uruchomienia zadania usługi Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212436"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą poleceń cmdlet programu PowerShell lub skryptów. W tym przewodniku Szybki start zawarto szczegółowe instrukcje korzystania z modułu Azure PowerShell w celu wdrożenia i uruchomienia zadania usługi Azure Stream Analytics. 

Przykładowe zadanie odczytuje dane przesyłane strumieniowo z obiektu blob w magazynie obiektów blob platformy Azure. Plik danych wejściowych używany w tym przewodniku Szybki start zawiera dane statyczne przeznaczone tylko dla celów ilustracyjnych. W rzeczywistym scenariuszu są używane dane wejściowe przesyłania strumieniowego powiązane z zadaniem usługi Stream Analytics. Następnie zadanie przekształca dane za pomocą języka zapytań usługi Stream Analytics, aby obliczyć średnią temperaturę w przypadku, gdy wynosi ona ponad 100°. Na koniec zapisuje wynikowe zdarzenia wyjściowe w innym pliku. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).  

* Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby znaleźć wersję zainstalowaną na komputerze lokalnym. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz artykuł [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i wprowadź swoje poświadczenia platformy Azure w podręcznym oknie przeglądarki:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Po zalogowaniu się, jeśli masz wiele subskrypcji, wybierz subskrypcję, z której chcesz korzystać na potrzeby tego przewodnika Szybki start, uruchamiając następujące polecenia cmdlet. Pamiętaj, aby zastąpić ciąg <your subscription name> nazwą swojej subskrypcji:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania usługi Stream Analytics przygotuj dane, które będą konfigurowane jako dane wejściowe zadania.

1. Pobierz [przykładowe dane czujników](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) z witryny GitHub. Kliknij prawym przyciskiem myszy link i wybierz polecenie **Zapisz link jako...** lub **Zapisz element docelowy jako**.

2. Poniższy blok kodu programu PowerShell wykonuje wiele poleceń, aby przygotować dane wejściowe wymagane przez zadanie. Przejrzyj sekcje, aby zapoznać się z kodem. 

   1. Utwórz standardowe konto magazynu ogólnego przeznaczenia za pomocą polecenia cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  W tym przykładzie tworzone jest konto magazynu o nazwie mystorageaccount z magazynem lokalnie nadmiarowym (LRS, locally redundant storage) i szyfrowaniem obiektów blob (domyślnie włączone).  

   2. Pobierz kontekst konta magazynu `$storageAccount.Context`, który definiuje konto magazynu do użycia. Podczas pracy z kontami magazynu możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia. 

   3. Utwórz kontener magazynu przy użyciu polecenia [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) i przekaż [przykładowe dane czujnika](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) pobrane wcześniej. 

   4. Skopiuj klucz magazynu zwrócony przez kod i wklej go do plików w formacie JSON służących do tworzenia danych wejściowych i wyjściowych zadania przesyłania strumieniowego na dalszym etapie.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Utwórz zadanie usługi Stream Analytics za pomocą polecenia cmdlet [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę grupy zasobów i definicję zadania. Nazwa zadania może być dowolną przyjazną nazwą, która identyfikuje zadanie. Może ona zawierać wyłącznie znaki alfanumeryczne, łączniki i podkreślenia oraz musi składać się z od 3 do 63 znaków. Definicja zadania to plik JSON, który zawiera właściwości wymagane do utworzenia zadania. Na maszynie lokalnej utwórz plik o nazwie `JobDefinition.json` i dodaj do niego następujące dane JSON:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Następnie uruchom polecenie cmdlet `New-AzureRmStreamAnalyticsJob`. Pamiętaj, aby zastąpić wartość zmiennej `jobDefinitionFile` ścieżką, w której jest przechowywany plik JSON definicji zadania. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Konfigurowanie danych wejściowych zadania

Dodaj dane wejściowe do zadania za pomocą polecenia cmdlet [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę danych wejściowych zadania, nazwę grupy zasobów i definicję danych wejściowych zadania. Definicja danych wejściowych zadania to plik JSON, który zawiera właściwości wymagane do skonfigurowania danych wejściowych zadania. W tym przykładzie jako dane wejściowe utworzysz magazyn obiektów blob. 

Na maszynie lokalnej utwórz plik o nazwie `JobInputDefinition.json` i dodaj do niego następujące dane JSON. Pamiętaj, aby zastąpić wartość `accountKey` kluczem dostępu konta magazynu, czyli wartością zapisaną w zmiennej $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Następnie uruchom polecenie cmdlet `New-AzureRmStreamAnalyticsInput`, pamiętając, aby zastąpić wartość zmiennej `jobDefinitionFile` ścieżką, w której jest przechowywany plik JSON definicji danych wejściowych zadania. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Konfigurowanie danych wyjściowych zadania

Dodaj dane wyjściowe do zadania za pomocą polecenia cmdlet [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę danych wyjściowych zadania, nazwę grupy zasobów i definicję danych wyjściowych zadania. Definicja danych wyjściowych zadania to plik JSON, który zawiera właściwości wymagane do skonfigurowania danych wyjściowych zadania. W tym przykładzie jako dane wyjściowe używany jest magazyn obiektów blob. 

Na maszynie lokalnej utwórz plik o nazwie `JobOutputDefinition.json` i dodaj do niego następujące dane JSON. Pamiętaj, aby zastąpić wartość `accountKey` kluczem dostępu konta magazynu, czyli wartością zapisaną w zmiennej $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Następnie uruchom polecenie cmdlet `New-AzureRmStreamAnalyticsOutput`. Pamiętaj, aby zastąpić wartość zmiennej `jobOutputDefinitionFile` ścieżką, w której jest przechowywany plik JSON definicji danych wyjściowych zadania. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

Dodaj przekształcenie zadania przy użyciu polecenia cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę przekształcenia zadania, nazwę grupy zasobów i definicję przekształcenia zadania. Na maszynie lokalnej utwórz plik o nazwie `JobTransformationDefinition.json` i dodaj do niego następujące dane JSON. Plik JSON zawiera parametr zapytania, który definiuje zapytanie przekształcenia:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Następnie uruchom polecenie cmdlet `New-AzureRmStreamAnalyticsTransformation`. Pamiętaj, aby zastąpić wartość zmiennej `jobTransformationDefinitionFile` ścieżką, w której jest przechowywany plik JSON definicji przekształcania zadania. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Uruchom zadanie przy użyciu polecenia cmdlet [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę grupy zasobów, tryb uruchamiania danych wyjściowych i czas rozpoczęcia. Parametr `OutputStartMode` przyjmuje wartość `JobStartTime`, `CustomTime` lub `LastOutputEventTime`. Aby dowiedzieć się więcej na temat znaczenia poszczególnych wartości, zobacz sekcję [parametrów](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) w dokumentacji programu PowerShell. Na potrzeby tego przykładu określ tryb `CustomTime` i podaj wartość parametru `OutputStartTime`. 

Jako wartość czasu wybierz `2018-01-01`. Ta data początkowa jest wybierana, ponieważ poprzedza znacznik czasu zdarzenia z przykładowych danych. Po uruchomieniu poniższego polecenia cmdlet zwróci ono wartość `True` jako dane wyjściowe, jeśli zadanie zostało uruchomione. W kontenerze magazynu zostanie utworzony folder wyjściowy z przekształconymi danymi. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz pominąć jego usuwanie i na razie je zatrzymać. Jeśli nie planujesz używania tego zadania w przyszłości, usuń wszystkie zasoby utworzone w ramach tego przewodnika Szybki start, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zostało wdrożone proste zadanie usługi Stream Analytics. Aby dowiedzieć się więcej o konfigurowaniu innych źródeł danych wejściowych i wykonywaniu wykrywania w czasie rzeczywistym, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Wykrywanie oszustw w czasie rzeczywistym za pomocą usługi Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
