---
title: Wykonywanie operacji na platformie Azure Queue storage przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak wykonywać operacje na Azure Queue storage przy użyciu programu PowerShell
services: storage
author: robinsh
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.component: queues
ms.openlocfilehash: 0b78f822bec2bd545331ed52f3339a07b5dfb466
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521051"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Wykonaj operacjami usługi Azure Queue storage przy użyciu programu Azure PowerShell

Usługa Azure Queue storage jest usługą służącą do przechowywania dużej liczby wiadomości, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do kolejek usługi Azure](storage-queues-introduction.md). W tym artykule omówiono typowe operacjami usługi Queue storage. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie kolejki
> * Pobieranie kolejki
> * Dodaj komunikat
> * Przeczytaj komunikat
> * Usuwanie wiadomości e-mail 
> * Usuwanie kolejki

Niniejszy instruktaż wymaga programu Azure PowerShell module w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Nie ma żadnych poleceń cmdlet programu PowerShell dla płaszczyzny danych dla kolejek. Do wykonania danych operacji płaszczyzny takich jak dodawanie wiadomości, przeczytaj komunikat i usuwanie wiadomości e-mail, należy użyć biblioteki klienta .NET magazynu, ponieważ jest widoczna w programie PowerShell. Utwórz obiekt wiadomości, a następnie można użyć poleceń, takich jak AddMessage do wykonywania operacji na tę wiadomość. W tym artykule pokazano, jak to zrobić.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Pobieranie listy lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. Użyje tego ćwiczenia **eastus**. Store to w zmiennej **lokalizacji** do użytku w przyszłości.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów na pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Store nazwę grupy zasobów w zmiennej, do użytku w przyszłości. W tym przykładzie grupa zasobów o nazwie *howtoqueuesrg* jest tworzony w *eastus* regionu.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu ogólnego przeznaczenia w warstwie standardowa przy użyciu magazynu lokalnie nadmiarowego (LRS) [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Pobierz kontekst konta magazynu, który definiuje konto magazynu ma być używany. Wykonując działania względem konta magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższy przykład najpierw ustanawia połączenie do usługi Azure Storage przy użyciu kontekst konta magazynu, który zawiera nazwę konta magazynu i klucza dostępu. Następnie wywołuje [New AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) polecenia cmdlet, aby utworzyć kolejkę o nazwie "queuename".

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Aby uzyskać informacji na temat konwencji nazewnictwa dla usługi Azure Queue Service, zobacz [nazewnictwo kolejek i metadanych](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Pobieranie kolejki

Można wykonywać zapytania i pobierać listę wszystkich kolejek na koncie magazynu lub określonej kolejki. Poniższe przykłady pokazują, jak można pobrać wszystkich kolejek na koncie magazynu i w odpowiedniej kolejce; Oba polecenia, użyj [Get AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) polecenia cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Operacje, które mają wpływ na rzeczywistych wiadomości w kolejce za pomocą biblioteki klienta .NET magazynu jako dostępne w programie PowerShell. Aby dodać komunikat do kolejki, Utwórz nowe wystąpienie obiektu komunikat [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) klasy. Następnie wywołaj metodę [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). CloudQueueMessage mogą być tworzone z ciągu (w formacie UTF-8) lub tablicą bajtów.

Poniższy przykład pokazuje, jak dodać komunikat do kolejki.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Jeśli używasz [Eksploratora usługi Azure Storage](http://storageexplorer.com), nawiąż połączenie z kontem platformy Azure i wyświetlić kolejek na koncie magazynu oraz przechodzenie do kolejki, aby wyświetlić komunikaty w kolejce. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Następnie usuń odczytu komunikatów z kolejki

Komunikaty są odczytywane w kolejności pierwszy wejściu — pierwszy na wyjściu najlepiej spróbuj. Nie jest gwarantowana. Podczas odczytywania komunikatów z kolejki, staje się niewidoczne dla innych procesów w kolejce. Gwarantuje to, że jeśli kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie.  

To **limitu czasu niewidoczności** definiuje, jak długo komunikat pozostaje niewidoczna, zanim stanie się znów dostępne do przetwarzania. Wartość domyślna to 30 sekund. 

Kod odczytuje komunikat z kolejki w dwóch etapach. Gdy wywołujesz [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metody, uzyskasz następny komunikat w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metody. 

W poniższym przykładzie możesz zapoznaj się z trzech kolejka komunikatów, a następnie poczekaj 10 sekund (limit czasu niewidoczności). A następnie przeczytaj ponownie trzy komunikaty usuwanie komunikatów po ich przeczytaniu przez wywołanie metody **DeleteMessage**. Jeśli zostanie podjęta próba odczytu kolejki po usunięciu komunikaty, $queueMessage zostaną zwrócone jako wartości NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj polecenie cmdlet Remove-AzureStorageQueue. Poniższy przykład pokazuje, jak można usunąć określonej kolejki używane w tym ćwiczeniu za pomocą polecenia cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone w tym ćwiczeniu, Usuń grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku powoduje usunięcie konta magazynu i samej grupy zasobów.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono podstawowe zarządzanie magazynem kolejki przy użyciu programu PowerShell, np.:

> [!div class="checklist"]
> * Tworzenie kolejki
> * Pobieranie kolejki
> * Dodaj komunikat
> * Przeczytaj następny komunikat
> * Usuwanie wiadomości e-mail 
> * Usuwanie kolejki

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Polecenia cmdlet programu PowerShell dla programu Microsoft Azure Storage
* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
