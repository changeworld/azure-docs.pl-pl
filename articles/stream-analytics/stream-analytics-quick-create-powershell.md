---
title: Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell
description: W tym przewodniku Szybki start zawarto szczegółowe instrukcje korzystania z modułu Azure PowerShell w celu wdrożenia i uruchomienia zadania usługi Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą poleceń cmdlet programu PowerShell lub skryptów. W tym przewodniku Szybki start zawarto szczegółowe instrukcje korzystania z modułu Azure PowerShell w celu wdrożenia i uruchomienia zadania usługi Azure Stream Analytics.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).  

* Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby znaleźć wersję zainstalowaną na komputerze lokalnym. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz artykuł [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Scenariusz przedstawiony w tym artykule opisuje odczytywanie danych z magazynu obiektów blob, przekształcanie ich i zapisywanie z powrotem do innego kontenera w tym samym magazynie obiektów blob.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i wprowadź swoje poświadczenia platformy Azure w podręcznym oknie przeglądarki. Po zalogowaniu się, jeśli masz wiele subskrypcji, wybierz subskrypcję, z której chcesz korzystać na potrzeby tego przewodnika Szybki start, uruchamiając następujące polecenia cmdlet. Pamiętaj, aby zastąpić ciąg <your subscription> nazwą swojej subskrypcji:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania usługi Stream Analytics przygotuj dane, które będą konfigurowane jako dane wejściowe zadania. Uruchom następujące kroki, aby przygotować dane wejściowe wymagane przez zadanie: 

1. Pobierz [przykładowe dane czujników](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) z witryny GitHub.  

2. Utwórz standardowe konto magazynu ogólnego przeznaczenia z replikacją LRS za pomocą polecenia [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount)  

3. Następnie pobierz kontekst konta magazynu, który definiuje konto magazynu do użycia. Podczas pracy z kontami magazynu możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia. W tym przykładzie tworzone jest konto magazynu o nazwie mystorageaccount z magazynem lokalnie nadmiarowym (LRS, locally redundant storage) i szyfrowaniem obiektów blob (domyślnie włączone).  

4. Następnie utwórz kontener przy użyciu polecenia [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), ustaw uprawnienia na wartość „blob”, aby zezwolić na publiczny dostęp do plików, a następnie przekaż [przykładowe dane czujników](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) pobrane wcześniej. 

Te kroki są wykonywane przez uruchomienie następującego skryptu programu PowerShell:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Utwórz zadanie usługi Stream Analytics za pomocą polecenia cmdlet [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę grupy zasobów i definicję zadania. Nazwą zadania może być dowolna przyjazna nazwa identyfikująca zadanie. Nazwa zadania usługi Stream Analytics może zawierać wyłącznie znaki alfanumeryczne, łączniki i podkreślenia oraz musi składać się z od 3 do 63 znaków. Definicja zadania to plik JSON, który zawiera właściwości wymagane do utworzenia zadania. Na komputerze lokalnym utwórz plik o nazwie „JobDefinition.json” i dodaj do niego następujące dane JSON:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Następnie uruchom polecenie cmdlet New-AzureRMStreamAnalyticsJob, zastępując wartość zmiennej jobDefinitionFile ścieżką, w której jest przechowywany plik JSON definicji zadania. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Konfigurowanie danych wejściowych zadania

Dodaj dane wejściowe do zadania za pomocą polecenia cmdlet [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę danych wejściowych zadania, nazwę grupy zasobów i definicję danych wejściowych zadania. Definicja danych wejściowych zadania to plik JSON zawierający właściwości wymagane do skonfigurowania danych wejściowych zadania. W tym przykładzie utworzysz magazyn obiektów blob jako dane wejściowe. Na komputerze lokalnym utwórz plik o nazwie „JobInputDefinition.json” i dodaj do niego następujące dane JSON, zastępując wartość **accountKey** swoim kluczem dostępu konta magazynu, którego wartość jest przechowywana w wartości $storageAccountKey. 

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
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
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

Następnie uruchom polecenie cmdlet New-AzureRMStreamAnalyticsInput, zastępując wartość zmiennej jobDefinitionFile ścieżką, w której jest przechowywany plik JSON definicji danych wejściowych zadania. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Konfigurowanie danych wyjściowych zadania

Dodaj dane wyjściowe do zadania za pomocą polecenia cmdlet [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę danych wyjściowych zadania, nazwę grupy zasobów i definicję danych wyjściowych zadania. Definicja danych wyjściowych zadania to plik JSON zawierający właściwości wymagane do skonfigurowania danych wyjściowych zadania. W tym przykładzie utworzysz magazyn obiektów blob jako dane wyjściowe. Na komputerze lokalnym utwórz plik o nazwie „JobOutputDefinition.json” i dodaj do niego następujące dane JSON, zastępując wartość **accountKey** swoim kluczem dostępu konta magazynu, którego wartość jest przechowywana w wartości $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
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

Następnie uruchom polecenie cmdlet New-AzureRMStreamAnalyticsOutput, zastępując wartość zmiennej jobOutputDefinitionFile ścieżką, w której jest przechowywany plik JSON definicji danych wyjściowych zadania. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

Dodaj przekształcenie zadania przy użyciu polecenia cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę przekształcenia zadania, nazwę grupy zasobów i definicję przekształcenia zadania. Na komputerze lokalnym utwórz plik o nazwie „JobTransformationDefinition.json” i dodaj do niego następujące dane JSON. Plik JSON zawiera parametr zapytania, który definiuje zapytanie przekształcenia:

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

Następnie uruchom polecenie cmdlet New-AzureRMStreamAnalyticsTransformation, zastępując wartość zmiennej jobTransformationDefinitionFile ścieżką, w której jest przechowywany plik JSON definicji przekształcenia zadania. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Uruchom zadanie przy użyciu polecenia cmdlet [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). To polecenie cmdlet przyjmuje jako parametry nazwę zadania, nazwę grupy zasobów, tryb uruchamiania danych wyjściowych i czas rozpoczęcia. Parametr OutputStartMode przyjmuje dwie wartości JobStartTime, CustomTime lub LastOutputEventTime. Aby dowiedzieć się więcej na temat każdej z tych wartości, zobacz sekcję [parametry](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) w dokumentacji programu PowerShell. W tym przykładzie możesz określić tryb jako CustomTime i podać wartość parametru OutputStartTime. 

Jako wartość czasu wybierz datę o jeden dzień wcześniejszą od przekazania pliku do magazynu obiektów blob, ponieważ czas, w którym plik został przekazany jest wcześniejszy od bieżącego. Po uruchomieniu poniższego polecenia cmdlet zwróci ono wartość „True” jako dane wyjściowe, jeśli zadanie zostało uruchomione. Kontener o nazwie „myoutputcontainer” jest tworzony na koncie magazynu przy użyciu przekształconych danych. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później, gdy będzie potrzebne. Jeśli nie planujesz używania tego zadania w przyszłości, usuń wszystkie zasoby utworzone w ramach tego przewodnika Szybki start, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zostało wdrożone proste zadanie usługi Stream Analytics. Aby dowiedzieć się więcej o konfigurowaniu innych źródeł danych wejściowych i wykonywaniu wykrywania w czasie rzeczywistym, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Wykrywanie oszustw w czasie rzeczywistym za pomocą usługi Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
