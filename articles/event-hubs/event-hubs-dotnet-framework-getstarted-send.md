---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą programu .NET Framework | Microsoft Docs
description: Rozpocznij wysyłanie zdarzeń do usługi Event Hubs przy użyciu programu .NET Framework
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: adfe2ae81115e498a44e95ae8d21d3d7b751c18c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248031"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą programu .NET Framework
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

Ten samouczek przedstawia do wysyłania zdarzeń do Centrum zdarzeń za pomocą aplikacji konsolowej napisanej w języku C# za pomocą programu .NET Framework. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* [Microsoft Visual Studio 2017 lub nowszego](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń
Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie wykonaj następujące czynności z tego samouczka.

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

W programie Visual Studio utwórz nowy projekt aplikacji klasycznej Visual C# za pomocą szablonu projektu **Aplikacja konsoli**. Nazwij projekt **Nadawca**.
   
![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Nadawca**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet rozwiązania**. 
2. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `WindowsAzure.ServiceBus`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Program Visual Studio pobierze, zainstaluje i doda odniesienia do [pakietu NuGet biblioteki usługi Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Dodaj następujące pola do klasy **Program**, zastępując symbole zastępcze nazwą centrum zdarzeń utworzonego w poprzedniej sekcji oraz zapisanymi wcześniej parametrami połączenia na poziomie przestrzeni nazw.
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Dodaj następującą metodę do klasy **Program**:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Ta metoda stale wysyła zdarzenia do centrum zdarzeń z opóźnieniem 200 ms.
4. Na koniec dodaj następujące wiersze do metody **Główne**:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.
  
Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start zostały wysłane wiadomości do Centrum zdarzeń za pomocą .NET Framework. Aby dowiedzieć się, jak odbierać zdarzenia z Centrum zdarzeń za pomocą .NET Framework, zobacz [odbieranie zdarzeń z Centrum zdarzeń — .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

