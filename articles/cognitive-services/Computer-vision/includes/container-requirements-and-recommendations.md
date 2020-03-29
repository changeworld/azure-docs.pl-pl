---
title: Wymagania i zalecenia dotyczące kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481766"
---
> [!NOTE]
> Wymagania i zalecenia są oparte na testach porównawczych z jednym żądaniem na sekundę, przy użyciu obrazu o przekątnej 8 MB zeskanowanej listu biznesowego, który zawiera 29 wierszy i łącznie 803 znaki.

W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera Odczytu.

| Kontener | Minimalne | Zalecane |Tps<br>(Minimum, Maksimum)|
|-----------|---------|-------------|--|
| Odczyt | 1 rdzenie, pamięć 8 GB, 0,24 TPS | 8 rdzeni, 16 GB pamięci, 1,17 TPS | 0.24, 1.17 |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub więcej.
* TPS - transakcje na sekundę.

Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.
