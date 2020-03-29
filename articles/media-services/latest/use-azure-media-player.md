---
title: Odtwarzanie za pomocą programu Azure Media Player — Azure | Dokumenty firmy Microsoft
description: Azure Media Player to internetowy odtwarzacz wideo stworzony do odtwarzania zawartości multimedialnej z usługi Microsoft Azure Media Services na różnych przeglądarkach i urządzeniach.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: d9e42e809443a2dd6cdeb989f692b96d63269f79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673582"
---
# <a name="azure-media-player-overview"></a>Omówienie usługi Azure Media Player

Azure Media Player to internetowy odtwarzacz wideo stworzony do odtwarzania zawartości multimedialnej z usługi Microsoft Azure Media Services na różnych przeglądarkach i urządzeniach. Usługa Azure Media Player korzysta ze standardów branżowych, takich jak HTML5, Rozszerzenia źródeł multimediów (MSE) i Rozszerzenia zaszyfrowanych multimediów (EME), aby zapewnić wzbogacone środowisko adaptacyjnego przesyłania strumieniowego. Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, program Azure Media Player używa programów Flash i Silverlight jako technologii rezerwowej. Niezależnie od zastosowanej technologii odtwarzania deweloperzy będą mieli ujednolicony interfejs JavaScript, aby uzyskać dostęp do interfejsów API. Dzięki temu zawartość obsługiwana przez usługę Azure Media Services ma być odtwarzana na wielu różnych urządzeniach i przeglądarkach bez dodatkowego wysiłku.

Usługa Microsoft Azure Media Services umożliwia przesyłanie zawartości w formatach strumieniowego HLS, DASH i Smooth Streaming w celu odtwarzania zawartości. Program Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza najlepsze łącze oparte na możliwościach platformy/przeglądarki. Usługa Media Services umożliwia również dynamiczne szyfrowanie zasobów za pomocą szyfrowania PlayReady lub szyfrowania kopert AES-128 bitów. Usługa Azure Media Player umożliwia odszyfrowywanie zawartości zaszyfrowanej bitem PlayReady i AES-128, gdy jest odpowiednio skonfigurowana. 

> [!NOTE]
> Odtwarzanie protokołu HTTPS jest wymagane dla zaszyfrowanej zawartości Widevine.

## <a name="use-azure-media-player-demo-page"></a>Korzystanie ze strony demonstracyjnej programu Azure Media Player

### <a name="start-using"></a>Zacznij używać

Za pomocą [strony demonstracyjnej programu Azure Media Player](https://aka.ms/azuremediaplayer) można odtwarzać przykłady usługi Azure Media Services lub własny strumień.  

Aby odtworzyć nowy film, wklej inny adres URL i naciśnij **przycisk Aktualizuj**.

Aby skonfigurować różne opcje odtwarzania (na przykład technologię, język lub szyfrowanie), naciśnij przycisk **Opcje zaawansowane**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorowanie diagnostyki strumienia wideo

Za pomocą [strony demonstracyjnej programu Azure Media Player](https://aka.ms/azuremediaplayer) można monitorować diagnostykę strumienia wideo. 

![Diagnostyka programu Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Konfigurowanie programu Azure Media Player w kodzie HTML

Program Azure Media Player jest łatwy w konfiguracji. Uzyskanie podstawowego odtwarzania zawartości multimedialnej z konta usługi Media Services zajmuje tylko kilka chwil. Zobacz [dokumentację programu Azure Media Player,](https://aka.ms/ampdocs) aby uzyskać szczegółowe informacje na temat konfigurowania i konfigurowania programu Azure Media Player. 

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja usługi Azure Media Player](https://aka.ms/ampdocs)
- [Przykłady programu Azure Media Player](https://aka.ms/ampsamples)
