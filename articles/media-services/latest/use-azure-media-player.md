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
ms.openlocfilehash: 6de626323c82689d0ead4f5aaad2a2e43187ebd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466650"
---
# <a name="azure-media-player-overview"></a>Omówienie usługi Azure Media Player

Usługa Azure Media Player jest odtwarzacz wideo w sieci web utworzona w celu odtwarzania zawartości multimedialnej z Microsoft Azure Media Services na podstawie różnych przeglądarek i urządzeń. Usługa Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródło nośnika (MSE) i Encrypted Media Extensions (EME), aby zapewnić zaawansowane adaptacyjne środowisko przesyłania strumieniowego. Gdy te standardy nie są dostępne na urządzeniu lub w przeglądarce, usługa Azure Media Player używa technologii Flash lub Silverlight jako technologii rezerwowej. Niezależnie od użytej technologii odtwarzania deweloperzy mają jednolitego interfejsu języka JavaScript, dostęp do interfejsów API. Dzięki temu zawartość udostępniana w usłudze Azure Media Services do odtwarzania na szeroki zakres urządzeń i w różnych przeglądarkach bez konieczności wykonywania dodatkowych działań.

Microsoft Azure Media Services umożliwia zawartości do być udostępniany przy użyciu protokołu HLS, DASH, Smooth Streaming, formatów przesyłania strumieniowego do odtwarzania zawartości. Usługa Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza łącze najlepsze możliwości platformy/przeglądarki. Usługa Media Services umożliwia również dynamiczne szyfrowanie zasobów za pomocą szyfrowanie PlayReady lub AES-128-bitowego szyfrowania koperty. Usługa Azure Media Player umożliwia odszyfrowywanie PlayReady i szyfrowania AES-128-bitowego zaszyfrowaną zawartość, gdy jest skonfigurowany prawidłowo. 

[Rozpocznij bezpłatną wersję próbną](https://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Użyj strony pokaz usługi Azure Media Player

### <a name="start-using"></a>Rozpoczynanie korzystania z

Możesz użyć [strony pokaz usługi Azure Media Player](https://aka.ms/amp) grać przykładów usługi Azure Media Services lub własnego strumienia.  

Aby odtworzyć nowy film wideo, Wklej innego adresu URL i naciśnij **aktualizacji**.

Aby skonfigurować różne opcje odtwarzania (na przykład technologii, języka lub szyfrowania), naciśnij klawisz **zaawansowane opcje**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorowanie diagnostyki strumienia wideo

Możesz użyć [strony pokaz usługi Azure Media Player](https://aka.ms/amp) monitorować diagnostykę strumienia wideo. 

![Diagnostyka Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Konfigurowanie usługi Azure Media Player w kodzie HTML

Usługa Azure Media Player jest łatwe do skonfigurowania. Rejestracja trwa tylko kilka minut, aby uzyskać podstawowe odtwarzanie zawartości multimedialnej z konta usługi Media Services. Zobacz [dokumentacji usługi Azure Media Player](https://aka.ms/ampdocs) Aby uzyskać szczegółowe informacje na temat sposobu instalowania i konfigurowania usługi Azure Media Player. 

## <a name="next-steps"></a>Kolejne kroki

- [Dokumentacja usługi Azure Media Player](https://aka.ms/ampdocs)
- [Przykłady usługi Azure Media Player](https://aka.ms/ampsamples)
