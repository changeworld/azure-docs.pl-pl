---
title: Omówienie transmisji strumieniowej na żywo za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: 0f63896239615699db63c9f48be6b291208408db
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247175"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe przy użyciu usługi Azure Media Services v3 na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby przesyłać strumieniowo zdarzenia na żywo za pomocą usługi Media Services, potrzebne są następujące elementy:  

- Kamera, która jest używana do przechwytywania zdarzeń na żywo.<br/>Zapoznać się z pomysłami instalacji, zapoznaj się z [konfiguracja sprzętu wideo zdarzenia przenośne i proste]( https://link.medium.com/KNTtiN6IeT).
- Koder wideo na żywo, który konwertuje sygnały z kamery (lub innego urządzenia, takie jak laptop) na udział źródła danych są wysyłane do usługi Media Services. Kanał informacyjny udział może obejmować sygnały związane z reklamy, takie jak znaczniki SCTE 35.<br/>Aby uzyskać listę zalecanych koderów transmisji strumieniowej na żywo, zobacz [transmisja strumieniowa koderów na żywo](recommended-on-premises-live-encoders.md). Ponadto zapoznaj się z tym blogu: [Na żywo przesyłania strumieniowego w środowisku produkcyjnym za pomocą systemu bankowości Internetowej](https://link.medium.com/ttuwHpaJeT).
- Składniki w usłudze Media Services pozwalają pozyskiwać, w wersji zapoznawczej, pakiet, rejestrowanie, szyfrowania i emisję wydarzenia na żywo dla klientów lub do sieci CDN w celu dalszej dystrybucji.

Za pomocą usługi Media Services, możesz korzystać z zalet **funkcję dynamicznego tworzenia pakietów**, co pozwala na przeglądanie i emisji strumieni na żywo w [formatów MPEG DASH, HLS i Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z udziału kanału informacyjnego Aby wysłać do usługi. Przeglądającym można odtwarzać transmisji strumieniowej na żywo za pomocą dowolnego odtwarzaczy zgodne HLS, DASH lub Smooth Streaming. Możesz użyć [usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) w sieci web lub aplikacji mobilnych, aby dostarczać strumień we wszystkich tych protokołów.

Usługa Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany (**szyfrowania dynamicznego**) za pomocą Advanced Encryption Standard (AES-128) lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM licencje do autoryzowanych klientów. Aby uzyskać więcej informacji na temat sposobu szyfrowanie zawartości przy użyciu usługi Media Services, zobacz [ochrony zawartości — omówienie](content-protection-overview.md)

Można także zastosować filtrowanie dynamiczne, dzięki której może służyć do kontrolowania liczby ścieżek, formatów, szybkości transmisji, i prezentacji okna czasowe, które są wysłane do odtwarzaczy. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

Ten artykuł zawiera omówienie i wskazówki dotyczące transmisji strumieniowych na żywo za pomocą usługi Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zrozumieć przepływ pracy transmisji strumieniowej na żywo w wersji 3 usługa Media Services, masz przejrzenia i zrozumienia następujące pojęcia: 

- [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)
- [Wydarzenia na żywo i na żywo dane wyjściowe](live-events-outputs-concept.md)
- [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)

## <a name="live-streaming-workflow"></a>Przepływ pracy transmisji strumieniowej na żywo

Poniżej przedstawiono kroki, aby uzyskać przepływ pracy transmisji strumieniowej na żywo:

1. Upewnij się, w ramach konta usługi Media Services **punkt końcowy przesyłania strumieniowego** jest uruchomiona. 
2. Tworzenie [wydarzenie na żywo](live-events-outputs-concept.md). <br/>Podczas tworzenia zdarzenia, można określić automatyczne uruchamianie go. Alternatywnie możesz rozpocząć zdarzenie, gdy jesteś gotowy rozpocząć przesyłanie strumieniowe.<br/> Gdy autostart jest ustawiona na wartość true, wydarzenie na żywo zostanie uruchomiony prawo po utworzeniu. Naliczanie opłat rozpoczyna się zaraz po uruchomieniu wydarzenie na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Uzyskaj adresy URL pozyskiwania i skonfigurować koder lokalnych wysyłać wkład źródła danych przy użyciu adresu URL.<br/>Zobacz [zalecane kodery na żywo](recommended-on-premises-live-encoders.md).
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.<br/>**Na żywo dane wyjściowe** spowoduje zarchiwizowanie strumienia do **zasobów**.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.<br/>Jeśli zamierzasz szyfrowanie zawartości, zapoznaj się z [Omówienie ochrony zawartości](content-protection-overview.md).
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL, aby użyć (są to deterministyczne).
9. Pobieranie nazwy hosta dla **punkt końcowy przesyłania strumieniowego** chcesz przesyłać strumieniowo z.
10. Adres URL w kroku 8 należy połączyć z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Jeśli chcesz zatrzymać, dzięki czemu Twoje **wydarzenie na żywo** widoczne, należy zatrzymać przesyłanie strumieniowe zdarzeń i Usuń **lokalizatora przesyłania strumieniowego**.

## <a name="other-important-articles"></a>Inne artykuły, ważne

- [Zalecane kodery na żywo](recommended-on-premises-live-encoders.md)
- [Za pomocą funkcji DVR w chmurze](live-event-cloud-dvr.md)
- [Porównanie funkcji na żywo typy zdarzeń](live-event-types-comparison.md)
- [Stany i rozliczenia](live-event-states-billing.md)
- [Opóźnienie](live-event-latency.md)

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
* [Wskazówki dotyczące migracji do przenoszenia z usługi Media Services v2 do v3](migrate-from-v2-to-v3.md)
