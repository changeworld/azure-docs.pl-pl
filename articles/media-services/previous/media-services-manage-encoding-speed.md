---
title: Zarządzanie szybkością i współbieżnością kodowania za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera krótkie omówienie, jak można zarządzać szybkością i współbieżnością kodowania zadań/zadań za pomocą usługi Azure Media Services.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463756"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Zarządzanie szybkością i współbieżnością kodowania  

Ten artykuł zawiera krótkie omówienie, jak można zarządzać szybkością i współbieżnością kodowania zadań/zadań.

## <a name="overview"></a>Omówienie

W usłudze Media Services **zastrzeżony typ jednostki** określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. [Skalowania jednostek kodowania](media-services-scale-media-processing-overview.md) temat zawiera tabelę, która pomoże Ci w podjęciu decyzji, wybierając między różne szybkości kodowania.

Oprócz określenia typu jednostki zarezerwowanej, można określić aprowizację swojego konta za pomocą **jednostek zarezerwowanych**. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostek zarezerwowanych, a następnie pięć zadań będą uruchomione jednocześnie tak długo, jak istnieją zadania do przetworzenia. Pozostałe zadania będzie oczekiwać w kolejce i będzie pobrać wybrany do przetwarzania sekwencyjnie, po zakończeniu bieżące zadanie. Jeśli konto nie ma żadnych jednostek zarezerwowanych zainicjowano obsługę administracyjną, następnie zadania zostaną pobrane po kolei. W tym przypadku czas oczekiwania między jedno zadanie zostało ukończone, a następnie bazujący będzie zależeć od dostępności zasobów w systemie.

Aby uzyskać szczegółowe informacje i przykłady, które pokazują sposób skalowania jednostek kodowania, zobacz [to](media-services-scale-media-processing-overview.md) tematu.

## <a name="next-step"></a>Następny krok

[Możliwość skalowania jednostek kodowania](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

