---
title: Wprowadzenie do połączeń hybrydowych usługi Azure Relay na platformie .NET | Microsoft Docs
description: Napisz aplikację konsolową w języku C# dla obiektów WebSocket połączeń hybrydowych usługi Azure Relay.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/15/2017
ms.author: spelluru
ms.openlocfilehash: 1ed401f6175d7ebea83a888898221d345791bc34
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697962"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Wprowadzenie do obiektów WebSocket połączeń hybrydowych usługi Relay na platformie .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ten samouczek zawiera wprowadzenie do [połączeń hybrydowych usługi Azure Relay](relay-what-is-it.md#hybrid-connections). Dowiedz się, jak używać programu Microsoft .NET do tworzenia aplikacji klienckiej, która wysyła komunikaty do odpowiedniej aplikacji odbiornika. 

## <a name="what-will-be-accomplished"></a>Co zostanie osiągnięte?
Połączenia hybrydowe wymagają zarówno składnika klienta, jak i składnika serwera. W tym samouczku wykonasz te kroki, aby utworzyć dwie aplikacje konsoli:

1. Utworzenie przestrzeni nazw usługi Relay za pomocą witryny Azure Portal.
2. Utworzenie połączenia hybrydowego w tej przestrzeni nazw za pomocą witryny Azure Portal.
3. Napisanie aplikacji konsolowej serwera (odbiornika) służącej do odbierania komunikatów.
4. Napisanie aplikacji konsolowej klienta (nadawcy) służącej do wysyłania komunikatów.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* [Program Visual Studio 2015 lub nowszy](http://www.visualstudio.com). W przykładach znajdujących się w tym samouczku używany jest program Visual Studio 2017.
* Subskrypcja platformy Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Tworzenie przestrzeni nazw usługi Relay za pomocą witryny Azure Portal
Jeśli przestrzeń nazw usługi Relay została już utworzona, przejdź do sekcji [Tworzenie połączenia hybrydowego za pomocą witryny Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Tworzenie połączenia hybrydowego za pomocą witryny Azure Portal
Jeśli połączenie hybrydowe zostało już utworzone, przejdź do sekcji [Tworzenie aplikacji serwera](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Tworzenie aplikacji serwera (odbiornika)
W programie Visual Studio napisz aplikację konsoli w języku C#, aby nasłuchiwać i odbierać komunikaty z usługi Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Tworzenie aplikacji klienta (nadawcy)
W programie Visual Studio napisz aplikację konsoli w języku C#, aby wysyłać komunikaty do usługi Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Uruchamianie aplikacji
1. Uruchom aplikację serwera.
2. Uruchom aplikację klienta i wprowadź jakiś tekst.
3. Upewnij się, że w konsoli aplikacji serwera jest wyświetlany tekst wprowadzony w aplikacji klienta.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Gratulacje, aplikacja end-to-end do obsługi połączeń hybrydowych jest gotowa.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)

