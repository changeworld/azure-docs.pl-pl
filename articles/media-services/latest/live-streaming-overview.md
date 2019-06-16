---
title: Omówienie przesyłania strumieniowego za pomocą usługi Azure Media Services v3 na żywo | Dokumentacja firmy Microsoft
description: Dzięki temu artykuł z omówieniem transmisja strumieniowa na żywo za pomocą usługi Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: fa09185e68c8d3a70562fe50c583ff872bf91e48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65556223"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe przy użyciu usługi Azure Media Services v3 na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby przesyłać strumieniowo zdarzenia na żywo za pomocą usługi Media Services, potrzebne są następujące elementy:  

- Kamera, która jest używana do przechwytywania zdarzeń na żywo.<br/>Zapoznać się z pomysłami instalacji, zapoznaj się z [konfiguracja sprzętu wideo zdarzenia przenośne i proste]( https://link.medium.com/KNTtiN6IeT).

    Jeśli nie masz dostępu do aparatu fotograficznego, narzędzia takie jak [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) może służyć Generowanie Kanał informacyjny na żywo na podstawie pliku wideo.
- Koder wideo na żywo, który konwertuje sygnały z kamery (lub innego urządzenia, takie jak laptop) na udział źródła danych są wysyłane do usługi Media Services. Kanał informacyjny udział może obejmować sygnały związane z reklamy, takie jak znaczniki SCTE 35.<br/>Aby uzyskać listę zalecanych koderów transmisji strumieniowej na żywo, zobacz [transmisja strumieniowa koderów na żywo](recommended-on-premises-live-encoders.md). Ponadto zapoznaj się z tym blogu: [Na żywo przesyłania strumieniowego w środowisku produkcyjnym za pomocą systemu bankowości Internetowej](https://link.medium.com/ttuwHpaJeT).
- Składniki w usłudze Media Services pozwalają pozyskiwać, w wersji zapoznawczej, pakiet, rejestrowanie, szyfrowania i emisję wydarzenia na żywo dla klientów lub do sieci CDN w celu dalszej dystrybucji.

Ten artykuł zawiera omówienie i wskazówki dotyczące transmisji strumieniowych na żywo za pomocą usługi Media Services i linki do innych odpowiednich artykułów.

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Za pomocą usługi Media Services, możesz korzystać z zalet [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md), co pozwala na przeglądanie i emisji strumieni na żywo w [formatów MPEG DASH, HLS i Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z udziału kanału informacyjnego który jest wysyłany do usługi. Przeglądającym można odtwarzać transmisji strumieniowej na żywo za pomocą dowolnego odtwarzaczy zgodne HLS, DASH lub Smooth Streaming. Możesz użyć [usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) w sieci web lub aplikacji mobilnych, aby dostarczać strumień we wszystkich tych protokołów.

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Szyfrowanie dynamiczne umożliwia dynamiczne szyfrowanie zawartości na żywo lub na żądanie przy użyciu algorytmu AES-128, ani żadnego z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. Aby uzyskać więcej informacji, zobacz [szyfrowania dynamicznego](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dynamiczne manifestu

Filtrowanie dynamiczne służy do kontrolowania liczby ścieżek, formatów, szybkości transmisji i prezentacji okna czasowe, które są wysłane do odtwarzaczy. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Typy zdarzeń na żywo

Wydarzenie na żywo może być jednym z dwóch typów: kodowanie przekazywania i na żywo. Aby uzyskać szczegółowe informacje o transmisji strumieniowej na żywo w wersji 3 usługa Media Services, zobacz [zdarzenia na żywo i na żywo dane wyjściowe](live-events-outputs-concept.md).

### <a name="pass-through"></a>Przekazywanie

![Przekazywanie](./media/live-streaming/pass-through.svg)

Kiedy używasz przekazywanego **wydarzenia na żywo**, oczekujesz, że Twój lokalny koder na żywo wygeneruje strumień wideo z wieloma szybkościami transmisji bitów i wyśle go jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo przekazuje następnie przychodzące strumienie wideo bez dalszego przetwarzania. Takie przekazywanego wydarzenie na żywo jest zoptymalizowany do wydarzeń na żywo długotrwałych lub 24 x 365 liniowej transmisja strumieniowa na żywo. 

### <a name="live-encoding"></a>Kodowanie na żywo  

![Kodowanie na żywo](./media/live-streaming/live-encoding.svg)

W przypadku korzystania z kodowania na żywo za pomocą usługi Media Services należy skonfigurować lokalny koder na żywo, aby wysłać klip wideo z jedną szybkością transmisji bitów jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo koduje ten strumień z jedną szybkością transmisji bitów do [strumienia z wieloma szybkościami transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), udostępnia go do dostarczenia do urządzeń odtwarzania za pomocą protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Przepływ pracy transmisji strumieniowej na żywo

Aby zrozumieć przepływ pracy transmisji strumieniowej na żywo w wersji 3 usługa Media Services, musisz pierwszy przegląd i zrozumieć następujące pojęcia: 

- [Punkty końcowe interfejsu API przesyłania strumieniowego](streaming-endpoint-concept.md)
- [Aktywne zdarzenia i dane wyjściowe na żywo interfejsu API](live-events-outputs-concept.md)
- [Interfejs API Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)

### <a name="general-steps"></a>Ogólne kroki

1. Upewnij się, w ramach konta usługi Media Services **punkt końcowy przesyłania strumieniowego** (źródło) jest uruchomiona. 
2. Tworzenie [wydarzenie na żywo](live-events-outputs-concept.md). <br/>Podczas tworzenia zdarzenia, można określić automatyczne uruchamianie go. Alternatywnie możesz rozpocząć zdarzenie, gdy jesteś gotowy rozpocząć przesyłanie strumieniowe.<br/> Gdy autostart jest ustawiona na wartość true, wydarzenie na żywo zostanie uruchomiony prawo po utworzeniu. Naliczanie opłat rozpoczyna się zaraz po uruchomieniu wydarzenie na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Uzyskaj adresy URL pozyskiwania i skonfiguruj swoje lokalny koder wysyłać wkład źródła danych przy użyciu adresu URL.<br/>Zobacz [zalecane kodery na żywo](recommended-on-premises-live-encoders.md).
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.<br/>**Na żywo dane wyjściowe** spowoduje zarchiwizowanie strumienia do **zasobów**.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.<br/>Jeśli zamierzasz szyfrowanie zawartości, zapoznaj się z [Omówienie ochrony zawartości](content-protection-overview.md).
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL, aby użyć (są to deterministyczne).
9. Pobieranie nazwy hosta dla **punkt końcowy przesyłania strumieniowego** (źródło) chcesz przesyłać strumieniowo z.
10. Adres URL w kroku 8 należy połączyć z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Jeśli chcesz zatrzymać, dzięki czemu Twoje **wydarzenie na żywo** widoczne, należy zatrzymać przesyłanie strumieniowe zdarzeń i Usuń **lokalizatora przesyłania strumieniowego**.

## <a name="other-important-articles"></a>Inne artykuły, ważne

- [Zalecane kodery na żywo](recommended-on-premises-live-encoders.md)
- [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
- [Porównanie funkcji na żywo typy zdarzeń](live-event-types-comparison.md)
- [Stany i rozliczenia](live-event-states-billing.md)
- [Opóźnienie](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
* [Wskazówki dotyczące migracji do przenoszenia z usługi Media Services v2 do v3](migrate-from-v2-to-v3.md)
