---
title: Komunikaty i połączenia w usłudze Azure SignalR
description: Przegląd kluczowych założeń dotyczących komunikatów i połączeń w usłudze Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352601"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Komunikat i połączenie w usłudze Azure SignalR Service

Usługa Azure SignalR Service ma model rozliczania oparty o liczbę połączeń i liczbę komunikatów. Poniżej wyjaśniono, w jaki sposób komunikaty i połączenia są definiowane i zliczane w celach rozliczeniowych.

## <a name="message-formats-supported"></a>Obsługiwane formaty komunikatów

Usługa Azure SignalR Service obsługuje te same formaty, które obsługuje biblioteka SignalR platformy ASP.NET Core: [JSON](https://www.json.org/) i [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Rozmiar komunikatu

Usługa Azure SignalR Service nie ma limitu rozmiaru komunikatu.

W praktyce duży komunikat jest dzielony na mniejsze, o rozmiarze nie większym niż 2 KB, i przekazywany jako oddzielne komunikaty. Dzielenie i składanie komunikatów jest obsługiwane przez zestawy SDK. Nie wymaga to żadnych prac deweloperskich.

Jednak duży komunikat ma negatywny wpływ na wydajność przesyłania wiadomości. Zawsze, gdy jest to możliwe, używaj mniejszego rozmiaru komunikatu i dla każdego scenariusza przypadków użycia przetestuj, jaki jest dla niego optymalny rozmiar komunikatu.

## <a name="how-to-count-messages-for-billing-purpose"></a>Jak należy liczyć komunikaty w celach rozliczeniowych?

Zliczamy tylko komunikaty wychodzące z usługi SignalR, ignorując komunikaty ping przesyłane między klientami a serwerami.

Komunikat o rozmiarze przekraczającym 2 KB jest liczony jako większa liczba komunikatów, z których każdy ma rozmiar 2 KB. Wykres liczby komunikatów w witrynie Azure Portal jest aktualizowany co 100 komunikatów dla każdego centrum.

Masz na przykład trzech klientów i jeden serwer aplikacji. Jeden klient wysyła jeden komunikat o rozmiarze 4 KB, a serwer rozgłasza go do wszystkich klientów. Liczba komunikatów wynosi 8: jeden komunikat z usługi do serwera aplikacji i trzy komunikaty z usługi do klientów, przy czym każdy komunikat jest liczony jako dwa komunikaty o rozmiarze 2 KB.

Liczba komunikatów wyświetlana w witrynie Azure Portal ma jednak wartość 0 aż do chwili, gdy przekroczy ona wartość 100.

## <a name="how-to-count-connections"></a>Jak należy liczyć połączenia?

Wyróżniamy połączenia z serwerem i połączenia klienckie. Domyślnie każdy serwer aplikacji ma pięć połączeń dla każdego centrum w usłudze SignalR Service, a każdy klient ma jedno połączenie klienckie w usłudze SignalR Service.

Liczba połączeń wyświetlana w witrynie Azure Portal obejmuje zarówno połączenia z serwerem, jak i połączenia klienckie.

Na przykład masz dwa serwery aplikacji i definiujesz w kodzie pięć centrów. Liczba połączeń serwera wynosi 50: 2 serwery aplikacji * 5 centrów * 5 połączeń/centrum.

Biblioteka SignalR platformy ASP.NET inaczej oblicza liczbę połączeń serwera. Oprócz centrów zdefiniowanych przez klienta ma ona jedno domyślne centrum. Każdy serwer aplikacji potrzebuje domyślnie jeszcze 5 połączeń serwera. Ta liczba połączeń dla centrum domyślnego jest spójna z innymi centrami.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Jak obliczyć ruch przychodzący/wychodzący

Ruch przychodzący/wychodzący jest obliczany z perspektywy usługi SignalR Service. Ruch jest obliczany w bajtach. Tak jak w przypadku liczby komunikatów ruch również ma swoją częstotliwość próbkowania. Wykres ruchu przychodzącego/wychodzącego w witrynie Azure Portal jest uaktualniany co 100 KB na centrum.

## <a name="related-resources"></a>Powiązane zasoby

- [Aggregation type in Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr ) (Typ agregacji w usłudze Azure Monitor)
- [Konfiguracja biblioteki SignalR platformy ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)