---
title: Skalowanie jednostek zarezerwowanych multimediów za pomocą interfejsu wiersza polecenia — Azure | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać interfejsu wiersza polecenia do skalowania przetwarzania multimediów za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 79f2df0f94b212ea45c01c825b23f1dbb4cc40db
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582288"
---
# <a name="scaling-media-processing"></a>Skalowanie przetwarzania multimediów

Usługa Azure Media Services umożliwia skalowanie przetwarzania multimediów w ramach konta dzięki funkcji zarządzania jednostkami zarezerwowanymi multimediów (MRU, Media Reserved Units). Mru określić szybkość przetwarzania multimediów są przetwarzane. Można wybrać między następującymi typami jednostek zarezerwowanych: **S1**, **S2**lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. 

Oprócz określenia typu jednostki zarezerwowanej można określić, aby aprowizować konto za pomocą jednostek zarezerwowanych. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Jeśli na przykład konto ma pięć jednostek zarezerwowanych, pięć zadań multimedialnych będzie uruchamianych jednocześnie, o ile istnieją zadania do przetworzenia. Pozostałe zadania będą czekać w kolejce i zostaną odebrane do przetwarzania sekwencyjnie po zakończeniu uruchomionego zadania. Jeśli konto nie ma żadnych jednostek zarezerwowanych aprowizowanych, zadania będą pobierane kolejno. W takim przypadku czas oczekiwania między jednym zakończeniem zadania a następnym uruchomieniem będzie zależeć od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybór między różnymi typami jednostek zarezerwowanych

Poniższa tabela ułatwia podejmowanie decyzji przy wyborze między różnymi szybkościami kodowania. Zawiera również kilka przypadków porównawczych na [filmie, który można pobrać,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) aby wykonać własne testy:

|Typ RU|Scenariusz|Przykładowe wyniki [wideo 7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kodowanie pojedynczej szybkości transmisji bitów. <br/>Pliki w rozdzielczości SD lub poniżej, nie wrażliwe na czas, niskie koszty.|Kodowanie do pojedynczego pliku MP4 o rozdzielczości bitrate SD za pomocą "H264 Single Bitrate SD 16x9" trwa około 7 minut.|
| **S2**|Kodowanie pojedynczej szybkości transmisji bitów i wielu bitrate.<br/>Normalne użycie zarówno kodowania SD, jak i HD.|Kodowanie z predefiniowane "H264 Single Bitrate 720p" trwa około 6 minut.<br/><br/>Kodowanie z ustawieniem predefiniowane "H264 Multiple Bitrate 720p" trwa około 12 minut.|
| **S3**|Kodowanie pojedynczej szybkości transmisji bitów i wielu bitrate.<br/>Filmy w rozdzielczości Full HD i 4K. Kodowanie zależne od czasu i szybsze.|Kodowanie z predefiniowaniem "H264 Single Bitrate 1080p" trwa około 3 minut.<br/><br/>Kodowanie z ustawieniem predefiniowane "H264 Multiple Bitrate 1080p" trwa około 8 minut.|

## <a name="considerations"></a>Zagadnienia do rozważenia

* W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services w wersji 3 lub indeksatora wideo, zdecydowanie zaleca się typ jednostki S3.
* Jeśli używasz puli udostępnionej, oznacza to, że bez żadnych jednostek zarezerwowanych, następnie zadania kodowania mają taką samą wydajność jak w przypadku jednostek RUs S1. Jednak nie ma górnej granicy czasu zadania można spędzić w stanie kolejki, a w danym momencie, co najwyżej tylko jedno zadanie będzie uruchomiony.

W dalszej części artykułu pokazano, jak używać [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) do skalowania mru.

> [!NOTE]
> Zdecydowanie zaleca się, aby w przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services wersji 3 lub Video Indexer, aby przeprowadzać aprowizację konta przy użyciu 10 jednostek MRU typu S3. Jeśli potrzebujesz więcej niż 10 mru S3, otwórz bilet pomocy technicznej za pomocą [witryny Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skalowanie jednostek zarezerwowanych multimediów z interfejsem wiersza polecenia

Uruchom polecenie `mru`.

Następujące [polecenie mru az ams account](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) ustawia jednostki zarezerwowane media na koncie "amsaccount" przy użyciu parametrów **zliczania** i **typu.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, przez którą jednostki zarezerwowane multimediów są aprowizowane na twoim koncie. Dzieje się tak niezależnie od tego, czy na twoim koncie są uruchomione zadania. Szczegółowe informacje można znaleźć w sekcji Często zadawane pytania na stronie [cennik usługi Media Services.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Następny krok

[Analizowanie plików wideo](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz też

* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
