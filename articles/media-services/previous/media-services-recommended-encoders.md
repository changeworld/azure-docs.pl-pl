---
title: Informacje o koderach zalecanych przez Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera listę koderów lokalnych zalecanych przez Azure Media Services.
services: media-services
keywords: encoding;encoders;media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4a0af9d040c801c125d04a5af72b2ea53322ccdb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74886575"
---
# <a name="recommended-on-premises-encoders"></a>Zalecane kodery lokalne
W przypadku przesyłania strumieniowego na żywo z Azure Media Services można określić, w jaki sposób kanał ma odbierać strumień wejściowy. Jeśli zdecydujesz się użyć kodera lokalnego z kanałem kodowania na żywo, koder powinien wypchnąć strumień o pojedynczej szybkości transmisji bitów o wysokiej jakości jako dane wyjściowe. Jeśli zdecydujesz się użyć kodera lokalnego z przekazaniem kanału, koder powinien wypchnąć strumień o różnej szybkości transmisji bitów jako dane wyjściowe ze wszystkimi żądanymi jakością wyjściową. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe na żywo z użyciem koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services zaleca użycie jednego z następujących koderów na żywo, które mają RTMP jako dane wyjściowe:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Azure Media Services zaleca się użycie jednego z następujących koderów na żywo, które mają wieloszybkościową fragmentację plików MP4 (Smooth Streaming) jako dane wyjściowe:
- Media Excel Hero Live i Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Wyobraź sobie komunikację Selenio MCP3

> [!NOTE]
> Koder na żywo może wysyłać strumień o pojedynczej szybkości transmisji bitów do kanału przekazującego, ale ta konfiguracja nie jest zalecana, ponieważ nie zezwala na przesyłanie strumieniowe transmisji bitów do klienta.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak przełączyć się do lokalnego partnera kodera
Jako Azure Media Services lokalnego partnera kodera Media Services promować produkt, zalecając koder do klientów korporacyjnych. Aby stać się lokalnym partnerem kodera, należy sprawdzić zgodność kodera lokalnego z Media Services. Aby to zrobić, wykonaj następujące weryfikacje:

Weryfikacja kanału przekazującego
1. Utwórz konto Azure Media Services lub przejdź do niego
2. Utwórz i uruchom kanał **Pass-through**
3. Skonfiguruj koder, aby wypchnąć strumień na żywo o pojedynczej szybkości transmisji bitów.
4. Tworzenie opublikowanego zdarzenia na żywo
5. Uruchamianie kodera na żywo przez około 10 minut
6. Zatrzymaj wydarzenie na żywo
7. Utwórz, uruchom punkt końcowy przesyłania strumieniowego, użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obejrzeć zarchiwizowany element zawartości, aby upewnić się, że odtwarzanie nie ma widocznych błędów dla wszystkich poziomów jakości (lub alternatywnie Obejrzyj i sprawdź poprawność przez adres URL wersji zapoznawczej przed krokiem 6)
8. Rejestrowanie identyfikatora zasobu, opublikowanego adresu URL przesyłania strumieniowego dla archiwum na żywo oraz ustawień i wersji używanych z kodera na żywo
9. Zresetuj stan kanału po utworzeniu każdego przykładu
10. Powtórz kroki od 3 do 9 dla wszystkich konfiguracji obsługiwanych przez koder (z niemniemi/napisami/podpisami lub innymi szybkościami kodowania)

Weryfikacja kanału kodowania na żywo
1. Utwórz konto Azure Media Services lub przejdź do niego
2. Utwórz i uruchom kanał **kodowania na żywo**
3. Skonfiguruj koder, aby wypchnąć strumień na żywo o pojedynczej szybkości transmisji bitów.
4. Tworzenie opublikowanego zdarzenia na żywo
5. Uruchamianie kodera na żywo przez około 10 minut
6. Zatrzymaj wydarzenie na żywo
7. Utwórz, uruchom punkt końcowy przesyłania strumieniowego, użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obejrzeć zarchiwizowany element zawartości, aby upewnić się, że odtwarzanie nie ma widocznych błędów dla wszystkich poziomów jakości (lub alternatywnie Obejrzyj i sprawdź poprawność przez adres URL wersji zapoznawczej przed krokiem 6)
8. Rejestrowanie identyfikatora zasobu, opublikowanego adresu URL przesyłania strumieniowego dla archiwum na żywo oraz ustawień i wersji używanych z kodera na żywo
9. Zresetuj stan kanału po utworzeniu każdego przykładu
10. Powtórz kroki od 3 do 9 w przypadku wszystkich konfiguracji obsługiwanych przez koder (z i bez sygnałów/napisów/podpisów AD/różnych szybkości kodowania)

Weryfikacja eksploatacji
1. Utwórz konto Azure Media Services lub przejdź do niego
2. Utwórz i uruchom kanał **Pass-through**
3. Skonfiguruj koder, aby wypchnąć strumień na żywo o pojedynczej szybkości transmisji bitów.
4. Tworzenie opublikowanego zdarzenia na żywo
5. Uruchamianie kodera na żywo przez jeden tydzień lub dłużej
6. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obserwować przesyłanie strumieniowe na żywo z czasu do czasu (lub zarchiwizowanego elementu zawartości), aby upewnić się, że odtwarzanie nie ma widocznych błędów
7. Zatrzymaj wydarzenie na żywo
8. Rejestrowanie identyfikatora zasobu, opublikowanego adresu URL przesyłania strumieniowego dla archiwum na żywo oraz ustawień i wersji używanych z kodera na żywo

Na koniec Wyślij zapisane ustawienia i parametry Archiwum na żywo, aby Media Services pocztą e-mail amsstreaming@microsoft.com. Po otrzymaniu Media Services wykonuje testy weryfikacyjne na przykładach z kodera na żywo. Możesz skontaktować się z Media Servicesą z pytaniami dotyczącymi tego procesu.
