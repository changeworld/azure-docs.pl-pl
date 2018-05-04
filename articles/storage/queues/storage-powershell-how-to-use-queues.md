---
title: Wykonywanie operacji na magazynu kolejek Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak wykonać operacji na magazynu kolejek Azure przy użyciu programu PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: bad9f1f3fd5737e865a8f4d1d15ab3d5eb68b4cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Wykonywanie operacji magazynu kolejek Azure przy użyciu programu Azure PowerShell

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do kolejek Azure](storage-queues-introduction.md). W tym artykule opisano typowe operacjami magazynu kolejek. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie kolejki
> * Pobrać kolejki
> * Dodaj komunikat
> * Przeczytaj wiadomość
> * Usuwanie wiadomości 
> * Usuwanie kolejki

Ta porada wymaga programu Azure PowerShell w wersji modułu 3,6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Nie ma żadnych poleceń cmdlet programu PowerShell dla płaszczyzny danych dla kolejek. Do wykonania danych płaszczyzny operacje takie jak Dodaj komunikat, przeczytaj wiadomość i usuwanie wiadomości, należy użyć biblioteki klienta usługi storage platformy .NET, jak jest uwidaczniany w programie PowerShell. Utwórz obiekt komunikat, a następnie można użyj polecenia takie jak AddMessage do wykonywania operacji na tę wiadomość. W tym artykule przedstawiono, jak to zrobić.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Pobieranie listy lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. Użyje tego ćwiczenia **eastus**. Zapisać w zmiennej **lokalizacji** do użytku w przyszłości.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów na pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Nazwa grupy zasobów należy przechowywać w zmiennej do użytku w przyszłości. W tym przykładzie grupy zasobów o nazwie *howtoqueuesrg* jest tworzony w *eastus* regionu.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Tworzenie konta standardowe magazynu ogólnego przeznaczenia z magazyn lokalnie nadmiarowy (LRS) przy użyciu [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Pobiera kontekst konta magazynu, który definiuje konto magazynu do użycia. Działając na konto magazynu, możesz odwoływać się kontekstu zamiast wielokrotnie podawania poświadczeń.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższy przykład najpierw nawiąże połączenie z usługi Azure Storage przy użyciu kontekstu konta magazynu, która zawiera nazwę konta magazynu i klucza dostępu. Następnie wywołuje [AzureStorageQueue nowy](/powershell/module/azure.storage/new-azurestoragequeue) polecenia cmdlet, aby utworzyć kolejkę o nazwie "queuename".

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Aby uzyskać informacji na temat konwencji nazewnictwa dla usługi kolejek platformy Azure, zobacz [nazewnictwo kolejek i metadanych](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Pobrać kolejki

Można zbadać i pobrać określonej kolejki, a także listę wszystkich kolejek na koncie magazynu. Poniższe przykłady pokazują, jak można pobrać wszystkich kolejek w ramach konta magazynu i określonej kolejki; Użyj polecenia zarówno [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) polecenia cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Dodaj komunikat do kolejki

Operacje, które mają wpływ na rzeczywisty wiadomości w kolejce użyj biblioteki klienta usługi storage platformy .NET, jak widoczne w programie PowerShell. Aby dodać wiadomości do kolejki, Utwórz nowe wystąpienie obiektu komunikat [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) klasy. Następnie wywołaj metodę [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). CloudQueueMessage można utworzyć z ciągu (w formacie UTF-8) lub tablicy bajtów.

W poniższym przykładzie pokazano, jak dodać komunikat do kolejki.

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

Jeśli używasz [Eksploratora usługi Storage Azure](http://storageexplorer.com), połącz się z kontem platformy Azure i wyświetlić kolejek w ramach konta magazynu i przejść do kolejki, aby wyświetlić komunikaty w kolejce. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Następnie usuń odczytu wiadomości z kolejki

Wiadomości są odczytywane w najlepiej spróbuj najpierw w pierwszym poza kolejności. To nie jest gwarantowana. Podczas czytania wiadomości z kolejki, staje się niewidoczny dla wszystkich innych procesów w kolejce. Dzięki temu, że jeśli kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie.  

To **limitu czasu niewidoczności** definiuje, jak długo wiadomość pozostaje niewidoczny mogła ponownie do przetwarzania. Wartość domyślna to 30 sekund. 

Kod odczytuje komunikat z kolejki w dwóch etapach. Podczas wywoływania [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metody, otrzymasz następnej wiadomości w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metody. 

W poniższym przykładzie użytkownik zapoznaj się z artykułem wiadomości w kolejce trzy, a następnie zaczekaj 10 sekund (limit czasu niewidoczności). Następnie odczytać wiadomości, usuwanie komunikatów po ich przeczytaniu przez wywołanie metody **DeleteMessage**. Próba odczytu kolejki po usunięciu wiadomości $queueMessage zostaną zwrócone jako wartość NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj polecenie cmdlet Remove-AzureStorageQueue. Poniższy przykład pokazuje, jak można usunąć określonej kolejki używane w tym ćwiczeniu za pomocą polecenia cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby, które zostały utworzone w tym ćwiczeniu, Usuń grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku usuwa utworzono konto magazynu i grupy zasobów, do samej siebie.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono informacje podstawowe zarządzanie magazynem kolejki przy użyciu programu PowerShell, w tym jak:

> [!div class="checklist"]
> * Tworzenie kolejki
> * Pobrać kolejki
> * Dodaj komunikat
> * Przeczytaj wiadomość dalej
> * Usuwanie wiadomości 
> * Usuwanie kolejki

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Polecenia cmdlet usługi Magazyn Microsoft Azure PowerShell
* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
