---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service
description: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258784"
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