---
title: Samouczek — Praca z kolejek usługi Azure storage — usługi Azure Storage
description: Samouczek dotyczący sposobu użycia usługi kolejek platformy Azure do tworzenia kolejek oraz wstawiania, pobieranie i usuwanie wiadomości.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 08ef140eb860637cc0c09619abe7051cc007e99f
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540297"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Samouczek: Praca z kolejkami magazynu platformy Azure

Usługa Azure Queue storage implementuje są oparte na chmurze kolejki, aby umożliwić komunikację między składnikami aplikacji rozproszonej. Każda kolejka utrzymuje listę komunikatów, które mogą być dodawane przez składnik nadawcy lub przetworzone przez składnik odbiorcy. Z kolejki aplikację można skalować od razu do spełnienia określonych wymagań. W tym artykule przedstawiono podstawowe czynności w przypadku pracy z kolejki usługi Azure storage.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie konta usługi Azure Storage
> - Tworzymy aplikację.
> - Dodano obsługę kodu asynchronicznego
> - Tworzenie kolejki
> - Wstawianie komunikatów do kolejki
> - Usuń z kolejki komunikatów
> - Usuń pustej kolejce
> - Sprawdź argumenty wiersza polecenia
> - Kompilowanie i uruchamianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz bezpłatną kopię międzyplatformowego [programu Visual Studio Code](https://code.visualstudio.com/download) edytora.
- Pobierz i zainstaluj [zestawu .NET Core SDK](https://dotnet.microsoft.com/download).
- Jeśli nie masz bieżącej subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw utwórz konto magazynu platformy Azure. Przewodnik krok po kroku dotyczący Tworzenie konta magazynu, zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) Szybki Start.

## <a name="create-the-app"></a>Tworzymy aplikację.

Tworzenie aplikacji .NET Core o nazwie **QueueApp**. Dla uproszczenia ta aplikacja będzie wysyłać i odbierać komunikaty w kolejce.

1. W oknie konsoli (np. CMD, PowerShell lub wiersza polecenia platformy Azure), należy użyć `dotnet new` polecenie, aby utworzyć nową aplikację konsoli o nazwie **QueueApp**. To polecenie umożliwia utworzenie prostego "Hello World" C# projektu z jednym pliku źródłowym: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Przełącz się do nowo utworzonego **QueueApp** folder i kompilacja aplikacji, aby sprawdzić, czy wszystkie poprawnie.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Powinny zostać wyświetlone wyniki podobne do następujących:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Dodano obsługę kodu asynchronicznego

Ponieważ aplikacja korzysta z zasobów w chmurze, kod jest uruchamiany asynchronicznie. Jednak C#firmy **async** i **await** nie były prawidłowe słów kluczowych w **Main** metody do momentu C# 7.1. Można łatwo przełączać przez kompilator za pomocą flagi w **csproj** pliku.

1. W wierszu polecenia w katalogu projektu wpisz `code .` można otworzyć programu Visual Studio Code w bieżącym katalogu. Nie zamykaj okna wiersza polecenia. Nastąpi kolejne polecenia nie można wykonać później. Jeśli zostanie wyświetlony monit, aby dodać C# zasoby wymagane do tworzenia i debugowania, kliknij przycisk **tak** przycisku.

2. Otwórz **QueueApp.csproj** plik w edytorze.

3. Dodaj `<LangVersion>7.1</LangVersion>` do pierwszej **PropertyGroup** w pliku kompilacji. Upewnij się, że można dodawać tylko **LangVersion** otaguj jako swojej **TargetFramework** mogą się różnić w zależności od zainstalowanej wersji programu .NET.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Zapisz **QueueApp.csproj** pliku.

5. Otwórz **Program.cs** źródło pliku i zaktualizuj **Main** metody, aby go uruchomić asynchronicznie. Zastąp **void** z **asynchroniczne zadanie** zwracają wartość.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Zapisz **Program.cs** pliku.

## <a name="create-a-queue"></a>Tworzenie kolejki

1. Zainstaluj **Microsoft.Azure.Storage.Common** i **Microsoft.Azure.Storage.Queue** pakietów do projektu z `dotnet add package` polecenia. Wykonaj następujące polecenia dotnet z folderu projektu w oknie konsoli.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. W górnej części **Program.cs** Dodaj następujące przestrzenie nazw od razu po `using System;` instrukcji. Ta aplikacja używa typów z te obszary nazw, łączenie z usługą Azure Storage i pracy z kolejkami.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Zapisz **Program.cs** pliku.

### <a name="get-your-connection-string"></a>Pobieranie parametrów połączenia

Biblioteka klienta używa parametrów połączenia do nawiązania połączenia. Parametry połączenia usługi jest dostępny w **ustawienia** sekcji konta magazynu w witrynie Azure portal.

1. W przeglądarce sieci web Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

2. W witrynie Azure Portal przejdź do swojego konta magazynu.

3. Wybierz **klucze dostępu**.

4. Kliknij przycisk **kopiowania** przycisk po prawej stronie **parametry połączenia** pola.

![Parametry połączenia](media/storage-tutorial-queues/get-connection-string.png)

Parametry połączenia mają następujący format:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Dodaj parametry połączenia do aplikacji

Dodaj parametry połączenia do aplikacji, aby mogli uzyskiwać dostęp na koncie magazynu.

1. Przełącz się do programu Visual Studio Code.

2. W **Program** klasy, Dodaj `private const string connectionString =` elementu członkowskiego, aby pomieścić ciąg połączenia.

3. Po znaku równości Wklej wartość ciągu, który został skopiowany wcześniej w portalu Azure. **ConnectionString** wartość jest unikatowa dla swojego konta.

4. Usuń kod "Hello World" z **Main**. Kod powinien wyglądać podobnie do poniższego, ale wartość parametrów połączenia unikatowy.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Aktualizacja **Main** utworzyć **CloudQueue** obiektu, który zostanie później przekazany do wysyłania i odbierania metody.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Zapisz plik.

## <a name="insert-messages-into-the-queue"></a>Wstawianie komunikatów do kolejki

Utworzenie nowej metody, aby wysłać komunikat do kolejki. Dodaj następującą metodę do swojej **Program** klasy. Ta metoda pobiera odwołanie do kolejki, a następnie tworzy nową kolejkę, jeśli jeszcze nie istnieje, wywołując [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Następnie dodaje komunikat do kolejki, wywołując [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Dodaj następujący kod **SendMessageAsync** metodę do swojej **Program** klasy.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Zapisz plik.

Wiadomość musi być w formacie, który można uwzględnić w żądaniu XML z kodowaniem UTF-8, a jego maksymalny rozmiar może wynosić maksymalnie 64 KB. Jeśli wiadomość zawiera dane binarne, zalecamy możesz kodowanie Base64 wiadomości.

Domyślnie maksymalny czas wygaśnięcia komunikatu wynosi 7 dni. Można określić dowolna liczba dodatnia dla komunikatu, time-to-live. Aby dodać wiadomości, które nie wygaśnie, należy użyć `Timespan.FromSeconds(-1)` w wywołania do **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Usuń z kolejki komunikatów

Utwórz nową metodę o nazwie **ReceiveMessageAsync**. Ta metoda otrzymuje komunikat z kolejki, wywołując [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Gdy wiadomość została odebrana pomyślnie, należy usunąć ją z kolejki, więc nie jest przetwarzane więcej niż jeden raz. Po otrzymaniu komunikatu usunąć ją z kolejki, wywołując [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Dodaj następujący kod **ReceiveMessageAsync** metodę do swojej **Program** klasy.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Zapisz plik.

## <a name="delete-an-empty-queue"></a>Usuń pustej kolejce

Jest najlepszym rozwiązaniem jest na końcu jest projekt, aby ustalić, czy nadal potrzebujesz zasobów, który został utworzony. Po lewej stronie umożliwia uruchamianie zasobów kosztów pieniądze. Jeśli kolejka istnieje, ale jest pusta, należy poprosić użytkownika, czy chce go usunąć.

1. Rozwiń **ReceiveMessageAsync** metodę, aby dołączyć wiersza do usunięcia pustej kolejki.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Zapisz plik.

## <a name="check-for-command-line-arguments"></a>Sprawdź argumenty wiersza polecenia

Jeśli ma żadnych argumentów wiersza polecenia przekazywane do aplikacji, należy założyć, że są one wiadomości, które mają zostać dodane do kolejki. Dołącz do argumentów razem w celu zapewnienia ciągu. Dodaj następujący ciąg do kolejki komunikatów, wywołując **SendMessageAsync** metody, które dodaliśmy wcześniej.

Jeśli nie ma żadnych argumentów wiersza polecenia, można wykonać operacji pobierania. Wywołaj **ReceiveMessageAsync** metodę, aby pobrać pierwszy komunikat w kolejce.

Na koniec poczekaj na dane wejściowe użytkownika przed zakończeniem pracy przez wywołanie metody **Console.ReadLine**.

1. Rozwiń **Main** metodę, aby sprawdzić, czy argumenty wiersza polecenia i czeka na dane wejściowe użytkownika.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Zapisz plik.

## <a name="complete-code"></a>Kompletny kod

Oto kompletny kod dla tego projektu.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. W wierszu polecenia w katalogu projektu uruchom następujące polecenie dotnet w celu skompilowania projektu.

   ```console
   dotnet build
   ```

2. Po projekt zostanie skompilowany poprawnie, uruchom następujące polecenie, aby dodać pierwszy komunikat do kolejki.

   ```console
   dotnet run First queue message
   ```

Powinny zostać wyświetlone następujące dane wyjściowe:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Uruchom aplikację bez argumentów wiersza polecenia do odbierania i Usuń pierwszy komunikat w kolejce.

   ```console
   dotnet run
   ```

4. Kontynuuj uruchomić aplikację, dopóki nie zostaną usunięte wszystkie komunikaty. Jeśli uruchamiasz go jeszcze raz, otrzymasz komunikat, że kolejka jest pusta i monit o usunięcie kolejki.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie kolejki
2. Dodawanie i usuwanie wiadomości z kolejki
3. Usuwanie kolejki usługi Azure storage

Zapoznaj się z opcji szybkiego startu kolejek systemu Azure, aby uzyskać więcej informacji.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start kolejek](storage-quickstart-queues-portal.md)
