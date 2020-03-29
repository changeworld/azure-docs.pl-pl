---
title: Skalowanie przetwarzania multimediów za pomocą witryny Azure portal | Dokumenty firmy Microsoft
description: W tym samouczku otrzymasz od kroków skalowania przetwarzania multimediów przy użyciu witryny Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61127545"
---
# <a name="change-the-reserved-unit-type"></a>Zmiana typu jednostki zarezerwowanej
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Reszta](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Omówienie

Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać między następującymi typami jednostek zarezerwowanych: **S1**, **S2**lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**.

Oprócz określania typu jednostki zarezerwowanej można określić, aby aprowizować konto za pomocą **jednostek zarezerwowanych** (RUs). Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta.

>[!NOTE]
>Jednostki zarezerwowane przekształcają wszystkie operacje przetwarzania multimediów do postaci równoległej, uwzględniając zadania Indeksowania za pomocą usługi Azure Media Indexer. Jednak w przeciwieństwie do kodowania zadania indeksowania nie są przetwarzane szybciej przy użyciu szybszych jednostek zarezerwowanych.

> [!IMPORTANT]
> Zapoznaj się z tematem [przeglądu,](media-services-scale-media-processing-overview.md) aby uzyskać więcej informacji na temat skalowania przetwarzania multimediów.
> 
> 

## <a name="scale-media-processing"></a>Skalowanie przetwarzania nośników
Aby zmienić typ jednostki zarezerwowanej i liczbę jednostek zarezerwowanych, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz pozycję **Multimedia zarezerwowane jednostki**.
   
    Aby zmienić liczbę jednostek zarezerwowanych dla wybranego typu jednostki zarezerwowanej, użyj suwaka **Jednostki zasłużone** w mediach u góry ekranu.
   
    Aby zmienić **typ jednostki zarezerwowanej,** kliknij pasek **Szybkości zarezerwowanych jednostek przetwarzania.** Następnie wybierz warstwę cenową, której potrzebujesz: S1, S2 lub S3.
   
3. Kliknij przycisk ZAPISZ, aby zapisać zmiany.
   
    Nowe jednostki zarezerwowane są przydzielane po naciśnięciu klawisza SAVE.

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

