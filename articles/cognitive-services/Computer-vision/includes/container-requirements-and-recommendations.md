---
title: Wymagania i zalecenia dotyczące kontenera.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034471"
---
W poniższej tabeli opisano minimalną i zalecaną liczbę rdzeni procesora CPU oraz ilość pamięci do przydzielenia dla każdego kontenera Rozpoznawanie tekstu.

| Kontener | Minimalne | Zalecane |TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|Rozpoznawanie tekstu|1 rdzeń, 8 GB pamięci, 0,5 TPS|2 rdzenie, 8 GB pamięci, 1 TPS|0,5, 1|

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę

Rdzeń i pamięć odpowiadają `--cpus` ustawieniom i `--memory` , które są `docker run` używane jako część polecenia.