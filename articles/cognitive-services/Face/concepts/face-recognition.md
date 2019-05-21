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
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890883"
---
# <a name="face-recognition-concepts"></a>Pojęcia rozpoznawania twarzy

W tym artykule opisano pojęcia operacji upewnij się, Znajdź podobne, grupy i identyfikowanie rozpoznawania twarzy i podstawowej struktury danych. Szeroko rozpoznawanie opisuje pracy porównanie dwóch różnych twarzy do ustalenia, czy konfiguracje są podobne lub należą do tej samej osoby.

## <a name="recognition-related-data-structures"></a>Struktury danych dotyczących ocen

Operacje rozpoznawania używają głównie następujące struktury danych. Te obiekty są przechowywane w chmurze i mogą być przywoływane przez ich ciągi identyfikatorów. Ciągi identyfikatorów są unikatowe w ramach subskrypcji. Nazwa pola, które mogą być zduplikowane.

|Name (Nazwa)|Opis|
|:--|:--|
|DetectedFace| Taka reprezentacja jednej powierzchni jest pobierany przez [wykrywanie twarzy](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operacji. Jego Identyfikatora wygasa po upływie 24 godzin po jego utworzeniu.|
|PersistedFace| DetectedFace obiekty są dodawane do grupy, takie jak FaceList lub osobę, staną się PersistedFace obiektów. Mogą to być [pobrać](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) w dowolnym czasie i nie wygasa.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) lub [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Ta struktura danych jest asortymencie listę obiektów PersistedFace. FaceList ma unikatowego Identyfikatora, ciągu nazwy i, opcjonalnie, ciąg danych użytkownika.|
|[Osoby](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Ta struktura danych znajduje się lista obiektów PersistedFace, które należą do tej samej osoby. Ma on unikatowego Identyfikatora, ciągu nazwy i, opcjonalnie, ciąg danych użytkownika.|
|[Grupie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) lub [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Ta struktura danych jest asortymencie listę obiektów osób. Ma on unikatowego Identyfikatora, ciągu nazwy i, opcjonalnie, ciąg danych użytkownika. Musi być grupie [uczonego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) zanim będzie można używać w operacjach rozpoznawania.|

## <a name="recognition-operations"></a>Rozpoznawanie operacji

W tej sekcji przedstawiono, jak cztery operacje rozpoznawania używają struktury danych, które opisano wcześniej. Szeroka opis każdej operacji rozpoznawania, zobacz [Przegląd](../Overview.md).

### <a name="verify"></a>Sprawdź

[Sprawdź](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operacja pobiera funkcji face ID DetectedFace lub PersistedFace i innej funkcji face ID lub osoby i określa, czy należą do tej samej osoby. Jeśli przekażesz w obiekcie osoby, można przekazać w grupie, do której tej osobie należy poprawić wydajność.

### <a name="find-similar"></a>Znajdź podobne

[Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) wymaga funkcji face ID z DetectedFace lub PersistedFace i FaceList lub szereg innych twarzy identyfikatorów. Za pomocą FaceList zwraca mniejszy FaceList twarzy, które są podobne do danej twarzy. Z tablicą face ID podobnie zwraca mniejszy tablicy.

### <a name="group"></a>Grupa

[Grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operacja pobiera tablicę asortymencie twarzy, identyfikatory z DetectedFace lub PersistedFace i zwraca takich samych identyfikatorów zgrupowane w kilku mniejszych tablic. Każda tablica "groups" zawiera identyfikatory, które pojawiają się podobnych twarzy. Tablica jednego "messyGroup" zawiera rozpoznawania twarzy, identyfikatory dla zostały znalezione nie podobieństwa.

### <a name="identify"></a>Identyfikacja

[Identyfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operacji przyjmuje jeden lub kilka twarzy identyfikatory DetectedFace lub PersistedFace i grupie i zwraca listę obiektów osób, które każdej twarzy może należeć do. Zwracane osoby, które obiekty zostaną opakowane jako kandydat obiekty, które mają wartość ufności prognozy.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z następujących porad, aby upewnić się, że Twoje obrazy wejściowe zapewniają najbardziej dokładne wyniki rozpoznawania:

* Formaty obrazu wejściowego obsługiwane są JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu powinna być nie większy niż 4 MB.
* Podczas tworzenia obiektów osoba, należy użyć zdjęcia, które są wyposażone w różne rodzaje kąty i oświetlenia.
* Niektóre twarzy mogą nie być rozpoznawane z powodu problemów technicznych, takich jak:
  * Obrazy z extreme oświetlenia, na przykład poważny podświetlenia.
  * Przeszkód, które blokują oczy jeden lub oba.
  * Różnice w włosów włosów typu lub twarzy.
  * Zmiany w wyglądzie twarzy ze względu na wiek.
  * Extreme mimiki twarzy.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już pojęcia rozpoznawania twarzy, Dowiedz się, jak napisać skrypt, który identyfikuje twarze względem uczonego grupie.

* [Identyfikowanie twarzy na obrazach](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)