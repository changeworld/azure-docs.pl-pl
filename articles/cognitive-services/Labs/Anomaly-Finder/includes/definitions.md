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
ms.openlocfilehash: ff36202b67f6262b7ba67fe48ef37f2b656b91fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348185"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definicje

<a name="point"></a>
### <a name="point"></a>Punkt

|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Znacznik czasu**  <br>*Opcjonalne*|Znacznik czasu dla punktu danych. Upewnij się, że powoduje wyrównanie o północy i użyj UTC Data i godzina ciąg znaków, na przykład 2017-08-01T00:00:00Z.|ciąg (daty i godziny)|
|**Wartość**  <br>*Opcjonalne*|Dane wartości miary.|numer (o podwójnej precyzji)|


<a name="request"></a>
### <a name="request"></a>Żądanie

|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Okres**  <br>*Opcjonalne*|Okres punktów danych. Jeśli wartość jest równa null lub nie są wyświetlane, interfejsu API będzie automatycznie określenia okresu.|numer (o podwójnej precyzji)|
|**Punkty**  <br>*Opcjonalne*|Punkty danych serii czasu. Dane mają być sortowane przez rosnącej odpowiadające wynik anomalii sygnatury czasowej. Jeśli dane nie są poprawnie sortowane lub sygnatura czasowa zduplikowany, interfejsu API wykryje punktów anomalii poprawnie, ale nie można także dopasować punktów zwrócona z danych wejściowych. W takim przypadku zostanie dodany komunikat ostrzegawczy w odpowiedzi.|< [polecenie](#point) > tablicy|


<a name="response"></a>
### <a name="response"></a>Odpowiedź

|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**ExpectedValues**  <br>*Opcjonalne*|Wartością prognozowaną przez naukę na podstawie modelu. Jeśli punkty danych wejściowych są sortowane według znaczników czasu w kolejności rosnącej, indeks tablicy można mapować oczekiwaną wartością i oryginalnej wartości.|< numer (o podwójnej precyzji) > tablicy|
|**IsAnomaly**  <br>*Opcjonalne*|Wynik tego, czy punkty danych są anomalii lub nie w obu kierunkach (nagłego lub DIP). wartość TRUE oznacza, że punkt anomalii, wartość false oznacza, że punkt znajduje się z systemem innym niż anomalii. Jeśli punkty danych wejściowych są sortowane według znaczników czasu w kolejności rosnącej, indeks tablicy można mapować oczekiwaną wartością i oryginalnej wartości.|Tablica < wartość logiczna >|
|**IsAnomaly_Neg**  <br>*Opcjonalne*|Wynik na to, czy punkty danych są anomalii w kierunku ujemną (DIP). PRAWDA oznacza, że kierunek anomalii jest ujemna. Jeśli punkty danych wejściowych są sortowane według znaczników czasu w kolejności rosnącej, indeks tablicy można mapować oczekiwaną wartością i oryginalnej wartości.|Tablica < wartość logiczna >|
|**IsAnomaly_Pos**  <br>*Opcjonalne*|Wynik na to, czy punkty danych są anomalii w kierunku dodatnią (nagłego). PRAWDA oznacza, że kierunek anomalii jest dodatnia. Jeśli punkty danych wejściowych są sortowane według znaczników czasu w kolejności rosnącej, indeks tablicy można służy do mapowania oczekiwanego i oryginalne wartości.|Tablica < wartość logiczna >|
|**LowerMargin**  <br>*Opcjonalne*|(ExpectedValue - LowerMargin) określa, że dolna granica punktu danych jest nadal traktować jako standardowa. Jeśli punkty danych wejściowych są sortowane według znaczników czasu w kolejności rosnącej, indeks tablicy można mapować oczekiwaną wartością i oryginalnej wartości.|< numer (o podwójnej precyzji) > tablicy|
|**Okres**  <br>*Opcjonalne*|Okres, który interfejsu API używany do wykrywania anomalii punktów.|numer (float)|
|**UpperMargin**  <br>*Opcjonalne*|Suma ExpectedValue i UpperMargin określa górną granicę punktu danych jest nadal traktować jako standardowa. Jeśli punkty danych wejściowych są sortowane według znaczników czasu w kolejności rosnącej, indeks tablicy można mapować oczekiwaną wartością i oryginalnej wartości.|< numer (o podwójnej precyzji) > tablicy|
|**WarningText**  <br>*Opcjonalne*|Jeśli punkty danych wejściowych, pod warunkiem nie znajdują się one regułę, która wymaga interfejsu API, a dane nadal mogą być wykrywane przez interfejs API, interfejsu API będzie analizować dane i Dołącz informacje ostrzeżenie w tym polu.|ciąg|



