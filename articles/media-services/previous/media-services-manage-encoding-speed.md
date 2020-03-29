---
title: Zarządzanie szybkością i współbieżnością kodowania za pomocą usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono krótkie omówienie sposobu zarządzania szybkością i współbieżnością zadań/zadań kodowania za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463756"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Zarządzanie szybkością i współbieżnością kodowania  

W tym artykule przedstawiono krótki przegląd sposobu zarządzania szybkością i współbieżnością zadań/zadań kodowania.

## <a name="overview"></a>Omówienie

W udziale usługi Media Services **typ jednostki zarezerwowanej** określa szybkość przetwarzania zadań przetwarzania multimediów. Można wybrać między następującymi typami jednostek zarezerwowanych: **S1**, **S2**lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. W temacie [jednostek kodowania skalowania](media-services-scale-media-processing-overview.md) przedstawiono tabelę, która ułatwia podejmowanie decyzji przy wyborze między różnymi szybkościami kodowania.

Oprócz określenia typu jednostki zarezerwowanej można określić, aby aprowizować konto za pomocą **jednostek zarezerwowanych**. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Jeśli na przykład konto ma pięć jednostek zarezerwowanych, pięć zadań multimedialnych będzie uruchamianych jednocześnie, o ile istnieją zadania do przetworzenia. Pozostałe zadania będą czekać w kolejce i zostaną odebrane do przetwarzania sekwencyjnie po zakończeniu uruchomionego zadania. Jeśli konto nie ma żadnych jednostek zarezerwowanych aprowizowanych, zadania będą pobierane kolejno. W takim przypadku czas oczekiwania między jednym zakończeniem zadania a następnym uruchomieniem będzie zależeć od dostępności zasobów w systemie.

Aby uzyskać szczegółowe informacje i przykłady, które pokazują, jak skalować jednostki kodowania, zobacz [ten](media-services-scale-media-processing-overview.md) temat.

## <a name="next-step"></a>Następny krok

[Skalowanie jednostek kodujących](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

