---
title: Komunikaty i połączenia w usłudze Azure SignalR Service
description: Omówienie kluczowych założeń dotyczących komunikatów i połączeń w usłudze Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75392811"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Komunikaty i połączenia w usłudze Azure SignalR Service

Model rozliczania usługi Azure SignalR Service jest oparty na liczbie połączeń i liczbie komunikatów. W tym artykule wyjaśniono, w jaki sposób komunikaty i połączenia są definiowane i zliczane w celach rozliczeniowych.


## <a name="message-formats"></a>Formaty komunikatów 

Usługa Azure SignalR obsługuje te same formaty co ASP.NET Core SignalR: [JSON](https://www.json.org/) i [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Rozmiar komunikatu

Usługa Azure SignalR Service nie ma limitu rozmiaru komunikatu.

Duże komunikaty są dzielone na mniejsze, o rozmiarze nie większym niż 2 KB, które są przekazywane oddzielnie. Dzielenie i składanie komunikatów jest obsługiwane przez zestawy SDK. Nie wymaga to żadnych prac deweloperskich.

Duże komunikaty negatywnie wpływają na wydajność przesyłania komunikatów. Zawsze, gdy jest to możliwe, używaj mniejszego rozmiaru komunikatu i dla każdego scenariusza przypadku użycia przetestuj, jaki jest dla niego optymalny rozmiar komunikatu.

## <a name="how-messages-are-counted-for-billing"></a>Jak komunikaty są liczone na potrzeby rozliczeń

Na potrzeby rozliczeń są uwzględniane tylko komunikaty wychodzące usługi Azure SignalR Service. Komunikaty ping między klientami a serwerami są ignorowane.

Komunikaty o rozmiarze przekraczającym 2 KB są liczone jako większa liczba komunikatów, z których każdy ma rozmiar 2 KB. Wykres liczby komunikatów w witrynie Azure Portal jest aktualizowany co 100 komunikatów dla każdego centrum.

Na przykład możesz mieć trzech klientów i jeden serwer aplikacji. Jeden klient wysyła komunikat o rozmiarze 4 KB, a serwer rozgłasza go do wszystkich klientów. Liczba komunikatów to 8: jeden od usługi do serwera aplikacji i trzy od usługi do klientów. Każdy komunikat jest liczony jako dwa komunikaty o rozmiarze 2 KB.

## <a name="how-connections-are-counted"></a>Jak liczone są połączenia

Istnieją połączenia z serwerem i połączenia klientów z usługą Azure SignalR. Domyślnie każdy serwer aplikacji rozpoczyna się od pięciu początkowych połączeń na koncentrator, a każdy klient ma jedno połączenie klienta.

Liczba połączeń wyświetlana w witrynie Azure Portal obejmuje zarówno połączenia z serwerem, jak i połączenia klienta.

Na przykład załóżmy, że masz dwa serwery aplikacji i definiujesz w kodzie pięć centrów. Liczba połączeń z serwerem będzie wynosić 50: 2 serwery aplikacji * 5 koncentratorów * 5 połączeń na koncentrator.

Biblioteka SignalR platformy ASP.NET inaczej oblicza liczbę połączeń serwera. Uwzględnia ona jedno centrum domyślne w dodatku do zdefiniowanych centrów. Domyślnie każdy serwer aplikacji potrzebuje jeszcze pięciu początkowych połączeń z serwerem. Początkowa liczba połączeń dla koncentratora domyślnego pozostaje zgodna z liczbą innych koncentratorów.

W okresie istnienia serwera aplikacji usługa i serwer aplikacji zachowują stan połączenia synchronizacji i dokonują dostosowania połączeń z serwerem w celu uzyskania lepszej wydajności i stabilności usług. Możesz więc od czasu do czasu widzieć zmiany numeru połączenia serwera.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak jest liczony ruch przychodzący/wychodzący

Rozróżnienie między ruchem przychodzącym a wychodzącym jest oparte na perspektywie usługi Azure SignalR Service. Ruch jest obliczany w bajtach.

## <a name="related-resources"></a>Powiązane zasoby

- [Aggregation types in Azure Monitor (Typy agregacji w usłudze Azure Monitor)](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Konfiguracja biblioteki SignalR platformy ASP.NET Core](/aspnet/core/signalr/configuration)
- [Json](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
