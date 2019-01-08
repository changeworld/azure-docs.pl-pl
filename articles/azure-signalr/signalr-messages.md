---
title: Komunikaty i połączenia w usłudze Azure SignalR
description: Przegląd kluczowych założeń dotyczących komunikatów i połączeń w usłudze Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812829"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Komunikat i połączenie w usłudze Azure SignalR Service

Usługa Azure SignalR Service ma model rozliczania oparty o liczbę połączeń i liczbę komunikatów. Poniżej wyjaśniono, w jaki sposób komunikaty i połączenia są zdefiniowane i zliczane w celach rozliczeniowych.

## <a name="message-formats-supported"></a>Obsługiwane formaty komunikatów

Usługa Azure SignalR Service obsługuje te same formaty, które obsługuje biblioteka SignalR platformy ASP.NET Core: [JSON](https://www.json.org/) i [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Rozmiar komunikatu

Usługa Azure SignalR Service nie ma limitu rozmiaru komunikatu.

W praktyce duży komunikat jest dzielony na mniejsze, o rozmiarze nie większym niż 2 KB, i przekazywany jako oddzielne komunikaty. Dzielenie i składanie komunikatów jest obsługiwane przez zestawy SDK. Nie wymaga to żadnych prac deweloperskich.

Jednak duży komunikat ma negatywny wpływ na wydajność przesyłania wiadomości. Zawsze, gdy jest to możliwe, używaj mniejszego rozmiaru komunikatu i dla każdego scenariusza przypadków użycia przetestuj, jaki jest dla niego optymalny rozmiar komunikatu.

## <a name="how-to-count-messages-for-billing-purpose"></a>Jak należy liczyć komunikaty w celach rozliczeniowych?

Zliczamy tylko komunikaty wychodzące z usługi SignalR, ignorując komunikaty ping przesyłane między klientami a serwerami.

Komunikat o rozmiarze przekraczającym 2 KB jest liczony jako większa liczba komunikatów, z których każdy ma rozmiar 2 KB. Wykres liczby komunikatów w witrynie Azure Portal jest aktualizowany co 100 komunikatów dla każdego centrum.

Na przykład użytkownik ma 3 klientów i 1 serwer aplikacji. Jeden klient wysyła jeden komunikat o rozmiarze 4 KB, a serwer rozgłasza go do wszystkich klientów. Liczba komunikatów będzie wynosić 8: 1 komunikat z usługi do serwera aplikacji, 3 komunikaty z usługi do klientów, przy czym każdy komunikat jest liczony jako 2 komunikaty o rozmiarze 2 KB.

Liczba komunikatów wyświetlana w witrynie Azure Portal ma jednak wartość 0 aż do chwili, gdy przekroczy ona wartość 100.

## <a name="how-to-count-connections"></a>Jak należy liczyć połączenia?

Wyróżniamy połączenia z serwerem i połączenia klienckie. Domyślnie każdy serwer aplikacji ma 5 połączeń dla każdego centrum przy użyciu usługi SignalR Service, a każdy klient ma 1 połączenie klienckie przy użyciu usługi SignalR Service.

Liczba połączeń wyświetlana w witrynie Azure Portal obejmuje zarówno połączenia z serwerem, jak i połączenia klienckie.

Na przykład użytkownik ma dwa serwery aplikacji i definiuje w kodzie 5 centrów. Liczba połączeń z serwerem wyświetlana w witrynie Azure Portal to 2 serwery aplikacji * 5 centrów * 5 połączeń/centrum = 50 połączeń z serwerem.

## <a name="related-resources"></a>Powiązane zasoby

- [Konfiguracja biblioteki SignalR platformy ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)