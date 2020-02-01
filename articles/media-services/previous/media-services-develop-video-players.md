---
title: Opracowywanie aplikacji odtwarzacza wideo
description: Temat zawiera linki do platform i wtyczek odtwarzacza, za pomocą których można opracowywać własne aplikacje klienckie, które mogą zużywać multimedia strumieniowe z Media Services.
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
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906828"
---
# <a name="develop-video-player-applications"></a>Opracowywanie aplikacji odtwarzacza wideo
## <a name="overview"></a>Przegląd
Usługa Azure Media Services udostępnia narzędzia potrzebne do tworzenia zaawansowanych, dynamicznych aplikacji klienckich odtwarzacza dla większości platform, takich jak: urządzenia z systemem iOS, urządzenia z systemem Android, urządzenia z systemem Windows, telefony z systemem Windows Phone, konsole Xbox i dekodery. Ten temat zawiera również linki do zestawów SDK i platform odtwarzacza, za pomocą których można opracowywać własne aplikacje klienckie, które mogą zużywać multimedia strumieniowe z Azure Media Services.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) to odtwarzacz wideo w sieci Web zbudowany w celu odtwarzania zawartości multimedialnej z Microsoft Azure Media Services w wielu różnych przeglądarkach i urządzeniach. Azure Media Player wykorzystuje standardy branżowe, takie jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzenia nośników zaszyfrowanych (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego. Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, Azure Media Player używa programu Flash i Silverlight jako technologii rezerwowej. Niezależnie od używanej technologii odtwarzania, deweloperzy będą mieli ujednolicony interfejs JavaScript do uzyskiwania dostępu do interfejsów API. Pozwala to na odtwarzanie zawartości obsługiwanej przez Azure Media Services na szeroką gamę urządzeń i w przeglądarkach bez dodatkowych nakładów pracy.

Microsoft Azure Media Services pozwala na obsługę zawartości z użyciem ŁĄCZNIKów, Smooth Streaming i HLS streaming w celu odtwarzania zawartości. Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza najlepsze łącze na podstawie możliwości platformy/przeglądarki. Microsoft Azure Media Services umożliwia również szyfrowanie dynamiczne zasobów za pomocą szyfrowania PlayReady lub szyfrowania koperty AES-128. Azure Media Player umożliwia odszyfrowywanie szyfrowanej zawartości w wersji PlayReady i AES-128, gdy zostanie odpowiednio skonfigurowana. 

Więcej informacji:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Dokumentacja Azure Media Player](https://aka.ms/ampdocs) 
* [Azure Media Player Wprowadzenie blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Zarejestruj się, aby otrzymywać najnowsze informacje z Azure Media Player](https://aka.ms/ampsignup)
* [Dodawanie nowych żądań funkcji, pomysłów, opinii](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Inne narzędzia do tworzenia aplikacji odtwarzacza
Można również użyć dowolnego z następujących zestawów SDK:

* [Zestaw SDK klienta Smooth Streaming](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming aplikacji ze sklepu Windows](media-services-build-smooth-streaming-apps.md)
* [Platforma Microsoft Media: Player Framework](https://playerframework.codeplex.com/) 
* [Dokumentacja struktury odtwarzacza HTML5](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Wtyczka Smooth Streaming firmy Microsoft dla OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencjonowanie zestawu portów programu Microsoft® Smooth Streaming klienta](https://aka.ms/sspk) 
* [Tworzenie aplikacji wideo dla konsoli XBOX](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>Reklamy
Azure Media Services zapewnia obsługę wstawiania do usługi AD za pomocą platformy Windows Media platform: Player Frameworks. Platformy odtwarzacza z obsługą usług AD są dostępne dla urządzeń z systemami Windows 8, Silverlight, Windows Phone 8 i iOS. Każda platforma odtwarzacza zawiera przykładowy kod, który pokazuje, jak wdrożyć aplikację odtwarzacza. Istnieją trzy różne rodzaje reklam, które można wstawiać do multimediów:

Liniowa — reklamy zawierające całą ramkę, która wstrzymuje główne wideo

Oferty nieliniowe — reklamy, które są wyświetlane jako główne wideo, są odtwarzane, zwykle logo lub innym obrazem statycznym umieszczonym w odtwarzaczu

Pomocnik — reklamy, które są wyświetlane poza odtwarzaczem

Reklamy można umieścić w dowolnym miejscu w linii czasu głównego wideo. Musisz poinformować odtwarzacz, kiedy ma być odtwarzany AD i które reklamy mają być odtwarzane. Jest to realizowane przy użyciu zestawu standardowych plików opartych na języku XML: szablonów usługi AD wideo (ogromny), cyfrowego wideo z wieloma listami (VMAP), abstrakcyjnych szablonów sekwencji (MAST) i Digital Video Player AD Definition (VPAID). W przypadku dużych plików można określić, które reklamy mają być wyświetlane. Pliki VMAP określają, kiedy mają być odtwarzane różne reklamy i zawierają OGROMNą zawartość XML. Pliki MAST są kolejną metodą sekwencjonowania reklam, które również mogą zawierać OGROMNą zawartość XML. Pliki VPAID definiują interfejs między odtwarzaczem wideo a serwerem AD lub AD. Aby uzyskać więcej informacji, zobacz [Wstawianie reklamy](https://msdn.microsoft.com/library/dn387398.aspx).

Aby uzyskać informacje na temat pomocy technicznej dotyczącej podpisów i usług AD w filmach wideo na żywo, zobacz [obsługiwane napisy i standardy wstawiania do usługi AD](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Prześlij opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Osadzanie plików wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repozytorium usługi GitHub. js](https://github.com/Dash-Industry-Forum/dash.js)

