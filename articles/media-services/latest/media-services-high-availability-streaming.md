---
title: Strumieniowanie wysokiej dostępności usługi Azure Media Services
description: Dowiedz się, jak przejść awaryjnie do dodatkowego konta usługi Media Services, jeśli wystąpi regionalna awaria lub awaria centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899232"
---
# <a name="media-services-high-availability-streaming"></a>Przesyłanie strumieniowe o wysokiej dostępności usługi Media Services

Usługa Azure Media Services obecnie nie zapewnia natychmiastowego trybu failover usługi, jeśli występuje regionalna awaria centrum danych lub awaria podstawowych składników lub usług zależnych. W tym artykule przedstawiono wskazówki dotyczące tworzenia przesyłania strumieniowego wideo na żądanie w regionie.

## <a name="prerequisites"></a>Wymagania wstępne

Przegląd [Jak zbudować międzyregionajny system kodowania](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Jak tworzyć przesyłanie strumieniowe wideo na żądanie między regionami 

* Przesyłanie strumieniowe międzyregionami wideo na żądanie obejmuje powielanie [zasobów,](assets-concept.md)zasad [klucza zawartości](content-key-policy-concept.md) (jeśli jest używany), [zasad przesyłania strumieniowego](streaming-policy-concept.md)i [lokalizatorów przesyłania strumieniowego.](streaming-locators-concept.md) 
* Trzeba będzie utworzyć zasady w obu regionach i aktualizować je. 
* Podczas tworzenia lokalizatorów przesyłania strumieniowego należy użyć tej samej wartości identyfikatora lokalizatora, wartości ContentKey ID i wartości ContentKey.  
* Jeśli kodujesz zawartość, zaleca się zakodowanie zawartości w regionie A i opublikowanie jej, a następnie skopiowanie zakodowanej zawartości do regionu B i opublikowanie jej przy użyciu tych samych wartości, co z regionu A.
* Usługi Traffic Manager można używać w nazwach hostów dla źródła i usługi dostarczania kluczy (w konfiguracji usługi Media Services będzie to wyglądać jak niestandardowy adres URL serwera kluczy).

## <a name="next-steps"></a>Następne kroki

Wymelduj się:

* [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo](stream-files-dotnet-quickstart.md)
* [przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
