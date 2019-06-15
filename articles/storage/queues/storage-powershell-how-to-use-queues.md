---
title: Wykonywanie operacji na Azure Queue storage przy użyciu programu PowerShell — usługi Azure Storage
description: Jak wykonywać operacje na Azure Queue storage przy użyciu programu PowerShell
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 6e8640b136c52f500de010f842ab73678acdce4f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991355"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Wykonaj operacjami usługi Azure Queue storage przy użyciu programu Azure PowerShell

Usługa Azure Queue storage jest usługą służącą do przechowywania dużej liczby wiadomości, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do kolejek usługi Azure](storage-queues-introduction.md). W tym artykule omówiono typowe operacjami usługi Queue storage. Omawiane kwestie:

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Pobieranie kolejki
> * Dodawanie komunikatu
> * Przeczytaj komunikat
> * Usuwanie wiadomości e-mail
> * Usuwanie kolejki

Niniejszy instruktaż wymaga modułu Azure PowerShell Az wersji 0,7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

Nie ma żadnych poleceń cmdlet programu PowerShell dla płaszczyzny danych dla kolejek. Do wykonania danych operacji płaszczyzny takich jak dodawanie wiadomości, przeczytaj komunikat i usuwanie wiadomości e-mail, należy użyć biblioteki klienta .NET magazynu, ponieważ jest widoczna w programie PowerShell. Utwórz obiekt wiadomości, a następnie można użyć poleceń, takich jak AddMessage do wykonywania operacji na tę wiadomość. W tym artykule pokazano, jak to zrobić.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Pobieranie listy lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. Użyje tego ćwiczenia **eastus**. Store to w zmiennej **lokalizacji** do użytku w przyszłości.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Store nazwę grupy zasobów w zmiennej, do użytku w przyszłości. W tym przykładzie grupa zasobów o nazwie *howtoqueuesrg* jest tworzony w *eastus* regionu.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu ogólnego przeznaczenia w warstwie standardowa przy użyciu magazynu lokalnie nadmiarowego (LRS) [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Pobierz kontekst konta magazynu, który definiuje konto magazynu ma być używany. Wykonując działania względem konta magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższy przykład najpierw ustanawia połączenie do usługi Azure Storage przy użyciu kontekst konta magazynu, który zawiera nazwę konta magazynu i klucza dostępu. Następnie wywołuje [New AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) polecenia cmdlet, aby utworzyć kolejkę o nazwie "queuename".

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Aby uzyskać informacji na temat konwencji nazewnictwa dla usługi Azure Queue Service, zobacz [nazewnictwo kolejek i metadanych](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Pobieranie kolejki

Można wykonywać zapytania i pobierać listę wszystkich kolejek na koncie magazynu lub określonej kolejki. Poniższe przykłady pokazują, jak można pobrać wszystkich kolejek na koncie magazynu i w odpowiedniej kolejce; Oba polecenia, użyj [Get AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) polecenia cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Operacje, które mają wpływ na rzeczywistych wiadomości w kolejce za pomocą biblioteki klienta .NET magazynu jako dostępne w programie PowerShell. Aby dodać komunikat do kolejki, Utwórz nowe wystąpienie obiektu komunikat [Microsoft.Azure.Storage.Queue.CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue_message) klasy. Następnie wywołaj metodę [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue.addmessage). CloudQueueMessage mogą być tworzone z ciągu (w formacie UTF-8) lub tablicą bajtów.

Poniższy przykład pokazuje, jak dodać komunikat do kolejki.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Jeśli używasz [Eksploratora usługi Azure Storage](https://storageexplorer.com), nawiąż połączenie z kontem platformy Azure i wyświetlić kolejek na koncie magazynu oraz przechodzenie do kolejki, aby wyświetlić komunikaty w kolejce.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Następnie usuń odczytu komunikatów z kolejki

Komunikaty są odczytywane w kolejności pierwszy wejściu — pierwszy na wyjściu najlepiej spróbuj. Nie jest gwarantowana. Podczas odczytywania komunikatów z kolejki, staje się niewidoczne dla innych procesów w kolejce. Gwarantuje to, że jeśli kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu można uzyskać ten sam komunikat i spróbuj ponownie.  

To **limitu czasu niewidoczności** definiuje, jak długo komunikat pozostaje niewidoczna, zanim stanie się znów dostępne do przetwarzania. Wartość domyślna to 30 sekund.

Kod odczytuje komunikat z kolejki w dwóch etapach. Gdy wywołujesz [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) metody, uzyskasz następny komunikat w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metody.

W poniższym przykładzie możesz zapoznaj się z trzech kolejka komunikatów, a następnie poczekaj 10 sekund (limit czasu niewidoczności). A następnie przeczytaj ponownie trzy komunikaty usuwanie komunikatów po ich przeczytaniu przez wywołanie metody **DeleteMessage**. Jeśli zostanie podjęta próba odczytu kolejki po usunięciu komunikaty, $queueMessage zostaną zwrócone jako wartości NULL.

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

Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj polecenie cmdlet Remove-AzStorageQueue. Poniższy przykład pokazuje, jak można usunąć określonej kolejki używane w tym ćwiczeniu za pomocą polecenia cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone w tym ćwiczeniu, Usuń grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku powoduje usunięcie konta magazynu i samej grupy zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono podstawowe zarządzanie magazynem kolejki przy użyciu programu PowerShell, np.:

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Pobieranie kolejki
> * Dodawanie komunikatu
> * Przeczytaj następny komunikat
> * Usuwanie wiadomości e-mail
> * Usuwanie kolejki

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Polecenia cmdlet programu PowerShell dla programu Microsoft Azure Storage

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
