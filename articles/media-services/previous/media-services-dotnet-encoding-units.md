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
ms.date: 03/18/2019
ms.author: juliako;milangada;
ms.openlocfilehash: 65b647a375ecbe70f7f29af5b09827f1c34dcd6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217227"
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

## <a name="overview"></a>Omówienie
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

