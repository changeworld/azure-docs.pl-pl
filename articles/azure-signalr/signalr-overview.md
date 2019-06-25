---
title: Co to jest usługa Azure SignalR Service?
description: Omówienie usługi Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: dc7ba3585ec49921c0a0e66185fc5550d3d4a006
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303614"
---
# <a name="what-is-azure-signalr-service"></a>Co to jest usługa Azure SignalR Service?

Usługa Azure SignalR Service upraszcza proces dodawania funkcji internetowych w czasie rzeczywistym do aplikacji za pośrednictwem protokołu HTTP. Ta funkcja w czasie rzeczywistym umożliwia usłudze wypychanie aktualizacji zawartości do połączonych klientów, takich jak pojedyncza strona internetowa lub aplikacja mobilna. W związku z tym klienci otrzymują aktualizacje bez konieczności sondowania serwera czy przesyłania nowych żądań HTTP o aktualizacje.


Ten artykuł zawiera omówienie usługi Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Do czego służy usługa Azure SignalR Service?

Wszystkich scenariuszy, które wymaga wypychanie danych z serwera do klienta w czasie rzeczywistym, można użyć usługi Azure SignalR Service.

Tradycyjne funkcje w czasie rzeczywistym, które często wymagają sondowania z serwera, można również użyć usługi Azure SignalR Service.

Usługi Azure SignalR Service został użyty w wielu różnych branżach, dla dowolnego typu aplikacji, która wymaga aktualizacje zawartości w czasie rzeczywistym. Listę kilka przykładów, które są odpowiednie do użycia usługi Azure SignalR Service:

* **Aktualizacje danych o dużej częstotliwości:** gry, głosowanie, sondowanie, aukcja.
* **Monitorowanie i pulpity nawigacyjne:** firmy pulpit nawigacyjny, dane z rynku finansowego, natychmiastowej aktualizacji sprzedaży, wielu graczy gra rankingu i monitorowania IoT.
* **Rozmowa:** live pokoju rozmów, czatbot, dział obsługi klienta online, w czasie rzeczywistym Asystenta zakupów, messenger, rozmowy w gry i tak dalej.
* **W czasie rzeczywistym lokalizacji na mapie:** logistycznej śledzenie, śledzenie stanu dostarczania, aktualizacje stanu transportu, aplikacje GPS.
* **Czasu rzeczywistego przeznaczone reklamy:** spersonalizowanych reklam wypychania czas odczytu i oferty, reklamy interaktywne.
* **Aplikacje współpracy:** współtworzenia aplikacji tablicy i spotkania oprogramowania zespołu.
* **Powiadomienia wypychane:** sieci społecznościowych, poczty e-mail, gry, alert podróży.
* **W czasie rzeczywistym emisji:** emisji audio/wideo na żywo, na żywo podpisów, przekształcania, emisji zdarzenia/wiadomości.
* **IoT i urządzeń podłączonych:** metryk czasu rzeczywistego IoT, zdalne sterowanie, stan w czasie rzeczywistym i śledzenie lokalizacji.
* **Automatyzacja:** wyzwalacza w czasie rzeczywistym z zdarzenia nadrzędnego.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Jakie są korzyści za pomocą usługi Azure SignalR Service?

**Standard na podstawie:**

Usługa SignalR udostępnia abstrakcję szeregu technik służących do tworzenia aplikacji internetowych działających w czasie rzeczywistym. [Protokół WebSockets](https://wikipedia.org/wiki/WebSocket) stanowi optymalną metodę transportu, ale inne techniki, takie jak [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) i Long Polling są używane, gdy nie są dostępne inne opcje. Biblioteka SignalR automatycznie wykrywa i inicjuje odpowiednią metodę transportu na podstawie funkcji obsługiwanych przez serwer i klienta.

**Macierzysta obsługa platformy ASP.NET Core:**

Usługa SignalR zapewnia natywne środowisko programowania za pomocą platformy ASP.NET Core i ASP.NET. Tworzenie nowej aplikacji SignalR przy użyciu usługi SignalR, czy migrujesz z SignalR istniejących aplikacji opartej na usłudze SignalR wymaga prosty sposób.
Usługa SignalR również obsługuje platformy ASP.NET Core nowa funkcja Blazor po stronie serwera.

**Obsługa szerokiego klientów:**

Usługa SignalR współpracuje z szerokiej gamy klientów, takich jak sieci web i przeglądarek dla urządzeń przenośnych, aplikacje klasyczne, aplikacje mobilne, proces serwera, urządzenia IoT i konsol do gier. Usługa SignalR zawiera zestawy SDK w różnych językach. Oprócz natywnych platformy ASP.NET Core lub ASP.NET C# zestawów SDK, SignalR Service udostępnia również klienta JavaScript SDK umożliwienie klientów w sieci web i wiele struktur języka JavaScript. Zestaw SDK klienta Java jest również obsługiwana dla aplikacji Java, w tym aplikacje natywne dla systemu Android. Usługa SignalR obsługuje interfejs API REST i bez użycia serwera dzięki integracji z usługi Azure Functions i Event Grid.

**Obsługa klientów na dużą skalę połączenia:**

Usługa SignalR jest przeznaczona dla aplikacji w czasie rzeczywistym na dużą skalę. SignalR Service pozwala wielu wystąpieniach, które współpracują ze sobą, skalowanie do milionów połączeń klientów. Usługa obsługuje również wiele regionów globalnych dzielenia na fragmenty, wysokiej dostępności lub celów odzyskiwania po awarii.

**Usuń obciążenia na potrzeby samodzielnego hostowania SignalR:**

W porównaniu do samodzielnie hostowanej aplikacji SignalR, przełączenie na SignalR Service spowoduje usunięcie konieczność zarządzania płaszczyzn Wstecz, obsługujące skaluje się i połączeń klientów. W pełni zarządzana usługa również upraszcza aplikacji sieci web i zapisuje koszty hostingu. Usługa SignalR oferuje globalny zasięg i centrum danych światowej klasy i sieci, może obsłużyć miliony połączeń, gwarancji umowy SLA, przy jednoczesnym zapewnieniu zgodności i zabezpieczeń, zgodnie ze standardowymi platformy Azure.

![Zarządzana usługa SignalR](./media/signalr-overview/managed-signalr-service.png)

**Oferuje zaawansowane interfejsy API dla różne wzorce obsługi komunikatów:**

Usługa SignalR zezwala serwerowi na wysyłanie komunikatów do określonego połączenia, wszystkie połączenia lub podzbiór połączeń, które należą do określonego użytkownika lub znajdować się w dowolnej grupie.

## <a name="how-to-use-azure-signalr-service"></a>Jak używać usługi Azure SignalR Service

Istnieje wiele różnych sposobów program za pomocą usługi Azure SignalR Service jako część próbki wymienione w tym miejscu:

- **[Skalowanie aplikacji SignalR platformy ASP.NET Core](signalr-concept-scale-aspnet-core.md)** — integracja usługi Azure SignalR Service z aplikacją SignalR platformy ASP.NET Core w celu skalowania do setek tysięcy połączeń.
- **[Tworzenie bezserwerowych aplikacji w czasie rzeczywistym](signalr-concept-azure-functions.md)** — wykorzystanie integracji usługi Azure Functions z usługą Azure SignalR Service do tworzenia bezserwerowych aplikacji w czasie rzeczywistym w językach takich jak JavaScript, C# i Java.
- **[Wysyłanie komunikatów z serwera do klientów za pośrednictwem interfejsu API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** — usługa Azure SignalR Service udostępnia interfejs API REST, aby umożliwić aplikacjom wysyłanie komunikatów do połączonych klientów przy użyciu usługi SignalR Service, w dowolnym języku programowania obsługiwanym przez REST.