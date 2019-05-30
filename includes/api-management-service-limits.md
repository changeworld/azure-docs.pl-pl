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
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238632"
---
| Resource | Limit |
| --- | --- |
| Maksymalna liczba jednostek skali | 10 na region<sup>1</sup> |
| Rozmiar pamięci podręcznej | 5 GB na jednostkę<sup>2</sup> |
| Równoczesnych połączeń zaplecza<sup>3</sup> na urząd HTTP | 2048 jednostkę<sup>4</sup> |
| Rozmiar maksymalny odpowiedzi z pamięci podręcznej | 2 MB |
| Zasady maksymalnego rozmiaru dokumentu | 256 KB<sup>5</sup> | 
| Maksymalna bram domen na wystąpienie usługi<sup>6</sup> | 20 |
| Maksymalna liczba certyfikatów urzędu certyfikacji na wystąpienie usługi | 10 | 
| Maksymalna liczba wystąpień usługi na subskrypcję<sup>7</sup> | 20 | 
| Maksymalna liczba subskrypcji na wystąpienie usługi<sup>7</sup> | 500 |
| Maksymalna liczba certyfikatów klienta dla każdego wystąpienia usługi<sup>7</sup> | 50 | 
| Maksymalna liczba interfejsów API na wystąpienie usługi<sup>7</sup> | 50 | 
| Maksymalna liczba operacji interfejsu API na wystąpienie usługi<sup>7</sup> | 1000 | 
| Maksymalny całkowity czas trwania żądania<sup>7</sup> | 30 sekund | 
| Maksymalna buforowane rozmiar ładunku<sup>7</sup> | 2 MB | 


<sup>1</sup>limity skalowania zależy od warstwy cenowej. Aby wyświetlić warstw cenowych i ich limitów skalowania, zobacz [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>na jednostkę w pamięci podręcznej rozmiar zależy od warstwy cenowej. Aby wyświetlić warstw cenowych i ich limitów skalowania, zobacz [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>połączeń w puli i ponownie, chyba że jawnie zamknięty przez zaplecze.<br/>
<sup>4</sup>to ograniczenie jest jednostkę warstwy podstawowa, standardowa i Premium. Warstwa Deweloper przeznaczona jest ograniczona do 1024. To ograniczenie nie dotyczy warstwa zużycie.<br/> 
<sup>5</sup>ten limit dotyczy warstw Basic, Standard i Premium. W warstwie użycie rozmiaru dokumentu zasad jest ograniczony do 4 KB.<br/>
<sup>6</sup>ten zasób był dostępny w ramach warstwy Premium.<br/>
<sup>7</sup>ten zasób ma zastosowanie do użycia tylko w warstwie.<br/>



