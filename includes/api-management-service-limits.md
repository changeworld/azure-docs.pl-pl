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
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31434943"
---
| Zasób | Limit |
| --- | --- |
| Jednostki skalowania | 10 dla regionu<sup>1</sup> |
| Pamięć podręczna | 5 GB na jednostkę<sup>1</sup> |
| Zaplecza równoczesnych połączeń<sup>2</sup> na urzędu HTTP | 2048 na jednostkę<sup>3</sup> |
| Rozmiar maksymalny buforowanej odpowiedzi | 10MB |
| Zasady maksymalny rozmiar dokumentu | 256KB |
| Maksymalna bram domen | 20 dla każdego wystąpienia usługi<sup>4</sup> |


<sup>1</sup>limity interfejsu API zarządzania są różne dla każdej warstwy cenowej. Aby wyświetlić ceny warstw i limity ich skalowania przejdź do [interfejsu API zarządzania cennik](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> połączenia są w puli i ponownego użycia, chyba że jawnie zamknięte przez wewnętrznej bazy danych.
<sup>3</sup> na jednostkę warstw podstawowa, standardowa i Premium. Warstwa dewelopera jest ograniczony do 1024.
<sup>4</sup> dostępne tylko w warstwie Premium.


