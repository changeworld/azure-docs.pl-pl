---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service | Microsoft Docs
description: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a1ca02a5e058816c133accf3027f0a951db42cf2
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664007"
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
