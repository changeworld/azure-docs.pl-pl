---
title: Odtwarzanie za pomocą usługi Azure Media Player — Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie usługi Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/03/2018
ms.author: juliako
ms.openlocfilehash: 4677fc9a56db3190073b3f310a51632055510bc2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028552"
---
# <a name="play-back-with-azure-media-player"></a>Odtwarzanie za pomocą usługi Azure Media Player

[Usługa Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) jest odtwarzacz wideo w sieci web utworzona w celu odtwarzania zawartości multimedialnej z Microsoft Azure Media Services na podstawie różnych przeglądarek i urządzeń. Usługa Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródło nośnika (MSE) i Encrypted Media Extensions (EME), aby zapewnić zaawansowane adaptacyjne środowisko przesyłania strumieniowego. Gdy te standardy nie są dostępne na urządzeniu lub w przeglądarce, usługa Azure Media Player używa technologii Flash lub Silverlight jako technologii rezerwowej. Niezależnie od użytej technologii odtwarzania deweloperzy mają jednolitego interfejsu języka JavaScript, dostęp do interfejsów API. Dzięki temu zawartość udostępniana w usłudze Azure Media Services do odtwarzania na szeroki zakres urządzeń i w różnych przeglądarkach bez konieczności wykonywania dodatkowych działań.

Microsoft Azure Media Services umożliwia zawartości do być udostępniany przy użyciu protokołu HLS, DASH, Smooth Streaming, formatów przesyłania strumieniowego do odtwarzania zawartości. Usługa Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza łącze najlepsze możliwości platformy/przeglądarki. Usługa Media Services umożliwia również dynamiczne szyfrowanie zasobów za pomocą szyfrowanie PlayReady lub AES-128-bitowego szyfrowania koperty. Usługa Azure Media Player umożliwia odszyfrowywanie PlayReady i szyfrowania AES-128-bitowego zaszyfrowaną zawartość, gdy jest skonfigurowany prawidłowo. 

[Rozpocznij bezpłatną wersję próbną](http://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorowanie diagnostyki strumienia wideo

Możesz użyć [strony pokaz usługi Azure Media Player](http://aka.ms/amp) monitorować diagnostykę strumienia wideo. 

! [Usługa azure Media Player diagnostyki] [amp_diagnostics]

## <a name="next-steps"></a>Kolejne kroki

- [Zarejestruj się na bieżąco dzięki najnowszym informacjom na platformie Azure Media Player](http://azuremediaplayerdemo.azurewebsites.net/amp_signup.html)
- [Dokumentacja usługi Azure Media Player](https://aka.ms/ampdocs)
- [Przykłady usługi Azure Media Player](https://aka.ms/ampsamples)
