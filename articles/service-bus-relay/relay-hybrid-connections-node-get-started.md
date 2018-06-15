---
title: Wprowadzenie do obiektów WebSocket połączeń hybrydowych usługi Azure Relay w języku Node | Microsoft Docs
description: Napisz aplikację konsolową w środowisku Node.js do obsługi obiektów WebSocket połączeń hybrydowych usługi Azure Relay
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 1e0b76b96029e1a7ed84f1c8cd895090e8acbc6f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893397"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-node"></a>Wprowadzenie do obiektów WebSocket połączeń hybrydowych usługi Relay w języku Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ten samouczek zawiera wprowadzenie do funkcji obiektów WebSocket [połączeń hybrydowych usługi Azure Relay](relay-what-is-it.md#hybrid-connections). Pokazano w nim, jak w środowisku Node.js utworzyć aplikację kliencką, która wysyła komunikaty obiektów WebSocket do odpowiadającej jej aplikacji odbiornika.

## <a name="what-will-be-accomplished"></a>Co zostanie osiągnięte?

Połączenia hybrydowe wymagają zarówno składnika klienta, jak i składnika serwera, dlatego w tym samouczku utworzysz dwie aplikacje konsolowe. Oto konkretne kroki:

1. Utworzenie przestrzeni nazw usługi Relay za pomocą witryny Azure Portal.
2. Tworzenie połączenia hybrydowego za pomocą witryny Azure Portal.
3. Napisanie aplikacji konsolowej serwera służącej do odbierania komunikatów.
4. Napisanie aplikacji konsolowej klienta służącej do wysyłania komunikatów.

## <a name="prerequisites"></a>Wymagania wstępne

1. Środowisko [Node.js](https://nodejs.org/en/).
2. Subskrypcja platformy Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Tworzenie przestrzeni nazw za pomocą usługi Azure Portal

Jeśli przestrzeń nazw usługi Relay została już utworzona, przejdź do sekcji [Tworzenie połączenia hybrydowego za pomocą witryny Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Tworzenie połączenia hybrydowego za pomocą witryny Azure Portal

Jeśli masz już utworzone połączenie hybrydowe, przejdź do sekcji [Tworzenie aplikacji serwera](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Tworzenie aplikacji serwera (odbiornika)

Aby nasłuchiwać i odbierać komunikaty z usługi Relay, napisz aplikację konsolową Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Tworzenie aplikacji klienta (nadawcy)

Aby wysyłać komunikaty do usługi Relay, napisz aplikację konsolową Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Uruchamianie aplikacji

1. Uruchom aplikację serwera: w wierszu polecenia Node.js wpisz `node listener.js`.
2. Uruchom aplikację klienta: w wiersza polecenia Node.js wpisz `node sender.js`, a następnie wpisz dowolny tekst.
3. Upewnij się, że w konsoli aplikacji serwera pojawia się tekst wprowadzony w aplikacji klienta.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Gratulacje! Aplikacja typu end-to-end do obsługi połączeń hybrydowych korzystająca ze środowiska Node.js jest gotowa.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do programu .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)

