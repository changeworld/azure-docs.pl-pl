---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą programu .NET Framework | Microsoft Docs
description: Rozpocznij wysyłanie zdarzeń do usługi Event Hubs przy użyciu programu .NET Framework
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: feb4332f8f6b5ab26067b5c80a376cdee62c7739
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984990"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą programu .NET Framework

## <a name="introduction"></a>Wprowadzenie

Event Hubs to usługa, która przetwarza duże ilości danych zdarzeń (danych telemetrycznych) z podłączonych urządzeń i aplikacji. Po zebraniu danych w usłudze Event Hubs można przechowywać dane przy użyciu klastra magazynu lub przekształcać je za pomocą dostawcy analiz w czasie rzeczywistym. Ta możliwość zbierania i przetwarzania zdarzeń na wielką skalę jest kluczowym składnikiem architektur nowoczesnych aplikacji, w tym Internetu rzeczy (IoT).

W tym samouczku pokazano, jak utworzyć centrum zdarzeń za pomocą witryny [Azure Portal](https://portal.azure.com). Pokazano w nim także, jak wysyłać zdarzenia do centrum zdarzeń za pomocą aplikacji konsoli napisanej w języku C# w programie .NET Framework. Aby odbierać zdarzenia przy użyciu programu .NET Framework, zobacz artykuł [Receive events using the .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md) (Odbieranie zdarzeń za pomocą programu .NET Framework) lub kliknij odpowiedni język odbierający w spisie treści po lewej stronie.

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* [Program Microsoft Visual Studio w wersji 2015 lub nowszej](http://visualstudio.com). Na zrzutach ekranów przedstawionych w tym samouczku używany jest program Visual Studio 2017.
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć bezpłatne konto w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń

Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie wykonaj następujące czynności z tego samouczka.

## <a name="create-a-sender-console-application"></a>Tworzenie aplikacji konsolowej nadawcy

W tej sekcji służy do pisania aplikacji konsoli systemu Windows, która wysyła zdarzenia do Centrum zdarzeń.

1. W programie Visual Studio utwórz nowy projekt aplikacji klasycznej Visual C# za pomocą szablonu projektu **Aplikacja konsoli**. Nazwij projekt **Nadawca**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Nadawca**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet rozwiązania**. 
3. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `WindowsAzure.ServiceBus`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Program Visual Studio pobierze, zainstaluje i doda odniesienia do [pakietu NuGet biblioteki usługi Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus).
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
  ```csharp
  using System.Threading;
  using Microsoft.ServiceBus.Messaging;
  ```
5. Dodaj następujące pola do klasy **Program**, zastępując symbole zastępcze nazwą centrum zdarzeń utworzonego w poprzedniej sekcji oraz zapisanymi wcześniej parametrami połączenia na poziomie przestrzeni nazw.
   
  ```csharp
  static string eventHubName = "{Event Hub name}";
  static string connectionString = "{send connection string}";
  ```
6. Dodaj następującą metodę do klasy **Program**:
   
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
7. Na koniec dodaj następujące wiersze do metody **Główne**:
   
  ```csharp
  Console.WriteLine("Press Ctrl-C to stop the sender process");
  Console.WriteLine("Press Enter to start now");
  Console.ReadLine();
  SendingRandomMessages();
  ```
8. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.
  
Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz utworzoną działającą aplikację, która tworzy centrum zdarzeń i wysyła dane, możesz przejść do następujących scenariuszy:

* [Odbieranie zdarzeń za pomocą hosta procesora zdarzeń](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Dokumentacja hosta procesora zdarzeń](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

