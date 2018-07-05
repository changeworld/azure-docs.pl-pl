---
title: Użyj istniejącego odtwarzacze do odtwarzania zawartości — Azure | Dokumentacja firmy Microsoft
description: W tym temacie wymieniono istniejącego odtwarzacze służącego do odtwarzania zawartości.
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
ms.date: 07/02/2018
ms.author: juliako
ms.openlocfilehash: 3fe82b98163182c73a144b72da371e8aa195e8cf
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435861"
---
# <a name="playing-your-content-with-existing-players"></a>Odtwarzanie zawartości za pomocą istniejących odtwarzaczy
Usługa Azure Media Services obsługuje wiele popularnych formatów przesyłania strumieniowego, takich jak Smooth Streaming, HTTP Live Streaming i MPEG-Dash. W tym temacie wskazuje istniejącego odtwarzacze, które można użyć do testowania strumienie.

### <a name="the-azure-portal-media-services-content-player"></a>Azure portal odtwarzacz zawartości Media Services
**Azure** portal udostępnia odtwarzacz zawartości, który służy do testowania pliku wideo.

Kliknij wybrany film wideo (Upewnij się, było [opublikowane](media-services-portal-publish.md)) i kliknij przycisk **Odtwórz** znajdujący się u dołu portalu.

Zagadnienia do rozważenia:

* **Odtwarzacz zawartości MEDIA SERVICES** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Do odtwarzania z punktu końcowego przesyłania strumieniowego innego niż domyślny należy użyć innego odtwarzacza Na przykład [usługi Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Użyj [usługi Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) do odtwarzania zawartości (Wyczyść lub protected) w dowolnej z następujących formatów:

* Smooth Streaming
* MPEG DASH
* HLS
* Progresywne plików MP4.

### <a name="flash-player"></a>Odtwarzacza w środowisku Flash
#### <a name="aes-encrypted-with-token"></a>Z szyfrowaniem AES przy użyciu tokenu
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Odtwarzaczy programu Silverlight

#### <a name="playready-with-token"></a>PlayReady z tokenem
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Odtwarzacze kreska
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Inne
Aby przetestować HLS adresów URL, możesz również użyć:

* **Safari** na urządzeniu z systemem iOS lub
* **Odtwarzacz HLS 3ivx** na Windows.

## <a name="developing-video-players"></a>Tworzenie odtwarzaczy wideo
Aby uzyskać informacje o sposobie tworzenia własnych graczy, zobacz [tworzenie odtwarzaczy wideo](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
