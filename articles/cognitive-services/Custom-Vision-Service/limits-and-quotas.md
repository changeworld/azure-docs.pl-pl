---
title: Limity i przydziały — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się o limity i przydziały dla usługi Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 9cff5fdac39be2338305cd37a4b2328a28a48255
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269257"
---
# <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia

Istnieją dwie warstwy kluczy dla usługi Custom Vision. Możesz zarejestrować się F0 (wersja bezpłatna) lub subskrypcji (standardowa) S0 za pośrednictwem witryny Azure portal. Zobacz odpowiednich [stronie cennika usługi Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) szczegółowe informacje na temat cen i transakcji.

Liczba uczone obrazy na projekt i tagi dla poszczególnych projektów powinny rosną z upływem czasu dla projektów S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projekty|2|100|
|Uczone obrazy na projekt |5,000|100,000|
|Prognozy / miesiąc|10 000 |Nieograniczona liczba|
|Tagi / project|50|500|
|Liczba iteracji |10|10|
|Min etykietą obrazy na Tag klasyfikacji (ponad 50 zalecane) |5|5|
|Min etykietą obrazy na Tag, wykrywanie obiektów (ponad 50 zalecane)|15|15|
|Jak długo są przechowywane obrazy prognoz|30 dni|30 dni|
|[Prognozowanie](https://go.microsoft.com/fwlink/?linkid=865445) operacji przy użyciu magazynu (transakcje na sekundę)|2|10|
|[Prognozowanie](https://go.microsoft.com/fwlink/?linkid=865445) operacji bez użycia formatu przechowywania (transakcje na sekundę)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (liczba wywołań interfejsu API na sekundę)|2|10|
|[Inne wywołania interfejsu API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcje na sekundę)|10|10|
|Maksymalny rozmiar obrazu (szkolenie przekazywania obrazów) |6 MB|6 MB|
|Maksymalny rozmiar obrazu (Prognozowane)|4 MB|4 MB|
|Maksymalna liczba obszarów dla każdej obraz szkolenia wykrywania obiektów|200|200|
|Maksymalna liczba tagów na klasyfikacji obrazów|30|30|
