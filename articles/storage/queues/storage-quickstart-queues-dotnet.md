---
title: 'Szybki start: Biblioteka magazynu kolejki platformy Azure w wersji 12 — .NET'
description: Dowiedz się, jak utworzyć kolejkę i dodać wiadomości do kolejki za pomocą biblioteki kolejki platformy Azure .NET w wersji 12. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: c69aa91596ff203445aa4fa3ccd59001ffe16649
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78197491"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Szybki start: biblioteka klienta magazynu usługi Azure Queue w wersji 12 dla platformy .NET

Wprowadzenie do biblioteki klienta usługi Azure Queue storage w wersji 12 dla platformy .NET. Usługa Azure Queue storage to usługa do przechowywania dużej liczby wiadomości do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

> [!NOTE]
> Aby rozpocząć pracę z poprzednią wersją SDK, zobacz [Szybki start: Zarządzanie kolejką za pomocą narzędzia Azure Storage SDK w wersji 11 dla platformy .NET](storage-quickstart-queues-dotnet-legacy.md).

Użyj biblioteki klienta magazynu usługi Azure Queue w wersji 12 dla platformy .NET, aby:

* Tworzenie kolejki
* Dodawanie wiadomości do kolejki
* Wgląd do wiadomości w kolejce
* Aktualizowanie wiadomości w kolejce
* Odbieranie wiadomości z kolejki
* Usuwanie wiadomości z kolejki
* Usuwanie kolejki

[Dokumentacja](/dotnet/api/azure.storage.queues) | dokumentacji interfejsu API[Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | (NuGet) kod źródłowy[(Biblioteka](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | [api)](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Bieżący [sdk .NET Core](https://dotnet.microsoft.com/download/dotnet-core) dla systemu operacyjnego. Pamiętaj, aby uzyskać SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji przeprowadzi Cię proces przygotowania projektu do pracy z biblioteką klienta magazynu usługi Azure Queue w wersji 12 dla platformy .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację .NET Core o nazwie *QueuesQuickstartV12*.

1. W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, aby utworzyć nową aplikację konsoli o nazwie *QueuesQuickstartV12*. To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Przełącz się do nowo utworzonego katalogu *QueuesQuickstartV12.*

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Będąc jeszcze w katalogu aplikacji, zainstaluj bibliotekę klienta magazynu usługi `dotnet add package` Azure Queue dla pakietu .NET za pomocą polecenia.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie pliku *Program.cs* w edytorze
1. Usuwanie `Console.WriteLine("Hello World!");` instrukcji
1. Dodawanie `using` dyrektyw
1. Aktualizowanie `Main` deklaracji metody do [obsługi kodu asynchronii](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Oto kod:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat kolejki może mieć rozmiar do 64 KB. Kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości pracy do przetwarzania asynchronicznie. Magazyn kolejek oferuje trzy typy zasobów:

* Konto magazynu
* Kolejka na koncie magazynu
* Wiadomości w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejek](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas platformy .NET, aby wchodzić w interakcje z tymi zasobami:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` Umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
* [QueueClient:](/dotnet/api/azure.storage.queues.queueclient) `QueueClient` Klasa umożliwia zarządzanie i manipulowanie poszczególnych kolejek i jej wiadomości.
* [QueueMessage:](/dotnet/api/azure.storage.queues.models.queuemessage) `QueueMessage` Klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje za pomocą biblioteki klienta magazynu usługi Azure Queue dla platformy .NET:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kolejki](#create-a-queue)
* [Dodawanie wiadomości do kolejki](#add-messages-to-a-queue)
* [Wgląd do wiadomości w kolejce](#peek-at-messages-in-a-queue)
* [Aktualizowanie wiadomości w kolejce](#update-a-message-in-a-queue)
* [Odbieranie wiadomości z kolejki](#receive-messages-from-a-queue)
* [Usuwanie wiadomości z kolejki](#delete-messages-from-a-queue)
* [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu. Parametry połączenia są przechowywane w zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `Main` wewnątrz metody:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Zdecyduj o nazwie nowej kolejki. Poniższy kod dołącza wartość guid do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się od litery lub liczby. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi również zawierać od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazywania kolejek, zobacz [Nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).


Utwórz wystąpienie klasy [QueueClient.](/dotnet/api/azure.storage.queues.queueclient) Następnie należy [wywołać CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) metody, aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na `Main` końcu metody:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Dodawanie wiadomości do kolejki

Poniższy fragment kodu asynchronicznie dodaje wiadomości do kolejki, wywołując [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) metody. Zapisuje również [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) zwrócony `SendMessageAsync` z wywołania. Pokwitowanie służy do aktualizacji wiadomości w dalszej części programu.

Dodaj ten kod na `Main` końcu metody:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd do wiadomości w kolejce

Zajrzeć do wiadomości w kolejce, wywołując [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metody. Metoda `PeekMessagesAsync` pobiera jeden lub więcej wiadomości z przodu kolejki, ale nie zmienia widoczności wiadomości.

Dodaj ten kod na `Main` końcu metody:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie wiadomości w kolejce

Zaktualizuj zawartość wiadomości, wywołując [updateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) metody. Metoda `UpdateMessageAsync` może zmienić limit czasu widoczności wiadomości i zawartość. Zawartość wiadomości musi być ciągiem zakodowanym w uiścić UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością wiadomości, przekazać w `SendReceipt` wartości z, który został zapisany wcześniej w kodzie. Wartości `SendReceipt` identyfikują komunikat, który ma być aktualizowany.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki

Pobierz wcześniej dodane wiadomości, wywołując [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metody.

Dodaj ten kod na `Main` końcu metody:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie wiadomości z kolejki

Usuń wiadomości z kolejki po ich przetworzyniu. W takim przypadku przetwarzanie jest po prostu wyświetlanie komunikatu na konsoli.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika, wywołując `Console.ReadLine` przed procesami i usunięciem wiadomości. Sprawdź w [witrynie Azure portal,](https://portal.azure.com) czy zasoby zostały utworzone poprawnie, zanim zostaną usunięte. Wszystkie wiadomości, które nie zostały jawnie usunięte, po pewnym czasie staną się ponownie widoczne w kolejce, aby uzyskać kolejną szansę na ich przetworzenie.

Dodaj ten kod na `Main` końcu metody:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając kolejkę przy użyciu metody [DeleteAsync.](/dotnet/api/azure.storage.queues.queueclient.deleteasync)

Dodaj ten kod na `Main` końcu metody:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę wiadomości w kolejce, a następnie pobiera je i usuwa, zanim na koniec usunie kolejkę.

W oknie konsoli przejdź do katalogu aplikacji, a następnie skompiluj i uruchom aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Gdy aplikacja wstrzymuje się przed odbierania wiadomości, sprawdź swoje konto magazynu w [witrynie Azure portal](https://portal.azure.com). Sprawdź, czy wiadomości znajdują się w kolejce.

Naciśnij klawisz **Enter,** aby odbierać i usuwać wiadomości. Po wyświetleniu monitu naciśnij ponownie klawisz **Enter,** aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć kolejkę i dodać do niej wiadomości przy użyciu asynchroniowego kodu .NET. Następnie nauczyłeś się podglądać, pobierać i usuwać wiadomości. Na koniec dowiedziałeś się, jak usunąć kolejkę wiadomości.

Aby uzyskać samouczki, przykłady, szybkie uruchamianie i inną dokumentację, odwiedź:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów .NET i .NET Core](https://docs.microsoft.com/dotnet/azure/)

* Aby dowiedzieć się więcej, zobacz [biblioteki usługi Azure Storage dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
* Aby wyświetlić więcej przykładowych aplikacji magazynu kolejki platformy Azure, przejdź do [przykładów biblioteki klienta usługi Azure Queue storage w wersji 12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
