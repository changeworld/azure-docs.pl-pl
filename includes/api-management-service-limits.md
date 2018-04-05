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
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
| Zasób | Limit |
| --- | --- |
| Jednostki skalowania | 10 dla regionu<sup>1</sup> |
| Pamięć podręczna | 5 GB na jednostkę<sup>1</sup> |
| Zaplecza równoczesnych połączeń<sup>2</sup> na urzędu HTTP | 2048 na jednostkę<sup>3</sup> |
| Rozmiar maksymalny buforowanej odpowiedzi | 10MB |
| Maksymalna bram domen | 20 dla każdego wystąpienia usługi<sup>4</sup> |


<sup>1</sup>limity interfejsu API zarządzania są różne dla każdej warstwy cenowej. Aby wyświetlić ceny warstw i limity ich skalowania przejdź do [interfejsu API zarządzania cennik](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> połączenia są w puli i ponownego użycia, chyba że jawnie zamknięte przez wewnętrznej bazy danych.
<sup>3</sup> na jednostkę warstw podstawowa, standardowa i Premium. Warstwa dewelopera jest ograniczony do 1024.
<sup>4</sup> dostępne tylko w warstwie Premium.


