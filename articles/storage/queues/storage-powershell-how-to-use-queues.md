---
title: Wykonywanie akcji magazynu kolejki platformy Azure w programie PowerShell
description: Jak wykonywać operacje w magazynie kolejki platformy Azure za pomocą programu PowerShell
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 96828a854c340b89c26023ce60f9c85dd1bb4cdd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473915"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Wykonywanie operacji usługi Azure Queue Storage przy użyciu programu Azure PowerShell

Usługa Azure Queue storage to usługa do przechowywania dużej liczby wiadomości, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do kolejek platformy Azure](storage-queues-introduction.md). W tym artykule opisano typowe operacje magazynowania kolejek. Omawiane kwestie:

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Pobieranie kolejki
> * Dodawanie komunikatu
> * Czytanie wiadomości
> * Usuwanie wiadomości
> * Usuwanie kolejki

Ten sposób wymaga modułu Azure PowerShell Az w wersji 0.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

Nie ma żadnych poleceń cmdlet programu PowerShell dla płaszczyzny danych dla kolejek. Aby wykonać operacje płaszczyzny danych, takie jak dodawanie wiadomości, odczytywanie wiadomości i usuwanie wiadomości, należy użyć biblioteki klienta magazynu .NET, która jest widoczna w programie PowerShell. Utworzyć obiekt wiadomości, a następnie można użyć poleceń, takich jak AddMessage do wykonywania operacji na tej wiadomości. W tym artykule pokazano, jak to zrobić.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Pobieranie listy lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. To ćwiczenie będzie korzystać **eastus**. Przechowuj to w **zmiennej lokalizacji** do wykorzystania w przyszłości.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Przechowuj nazwę grupy zasobów w zmiennej do wykorzystania w przyszłości. W tym przykładzie grupa zasobów o nazwie *howtoqueuesrg* jest tworzony w regionie *eastus.*

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz standardowe konto magazynu ogólnego przeznaczenia z lokalnie nadmiarowym magazynem (LRS) przy użyciu [funkcji New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Pobierz kontekst konta magazynu, który definiuje konto magazynu, które ma być używane. Wykonując działania względem konta magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższy przykład najpierw ustanawia połączenie z usługą Azure Storage przy użyciu kontekstu konta magazynu, który zawiera nazwę konta magazynu i jego klucz dostępu. Następnie wywołuje polecenie cmdlet [New-AzStorageQueue,](/powershell/module/az.storage/New-AzStorageQueue) aby utworzyć kolejkę o nazwie "howtoqueue".

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Aby uzyskać informacje na temat konwencji nazewnictwa usługi kolejkowania platformy Azure, zobacz [Nazywanie kolejek i metadanych](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Pobieranie kolejki

Można wysyłać zapytania i pobierać określoną kolejkę lub listę wszystkich kolejek na koncie magazynu. Poniższe przykłady pokazują, jak pobrać wszystkie kolejki na koncie magazynu i określonej kolejki; oba polecenia używają polecenia cmdlet [Get-AzStorageQueue.](/powershell/module/az.storage/Get-AzStorageQueue)

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie wiadomości do kolejki

Operacje wpływające na rzeczywiste komunikaty w kolejce używają biblioteki klienta magazynu .NET jako udostępnianej w programie PowerShell. Aby dodać wiadomość do kolejki, utwórz nowe wystąpienie obiektu wiadomości, [microsoft.Azure.Storage.Queue.CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) klasy. Następnie wywołaj metodę [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage). Klasę CloudQueueMessage można utworzyć przy użyciu ciągu (w formacie UTF-8) lub tablicy bajtów.

W poniższym przykładzie pokazano, jak dodać wiadomość do kolejki.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Jeśli używasz [Eksploratora usługi Azure Storage,](https://storageexplorer.com)możesz połączyć się z kontem platformy Azure i wyświetlić kolejki na koncie magazynu i przejść do kolejki, aby wyświetlić wiadomości w kolejce.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Odczytywanie wiadomości z kolejki, a następnie jej usuwanie

Wiadomości są odczytywane w najlepszej kolejności pierwszej w pierwszej kolejności. Nie jest to gwarantowane. Po przeczytaniu wiadomości z kolejki staje się niewidoczny dla wszystkich innych procesów patrząc na kolejce. Gwarantuje to, że jeśli kod nie może przetworzyć wiadomości z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie.  

Ten **limit czasu niewidzialności** określa, jak długo wiadomość pozostaje niewidoczna, zanim będzie ponownie dostępna do przetworzenia. Wartość domyślna to 30 sekund.

Kod odczytuje wiadomość z kolejki w dwóch krokach. Po wywołaniu [metody Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) pojawi się następna wiadomość w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie wiadomości z kolejki, należy wywołać metodę [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)

W poniższym przykładzie można przeczytać trzy komunikaty kolejki, a następnie odczekać 10 sekund (limit czasu niewidzialności). Następnie ponownie przeczytać trzy wiadomości, usuwając wiadomości po ich przeczytaniu, dzwoniąc **DeleteMessage**. Jeśli spróbujesz odczytać kolejkę po usunięciu wiadomości, $queueMessage zostaną zwrócone jako NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie zawarte w niej wiadomości, należy wywołać polecenie cmdlet Remove-AzStorageQueue. W poniższym przykładzie pokazano, jak usunąć określoną kolejkę używaną w tym ćwiczeniu przy użyciu polecenia cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone w tym ćwiczeniu, usuń grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku usuwa utworzone konto magazynu i samą grupę zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano podstawowe zarządzanie magazynem kolejek w programie PowerShell, w tym sposób:

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Pobieranie kolejki
> * Dodawanie komunikatu
> * Przeczytaj następną wiadomość
> * Usuwanie wiadomości
> * Usuwanie kolejki

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Polecenia cmdlet magazynu programu Microsoft Azure PowerShell

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
