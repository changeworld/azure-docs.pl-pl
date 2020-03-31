---
title: Co to jest usługa Azure SignalR?
description: Lepiej zrozumieć, jakie typowe scenariusze przypadków użycia do korzystania z usługi Azure SignalR i dowiedzieć się kluczowych korzyści z usługi Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157578"
---
# <a name="what-is-azure-signalr-service"></a>Co to jest usługa Azure SignalR?

Usługa Azure SignalR Service upraszcza proces dodawania funkcji internetowych w czasie rzeczywistym do aplikacji za pośrednictwem protokołu HTTP. Ta funkcja w czasie rzeczywistym umożliwia usłudze wypychanie aktualizacji zawartości do połączonych klientów, takich jak pojedyncza strona internetowa lub aplikacja mobilna. W związku z tym klienci otrzymują aktualizacje bez konieczności sondowania serwera czy przesyłania nowych żądań HTTP o aktualizacje.


Ten artykuł zawiera omówienie usługi Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Do czego służy usługa Azure SignalR Service?

Każdy scenariusz, który wymaga wypychania danych z serwera do klienta w czasie rzeczywistym, można użyć usługi Azure SignalR.

Tradycyjne funkcje w czasie rzeczywistym, które często wymagają sondowania z serwera, można również użyć usługi Azure SignalR.

Usługa Azure SignalR service została wykorzystana w wielu różnych branżach, dla każdego typu aplikacji, która wymaga aktualizacji zawartości w czasie rzeczywistym. Wymieniamy kilka przykładów, które są dobre do korzystania z usługi Azure SignalR:

* **Aktualizacje danych o wysokiej częstotliwości:** gry, głosowanie, sondowanie, aukcja.
* **Pulpity nawigacyjne i monitoring:** panel firmy, dane z rynku finansowego, natychmiastowa aktualizacja sprzedaży, tablica liderów gier dla wielu graczy i monitorowanie IoT.
* **Czat:** czat na żywo, czat bot, obsługa klienta on-line, asystent zakupów w czasie rzeczywistym, komunikator, czat w grze i tak dalej.
* **Lokalizacja w czasie rzeczywistym na mapie:** śledzenie logistyczne, śledzenie stanu dostawy, aktualizacje statusu transportu, aplikacje GPS.
* **Reklamy kierowane w czasie rzeczywistym:** spersonalizowane reklamy i oferty push w czasie rzeczywistym, reklamy interaktywne.
* **Aplikacje do współpracy:** współtworzenie, aplikacje tablicy i oprogramowanie do spotkań zespołowych.
* **Powiadomienia push:** sieć społecznościowa, e-mail, gra, alert podróży.
* **Transmisje w czasie rzeczywistym:** transmisja audio/wideo na żywo, podpisy na żywo, tłumaczenie, wydarzenia / transmisja wiadomości.
* **IoT i podłączone urządzenia:** metryki IoT w czasie rzeczywistym, zdalne sterowanie, stan w czasie rzeczywistym i śledzenie lokalizacji.
* **Automatyzacja:** wyzwalacz w czasie rzeczywistym z zdarzeń nadrzędnych.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Jakie są korzyści z korzystania z usługi Azure SignalR?

**Oparte na standardzie:**

Usługa SignalR udostępnia abstrakcję szeregu technik służących do tworzenia aplikacji internetowych działających w czasie rzeczywistym. [Protokół WebSockets](https://wikipedia.org/wiki/WebSocket) stanowi optymalną metodę transportu, ale inne techniki, takie jak [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) i Long Polling są używane, gdy nie są dostępne inne opcje. Biblioteka SignalR automatycznie wykrywa i inicjuje odpowiednią metodę transportu na podstawie funkcji obsługiwanych przez serwer i klienta.

**Natywna obsługa ASP.NET Core:**

SignalR Service zapewnia natywne doświadczenie programowania z ASP.NET Core i ASP.NET. Opracowanie nowej aplikacji SignalR z usługą SignalR lub migracja z istniejącej aplikacji opartej na SignalR do usługi SignalR wymaga minimalnych nakładów.
SignalR Service obsługuje również nową funkcję ASP.NET Core, Blazor po stronie serwera.

**Szeroka obsługa klienta:**

SignalR Service współpracuje z szeroką gamą klientów, takich jak przeglądarki internetowe i mobilne, aplikacje klasyczne, aplikacje mobilne, proces serwera, urządzenia IoT i konsole do gier. SignalR Service oferuje SDK w różnych językach. Oprócz natywnych ASP.NET Core lub ASP.NET C# SDK, SignalR Service zapewnia również javascript klienta SDK, aby włączyć klientów sieci web i wiele platform JavaScript. SDK klienta Java jest również obsługiwany dla aplikacji Java, w tym aplikacji natywnych dla systemu Android. Usługa SignalR obsługuje interfejs API REST i bezserwerowe za pośrednictwem integracji z usługami Azure Functions i Event Grid.

**Obsługa połączeń klientów na dużą skalę:**

SignalR Service jest przeznaczony do zastosowań w czasie rzeczywistym na dużą skalę. SignalR Service umożliwia wiele wystąpień do współpracy w celu skalowania do milionów połączeń klienta. Usługa obsługuje również wiele regionów globalnych do dzielenia na fragmenty, wysokiej dostępności lub odzyskiwania po awarii.

**Usuń obciążenie dla samodzielnego hosta SignalR:**

W porównaniu do samodzielnych aplikacji SignalR, przełączenie na usługę SignalR usunie konieczność zarządzania tylnymi płaszczyznami obsługującymi skale i połączenia klientów. W pełni zarządzana usługa upraszcza również aplikacje internetowe i oszczędza koszty hostingu. Usługa SignalR service oferuje globalny zasięg i światowej klasy centrum danych i sieć, skaluje się do milionów połączeń, gwarantuje umowy SLA, zapewniając jednocześnie wszystkie zgodność i bezpieczeństwo w standardzie platformy Azure.

![Zarządzana usługa signalr](./media/signalr-overview/managed-signalr-service.png)

**Zaoferuj zaawansowane interfejsy API dla różnych wzorców obsługi wiadomości:**

SignalR Service umożliwia serwerowi wysyłanie wiadomości do określonego połączenia, wszystkich połączeń lub podzbioru połączeń, które należą do określonego użytkownika lub zostały umieszczone w dowolnej grupie.

## <a name="how-to-use-azure-signalr-service"></a>Jak używać usługi Azure SignalR Service

Istnieje wiele różnych sposobów programowania za pomocą usługi Azure SignalR Service, ponieważ niektóre przykłady wymienione w tym miejscu:

- **[Skalowanie aplikacji SignalR platformy ASP.NET Core](signalr-concept-scale-aspnet-core.md)** — integracja usługi Azure SignalR Service z aplikacją SignalR platformy ASP.NET Core w celu skalowania do setek tysięcy połączeń.
- **[Tworzenie bezserwerowych aplikacji w czasie rzeczywistym](signalr-concept-azure-functions.md)** — wykorzystanie integracji usługi Azure Functions z usługą Azure SignalR Service do tworzenia bezserwerowych aplikacji w czasie rzeczywistym w językach takich jak JavaScript, C# i Java.
- **[Wysyłanie komunikatów z serwera do klientów za pośrednictwem interfejsu API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** — usługa Azure SignalR Service udostępnia interfejs API REST, aby umożliwić aplikacjom wysyłanie komunikatów do połączonych klientów przy użyciu usługi SignalR Service, w dowolnym języku programowania obsługiwanym przez REST.
