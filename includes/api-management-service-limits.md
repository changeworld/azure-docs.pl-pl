---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 676bd3b3a369e3f834016f626a8e69f6b41d9b23
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170704"
---
| Zasób | Limit |
| --- | --- |
| Jednostki skalowania | 10 na region<sup>1</sup> |
| Pamięć podręczna | 5 GB na jednostkę<sup>1</sup> |
| Połączenia współbieżne zaplecza<sup>2</sup> na urząd HTTP | 2048 jednostkę<sup>3</sup> |
| Rozmiar maksymalny odpowiedzi z pamięci podręcznej | 2MB |
| Zasady maksymalnego rozmiaru dokumentu | 256KB |
| Maksymalna bram domen | 20 za wystąpienie usługi<sup>4</sup> |


<sup>1</sup>limitów interfejsu API zarządzania różnią się dla każdej warstwy cenowej. Aby zobaczyć ceny warstwy i ich limitów skalowania przejdź do [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> połączenia są w puli i ponownego użycia, chyba że jawnie zamknięty przez zaplecze.
<sup>3</sup> jednostkę warstwy podstawowa, standardowa i Premium. Warstwa Deweloper przeznaczona jest ograniczona do 1024.
<sup>4</sup> dostępne w tylko w warstwie Premium.


