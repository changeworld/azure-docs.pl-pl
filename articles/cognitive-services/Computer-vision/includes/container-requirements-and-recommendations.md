---
title: Kontener wymagania i zalecenia
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129815"
---
> [!NOTE]
> Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków.

#### <a name="readtabread"></a>[Odczyt](#tab/read)

W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera odczytu.

| Kontener | Minimalne | Zalecane |TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
| Odczyt | 1 rdzenie, 8 GB pamięci, 0,24 TPS | 8 rdzeni, 16 GB pamięci, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Rozpoznawanie tekstu](#tab/recognize-text)

W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera Rozpoznawanie tekstu.

| Kontener | Minimalne | Zalecane |TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
| Rozpoznawanie tekstu | 1 rdzeń, 8 GB pamięci, 0,12 TPS | 8 rdzeni, 16 GB pamięci, 0,60 TPS | 0,12, 0,60 |

***

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę.

Rdzeń i pamięć odpowiadają `--cpus` ustawieniom i `--memory` , które są `docker run` używane jako część polecenia.
