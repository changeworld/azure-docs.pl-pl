---
title: Użyj interfejsu wiersza polecenia, aby skalować jednostki zarezerwowane multimediów — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skalowanie przetwarzania multimediów z użyciem usługi Azure Media Services za pomocą interfejsu wiersza polecenia.
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
ms.date: 03/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0dcfa4e7cd792f61d1620a57330f87c5c86e6c9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58915699"
---
# <a name="scaling-media-processing"></a>Skalowanie przetwarzania multimediów

Usługa Azure Media Services umożliwia skalowanie przetwarzania multimediów w ramach konta dzięki funkcji zarządzania jednostkami zarezerwowanymi multimediów (MRU, Media Reserved Units). Lokalizacje MRU określają szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. 

Oprócz określenia typu jednostki zarezerwowanej, można określić aprowizację swojego konta przy użyciu jednostek zarezerwowanych. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostek zarezerwowanych, a następnie pięć zadań będą uruchomione jednocześnie tak długo, jak istnieją zadania do przetworzenia. Pozostałe zadania będzie oczekiwać w kolejce i będzie pobrać wybrany do przetwarzania sekwencyjnie, po zakończeniu bieżące zadanie. Jeśli konto nie ma żadnych jednostek zarezerwowanych zainicjowano obsługę administracyjną, następnie zadania zostaną pobrane po kolei. W tym przypadku czas oczekiwania między jedno zadanie zostało ukończone, a następnie bazujący będzie zależeć od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybieranie między różnych zastrzeżonych typów jednostek

Poniższa tabela pomaga Ci w podjęciu decyzji, wybierając między różne szybkości kodowania. Zawiera także kilka przypadków testów porównawczych na [film wideo, który można pobrać](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) przeprowadzić własne testy:

|Typ jednostek|Scenariusz|Przykładowe wyniki dla [7 min 1080 p wideo](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Pojedyncza szybkość transmisji bitów kodowania. <br/>Pliki SD większa niż rozwiązania, nie raz poufnych, niskich kosztów.|Kodowanie do pojedynczego szybkości transmisji bitów SD rozpoznawania plik MP4 przy użyciu "Jednej transmisji bitów H264 SD 16 x 9" Trwa 10 minut.|
| **S2**|Pojedyncza szybkość transmisji bitów i wielu kodowanie szybkości transmisji bitów.<br/>Normalnego użycia na potrzeby SD i HD kodowania.|Za pomocą "Pojedynczy szybkość transmisji bitów H264 720p" ustawienie wstępne kodowania zajmuje około 8 minut.<br/><br/>Kodowanie za pomocą "H264 szybkość transmisji bitów h264 720p" ustawienie wstępne zajmuje około 16,8 minut.|
| **S3**|Pojedyncza szybkość transmisji bitów i wielu kodowanie szybkości transmisji bitów.<br/>Pełne HD i 4K rozdzielczości wideo. Czas przetwarzania poufnych, szybsze kodowanie.|Za pomocą "Pojedynczy szybkość transmisji bitów H264 1080p" ustawienie wstępne kodowania zajmuje około 4 minuty.<br/><br/>Kodowanie za pomocą "H264 szybkość transmisji bitów h264 1080p" ustawienie wstępne zajmuje około 8 minut.|

## <a name="considerations"></a>Zagadnienia do rozważenia

* Dla zadania analiza Audio i wideo w analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer zdecydowanie zaleca się typ jednostki S3.
* Jeśli z udostępnionej puli, oznacza to, bez żadnych jednostek zarezerwowanych następnie zadań Koduj mieć ta sama wydajność podobnie jak w przypadku jednostek zarezerwowanych S1. Jednak istnieje nie górną granicę do chwili, gdy zadania podrzędne mogą poświęcać w stanie umieszczenia w kolejce i w dowolnym momencie, będzie uruchamiany co najwyżej tylko jedno zadanie.

W pozostałej części artykułu pokazuje, jak używać [interfejsu wiersza polecenia usługi Media Services v3](https://aka.ms/ams-v3-cli-ref) skalowania lokalizacje MRU.

> [!NOTE]
> Zdecydowanie zaleca się, aby w przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services wersji 3 lub Video Indexer, aby przeprowadzać aprowizację konta przy użyciu 10 jednostek MRU typu S3. Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).
>
> Obecnie nie można użyć witryny Azure portal do zarządzania zasobami innych v3. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](developers-guide.md).

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Zarezerwowane multimediów skalowania jednostek przy użyciu interfejsu wiersza polecenia

Uruchom polecenie `mru`.

Następujące [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) zestawy jednostek zarezerwowanych multimediów na "amsaccount" konta przy użyciu polecenia **liczba** i **typu** parametrów.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Rozliczenia

Możesz są naliczane na podstawie liczby minut działania aprowizowanych jednostek zarezerwowanych multimediów w ramach Twojego konta. Dzieje się tak niezależnie od czy istnieją wszystkie zadania uruchomione w ramach Twojego konta. Aby uzyskać szczegółowy opis, zobacz sekcję Często zadawane pytania dotyczące [cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="next-step"></a>Następny krok

[Analizowanie plików wideo](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
