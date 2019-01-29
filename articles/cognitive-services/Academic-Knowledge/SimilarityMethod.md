---
title: Metoda podobieństwa — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Metoda podobieństwa do obliczania akademickich podobieństwa dwóch ciągów.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 7484b570784f5f058ebd23b1e3c225c5d858a274
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183343"
---
# <a name="similarity-method"></a>Metoda podobieństwa

**Podobieństwa** interfejsu API REST jest używane do obliczania akademickich podobieństwa między dwa ciągi. 
<br>

**Punkt końcowy REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametry żądania
Parametr        |Typ danych      |Wymagany | Opis
----------|----------|----------|------------
**s1**        |Ciąg   |Yes  |Ciąg * do porównania
**s2**        |Ciąg   |Yes  |Ciąg * do porównania
<sub> * Ciągi do porównania ma długość maksymalna to 1MB. </sub>
<br>
## <a name="response"></a>Odpowiedź
Name | Opis
--------|---------
**SimilarityScore**        |Zmiennoprzecinkowa wartość reprezentującą podobieństwa cosinus w wersjach s1 i s2, za pomocą wartości bliższe 1.0 znaczenia więcej podobne i wartości bliżej od – 1,0 oznacza mniej
<br>

## <a name="successerror-conditions"></a>Powodzenie/warunków błędów
Stan HTTP | Przyczyna | Odpowiedź
-----------|----------|--------
**200**         |Powodzenie | Liczba zmiennoprzecinkowa
**400**         | Nieprawidłowe żądanie lub nieprawidłowe żądanie | Komunikat o błędzie      
**500**         |Wewnętrzny błąd serwera | Komunikat o błędzie
**Upłynął limit czasu**     | Upłynął limit czasu żądania.  | Komunikat o błędzie
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Przykład: Oblicz podobieństwa dwóch streszczenia częściowe
#### <a name="request"></a>Żądanie:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
W tym przykładzie generowany wynik podobieństwa między dwa streszczenia częściowe za pomocą **podobieństwa** interfejsu API.
#### <a name="response"></a>Odpowiedź:
```
0.520
```
#### <a name="remarks"></a>Uwagi:
Wynik podobieństwa jest określany przez ocenę akademickich pojęcia przez osadzanie programu word. W tym przykładzie 0.52 oznacza, że dwa streszczenia częściowe są nieco podobne.
<br>
