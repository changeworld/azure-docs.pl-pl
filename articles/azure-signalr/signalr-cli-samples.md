---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service | Microsoft Docs
description: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779858"
---
# <a name="azure-cli-samples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

Poniższa tabela zawiera linki do skryptów powłoki systemowej dla usługi Azure SignalR Service używających interfejsu wiersza polecenia platformy Azure.

| | |
|-|-|
|**Tworzenie**||
| [Tworzenie nowej usługi SignalR Service i grupy zasobów](scripts/signalr-cli-create-service.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie.  |
|**Integracja**||
| [Tworzenie nowej usługi SignalR Service i aplikacji internetowej skonfigurowanej na potrzeby korzystania z usługi SignalR](scripts/signalr-cli-create-with-app-service.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie. Dodaje również nowy plan aplikacji internetowej i usługi App Service do hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji, aby nawiązać połączenie z nowym zasobem usługi SignalR Service. |
| [Tworzenie nowej usługi SignalR Service i aplikacji internetowej skonfigurowanej na potrzeby korzystania z usług SignalR i GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie. Dodaje również nową aplikację internetową platformy Azure i plan hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji, aby parametry połączenia z nowym zasobem usługi SignalR i klucze tajne klientów obsługiwały [uwierzytelnianie GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), jak pokazano w [samouczku uwierzytelniania](signalr-authenticate-oauth.md). Aplikacja internetowa jest również skonfigurowana do używania lokalnego źródła wdrożenia repozytorium git. |
| | |
