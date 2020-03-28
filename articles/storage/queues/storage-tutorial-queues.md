---
title: Samouczek — praca z kolejkami magazynu platformy Azure — usługa Azure Storage
description: Samouczek dotyczący używania usługi Kolejka platformy Azure do tworzenia kolejek oraz wstawiania, pobierania i usuwania wiadomości.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968196"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Samouczek: Praca z kolejkami magazynu platformy Azure

Usługa Azure Queue storage implementuje kolejki oparte na chmurze, aby umożliwić komunikację między składnikami aplikacji rozproszonej. Każda kolejka przechowuje listę wiadomości, które mogą być dodawane przez składnik nadawcy i przetwarzane przez składnik odbiornika. W kolejce aplikacja może natychmiast skalować, aby zaspokoić zapotrzebowanie. W tym artykule przedstawiono podstawowe kroki pracy z kolejką magazynu platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie konta usługi Azure Storage
> - Tworzymy aplikację.
> - Dodaj obsługę kodu asynchroniowego
> - Tworzenie kolejki
> - Wstawianie wiadomości do kolejki
> - Usuwanie wysuwu wiadomości
> - Usuwanie pustej kolejki
> - Sprawdzanie argumentów wiersza polecenia
> - Kompilowanie i uruchamianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz bezpłatną kopię edytora [kodu programu Visual Studio](https://code.visualstudio.com/download) na różnych platformach.
- Pobierz i zainstaluj [pakiet .NET Core SDK](https://dotnet.microsoft.com/download).
- Jeśli nie masz bieżącej subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw utwórz konto magazynu platformy Azure. Aby uzyskać przewodnik krok po kroku dotyczący tworzenia konta magazynu, zobacz szybki start [tworzenia konta magazynu.](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)

## <a name="create-the-app"></a>Tworzymy aplikację.

Utwórz aplikację .NET Core o nazwie **QueueApp**. Dla uproszczenia ta aplikacja będzie zarówno wysyłać i odbierać wiadomości za pośrednictwem kolejki.

1. W oknie konsoli (takim jak CMD, PowerShell lub `dotnet new` Azure CLI) użyj polecenia, aby utworzyć nową aplikację konsoli o nazwie **QueueApp**. To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Przełącz się do nowo utworzonego folderu **QueueApp** i skompiluj aplikację, aby sprawdzić, czy wszystko jest dobrze.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Powinny być widoczne wyniki podobne do następujących:

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

## <a name="add-support-for-asynchronous-code"></a>Dodaj obsługę kodu asynchroniowego

Ponieważ aplikacja używa zasobów w chmurze, kod jest uruchamiany asynchronicznie. Jednak C#async i **await** nie były prawidłowe słowa kluczowe w **Main** metody do C# 7.1. **async** Możesz łatwo przełączyć się do tego kompilatora za pomocą flagi w pliku **csproj.**

1. Z wiersza polecenia w katalogu `code .` projektu wpisz, aby otworzyć program Visual Studio Code w bieżącym katalogu. Okno wiersza polecenia należy zachować otwartą. Będzie więcej poleceń do wykonania później. Jeśli zostanie wyświetlony monit o dodanie zasobów języka C# wymaganych do tworzenia i debugowania, kliknij przycisk **Tak.**

2. Otwórz plik **QueueApp.csproj** w edytorze.

3. Dodaj `<LangVersion>7.1</LangVersion>` do pierwszej **Grupy Właściwości** w pliku kompilacji. Upewnij się, że tag **LangVersion** jest dodawany tylko jako **targetframework** może się różnić w zależności od wersji zainstalowanej platformy .NET.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Zapisz plik **QueueApp.csproj.**

5. Otwórz **plik** źródłowy Program.cs i zaktualizuj **metodę Main,** aby uruchomić asynchronicznie. Zastąp **pustkę** wartością zwracaną **zadania asynchronicznego.**

   ```csharp
   static async Task Main(string[] args)
   ```

6. Zapisz plik **Program.cs.**

## <a name="create-a-queue"></a>Tworzenie kolejki

1. Zainstaluj pakiety **Microsoft.Azure.Storage.Common** i **Microsoft.Azure.Storage.Queue** `dotnet add package` do projektu za pomocą polecenia. Wykonaj następujące polecenia dotnet z folderu projektu w oknie konsoli.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. W górnej części pliku **Program.cs** dodaj następujące przestrzenie nazw `using System;` zaraz po instrukcji. Ta aplikacja używa typów z tych obszarów nazw, aby połączyć się z usługą Azure Storage i pracować z kolejkami.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Zapisz plik **Program.cs.**

### <a name="get-your-connection-string"></a>Pobieranie parametrów połączenia

Biblioteka klienta do nawiązania połączenia używa parametrów połączenia. Twój parametry połączenia są dostępne w sekcji **Ustawienia** konta magazynu w witrynie Azure portal.

1. W przeglądarce sieci Web zaloguj się do [witryny Azure portal](https://portal.azure.com/).

2. W witrynie Azure Portal przejdź do swojego konta magazynu.

3. Wybierz **klawisze dostępu**.

4. Kliknij przycisk **Kopiuj** po prawej stronie pola **Parametry połączenia.**

![Parametry połączenia](media/storage-tutorial-queues/get-connection-string.png)

Parametry połączenia mają następujący format:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Dodawanie ciągu połączenia do aplikacji

Dodaj ciąg połączenia do aplikacji, aby mógł uzyskać dostęp do konta magazynu.

1. Przełącz się z powrotem do programu Visual Studio Code.

2. W **klasie Program** dodaj `private const string connectionString =` element członkowski, aby przytrzymać ciąg połączenia.

3. Po znaku równości wklej wartość ciągu skopiowaną wcześniej w witrynie Azure Portal. **ConnectionString** wartość będzie unikatowa dla twojego konta.

4. Usuń kod "Hello World" z **Main**. Kod powinien wyglądać podobnie do następującego, ale z unikatową wartością ciągu połączenia.

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

5. Aktualizuj **main,** aby utworzyć **CloudQueue** obiektu, który jest później przekazywane do wysyłania i odbierania metod.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Zapisz plik.

## <a name="insert-messages-into-the-queue"></a>Wstawianie wiadomości do kolejki

Utwórz nową metodę wysyłania wiadomości do kolejki. Dodaj następującą metodę do klasy **Program.** Ta metoda pobiera odwołanie do kolejki, a następnie tworzy nową kolejkę, jeśli jeszcze nie istnieje, wywołując [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Następnie dodaje wiadomość do kolejki, wywołując [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Dodaj następującą metodę **SendMessageAsync** do klasy **Program.**

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

Wiadomość musi być w formacie, który można uwzględnić w żądaniu XML z kodowaniem UTF-8, a jego maksymalny rozmiar może wynosić maksymalnie 64 KB. Jeśli wiadomość zawiera dane binarne, zaleca się kodowanie wiadomości przez base64.

Domyślnie maksymalny czas wygaśnięcia komunikatu wynosi 7 dni. Można określić dowolną liczbę dodatnią dla czasu wiadomości do żywo. Aby dodać wiadomość, która nie `Timespan.FromSeconds(-1)` wygasa, użyj połączenia **z AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Usuwanie wysuwu wiadomości

Utwórz nową metodę o nazwie **ReceiveMessageAsync**. Ta metoda odbiera wiadomość z kolejki, wywołując [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Po pomyślnym odebraniu wiadomości należy ją usunąć z kolejki, aby nie była przetwarzana więcej niż jeden raz. Po odebraniu wiadomości usuń ją z kolejki, dzwoniąc do [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Dodaj następującą metodę **ReceiveMessageAsync** do klasy **Program.**

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

## <a name="delete-an-empty-queue"></a>Usuwanie pustej kolejki

Jest najlepszym rozwiązaniem na końcu projektu, aby określić, czy nadal potrzebujesz zasobów, które zostały utworzone. Nadal uruchomione zasoby mogą generować koszty. Jeśli kolejka istnieje, ale jest pusta, zapytaj użytkownika, czy chce ją usunąć.

1. Rozwiń **ReceiveMessageAsync** metoda, aby uwzględnić monit o usunięcie pustej kolejki.

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

## <a name="check-for-command-line-arguments"></a>Sprawdzanie argumentów wiersza polecenia

Jeśli istnieją argumenty wiersza polecenia przekazywane do aplikacji, załóżmy, że jest to komunikat, który ma zostać dodany do kolejki. Połącz argumenty razem, aby zrobić ciąg. Dodaj ten ciąg do kolejki wiadomości, wywołując **SendMessageAsync** metody dodaliśmy wcześniej.

Jeśli nie ma żadnych argumentów wiersza polecenia, wykonaj operację pobierania. Wywołanie **ReceiveMessageAsync** metody, aby pobrać pierwszą wiadomość w kolejce.

Na koniec poczekaj na dane wejściowe użytkownika przed zamknięciem, wywołując **Console.ReadLine**.

1. Rozwiń **Metodę Main,** aby sprawdzić argumenty wiersza polecenia i poczekać na dane wejściowe użytkownika.

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

Oto pełna lista kodów dla tego projektu.

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

1. W wierszu polecenia w katalogu projektu uruchom następujące polecenie dotnet, aby utworzyć projekt.

   ```console
   dotnet build
   ```

2. Po pomyślnym utworzeniu projektu uruchom następujące polecenie, aby dodać pierwszą wiadomość do kolejki.

   ```console
   dotnet run First queue message
   ```

Powinny być widoczne następujące dane wyjściowe:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Uruchom aplikację bez argumentów wiersza polecenia, aby odbierać i usuwać pierwszą wiadomość w kolejce.

   ```console
   dotnet run
   ```

4. Kontynuuj uruchamianie aplikacji, dopóki wszystkie wiadomości nie zostaną usunięte. Jeśli uruchomisz go jeszcze raz, zostanie wyświetlony komunikat, że kolejka jest pusta i monit o usunięcie kolejki.

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

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie kolejki
2. Dodawanie i usuwanie wiadomości z kolejki
3. Usuwanie kolejki magazynu platformy Azure

Aby uzyskać więcej informacji, zapoznaj się z przewodnikiem szybki start kolejek platformy Azure.

> [!div class="nextstepaction"]
> [Szybki start kolejek](storage-quickstart-queues-portal.md)
