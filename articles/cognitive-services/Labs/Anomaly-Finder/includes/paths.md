---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309534"
---
<a name="paths"></a>
## <a name="paths"></a>Ścieżki

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Wykrywanie anomalii punktów dla punktów danych serii czasu żądane
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametry

|Type|Name (Nazwa)|Opis|Schemat|
|---|---|---|---|
|**Treść**|**body**  <br>*Wymagane*|Szeregi czasowe punktów danych i okres, w razie potrzeby.|[request](#request)|


#### <a name="responses"></a>Odpowiedzi

|Kod HTTP|Opis|Schemat|
|---|---|---|
|**200**|Powodzenie operacji.|< [odpowiedź](#response) > tablicy|
|**400**|Nie można przeanalizować żądania JSON.|Brak zawartości|
|**403**|Podany certyfikat nie jest akceptowane przez serwer.|Brak zawartości|
|**405**|Metoda nie jest dozwolona.|Brak zawartości|
|**500**|Wewnętrzny błąd serwera.|Brak zawartości|


#### <a name="consumes"></a>Wykorzystuje

* `application/json`


#### <a name="produces"></a>Tworzy

* `application/json`


#### <a name="tags"></a>Tagi

* anomalydetection



