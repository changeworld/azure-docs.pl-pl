---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: fb02496d9c107a2c21acca6c65ef69fdfceb4597
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904655"
---
<a name="paths"></a>
## <a name="paths"></a>Ścieżki

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Wykrywanie anomalii punktów dla punktów danych serii czasu żądane
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametry

|Typ|Name (Nazwa)|Opis|Schemat|
|---|---|---|---|
|**Treść**|**body**  <br>*Wymagane*|Szeregi czasowe punktów danych i okres, w razie potrzeby.|[Żądanie](#request)|


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



