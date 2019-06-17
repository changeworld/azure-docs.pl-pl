---
title: Opracowywanie aplikacji odtwarzacza wideo
description: Temat zawiera linki do różnym Architekturom odtwarzaczy i wtyczek, używanej do tworzenia własnych aplikacji klienckich, które mogą wykorzystywać multimediów strumieniowych dostarczanych przez usługi Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465647"
---
# <a name="develop-video-player-applications"></a>Opracowywanie aplikacji odtwarzacza wideo
## <a name="overview"></a>Omówienie
Usługa Azure Media Services udostępnia narzędzia potrzebne do tworzenia zaawansowanych, dynamicznych aplikacji klienckich odtwarzacza dla większości platform, takich jak: urządzenia z systemem iOS, urządzenia z systemem Android, urządzenia z systemem Windows, telefony z systemem Windows Phone, konsole Xbox i dekodery. Ten temat zawiera także łącza do zestawów SDK oraz struktur odtwarzaczy, można użyć do tworzenia własnych aplikacji klienckich, które mogą wykorzystywać multimediów strumieniowych dostarczanych przez Azure Media Services.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Usługa Azure Media Player](https://aka.ms/ampinfo) jest odtwarzacz wideo w sieci web utworzona w celu odtwarzania zawartości multimedialnej z Microsoft Azure Media Services na podstawie różnych przeglądarek i urządzeń. Usługa Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródło nośnika (MSE) i Encrypted Media Extensions (EME), aby zapewnić zaawansowane adaptacyjne środowisko przesyłania strumieniowego. Gdy te standardy nie są dostępne na urządzeniu lub w przeglądarce, usługa Azure Media Player używa technologii Flash lub Silverlight jako technologii rezerwowej. Niezależnie od użytej technologii odtwarzania deweloperzy mają jednolitego interfejsu języka JavaScript, dostęp do interfejsów API. Dzięki temu zawartość udostępniana w usłudze Azure Media Services do odtwarzania na szeroki zakres urządzeń i w różnych przeglądarkach bez konieczności wykonywania dodatkowych działań.

Microsoft Azure Media Services umożliwia zawartości do być udostępniany przy użyciu DASH, Smooth Streaming i HLS formatów przesyłania strumieniowego do odtwarzania zawartości. Usługa Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza łącze najlepsze możliwości platformy/przeglądarki. Microsoft Azure Media Services umożliwia również dynamiczne szyfrowanie zasobów za pomocą szyfrowanie PlayReady lub AES-128-bitowego szyfrowania koperty. Usługa Azure Media Player umożliwia odszyfrowywanie PlayReady i szyfrowania AES-128-bitowego zaszyfrowaną zawartość, gdy jest skonfigurowany prawidłowo. 

Informacje dodatkowe:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Usługa Azure Media Player dokumentacji](https://aka.ms/ampdocs) 
* [Usługa Azure Media Player wprowadzenie pracę Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Zarejestruj się na bieżąco dzięki najnowszym informacjom na platformie Azure Media Player](https://aka.ms/ampsignup)
* [Dodawaj sugestie dotyczące nowych funkcji, pomysły i opinie](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Inne narzędzia umożliwiające tworzenie aplikacji odtwarzacza
Można również użyć dowolnego z następujących SDK:

* [Zestaw Smooth Streaming Client SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Bezproblemowe przesyłania strumieniowego aplikacji Windows Store](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](https://playerframework.codeplex.com/) 
* [HTML5 Dokumentacja Framework odtwarzaczu](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming wtyczkę dla platformy OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencjonowania Microsoft® Smooth Streaming Client Porting Kit](https://aka.ms/sspk) 
* [Tworzenie aplikacji wideo XBOX](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Reklamy
Usługa Azure Media Services zapewnia obsługę wstawiania reklam za pośrednictwem platformy Media Windows: Architektury odtwarzaczy. Architektury odtwarzaczy z obsługą usługi ad są dostępne dla urządzeń z systemem Windows 8, Silverlight, Windows Phone 8 i iOS. Każdy struktury odtwarzacza zawiera przykładowy kod, który pokazuje, jak zaimplementować aplikacja odtwarzacza. Istnieją trzy różne rodzaje reklam, które można wstawić do multimediów:

Liniowy — reklamy pełną ramki, które wstrzymanie odtwarzania filmu głównego

Nieliniowych — reklamy nakładki, które są wyświetlane jako odtwarzania wideo głównego, zwykle logo lub inne obraz statyczny umieszczony w odtwarzaczu

Pomocnik — reklam, które są wyświetlane poza odtwarzacza

Usługa AD można umieścić w dowolnym momencie w głównym wideo osi czasu. Musisz poinformować odtwarzacza, kiedy należy odtworzyć ad i reklam, które do odtwarzania. Odbywa się przy użyciu zestawu standardowych plików oparty na formacie XML: Szablon (VAST), Digital Video usługi Ad wideo, wiele Ad odtwarzania (VMAP), Media abstrakcji sekwencjonowania szablonu (MASZTÓW), a definicja interfejsu Ad cyfrowy odtwarzacz wideo (VPAID). OGROMNA pliki określają, jakie reklam, aby wyświetlić. Pliki VMAP Określ, kiedy do odtwarzania różnych reklam i zawierać OGROMNA kod XML. Pliki MASZTÓW są sekwencji reklam, które również mogą zawierać duże XML w inny sposób. Pliki VPAID definiują interfejs między odtwarzacza wideo i usługi ad lub serwera usługi ad. Aby uzyskać więcej informacji, zobacz [Wstawianie reklam](https://msdn.microsoft.com/library/dn387398.aspx).

Dla informacji o kodowane i obsługa reklam w transmisji strumieniowej na żywo w filmach wideo, zobacz [Ad wstawiania standardów i obsługiwane kodowane](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Osadzanie plików wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repozytorium w witrynie GitHub implementacją dash.js](https://github.com/Dash-Industry-Forum/dash.js)

