---
title: Zarządzanie szybkość i współbieżność kodowania usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera krótkie omówienie sposobu zarządzania szybkość i współbieżność kodowania zadań/zadań z usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: d7e3d6d0c176d0a903c3027ab4feddb332557566
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Zarządzanie szybkość i współbieżności z kodowaniem

Ten artykuł zawiera krótkie omówienie sposobu zarządzania szybkość i współbieżność zadań/zadania kodowania.

## <a name="overview"></a>Przegląd

W usłudze Media Services **typ jednostki zarezerwowane** określa szybkość, z którym są przetwarzane zadania przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. [Skalowania jednostki kodowania](media-services-scale-media-processing-overview.md) tematu zawiera tabelę, która ułatwia podjęcie decyzji, wybierając między różnych szybkościach kodowania.

Oprócz określenie typu jednostki zarezerwowane, możesz określić do udostępnienia konta z **jednostki zarezerwowanego**. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostki zarezerwowanego, następnie nośnika pięć zadań będą uruchomione jednocześnie tak długo, jak istnieją zadania do przetworzenia. Pozostałych zadań będzie czekać w kolejce i ma pobrać pobierany do przetwarzania sekwencyjnie, po zakończeniu uruchomione zadanie. Jeśli konto nie ma żadnych jednostek zarezerwowanego zainicjowano obsługę administracyjną, następnie zadania będą pobierane po kolei. W takim przypadku czas oczekiwania między jedno zadanie zakończenia i kolejnej początkowe zależy od dostępności zasobów w systemie.

Aby uzyskać szczegółowe informacje i przykłady, które przedstawiają sposób kodowania jednostek skalowania, zobacz [to](media-services-scale-media-processing-overview.md) tematu.

## <a name="next-step"></a>Następny krok

[Kodowania jednostki skalowania](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

