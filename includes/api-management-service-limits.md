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
ms.openlocfilehash: e01eebe41010135d0dc0a2cb4170e6b6687ff546
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292701"
---
| Zasób | Limit |
| --- | --- |
| Maksymalna liczba jednostek skali | 10 na region<sup>1</sup> |
| Rozmiar pamięci podręcznej | 5 GB na jednostkę<sup>2</sup> |
| Połączenia współbieżne zaplecza<sup>3</sup> na urząd HTTP | 2048 jednostkę<sup>4</sup> |
| Rozmiar maksymalny odpowiedzi z pamięci podręcznej | 2MB |
| Zasady maksymalnego rozmiaru dokumentu | 256KB<sup>5</sup> | 
| Maksymalna bram domen na wystąpienie usługi<sup>6</sup> | 20 | 
| Maksymalna liczba wystąpień usługi na subskrypcję<sup>7</sup> | 5 | 
| Maksymalna liczba subskrypcji na wystąpienie usługi<sup>7</sup> | 500 |
| Maksymalna liczba certyfikatów klienta dla każdego wystąpienia usługi<sup>7</sup> | 50 | 
| Maksymalna liczba interfejsów API na wystąpienie usługi<sup>7</sup> | 50 | 
| Maksymalna liczba operacji interfejsu API na wystąpienie usługi<sup>7</sup> | 1000 | 
| Maksymalny całkowity czas trwania żądania<sup>7</sup> | 30 sekund | 
| Maksymalna buforowane rozmiar ładunku<sup>7</sup> | 2MB | 


<sup>1</sup> limity skalowania zależy od warstwy cenowej. Aby zobaczyć ceny warstwy i ich limitów skalowania przejdź do [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> na jednostkę w pamięci podręcznej rozmiar zależy od warstwy cenowej. Aby zobaczyć ceny warstwy i ich limitów skalowania przejdź do [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> połączenia są w puli i ponownego użycia, chyba że jawnie zamknięty przez zaplecze.<br/>
<sup>4</sup> jednostkę warstwy podstawowa, standardowa i Premium. Warstwa Deweloper przeznaczona jest ograniczona do 1024. Nie ma zastosowania do warstwy zużycia.<br/> 
<sup>5</sup> warstw w podstawowa, standardowa i Premium. Zużycia warstwy o rozmiarze dokumentu zasad jest ograniczony do 4KB.<br/>
<sup>6</sup> dostępne w ramach warstwy Premium.<br/>
<sup>7</sup> dotyczy tylko warstwa zużycie.<br/>



