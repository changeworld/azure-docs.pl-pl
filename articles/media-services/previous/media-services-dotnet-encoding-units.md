---
title: Skalowanie przetwarzania przez dodanie jednostek kodowania - Azure multimediów |  Dokumentacja firmy Microsoft
description: Dowiedz się, jak sposób dodawania jednostki kodowania za pomocą platformy .NET
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako;milangada;
ms.openlocfilehash: 8f17d5e6d45b678f5c4a0c4318e74a18c42ff0c8
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035720"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Skalowanie kodowania za pomocą zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Przegląd
> [!IMPORTANT]
> Upewnij się zapoznać się z [Przegląd](media-services-scale-media-processing-overview.md) Aby uzyskać więcej informacji na temat skalowanie przetwarzania multimediów.
> 
> 

Aby zmienić typ jednostki zarezerwowanej i Liczba zastrzeżonych jednostek przy użyciu zestawu .NET SDK kodowania, wykonaj następujące czynności:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Otwierania biletu pomocy technicznej

Domyślnie każde konto usługi Media Services można skalować do maksymalnie 10 S2 jednostek zarezerwowanych multimediów S3 (lokalizacje MRU) lub 25 S1 lokalizacje MRU i 5 na żądanie zastrzeżone jednostki przesyłania strumieniowego. Możesz poprosić wyższy limit, otwierając bilet pomocy technicznej.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

