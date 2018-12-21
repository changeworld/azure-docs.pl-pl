---
title: Co to jest usługa Azure SignalR?
description: Omówienie usługi Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: e66326c6c4d93a92c579255cb00b6614ecc03b8c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255180"
---
# <a name="what-is-azure-signalr-service"></a>Co to jest Azure SignalR Service

Usługa Azure SignalR Service upraszcza proces dodawania funkcji internetowych w czasie rzeczywistym do aplikacji za pośrednictwem protokołu HTTP. Ta funkcja w czasie rzeczywistym umożliwia usłudze wypychanie aktualizacji zawartości do połączonych klientów, takich jak pojedyncza strona internetowa lub aplikacja mobilna. W związku z tym klienci otrzymują aktualizacje bez konieczności sondowania serwera czy przesyłania nowych żądań HTTP o aktualizacje.

Ten artykuł zawiera omówienie usługi Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Do czego służy usługa Azure SignalR Service?

Istnieje wiele typów aplikacji, które wymagają aktualizacji zawartości w czasie rzeczywistym. Następujące przykłady są dobrymi kandydatami do korzystania z usługi Azure SignalR Service:

* Aplikacje, które wymagają częstych aktualizacji z serwera. Są to na przykład gry i aplikacje do obsługi głosowania, aukcji, map i nawigacji GPS.
* Pulpity nawigacyjne i aplikacje do monitorowania. Przykładami mogą być firmowe pulpity nawigacyjne oraz błyskawiczne powiadomienia o sprzedaży.
* Aplikacje do współpracy. Przykładami aplikacji do współpracy są aplikacje tablicy i oprogramowanie do spotkań zespołowych.
* Aplikacje, które wymagają powiadomień. Wiele aplikacji korzysta z powiadomień, w tym gry i aplikacje do obsługi sieci społecznościowych, poczty e-mail, czatów i alertów dotyczących podróży.

Usługa SignalR udostępnia abstrakcję szeregu technik służących do tworzenia aplikacji internetowych działających w czasie rzeczywistym. [Protokół WebSockets](https://wikipedia.org/wiki/WebSocket) stanowi optymalną metodę transportu, ale inne techniki, takie jak [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) i Long Polling są używane, gdy nie są dostępne inne opcje. Biblioteka SignalR automatycznie wykrywa i inicjuje odpowiednią metodę transportu na podstawie funkcji obsługiwanych przez serwer i klienta.

Ponadto usługa SignalR udostępnia model programowania dla aplikacji w czasie rzeczywistym, który umożliwia serwerowi wysyłanie komunikatów do wszystkich połączeń lub podzbioru połączeń, które należą do określonego użytkownika lub zostały umieszczone w jakiejś grupie.

## <a name="how-to-use-azure-signalr-service"></a>Jak używać usługi Azure SignalR Service

Obecnie istnieją trzy sposoby korzystania z usługi Azure SignalR Service:

- **[Skalowanie aplikacji SignalR platformy ASP.NET Core](signalr-overview-scale-aspnet-core.md)** — integracja usługi Azure SignalR Service z aplikacją SignalR platformy ASP.NET Core w celu skalowania do setek tysięcy połączeń.
- **[Tworzenie bezserwerowych aplikacji w czasie rzeczywistym](signalr-overview-azure-functions.md)** — wykorzystanie integracji usługi Azure Functions z usługą Azure SignalR Service do tworzenia bezserwerowych aplikacji w czasie rzeczywistym w językach takich jak JavaScript, C# i Java.
- **[Wysyłanie komunikatów z serwera do klientów za pośrednictwem interfejsu API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** — usługa Azure SignalR Service udostępnia interfejs API REST, aby umożliwić aplikacjom wysyłanie komunikatów do połączonych klientów przy użyciu usługi SignalR Service, w dowolnym języku programowania obsługiwanym przez REST.