---
title: Wprowadzenie do usługi Azure queue storage i Visual Studio połączonych usług (ASP.NET) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę, przy użyciu usługi Azure queue storage w projektach programu ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 3f3a649308449a8ba4e1dbaebd4db4afbf731c5b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110489"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Wprowadzenie do usługi Azure queue storage i Visual Studio połączonych usług (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

Usługa Azure queue storage umożliwia przesyłanie komunikatów między składnikami aplikacji w chmurze. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Usługa Queue Storage obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu usługi Azure queue storage jednostek. Te scenariusze obejmują typowe zadania, takie jak tworzenie kolejki platformy Azure i dodawanie, modyfikowanie, odczytywanie i usuwanie wiadomości w kolejce.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto usługi Azure Storage](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**i wybierz z menu kontekstowego **Dodaj -> kontrolera**.

    ![Dodawanie kontrolera do aplikacji ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Na **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC 5 — pusty**i wybierz **Dodaj**.

    ![Określ typ kontrolera MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Na **Dodaj kontroler** okno dialogowe, nazwy kontrolera *QueuesController*i wybierz **Dodaj**.

    ![Nazwa kontrolera MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Dodaj następujący kod *przy użyciu* dyrektywy `QueuesController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższe kroki pokazują, jak utworzyć kolejkę:

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`. 

1. Dodaj metodę o nazwie **CreateQueue** zwracającego **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **CreateQueue** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudQueueClient** obiekt reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz **CloudQueue** obiekt, który reprezentuje odwołanie do nazwy żądanej kolejki. **CloudQueueClient.GetQueueReference** metoda nie powoduje żądanie względem usługi queue storage. Odwołanie jest zwracany, czy kolejka istnieje. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołaj **CloudQueue.CreateIfNotExists** metodę, aby utworzyć kolejkę, jeśli jeszcze nie istnieje. **CloudQueue.CreateIfNotExists** metoda zwraca **true** Jeśli kolejka nie istnieje i został pomyślnie utworzony. W przeciwnym razie **false** jest zwracana.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualizacja **obiekt ViewBag** nazwą kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **CreateQueue** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `CreateQueue.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Utwórz kolejkę** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Tworzenie kolejki](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak wspomniano wcześniej, **CloudQueue.CreateIfNotExists** metoda zwraca **true** tylko gdy kolejka nie istnieje i zostanie utworzony. W związku z tym, po uruchomieniu aplikacji, gdy istnieje kolejka, metoda zwraca **false**. Aby uruchomić aplikację wiele razy, możesz usunąć kolejki przed ponownym uruchomieniem aplikacji. Usuwanie kolejki może odbywać się za pośrednictwem **CloudQueue.Delete** metody. Możesz także usunąć za pomocą kolejki [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Po [utworzono kolejkę](#create-a-queue), można dodawać komunikaty do tej kolejki. Ta sekcja przeprowadzi Cię przez dodający komunikat do kolejki *kolejki testowej*. 

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **AddMessage** zwracającego **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **AddMessage** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
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

1. Tworzenie **CloudQueueMessage** obiekt reprezentujący wiadomość, którą chcesz dodać do kolejki. A **CloudQueueMessage** obiektu można tworzyć na podstawie ciągu (w formacie UTF-8) lub tablicą bajtów.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Wywołaj **CloudQueue.AddMessage** metody w celu dodania messaged do kolejki.

    ```csharp
    queue.AddMessage(message);
    ```

1. Utwórz i ustaw kilku **obiekt ViewBag** właściwości do wyświetlenia w widoku.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **AddMessage** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `AddMessage.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj komunikat** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Dodaj komunikat](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Dwie sekcje - [odczytywać komunikaty z kolejki bez jego usuwania](#read-a-message-from-a-queue-without-removing-it) i [Odczyt i usuwanie komunikatu z kolejki](#read-and-remove-a-message-from-a-queue) -pokazują, jak odczytywać komunikaty z kolejki.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Przeczytaj komunikat z kolejki bez jego usuwania

W tej sekcji przedstawiono sposób wglądu do wiadomości w kolejce (do odczytu do pierwszej wiadomości bez jego usuwania).  

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **PeekMessage** zwracającego **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **PeekMessage** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
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

1. Wywołaj **CloudQueue.PeekMessage** metodę w celu odczytania do pierwszej wiadomości w kolejce bez usuwania go z kolejki. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualizacja **obiekt ViewBag** z dwóch wartości: Nazwa kolejki i komunikat, który został odczytany. **CloudQueueMessage** obiekt udostępnia dwie właściwości w celu uzyskania wartości obiektu: **CloudQueueMessage.AsBytes** i **CloudQueueMessage.AsString**. **AsString** (używane w tym przykładzie) zwraca ciąg, podczas gdy **AsBytes** zwraca tablicę bajtów.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **PeekMessage** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `PeekMessage.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Odbierz wiadomość** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Wgląd do wiadomości](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Odczyt i usuwanie komunikatu z kolejki

W tej sekcji dowiesz się, jak czytać i usuwanie komunikatu z kolejki.   

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **przeczytanyWiadomość** zwracającego **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **przeczytanyWiadomość** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
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

1. Wywołaj **CloudQueue.GetMessage** metodę w celu odczytania do pierwszej wiadomości w kolejce. **CloudQueue.GetMessage** metody sprawia, że komunikat niewidoczny przez 30 sekund (domyślnie) do innego kodu odczytującego komunikaty, tak aby żadnego innego kodu, można zmodyfikować lub usunąć komunikat podczas Twojej jego przetwarzania. Aby zmienić ilość czasu, wiadomość jest niewidoczny, zmodyfikuj **visibilityTimeout** parametr został przekazany do **CloudQueue.GetMessage** metody.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Wywołaj **CloudQueueMessage.Delete** metody do usuwania komunikatu z kolejki.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualizacja **obiekt ViewBag** wiadomość usunięta i nazwę kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **przeczytanyWiadomość** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `ReadMessage.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Odczyt i usuwanie wiadomości** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Odczyt i usuwanie wiadomości](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

W tej sekcji pokazano, jak pobrać długość kolejki (liczba komunikatów). 

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **GetQueueLength** zwracającego **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **przeczytanyWiadomość** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
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

1. Wywołaj **CloudQueue.FetchAttributes** metodę, aby pobrać atrybutów kolejki (w tym jego długość). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Dostęp do **CloudQueue.ApproximateMessageCount** właściwości do pobrania długość kolejki.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualizacja **obiekt ViewBag** nazwą kolejki, a jego długość.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **GetQueueLength** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `GetQueueLengthMessage.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **pobieranie długości kolejki** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Pobieranie długości kolejki](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Usuwanie kolejki
W tej sekcji pokazano, jak można usunąć kolejki. 

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **DeleteQueue** zwracającego **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **DeleteQueue** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
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

1. Wywołaj **CloudQueue.Delete** metodę, aby usunąć kolejkę, reprezentowane przez **CloudQueue** obiektu.

    ```csharp
    queue.Delete();
    ```

1. Aktualizacja **obiekt ViewBag** nazwą kolejki, a jego długość.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **kolejek**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **DeleteQueue** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `DeleteQueue.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **pobieranie długości kolejki** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Usuwanie kolejki](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure blob storage i Visual Studio połączonych usług (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Rozpoczynanie pracy z usługą Azure table storage i Visual Studio połączonych usług (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
