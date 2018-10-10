---
title: Limity i przydziały — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się o limity i przydziały dla usługi Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: ce06effbce12abb6271e050829d3218f4fbbfbf4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902864"
---
# <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia

Istnieją trzy warstwy kluczy dla usługi Custom Vision Service. Zasoby f0 i S0 uzyskuje się za pośrednictwem witryny Azure portal. Szczegółowe informacje na temat cen i transakcje definicje znajdują się na [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  Projekty F0 można uaktualnić do projektów S0.

Ograniczone zasoby projektu okres próbny są dołączone do swoich danych logowania Custom Vision (czyli konta usługi AAD lub konto MSA.) Są one przeznaczone do użycia w przypadku krótkich wersji próbnej usługi.  Kont utworzonych podczas wczesnych bezpłatnej wersji zapoznawczej przed wprowadzeniem wersji zapoznawczych systemu Azure (1 marca 2018 r.), zostaną zachowane ich poprzednich przydziały w przypadku ograniczonej wersji próbnej. 

||**Ograniczona wersja próbna**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projekty|2|2|100|
|Uczone obrazy na projekt i klasyfikacji|5000|5000|50,000|
|Uczone obrazy na projekt, wykrywanie obiektów|5000|5000|10 000|
|Prognozy / miesiąc|10 000 |10 000|Nieograniczona liczba|
|Tagi / project|50|50|250|
|Liczba iteracji |10|10|10|
|Co najmniej etykietą obrazy na Tag klasyfikacji (ponad 50 zalecane) |5|5|5|
|Co najmniej etykietą obrazy na Tag, wykrywanie obiektów (ponad 50 zalecane)|15|15|15|
|Jak długo są przechowywane obrazy prognoz|30 dni|30 dni|30 dni|
|[Prognozowanie](https://go.microsoft.com/fwlink/?linkid=865445) operacji przy użyciu magazynu (transakcje na sekundę)|2|2|10|
|[Prognozowanie](https://go.microsoft.com/fwlink/?linkid=865445) operacji bez użycia formatu przechowywania (transakcje na sekundę)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (liczba wywołań interfejsu API na sekundę)|2|2|10|
|[Inne wywołania interfejsu API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcje na sekundę)|10|10|10|
|Maksymalny rozmiar obrazu (szkolenie przekazywania obrazów) |6MB|6MB|6MB|
|Maksymalny rozmiar obrazu (Prognozowane)|4MB|4MB|4MB|

Ograniczenia dotyczące *# uczone obrazy na projekt* i *# tagów lub projekt docelowy* powinny zostać zwiększona wraz z upływem czasu dla projektów S0. 
