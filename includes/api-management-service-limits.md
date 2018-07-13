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
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756109"
---
| Zasób | Limit |
| --- | --- |
| Jednostki skalowania | 10 na region<sup>1</sup> |
| Pamięć podręczna | 5 GB na jednostkę<sup>1</sup> |
| Połączenia współbieżne zaplecza<sup>2</sup> na urząd HTTP | 2048 jednostkę<sup>3</sup> |
| Rozmiar maksymalny odpowiedzi z pamięci podręcznej | 10MB |
| Zasady maksymalnego rozmiaru dokumentu | 256KB |
| Maksymalna bram domen | 20 za wystąpienie usługi<sup>4</sup> |


<sup>1</sup>limitów interfejsu API zarządzania różnią się dla każdej warstwy cenowej. Aby zobaczyć ceny warstwy i ich limitów skalowania przejdź do [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> połączenia są w puli i ponownego użycia, chyba że jawnie zamknięty przez zaplecze.
<sup>3</sup> jednostkę warstwy podstawowa, standardowa i Premium. Warstwa Deweloper przeznaczona jest ograniczona do 1024.
<sup>4</sup> dostępne w tylko w warstwie Premium.


