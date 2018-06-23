---
title: Limity i przydziały niestandardowe wizji usługi - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o limity i przydziału usługi Azure Cognitives usług niestandardowe wizji.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349320"
---
# <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia

Istnieją trzy warstwy kluczy dla usługi wizji niestandardowe. F0 i S0 zasoby są otrzymywane za pośrednictwem portalu Azure. Szczegóły dotyczące definicji ceny i transakcje są na [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  Projekty F0 można uaktualnić do projektów S0.

Ograniczone zasoby projektu wersji próbnej są dołączone do logowanie wizji niestandardowe (czyli konta usługi AAD lub konta MSA.) Są one przeznaczone do użycia w przypadku krótkich wersji próbnej usługi.  Kont utworzonych wczesne bezpłatnej wersji zapoznawczej, przed wprowadzeniem wersji zapoznawczych platformy Azure (1 marca 2018), zachowują ich poprzednich przydziały prób ograniczone. 

||**Ograniczona wersja próbna**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projekty|2|2|100|
|Obrazy szkolenia na projekt|5000|5000|50,000|
|Operacje przewidywania dla / miesiąc|10 000 |10 000|Nieograniczona liczba|
|Znaczniki / project|50|50|250|
|Liczba iteracji |10|10|10|
|Co najmniej z etykietą obrazów na Tag, klasyfikacji (50 + zalecane) |5|5|5|
|Co najmniej z etykietą obrazów na Tag, wykrywania obiektu (50 + zalecane)|15|15|15|
|Jak długo są przechowywane obrazy prognozowania|30 dni|30 dni|30 dni|
|[Prognozowanie](https://go.microsoft.com/fwlink/?linkid=865445) operacje przy użyciu magazynu (transakcje na sekundę)|2|2|10|
|[Prognozowanie](https://go.microsoft.com/fwlink/?linkid=865445) operacji bez magazynu (transakcje na sekundę)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (na sekundę wywołania interfejsu API)|2|2|10|
|[Inne wywołania interfejsu API](https://go.microsoft.com/fwlink/?linkid=865446) (transakcje na sekundę)|10|10|10|
|Maksymalny rozmiar obrazu (przesyłanie obrazu szkolenia) |6MB|6MB|6MB|
|Maksymalny rozmiar obrazu (Prognozowane)|4MB|4MB|4MB|

Ograniczenia dotyczące *# szkolenia obrazów na projekt* i *# znaczników lub projekt* powinny być zwiększeniu z upływem czasu dla projektów S0. 
