---
title: Opracowywanie aplikacji odtwarzacza wideo
description: Temat zawiera linki do struktur odtwarzaczy i wtyczek, który służy do opracowywania własnych aplikacji klienckich, które multimediów strumieniowych dostarczanych z usługi Media Services.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 71d8b020fe96094c15965fd82615e3182e333990
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788690"
---
# <a name="develop-video-player-applications"></a>Opracowywanie aplikacji odtwarzacza wideo
## <a name="overview"></a>Przegląd
Usługa Azure Media Services udostępnia narzędzia potrzebne do tworzenia zaawansowanych, dynamicznych aplikacji klienckich odtwarzacza dla większości platform, takich jak: urządzenia z systemem iOS, urządzenia z systemem Android, urządzenia z systemem Windows, telefony z systemem Windows Phone, konsole Xbox i dekodery. Ten temat zawiera również linki do zestawów SDK oraz struktur odtwarzaczy, których można użyć do opracowywania własnych aplikacji klienckich, które multimediów strumieniowych dostarczanych z usługą Azure Media Services.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](http://aka.ms/ampinfo) jest odtwarzacza wideo w sieci web utworzone w celu odtwarzania zawartości multimedialnej z usługi Microsoft Azure Media Services na różnych przeglądarkach i urządzeniach. Azure Media Player wykorzystuje standardy branżowe, takie jak HTML5, rozszerzenia źródło nośnika (MSE) i szyfrowane nośnika rozszerzenia (EME) umożliwia wzbogaconego adaptacyjnego przesyłania strumieniowego. Jeśli te standardy są niedostępne na urządzeniu lub w przeglądarce, Azure Media Player używa Flash i Silverlight jako rezerwowy technologii. Niezależnie od technologii odtwarzania używany deweloperzy mają ujednolicony interfejs JavaScript dostępu do interfejsów API. Dzięki temu zawartości obsługiwanej przez usługi Azure Media Services można odtworzyć w zakresie sieci, urządzeń i przeglądarki bez żadnych dodatkowych wysiłku.

Microsoft Azure Media Services umożliwia zawartość ma być obsługiwana w o DASH, Smooth Streaming i HLS formatach transmisji strumieniowej do odtwarzania zawartości. Azure Media Player uwzględnia tych różnych formatach i automatycznie odtwarza łącze najlepsze możliwości platformy/przeglądarki. Microsoft Azure Media Services umożliwia również szyfrowania dynamicznego zasobów z szyfrowaniem PlayReady lub AES-128-bitowego szyfrowania koperty. Azure Media Player umożliwia odszyfrowanie PlayReady i AES-128-bitowego zaszyfrowaną zawartość, gdy jest skonfigurowany prawidłowo. 

Więcej informacji:

* [Azure Media Player](http://aka.ms/ampinfo)
* [Azure Media Player dokumentacji](http://aka.ms/ampdocs) 
* [Azure Media Player pobieranie rozpoczęto blogu](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Zarejestruj się, aby bądź na bieżąco przy użyciu najnowszych danych z usługi Azure Media Player](http://aka.ms/ampsignup)
* [Dodaj nowe żądania dotyczące funkcji, pomysły, opinie](http://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Inne narzędzia do tworzenia aplikacji odtwarzacza
Można również użyć dowolnego z następujących zestawów SDK:

* [Smooth Streaming Client w zestawie SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Płynnego przesyłania strumieniowego aplikacji ze Sklepu Windows](media-services-build-smooth-streaming-apps.md)
* [Platformy Media firmy Microsoft: Framework Player](http://playerframework.codeplex.com/) 
* [HTML5 Dokumentację Framework Player](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming wtyczki dla OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencjonowania Microsoft® Smooth Streaming Client Eksportowanie zestawu](http://aka.ms/sspk) 
* [Projektowanie aplikacji wideo XBOX](http://xbox.create.msdn.com/) 

## <a name="advertising"></a>Reklama
Usługa Azure Media Services obsługuje wstawiania reklam za pośrednictwem platformy Windows Media: struktur odtwarzaczy. Struktur odtwarzaczy z obsługą ad są dostępne dla urządzeń z systemem Windows 8, Silverlight, Windows Phone 8 i iOS. Każdy framework player zawiera przykładowy kod, który pokazuje, jak wdrożyć aplikacja odtwarzacza. Istnieją trzy różne rodzaje reklamy, które można wstawić do multimediów:

Liniowy — reklam pełne ramki Wstrzymaj głównego wideo

Rożne — nakładki reklamy, które są wyświetlane podczas odtwarzania wideo głównego, zwykle logo lub innego obrazu statycznych, umieszczone w ramach odtwarzacza

Pomocnik — reklam wyświetlanych poza Windows Media player

Usługa AD można umieścić w dowolnym momencie wideo głównych osi czasu. Odtwarzacz musi sprawdzić, kiedy odtwarzać ad i które reklam, aby odtworzyć. Jest to realizowane przy użyciu zestawu standardowych plików opartych na języku XML: wideo Ad Service szablonu (VAST), cyfrowy wideo wielu Ad listy odtwarzania (VMAP) nośnika abstrakcyjny sekwencjonowania szablonu (MASZTÓW) i cyfrowy wideo Player Ad interfejsu definicji (VPAID). Pliki PRZEWAŻAJĄCA określają, jakie reklam, aby wyświetlić. Pliki VMAP Określ, kiedy do odtwarzania różnych reklam i zawierać PRZEWAŻAJĄCA XML. Pliki MASZTÓW są reklam sekwencji, których może również zawierać PRZEWAŻAJĄCA XML w inny sposób. Pliki VPAID zdefiniuj interfejs między odtwarzacza wideo i ad lub serwera usługi ad. Aby uzyskać więcej informacji, zobacz [wstawiania reklam](https://msdn.microsoft.com/library/dn387398.aspx).

Informacje o kodowane i obsługę reklam w wideo transmisji strumieniowej na żywo, zobacz [obsługiwane kodowane i standardy wstawiania Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Osadzanie plików wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repozytorium GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js)

