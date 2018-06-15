---
title: Rozpoczynanie pracy z magazynem kolejek Azure i programu Visual Studio połączone usługi (ASP.NET) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę po nawiązaniu połączenia z kontem magazynu przy użyciu programu Visual Studio usług połączonych za pomocą magazynu kolejek Azure w projekcie platformy ASP.NET w programie Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: d06fde0dc6c289a09b9fe4c9e2ffbb50c3027490
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798325"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z magazynem kolejek Azure i programu Visual Studio połączone usługi (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Przegląd

Azure queue storage umożliwia wysyłanie komunikatów między składnikami aplikacji chmury. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Usługa Queue Storage obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Ten samouczek pokazuje, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostek magazynu kolejek Azure. Te scenariusze obejmują typowych zadań, takich jak tworzenie kolejki systemu Azure i dodawanie, modyfikowanie, Odczyt i usuwanie wiadomości w kolejce.

##<a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto usługi Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**i z menu kontekstowego wybierz **kontrolera -> Dodaj**.

    ![Dodawanie kontrolera do aplikacji ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Na **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC 5 — pusty**i wybierz **Dodaj**.

    ![Określ typ kontrolera MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Na **Dodaj kontroler** okna dialogowego, nazwy kontrolera *QueuesController*i wybierz **Dodaj**.

    ![Nazwa kontrolera MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Dodaj następujące *przy użyciu* dyrektywy `QueuesController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższe kroki pokazano, jak utworzyć kolejkę:

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`. 

1. Dodaj metodę o nazwie **CreateQueue** zwracającą **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **CreateQueue** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz **CloudQueue** obiekt, który reprezentuje odwołanie do nazwy wymaganej kolejki. **CloudQueueClient.GetQueueReference** — metoda nie powoduje żądanie magazynu kolejek. Czy kolejka istnieje, zwracany jest odwołanie. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.CreateIfNotExists** metody, aby utworzyć kolejkę, jeśli jeszcze nie istnieje. **CloudQueue.CreateIfNotExists** metoda zwraca **true** Jeśli kolejka nie istnieje i został utworzony pomyślnie. W przeciwnym razie **false** jest zwracany.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualizacja **obiekt ViewBag** o nazwie kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **CreateQueue** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `CreateQueue.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **Utwórz kolejkę** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Tworzenie kolejki](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak wspomniano wcześniej, **CloudQueue.CreateIfNotExists** metoda zwraca **true** tylko gdy kolejka nie istnieje i zostanie utworzony. W związku z tym po uruchomieniu aplikacji, gdy kolejka istnieje, metoda zwraca **false**. Aby uruchomić aplikację wiele razy, musisz usunąć kolejkę przed ponownym uruchomieniem aplikacji. Usuwanie kolejki może odbywać się za pośrednictwem **CloudQueue.Delete** metody. Możesz także usunąć przy użyciu kolejki [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Dodaj komunikat do kolejki

Po wprowadzeniu [utworzenie kolejki](#create-a-queue), można dodawać komunikaty do tej kolejki. Ta sekcja przeprowadzi Cię przez procedurę dodawania wiadomości do kolejki *kolejki testowej*. 

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **AddMessage** zwracającą **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **AddMessage** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiekt, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Utwórz **CloudQueueMessage** obiekt reprezentujący wiadomości, które chcesz dodać do kolejki. A **CloudQueueMessage** obiektu można utworzyć z ciągu (w formacie UTF-8) lub tablicy bajtów.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Wywołanie **CloudQueue.AddMessage** metody w celu dodania messaged do kolejki.

    ```csharp
    queue.AddMessage(message);
    ```

1. Utwórz i ustaw kilku **obiekt ViewBag** właściwości do wyświetlenia w widoku.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **AddMessage** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `AddMessage.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **komunikat Dodaj** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Dodaj komunikat](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Dwie sekcje - [Przeczytaj wiadomość z kolejki bez jego usuwania](#read-a-message-from-a-queue-without-removing-it) i [odczytu i usuwania komunikatu z kolejki](#read-and-remove-a-message-from-a-queue) -zilustrować odczytywać wiadomości z kolejki.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Przeczytaj wiadomość z kolejki bez jego usuwania

W tej sekcji przedstawiono sposób wglądu do wiadomości w kolejce (do odczytu do pierwszej wiadomości bez jego usuwania).  

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **PeekMessage** zwracającą **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **PeekMessage** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiekt, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.PeekMessage** metody do odczytu do pierwszej wiadomości w kolejce bez usuwania go z kolejki. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualizacja **obiekt ViewBag** z dwóch wartości: Nazwa kolejki i komunikat, który został odczytany. **CloudQueueMessage** obiekt udostępnia dwie właściwości pobierania wartości obiektu: **CloudQueueMessage.AsBytes** i **CloudQueueMessage.AsString**. **AsString** (używane w tym przykładzie) zwraca ciąg znaków, podczas gdy **AsBytes** zwraca tablicę bajtów.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **PeekMessage** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `PeekMessage.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **komunikat Peek** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Wgląd do wiadomości](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Przeczytaj i usunąć wiadomości z kolejki

W tej sekcji możesz dowiedzieć się, jak na odczytywanie i usuwanie wiadomości z kolejki.   

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **przeczytanyWiadomość** zwracającą **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **przeczytanyWiadomość** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiekt, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.GetMessage** metody do odczytu do pierwszej wiadomości w kolejce. **CloudQueue.GetMessage** — metoda sprawia, że komunikat niewidoczne dla 30 sekund (domyślnie) do innego kodu odczytującego komunikaty, aby żadnego innego kodu można zmodyfikować lub usunąć komunikat podczas z jego przetworzeniem. Aby zmienić czas wiadomość jest niewidoczny, zmodyfikuj **visibilityTimeout** parametr przekazywany do **CloudQueue.GetMessage** metody.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Wywołanie **CloudQueueMessage.Delete** metodę, aby usunąć wiadomości z kolejki.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualizacja **obiekt ViewBag** z komunikatu usunięte i nazwy kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **przeczytanyWiadomość** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `ReadMessage.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **komunikat odczytu/usuwania** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Komunikat do odczytu i usuwania](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

W tej sekcji przedstawiono sposób pobrać długości kolejki (liczba komunikatów). 

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **GetQueueLength** zwracającą **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **przeczytanyWiadomość** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiekt, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.FetchAttributes** metody można pobrać atrybutów kolejki (w tym jej długość). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Dostęp **CloudQueue.ApproximateMessageCount** właściwości do pobrania długość kolejki.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualizacja **obiekt ViewBag** o nazwie kolejka, a jego długość.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **GetQueueLength** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `GetQueueLengthMessage.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **pobieranie długości kolejki** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Pobieranie długości kolejki](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Usuwanie kolejki
W tej części przedstawiono sposób usunąć kolejkę. 

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **DeleteQueue** zwracającą **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **DeleteQueue** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiekt, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.Delete** metodę, aby usunąć kolejkę reprezentowany przez **CloudQueue** obiektu.

    ```csharp
    queue.Delete();
    ```

1. Aktualizacja **obiekt ViewBag** o nazwie kolejka, a jego długość.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **DeleteQueue** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `DeleteQueue.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **pobieranie długości kolejki** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Usuwanie kolejki](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Wprowadzenie do magazynu obiektów blob platformy Azure i programu Visual Studio połączone usługi (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Rozpoczynanie pracy z magazynu tabel platformy Azure i programu Visual Studio połączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
