---
title: "Więcej informacji na temat koderów zalecane przez usługi Azure Media Services | Dokumentacja firmy Microsoft"
description: "Więcej informacji na temat koderów zalecane przez usługę media services"
services: media-services
keywords: "kodowanie koderów; nośnika"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Koderów lokalnych zalecane
Gdy na żywo strumienia z usługi Azure Media Services można określić sposób kanału do odbierania strumień wejściowy. Jeśli użytkownik chce używać kodera lokalnego z kanałem kodowania na żywo, kodera ma wypychać strumienia wysokiej jakości pojedynczej szybkości transmisji bitów jako dane wyjściowe. Jeśli wybierzesz kodera lokalnego za pomocą przekazywania kanału, kodera ma wypychać strumień o wielokrotnej szybkości transmisji bitów jako dane wyjściowe z wszystkich właściwości żądanego wyniku. Aby uzyskać więcej informacji, zobacz [Live streaming za pomocą koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

Usługa Azure Media Services zaleca przy użyciu jednej z następujących koderów na żywo, które mają RTMP jako dane wyjściowe:
- Adobe Flash Media Encoder 3.2 na żywo
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Wycinek Teradek 756
- TriCaster 8000
- Tricaster Mini HD-4

Usługa Azure Media Services zaleca przy użyciu jednej z następujących koderów na żywo, które mają wielokrotnej szybkości transmisji bitów Smooth Streaming jako dane wyjściowe:
- Ateme TITAN na żywo
- Koder nośnika cyfrowego Cisco 2200
- Elemental na żywo
- Envivio 4Caster C4 Gen III
- Wyobraź sobie Selenio MCP3 komunikacji
- Bohater Excel Media na żywo

> [!NOTE]
> Koder na żywo może wysyłać o pojedynczej szybkości transmisji bitów do kanału przekazywania, ale ta konfiguracja nie jest zalecane, ponieważ nie zezwala na adaptacyjną szybkością transmisji bitów przesyłania strumieniowego do klienta.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Jak tworzyć partnera kodera lokalnego
Jako partner kodera lokalnej usługi Azure Media Services usługi Media Services zamienia produktu zalecając kodera klientom przedsiębiorstwa. Jako partner kodera lokalnego, należy sprawdzić zgodność kodera lokalnego z usługi Media Services. Aby to zrobić, wykonaj następujące weryfikacji:

Przekazuj weryfikacji kanału
1. Utwórz lub odwiedź konta usługi Azure Media Services
2. Tworzenie i uruchamianie **przekazywanego** kanału
3. Skonfiguruj kodera do wypychania strumienia na żywo różnych szybkościach transmisji bitów.
4. Utwórz opublikowanych wydarzenia na żywo
5. Uruchom kodera na żywo na około 10 minut.
6. Zatrzymaj wydarzenie na żywo
7. Zapisz identyfikator zasobu opublikowane adresu URL przesyłania strumieniowego na żywo archiwum i ustawienia oraz wersja użyta z kodera na żywo
8. Zresetuj stan kanału po utworzeniu każda próbka
9. Powtórz kroki od 3 do 8 dla wszystkich konfiguracji obsługiwanych przez kodera (z lub bez ad sygnalizowania/podpisy/różne kodowania szybkości)

Weryfikacja kanału kodowania na żywo
1. Utwórz lub odwiedź konta usługi Azure Media Services
2. Tworzenie i uruchamianie **kodowanie na żywo** kanału
3. Skonfiguruj kodera wypychanej strumień na żywo o pojedynczej szybkości transmisji bitów.
4. Utwórz opublikowanych wydarzenia na żywo
5. Uruchom kodera na żywo na około 10 minut.
6. Zatrzymaj wydarzenie na żywo
7. Zapisz identyfikator zasobu opublikowane adresu URL przesyłania strumieniowego na żywo archiwum i ustawienia oraz wersja użyta z kodera na żywo
8. Zresetuj stan kanału po utworzeniu każda próbka
9. Powtórz kroki od 3 do 8 dla wszystkich konfiguracji obsługiwanych przez kodera (z lub bez ad sygnalizowania/podpisy/różnych szybkościach kodowanie)

Trwałość weryfikacji
1. Utwórz lub odwiedź konta usługi Azure Media Services
2. Tworzenie i uruchamianie **przekazywanego** kanału
3. Skonfiguruj kodera do wypychania strumienia na żywo różnych szybkościach transmisji bitów.
4. Utwórz opublikowanych wydarzenia na żywo
5. Uruchom kodera na żywo, co tydzień lub dłużej
6. Zatrzymaj wydarzenie na żywo
7. Zapisz identyfikator zasobu opublikowane adresu URL przesyłania strumieniowego na żywo archiwum i ustawienia oraz wersja użyta z kodera na żywo

Ponadto wysyłania ustawienia zarejestrowane i na żywo parametry archiwum do usługi Media Services przez wysyłanie amsstreaming@microsoft.com. Po otrzymaniu Media Services wykonuje testy weryfikacji próbki z kodera na żywo. Możesz skontaktować się z usługi Media Services z pytania dotyczące tego procesu.