---
title: Wprowadzenie do magazynu kolejki platformy Azure przy użyciu programu Visual Studio (ASP.NET)
description: Jak rozpocząć korzystanie z magazynu kolejek platformy Azure w projekcie ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług Połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980756"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Wprowadzenie do magazynu kolejek platformy Azure i połączonych usług programu Visual Studio (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

Usługa Azure queue storage zapewnia komunikację w chmurze między składnikami aplikacji. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostek magazynu kolejki platformy Azure. Te scenariusze obejmują typowe zadania, takie jak tworzenie kolejki platformy Azure i dodawanie, modyfikowanie, czytanie i usuwanie wiadomości kolejki.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto magazynu platformy Azure](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Kontrolery,** a następnie z menu kontekstowego wybierz polecenie **Kontroler >add.**

    ![Dodawanie kontrolera do ASP.NET aplikacji MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC 5 — opróżnij**i wybierz pozycję **Dodaj**.

    ![Określanie typu kontrolera MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. W oknie dialogowym **Dodawanie kontrolera** nazwij kontroler *QueuesController*i wybierz pozycję **Dodaj**.

    ![Nadaj nazwę kontrolerowi MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Dodaj do *using* `QueuesController.cs` pliku następujące elementy za pomocą dyrektyw:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Tworzenie kolejki

W poniższych krokach pokazano, jak utworzyć kolejkę:

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`. 

1. Dodaj metodę o nazwie **CreateQueue,** która zwraca **actionresult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **CreateQueue** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudQueueClient** obiektu reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz **CloudQueue** obiektu, który reprezentuje odwołanie do żądanej nazwy kolejki. **Metoda CloudQueueClient.GetQueueReference** nie powoduje żądania względem magazynu kolejki. Odwołanie jest zwracane, czy kolejka istnieje. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.CreateIfNotExists** metody, aby utworzyć kolejkę, jeśli jeszcze nie istnieje. **CloudQueue.CreateIfNotExists** Metoda zwraca **wartość true,** jeśli kolejka nie istnieje i jest pomyślnie utworzony. W przeciwnym razie zwracana jest **wartość false.**    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Zaktualizuj **ViewBag** o nazwę kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Kolejki,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **Utwórz kolejka** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `CreateQueue.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz **pozycję Utwórz kolejkę,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Tworzenie kolejki](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak wspomniano wcześniej, **CloudQueue.CreateIfNotExists** metoda zwraca **true** tylko wtedy, gdy kolejka nie istnieje i jest tworzony. W związku z tym po uruchomieniu aplikacji, gdy istnieje kolejka, metoda zwraca **false**. Aby uruchomić aplikację wiele razy, należy usunąć kolejkę przed ponownym uruchomieniem aplikacji. Usuwanie kolejki można wykonać za pomocą **CloudQueue.Delete** metody. Kolejkę można również usunąć za pomocą [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Eksploratora magazynu platformy Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Dodawanie wiadomości do kolejki

Po [utworzeniu kolejki](#create-a-queue)można dodawać wiadomości do tej kolejki. W tej sekcji można przejść przez dodawanie wiadomości do *kolejki testowej kolejki*. 

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **AddMessage,** która zwraca **actionresult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **AddMessage** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiektu reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiektu, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Utwórz **obiekt CloudQueueMessage reprezentujący** wiadomość, którą chcesz dodać do kolejki. Obiekt **CloudQueueMessage** można utworzyć z ciągu (w formacie UTF-8) lub tablicy bajtowej.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Wywołanie **CloudQueue.AddMessage** metody, aby dodać messaged do kolejki.

    ```csharp
    queue.AddMessage(message);
    ```

1. Utwórz i ustaw kilka właściwości **ViewBag** do wyświetlania w widoku.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Kolejki,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **AddMessage** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `AddMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj komunikat,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Dodaj wiadomość](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Dwie sekcje — [odczytywanie wiadomości z kolejki bez usuwania jej](#read-a-message-from-a-queue-without-removing-it) i [odczytu i usuwania wiadomości z kolejki](#read-and-remove-a-message-from-a-queue) — ilustrują sposób odczytywania wiadomości z kolejki.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Czytanie wiadomości z kolejki bez jej usuwania

W tej sekcji pokazano, jak zajrzeć do wiadomości w kolejce (przeczytaj pierwszą wiadomość bez jej usuwania).  

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **PeekMessage,** która zwraca **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **Metody PeekMessage** pobierz **Obiekt CloudStorageAccount,** który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiektu reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiektu, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.PeekMessage** metody, aby odczytać pierwszą wiadomość w kolejce bez usuwania go z kolejki. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Zaktualizuj **ViewBag** o dwie wartości: nazwę kolejki i wiadomość, która została odczytana. **Obiekt CloudQueueMessage** udostępnia dwie właściwości uzyskiwania wartości obiektu: **CloudQueueMessage.AsBytes** i **CloudQueueMessage.AsString**. **AsString** (używany w tym przykładzie) zwraca ciąg, podczas gdy **AsBytes** zwraca tablicę bajtów.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Kolejki,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **Wgląd** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `PeekMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

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

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **komunikat Zerknij,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Komunikat wglądu](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Odczytywanie i usuwanie wiadomości z kolejki

W tej sekcji dowiesz się, jak odczytać i usunąć wiadomość z kolejki.   

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **ReadMessage,** która zwraca **actionresult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **ReadMessage** metody, uzyskać **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiektu reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiektu, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.GetMessage** metody, aby odczytać pierwszą wiadomość w kolejce. **CloudQueue.GetMessage** Metoda sprawia, że wiadomość niewidoczna przez 30 sekund (domyślnie) do innych komunikatów odczytu kodu, tak aby żaden inny kod można zmodyfikować lub usunąć wiadomość podczas przetwarzania go. Aby zmienić czas, przez który wiadomość jest niewidoczna, zmodyfikuj parametr **visibilityTimeout** przekazywany do metody **CloudQueue.GetMessage.**

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Wywołanie **CloudQueueMessage.Delete** metody, aby usunąć wiadomość z kolejki.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Zaktualizuj **ViewBag** z usuniętą wiadomością i nazwą kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Kolejki,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **ReadMessage** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `ReadMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

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

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **komunikat Odczyt/Usuń,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Czytanie i usuwanie wiadomości](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

W tej sekcji pokazano, jak uzyskać długość kolejki (liczba wiadomości). 

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **GetQueueLength,** która zwraca **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **ReadMessage** metody, uzyskać **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiektu reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiektu, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.FetchAttributes** metody, aby pobrać atrybuty kolejki (w tym jego długość). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Dostęp do **CloudQueue.ApproximateMessageCount** właściwości, aby uzyskać długość kolejki.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Zaktualizuj **ViewBag** o nazwę kolejki i jej długość.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Kolejki,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **GetQueueLength** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `GetQueueLengthMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Pobierz długość kolejki,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Uzyskaj długość kolejki](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Usuwanie kolejki
W tej sekcji pokazano, jak usunąć kolejkę. 

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **DeleteQueue,** która zwraca **actionresult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W ramach **DeleteQueue** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz **CloudQueueClient** obiektu reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz **CloudQueueContainer** obiektu, który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołanie **CloudQueue.Delete** metody, aby usunąć kolejkę reprezentowane przez **CloudQueue** obiektu.

    ```csharp
    queue.Delete();
    ```

1. Zaktualizuj **ViewBag** o nazwę kolejki i jej długość.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Kolejki,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **Usuń zaokrążenie** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `DeleteQueue.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Pobierz długość kolejki,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Usuń kolejkę](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Wprowadzenie do magazynu obiektów blob platformy Azure i usług połączonych programu Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Wprowadzenie do magazynu tabel platformy Azure i połączonych usług programu Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
