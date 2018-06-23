---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348172"
---
<a name="paths"></a>
## <a name="paths"></a>Ścieżki

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Wykrywanie anomalii punktów raz punktów danych serii żądanie
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametry

|Typ|Name (Nazwa)|Opis|Schemat|
|---|---|---|---|
|**Treść**|**body**  <br>*Wymagane*|Szeregów czasowych punktów danych i okres, w razie potrzeby.|[Żądanie](#request)|


#### <a name="responses"></a>Odpowiedzi

|Kod HTTP|Opis|Schemat|
|---|---|---|
|**200**|Powodzenie operacji.|< [odpowiedź](#response) > tablicy|
|**400**|Nie można przeanalizować żądania JSON.|Brak zawartości|
|**403**|Podany certyfikat nie jest akceptowane przez serwer.|Brak zawartości|
|**405**|Metoda nie jest dozwolone.|Brak zawartości|
|**500**|Wewnętrzny błąd serwera.|Brak zawartości|


#### <a name="consumes"></a>Wykorzystuje

* `application/json`


#### <a name="produces"></a>Tworzy

* `application/json`


#### <a name="tags"></a>Tagi

* anomalydetection



