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
ms.openlocfilehash: 5ad589c4adb60369f81979e214935f73d9eb0755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309537"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definicje

<a name="point"></a>
### <a name="point"></a>Punkt

|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Sygnatura czasowa**  <br>*Opcjonalne*|Sygnatura czasowa dla punktu danych. Upewnij się, powoduje wyrównanie z o północy i użyj formatu UTC Data i godzina ciąg znaków, na przykład 2017-08-01T00:00:00Z.|ciąg (Data godzina)|
|**Wartość**  <br>*Opcjonalne*|Dane wartości miary.|Liczba (podwójny)|


<a name="request"></a>
### <a name="request"></a>Żądanie

|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Okres**  <br>*Opcjonalne*|Okres punktów danych. Jeśli wartość jest pusta lub nie są wyświetlane, interfejs API określa okres automatycznie.|Liczba (podwójny)|
|**punkty**  <br>*Opcjonalne*|Punkty danych szeregów czasowych. Dane powinny być posortowane według sygnatur czasowych w kolejności rosnącej, aby dopasować wyniku anomalii. Jeśli dane nie są sortowane poprawnie, istnieje zduplikowana sygnatura czasowa interfejsu API będzie wykrywał punktów anomalii poprawnie, ale nie można również zgodne punkty zwracany za pomocą danych wejściowych. W takim przypadku zostanie dodany komunikat ostrzegawczy w odpowiedzi.|< [polecenie](#point) > tablicy|


<a name="response"></a>
### <a name="response"></a>Odpowiedź

|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**ExpectedValues**  <br>*Opcjonalne*|Wartością prognozowaną ucząc model oparty na protokole. Jeśli punkty danych wejściowych są posortowane według sygnatur czasowych w kolejności rosnącej, indeks tablicy może służyć do mapowania oczekiwaną wartość i oryginalna wartość.|Tablica < numer (podwójny) >|
|**IsAnomaly**  <br>*Opcjonalne*|Wynik tego, czy punkty danych są anomalii lub nie w obu kierunkach (wzrostów lub spadku). wartość TRUE oznacza, że punkt znajduje się anomalii, wartość false oznacza, że punkt znajduje się bez anomalii. Jeśli punkty danych wejściowych są posortowane według sygnatur czasowych w kolejności rosnącej, indeks tablicy może służyć do mapowania oczekiwaną wartość i oryginalna wartość.|Tablica < wartość logiczna >|
|**IsAnomaly_Neg**  <br>*Opcjonalne*|Wynik dla tego, czy punkty danych są anomalie w kierunku ujemna (spadku). PRAWDA oznacza, że kierunek anomalii jest ujemna. Jeśli punkty danych wejściowych są posortowane według sygnatur czasowych w kolejności rosnącej, indeks tablicy może służyć do mapowania oczekiwaną wartość i oryginalna wartość.|Tablica < wartość logiczna >|
|**IsAnomaly_Pos**  <br>*Opcjonalne*|Wynik dla tego, czy punkty danych są anomalie w kierunku dodatnią (wzrostów). PRAWDA oznacza, że kierunek anomalii jest dodatni. Jeśli punkty danych wejściowych są posortowane według sygnatur czasowych w kolejności rosnącej, indeks tablicy może służyć do mapowania wartości oczekiwanej a wersją z oryginalnego.|Tablica < wartość logiczna >|
|**LowerMargin**  <br>*Opcjonalne*|(ExpectedValue - LowerMargin) określa dolną granicę, że punkt danych nadal jest uznawane za w zwykły sposób. Jeśli punkty danych wejściowych są posortowane według sygnatur czasowych w kolejności rosnącej, indeks tablicy może służyć do mapowania oczekiwaną wartość i oryginalna wartość.|Tablica < numer (podwójny) >|
|**Okres**  <br>*Opcjonalne*|Okres interfejsu API używaną do wykrywania anomalii punkty.|Liczba (zmiennoprzecinkowego)|
|**UpperMargin**  <br>*Opcjonalne*|Suma ExpectedValue i UpperMargin okaże się, że górna granica, która punktu danych jest nadal traktować w zwykły sposób. Jeśli punkty danych wejściowych są posortowane według sygnatur czasowych w kolejności rosnącej, indeks tablicy może służyć do mapowania oczekiwaną wartość i oryginalna wartość.|Tablica < numer (podwójny) >|
|**WarningText**  <br>*Opcjonalne*|Jeśli reguła, która wymaga interfejsu API i dane nadal może zostać wykryte przez interfejs API nie postępujesz zgodnie z punktów danych wejściowych, pod warunkiem, interfejs API analizy danych i Dołącz informacje ostrzeżenie w tym polu.|string|



