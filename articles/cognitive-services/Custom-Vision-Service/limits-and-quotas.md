---
title: Limity i przydziały — usługa Custom Vision Service
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano różne typy kluczy licencjonowania oraz limity i przydziały dla usługi Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081781"
---
# <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia

Istnieją dwie warstwy kluczy dla usługi Custom Vision. Możesz zarejestrować się w celu utworzenia subskrypcji F0 (bezpłatna) lub S0 (standardowa) za pośrednictwem witryny Azure portal. Szczegółowe informacje na temat cen i transakcji można znaleźć na odpowiedniej [stronie cen usług Cognitive Services.](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)

Oczekuje się, że liczba obrazów szkoleniowych na projekt i tagi na projekt będzie rosła wraz z czasem dla projektów S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projekty|2|100|
|Obrazy szkoleniowe na projekt |5000|100 000|
|Prognozy / miesiąc|10 000 |Unlimited (nieograniczony)|
|Tagi / projekt|50|500|
|Iteracji |10|10|
|Min oznaczone obrazy na tag, Klasyfikacja (50+ zalecane) |5|5|
|Min oznaczone obrazy na tag, wykrywanie obiektów (50+ zalecane)|15|15|
|Czas przechowywania obrazów przewidywania|30 dni|30 dni|
|[Operacje przewidywania](https://go.microsoft.com/fwlink/?linkid=865445) z magazynem (transakcje na sekundę)|2|10|
|[Operacje przewidywania](https://go.microsoft.com/fwlink/?linkid=865445) bez magazynu (transakcje na sekundę)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (wywołania interfejsu API na sekundę)|2|10|
|[Inne wywołania interfejsu API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcje na sekundę)|10|10|
|Akceptowane typy obrazów|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Minimalna wysokość/szerokość obrazu w pikselach|256 (patrz uwaga)|256 (patrz uwaga)|
|Maksymalna wysokość/szerokość obrazu w pikselach|bez ograniczeń|bez ograniczeń|
|Maksymalny rozmiar obrazu (przesyłanie obrazu szkoleniowego) |6 MB|6 MB|
|Maksymalny rozmiar obrazu (przewidywanie)|4 MB|4 MB|
|Maksymalna liczba regionów na obraz szkolenia wykrywania obiektów|300|300|
|Maksymalna liczba znaczników na obraz klasyfikacji|100|100|

> [!NOTE]
> Obrazy o rozmiarze mniejszym niż 256 pikseli będą akceptowane, ale skalowane.
