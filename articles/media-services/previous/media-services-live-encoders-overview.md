---
title: Konfigurowanie koderów lokalnych podczas korzystania z Azure Media Services do tworzenia strumieni o większej szybkości transmisji bitów | Microsoft Docs
description: Ten temat zawiera listę lokalnych koderów na żywo, których można użyć do przechwytywania wydarzeń na żywo i wysyłania strumieni na żywo o pojedynczej szybkości transmisji bitów do kanałów AMS (na których włączono obsługę kodowania na żywo) w celu dalszej obróbki. Temat zawiera linki do samouczków, które pokazują, jak skonfigurować kodery na liście.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 71a31228602ef161158eaa05c80d50f65de98a50
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133268"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Jak skonfigurować kodery lokalne przy użyciu Azure Media Services do tworzenia strumieni o większej szybkości transmisji bitów
Ten temat zawiera listę lokalnych koderów na żywo, których można użyć do przechwytywania wydarzeń na żywo i wysyłania strumieni na żywo o pojedynczej szybkości transmisji bitów do kanałów AMS (na których włączono obsługę kodowania na żywo) w celu dalszej obróbki. Temat zawiera również linki do samouczków, które pokazują, jak skonfigurować kodery na liście.

> [!NOTE]
> Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.

## <a name="haivision-kb-encoder"></a>Koder Haivision KB
Aby uzyskać informacje dotyczące sposobu konfigurowania kodera [kodera HAIVISION KB](https://www.haivision.com/products/kb-series/) w celu wysłania strumienia na żywo o pojedynczej szybkości transmisji bitów do kanału usługi AMS, zobacz [Konfigurowanie Haivision KB](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Wirecast telestrumienia
Aby uzyskać informacje na temat sposobu konfigurowania kodera [Wirecast usługi webstream](https://www.telestream.net/wirecast/overview.htm) do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów do kanału usługi AMS, zobacz [Konfigurowanie Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Aby uzyskać informacje na temat sposobu konfigurowania kodera [TriCaster](https://newtek.com/products/tricaster-40.html) w celu wysłania strumienia na żywo o pojedynczej szybkości transmisji bitów do kanału AMS, zobacz [Konfigurowanie TriCaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Aby uzyskać więcej informacji, zobacz [elementy na żywo](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekaż opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe na żywo przy użyciu Azure Media Services do tworzenia strumieni o większej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).

