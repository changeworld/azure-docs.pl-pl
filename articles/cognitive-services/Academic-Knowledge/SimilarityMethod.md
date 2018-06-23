---
title: Metoda podobieństwa w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Metoda podobieństwa do obliczenia academic podobieństwa dwa ciągi w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 472498d6bfe06ae4477a30f892d44e79c901acf5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346880"
---
# <a name="similarity-method"></a>Podobieństwa — metoda

**Podobieństwa** interfejsu API REST jest używane do obliczania academic podobieństwa między dwa ciągi. 
<br>

**Punkt końcowy REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametry żądania
Parametr        |Typ danych      |Wymagane | Opis
----------|----------|----------|------------
**S1**        |Ciąg   |Yes  |Ciąg * do porównania
**S2**        |Ciąg   |Yes  |Ciąg * do porównania
<sub> * Ciągi do porównania są maksymalną dopuszczalną długość równą 1MB. </sub>
<br>
## <a name="response"></a>Odpowiedź
Name (Nazwa) | Opis
--------|---------
**SimilarityScore**        |Zmiennoprzecinkowa wartość reprezentującą podobieństwa cosinus s1 i s2 z wartości bliższe 1.0 znaczenie więcej podobne i wartości bliższe -1,0 oznacza mniejsza
<br>

## <a name="successerror-conditions"></a>Powodzenie lub błąd warunków
Stan HTTP | Przyczyna | Odpowiedź
-----------|----------|--------
**200**         |Powodzenie | Liczba zmiennoprzecinkowa
**400**         | Nieprawidłowe żądanie lub nieprawidłowe żądanie | Komunikat o błędzie      
**500**         |Wewnętrzny błąd serwera | Komunikat o błędzie
**Upłynął limit czasu**     | Upłynął limit czasu żądania.  | Komunikat o błędzie
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Przykład: Obliczenia podobieństwa dwóch streszczenia częściowe
#### <a name="request"></a>Żądanie:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
W tym przykładzie mamy Generowanie wynik podobieństwa między dwa streszczenia częściowe przy użyciu **podobieństwa** interfejsu API.
#### <a name="response"></a>Odpowiedź:
```
0.520
```
#### <a name="remarks"></a>Uwagi:
Wynik podobieństwa jest określany przez ocenę academic pojęcia za pośrednictwem osadzanie programu word. W tym przykładzie 0.52 oznacza, że dwa streszczenia częściowe są nieco podobne.
<br>