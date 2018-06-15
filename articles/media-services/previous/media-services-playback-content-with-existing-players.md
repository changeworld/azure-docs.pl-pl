---
title: Korzystanie z istniejących odtwarzaczy do odtwarzania zawartości - Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera listę istniejących odtwarzacze czy służy do odtwarzania zawartości.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: a64d32747371aab2bd927e53c5d43e30de9108f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788480"
---
# <a name="playing-your-content-with-existing-players"></a>Odtwarzanie zawartości z istniejących graczy
Usługa Azure Media Services obsługuje wiele popularnych formatach przesyłania strumieniowego, takie jak Smooth Streaming, HTTP transmisji strumieniowej na żywo i MPEG-Dash. W tym temacie punktów do istniejących odtwarzaczy, których można użyć do testowania strumienie.

### <a name="the-azure-portal-media-services-content-player"></a>Azure portalu odtwarzacz zawartości Media Services
**Azure** portal udostępnia odtwarzacz zawartości, który służy do testowania pliku wideo.

Kliknij wybrany film wideo (Upewnij się, że został on [opublikowane](media-services-portal-publish.md)) i kliknij przycisk **odtwarzanie** przycisk w dolnej części portalu.

Zagadnienia do rozważenia:

* **Odtwarzacz zawartości MEDIA SERVICES** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Do odtwarzania z punktu końcowego przesyłania strumieniowego innego niż domyślny należy użyć innego odtwarzacza Na przykład [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Użyj [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) do odtwarzania zawartości (Wyczyść lub protected) w jednym z następujących formatów:

* Smooth Streaming
* MPEG DASH
* HLS
* MP4 progresywnego

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>Szyfrowania AES z tokenem
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Odtwarzaczy programu Silverlight
#### <a name="monitoring"></a>Monitorowanie
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady z tokenem
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Odtwarzacze kreska
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Inne
W celu przetestowania adresów URL HLS, można również użyć:

* **Safari** na urządzeniu z systemem iOS lub
* **Odtwarzacz HLS 3ivx** w systemie Windows.

## <a name="developing-video-players"></a>Tworzenie odtwarzaczy wideo
Aby uzyskać informacje dotyczące sposobu tworzenia własnych odtwarzaczy, zobacz [opracowywanie odtwarzaczy wideo](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
