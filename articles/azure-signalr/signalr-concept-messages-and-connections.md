---
title: Komunikaty i połączenia w usłudze Azure SignalR Service
description: Omówienie kluczowych założeń dotyczących komunikatów i połączeń w usłudze Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401206"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Komunikaty i połączenia w usłudze Azure SignalR Service

Model rozliczania usługi Azure SignalR Service jest oparty na liczbie połączeń i liczbie komunikatów. W tym artykule wyjaśniono, w jaki sposób komunikaty i połączenia są definiowane i zliczane w celach rozliczeniowych.


## <a name="message-formats"></a>Formaty komunikatów 

Usługa Azure SignalR Service obsługuje te same formaty, które obsługuje biblioteka SignalR platformy ASP.NET Core: [JSON](https://www.json.org/) i [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Rozmiar komunikatu

Usługa Azure SignalR Service nie ma limitu rozmiaru komunikatu.

Duże komunikaty są dzielone na mniejsze, o rozmiarze nie większym niż 2 KB, które są przekazywane oddzielnie. Dzielenie i składanie komunikatów jest obsługiwane przez zestawy SDK. Nie wymaga to żadnych prac deweloperskich.

Duże komunikaty negatywnie wpływają na wydajność przesyłania komunikatów. Zawsze, gdy jest to możliwe, używaj mniejszego rozmiaru komunikatu i dla każdego scenariusza przypadku użycia przetestuj, jaki jest dla niego optymalny rozmiar komunikatu.

## <a name="how-messages-are-counted-for-billing"></a>Jak komunikaty są liczone na potrzeby rozliczeń

Na potrzeby rozliczeń są uwzględniane tylko komunikaty wychodzące usługi Azure SignalR Service. Komunikaty ping między klientami a serwerami są ignorowane.

Komunikaty o rozmiarze przekraczającym 2 KB są liczone jako większa liczba komunikatów, z których każdy ma rozmiar 2 KB. Wykres liczby komunikatów w witrynie Azure Portal jest aktualizowany co 100 komunikatów dla każdego centrum.

Na przykład możesz mieć trzech klientów i jeden serwer aplikacji. Jeden klient wysyła komunikat o rozmiarze 4 KB, a serwer rozgłasza go do wszystkich klientów. Liczba komunikatów to 8: jeden od usługi do serwera aplikacji i trzy od usługi do klientów. Każdy komunikat jest liczony jako dwa komunikaty o rozmiarze 2 KB.

Liczba komunikatów wyświetlana w witrynie Azure Portal ma jednak wartość 0 aż do chwili, gdy przekroczy ona wartość 100.

## <a name="how-connections-are-counted"></a>Jak liczone są połączenia

Wyróżniamy połączenia z serwerem i połączenia klienckie. Domyślnie każdy serwer aplikacji ma pięć połączeń dla centrum w usłudze Azure SignalR Service, a każdy klient ma jedno połączenie klienta w usłudze Azure SignalR Service.

Liczba połączeń wyświetlana w witrynie Azure Portal obejmuje zarówno połączenia z serwerem, jak i połączenia klienta.

Na przykład załóżmy, że masz dwa serwery aplikacji i definiujesz w kodzie pięć centrów. Liczba połączeń serwera wynosi 50: 2 serwery aplikacji * 5 centrów * 5 połączeń dla centrum.

Biblioteka SignalR platformy ASP.NET inaczej oblicza liczbę połączeń serwera. Uwzględnia ona jedno centrum domyślne w dodatku do zdefiniowanych centrów. Każdy serwer aplikacji potrzebuje domyślnie jeszcze 5 połączeń serwera. Ta liczba połączeń dla centrum domyślnego jest spójna z liczbą połączeń innych centrów.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak jest liczony ruch przychodzący/wychodzący

Rozróżnienie między ruchem przychodzącym a wychodzącym jest oparte na perspektywie usługi Azure SignalR Service. Ruch jest obliczany w bajtach. Tak jak w przypadku liczby komunikatów, ruch również ma swoją częstotliwość próbkowania. Wykres ruchu przychodzącego/wychodzącego w witrynie Azure Portal jest uaktualniany co 100 KB dla centrum.

## <a name="related-resources"></a>Powiązane zasoby

- [Aggregation types in Azure Monitor (Typy agregacji w usłudze Azure Monitor)](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Konfiguracja biblioteki SignalR platformy ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)