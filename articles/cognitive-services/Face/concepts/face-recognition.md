---
title: Pojęcia rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące rozpoznawanie twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "64416001"
---
# <a name="face-recognition-concepts"></a>Pojęcia rozpoznawania twarzy

W tym artykule opisano różne operacje rozpoznawania twarzy (weryfikacji, należy znaleźć podobne, grupowanie i identyfikacji) i podstawowej struktury danych. Szeroko rozpoznawanie opisano pracę porównywanie dwie różne twarze, aby ustalić, czy są one podobne lub należą do tej samej osoby.

## <a name="recognition-related-data-structures"></a>Struktury danych dotyczących ocen

Operacje rozpoznawania używają głównie następujące struktury danych. Te obiekty są przechowywane w chmurze i mogą być przywoływane przez ich ciągi identyfikatorów. Ciągi identyfikatorów są w związku z tym zawsze unikatowa w ramach subskrypcji, podczas gdy nazwa pola, które mogą być zduplikowane.

|Name (Nazwa)|Opis|
|:--|:--|
|**DetectedFace**| Jest to reprezentacja jednej powierzchni pobierane przez [wykrywanie twarzy](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operacji. Jego Identyfikatora wygasa po upływie 24 godzin po jego utworzeniu.|
|**PersistedFace**| Podczas **DetectedFace** obiekty są dodawane do grupy (takie jak **FaceList** lub **osoby**), będą one **PersistedFace** obiektów, które można można [pobrać](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) w dowolnym czasie i nie wygasają.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Jest to lista są formatowane przy **PersistedFace** obiektów. A **FaceList** ma unikatowego Identyfikatora, ciągu nazwy i, opcjonalnie, ciąg danych użytkownika.|
|**[Osoby](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Jest to lista **PersistedFace** obiektów, które należą do tej samej osoby. Ma on unikatowego Identyfikatora, ciągu nazwy i, opcjonalnie, ciąg danych użytkownika.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Jest to lista są formatowane przy **osoby** obiektów. Ma on unikatowego Identyfikatora, ciągu nazwy i, opcjonalnie, ciąg danych użytkownika. A **grupie** musi być [uczonego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) zanim będzie można używać w operacjach rozpoznawania.|

## <a name="recognition-operations"></a>Rozpoznawanie operacji

W tej sekcji przedstawiono, jak cztery operacje rozpoznawania używają powyżej struktury danych. Zobacz [Przegląd](../Overview.md) dla każdej operacji rozpoznawania szeroki opis.

### <a name="verification"></a>Weryfikacja

[Sprawdź](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) wymaga funkcji face ID (**DetectedFace** lub **PersistedFace**) i albo innej funkcji face ID lub **osoby** obiektu i Określa, czy należą do tej samej osoby. Jeśli przekażesz w **osoby**, można opcjonalnie przekazać **grupie** do którego **osoby** należy w celu poprawy wydajności.

### <a name="find-similar"></a>Znajdź podobne

[Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) wymaga funkcji face ID (**DetectedFace** lub **PersistedFace**) i **FaceList** lub tablicę innych twarzy Identyfikatory. Za pomocą **FaceList**, funkcja zwraca mniejszy **FaceList** twarzy, które są podobne do danej twarzy. Z tablicą face ID podobnie zwraca mniejszy tablicy.

### <a name="grouping"></a>Grupowanie

[Grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operacja pobiera tablicę asortymencie twarzy, identyfikatory (**DetectedFace** lub **PersistedFace**) i zwraca takich samych identyfikatorów zgrupowane w kilku mniejszych tablic. Każdej macierzy "groups" zawiera identyfikatory, które pojawiają się podobnych twarzy, a tablica jednego "messyGroup" face ID, dla której nie podobieństwa nie znaleziono.

### <a name="identification"></a>Identyfikacja

[Identyfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operacja przyjmuje jeden lub kilka twarzy identyfikatorów (**DetectedFace** lub **PersistedFace**) i **grupie** i zwracający listę z **osoby** obiektów, które każdej twarzy może należeć do. Zwrócone **osoby** obiekty zostaną opakowane jako **Release Candidate** obiektów, które mają wartość ufności prognozy.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z następujących porad, aby upewnić się, że Twoje obrazy wejściowe zapewniają najbardziej dokładne wyniki rozpoznawania:

* Formaty obrazu wejściowego obsługiwane są JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu powinna być nie większy niż 4 MB.
* Podczas tworzenia **osoby** obiektów, należy użyć zdjęcia, które są wyposażone w szereg kąty i oświetlenia.
* Nie można rozpoznać niektórych twarzy ze względu na problemy techniczne:
  * Obrazy oświetlenie extreme (na przykład poważny Podświetlenie).
  * Przeszkód blokuje oczy jeden lub oba.
  * Różnice w włosów włosów stylu lub twarzy.
  * Zmiany w wyglądzie twarzy ze względu na wiek.
  * Extreme mimiki twarzy.

## <a name="next-steps"></a>Kolejne kroki

Skoro znasz pojęć rozpoznawania twarzy, Dowiedz się, jak napisać prosty skrypt, który identyfikuje twarze względem uczonego **grupie**.

* [Identyfikowanie twarzy na obrazach](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)