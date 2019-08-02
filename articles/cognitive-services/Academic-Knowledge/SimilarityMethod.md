---
title: Metoda podobieństwa — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Użyj metody podobieństwa, aby obliczyć podobieństwo naukowe dwóch ciągów.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704847"
---
# <a name="similarity-method"></a>Metoda podobieństwa

**Podobny** interfejs API REST jest używany do obliczania podobieństwa naukowego między dwoma ciągami. 
<br>

**Punkt końcowy REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parametry żądania

Parametr        |Typ danych      |Wymagane | Opis
----------|----------|----------|------------
**s1**        |Ciąg   |Yes  |Ciąg * do porównania
**s2**        |String   |Yes  |Ciąg * do porównania

<sub>* Ciągi do porównania mają maksymalną długość 1 MB.</sub>
<br>

## <a name="response"></a>Odpowiedź

Name (Nazwa) | Opis
--------|---------
**SimilarityScore**        |Wartość zmiennoprzecinkowa reprezentująca stopę podobieństwa S1 i S2, z wartościami zbliżonymi do 1,0 oznacza bardziej podobne i wartości zbliżone do-1,0 znaczenie mniej

<br>

## <a name="successerror-conditions"></a>Warunki sukcesu/błędów

Stan HTTP | Reason | Odpowiedź
-----------|----------|--------
**200**         |Powodzenie | Liczba zmiennoprzecinkowa
**400**         | Złe żądanie lub nieprawidłowe żądanie | Komunikat o błędzie      
**500**         |Wewnętrzny błąd serwera | Komunikat o błędzie
**Przekroczono limit czasu**     | Upłynął limit czasu żądania.  | Komunikat o błędzie

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Przykład: Oblicz podobieństwo dwóch abstrakcyjnych częściowych
#### <a name="request"></a>Żądanie:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
W tym przykładzie wygenerujemy wynik podobieństwa między dwoma abstrakcyjnymi częściami przy użyciu interfejsu API podobieństwa.
#### <a name="response"></a>Odpowiedź:
```
0.520
```
#### <a name="remarks"></a>Uwagi
Wynik podobieństwa jest określany przez ocenę koncepcji naukowych przy użyciu osadzania wyrazów. W tym przykładzie 0,52 oznacza, że dwa częściowe streszczenia są nieco podobne.
<br>
