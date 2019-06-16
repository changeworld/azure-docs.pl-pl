---
title: Użyj istniejącego odtwarzacze do odtwarzania zawartości — Azure | Dokumentacja firmy Microsoft
description: W tym temacie wymieniono istniejącego odtwarzacze służącego do odtwarzania zawartości.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c96710d6dcca9f5ef99b3a02a0bc875d433f814d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463425"
---
# <a name="playing-your-content-with-existing-players"></a>Odtwarzanie zawartości za pomocą istniejących odtwarzaczy
Usługa Azure Media Services obsługuje wiele popularnych formatów przesyłania strumieniowego, takich jak Smooth Streaming, HTTP Live Streaming i MPEG-Dash. W tym temacie wskazuje istniejącego odtwarzacze, które można użyć do testowania strumienie.

### <a name="the-azure-portal-media-services-content-player"></a>Azure portal odtwarzacz zawartości Media Services
**Azure** portal udostępnia odtwarzacz zawartości, który służy do testowania pliku wideo.

Kliknij wybrany film wideo (Upewnij się, było [opublikowane](media-services-portal-publish.md)) i kliknij przycisk **Odtwórz** znajdujący się u dołu portalu.

Zagadnienia do rozważenia:

* **Odtwarzacz zawartości MEDIA SERVICES** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Do odtwarzania z punktu końcowego przesyłania strumieniowego innego niż domyślny należy użyć innego odtwarzacza Na przykład [usługi Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Użyj [usługi Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) do odtwarzania zawartości (Wyczyść lub protected) w dowolnej z następujących formatów:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressive MP4

### <a name="flash-player"></a>Odtwarzacza w środowisku Flash
#### <a name="aes-encrypted-with-token"></a>Z szyfrowaniem AES przy użyciu tokenu
[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Odtwarzaczy programu Silverlight

#### <a name="playready-with-token"></a>PlayReady z tokenem
[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Odtwarzacze kreska
[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

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
