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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67183634"
---
| Zasób | Limit |
| --- | --- |
| Maksymalna liczba jednostek skalowania | 10 na region<sup>1</sup> |
| Rozmiar pamięci podręcznej | 5 GB na jednostkę<sup>2</sup> |
| Współbieżne połączenia zaplecza<sup>3</sup> na urząd http | 2 048 na jednostkę<sup>4</sup> |
| Maksymalny rozmiar buforowanej odpowiedzi | 2 MB |
| Maksymalny rozmiar dokumentu zasad | 256 KB<sup>5</sup> | 
| Maksymalna liczba niestandardowych domen bramy na wystąpienie usługi<sup>6</sup> | 20 |
| Maksymalna liczba certyfikatów urzędu certyfikacji na wystąpienie usługi | 10 | 
| Maksymalna liczba wystąpień usługi na subskrypcję<sup>7</sup> | 20 | 
| Maksymalna liczba subskrypcji na wystąpienie usługi<sup>7</sup> | 500 |
| Maksymalna liczba certyfikatów klienta na wystąpienie usługi<sup>7</sup> | 50 | 
| Maksymalna liczba interfejsów API na wystąpienie usługi<sup>7</sup> | 50 | 
| Maksymalna liczba operacji interfejsu API na wystąpienie usługi<sup>7</sup> | 1000 | 
| Maksymalny łączny czas trwania żądania<sup>7</sup> | 30 sekund | 
| Maksymalny rozmiar zbuforowanego ładunku<sup>7</sup> | 2 MB | 


<sup>1</sup> Limity skalowania zależą od warstwy cenowej. Aby sprawdzić warstwy cenowe i ich limity skalowania, zobacz [cennik API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Rozmiar pamięci podręcznej na jednostkę zależy od warstwy cenowej. Aby sprawdzić warstwy cenowe i ich limity skalowania, zobacz [cennik API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Połączenia są w puli i ponownie używane, chyba że zostały jawnie zamknięte przez zaplecze.<br/>
<sup>4</sup> Ten limit jest na jednostkę warstw Podstawowa, standardowa i Premium. Warstwa dewelopera jest ograniczona do 1 024. Ten limit nie dotyczy warstwy zużycia.<br/> 
<sup>5</sup>ten limit dotyczy warstw Podstawowa, standardowa i Premium. W warstwie zużycia rozmiar dokumentu zasad jest ograniczony do 4 KB.<br/>
<sup>6</sup> Ten zasób jest dostępny tylko w warstwie Premium.<br/>
<sup>7</sup> Ten zasób ma zastosowanie tylko do warstwy zużycia.<br/>



