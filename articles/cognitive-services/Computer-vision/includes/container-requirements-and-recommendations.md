---
title: Wymagania i zalecenia dotyczące kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877982"
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
