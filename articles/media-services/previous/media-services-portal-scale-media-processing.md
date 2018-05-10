---
title: Skalowania przetwarzania nośnika przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia kroki nośnika skalowania przetwarzania przy użyciu portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 51973916c97282ac93032ab833402d9d1356647e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="change-the-reserved-unit-type"></a>Zmiana typu jednostki zarezerwowanej
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> Aby uzyskać najnowszą wersję zestawu SDK języka Java i zacząć programować w języku Java, zobacz [Rozpoczynanie korzystania z zestawu SDK klienta Java dla usług Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Aby pobrać najnowszy zestaw SDK języka PHP dla usługi Media Services, poszukaj wersji 0.5.7 pakietu Microsoft/WindowAzure w [repozytorium Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Przegląd

Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**.

Oprócz określenia typu jednostki zarezerwowanej można określić aprowizację swojego konta przy użyciu **jednostek zarezerwowanych** (RU, Reserved Unit). Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta.

>[!NOTE]
>Jednostki zarezerwowane przekształcają wszystkie operacje przetwarzania multimediów do postaci równoległej, uwzględniając zadania Indeksowania za pomocą usługi Azure Media Indexer. Jednak w przeciwieństwie do kodowania zadania indeksowania nie są przetwarzane szybciej przy użyciu szybszych jednostek zarezerwowanych.

> [!IMPORTANT]
> Upewnij się przejrzeć [omówienie](media-services-scale-media-processing-overview.md) tematu, aby uzyskać więcej informacji na temat skalowania przetwarzania tematu nośnika.
> 
> 

## <a name="scale-media-processing"></a>Skalowanie przetwarzania multimediów
Aby zmienić typ jednostki zarezerwowane i liczbę jednostek zarezerwowanego, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W **ustawienia** wybierz **jednostki zarezerwowane multimediów**.
   
    Aby zmienić liczbę jednostek zarezerwowanego dla typu wybranej jednostki zarezerwowane, użyj **jednostki obsługiwanej nośnika** suwak w górnej części ekranu.
   
    Aby zmienić **typ jednostki zarezerwowane**, kliknij **szybkości przetwarzania zarezerwowanych jednostek** paska. Wybierz warstwę cenową należy: S1, S2 lub S3.
   
3. Kliknij przycisk ZAPISZ, aby zapisać zmiany.
   
    Nowe zastrzeżone jednostki są przydzielane po naciśnięciu przycisku ZAPISZ.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

