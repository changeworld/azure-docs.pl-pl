---
title: Wymagania i zalecenia dotyczące kontenera
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481766"
---
> [!NOTE]
> Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków.

W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera odczytu.

| Kontener | Minimalne | Zalecane |TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
| Odczytywanie | 1 rdzenie, 8 GB pamięci, 0,24 TPS | 8 rdzeni, 16 GB pamięci, 1,17 TPS | 0,24, 1,17 |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę.

Rdzeń i pamięć odpowiadają ustawieniom `--cpus` i `--memory`, które są używane jako część polecenia `docker run`.
