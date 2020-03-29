---
title: Pojęcia rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano pojęcia weryfikuj, znajdź podobne, grupuj i identyfikuj operacje rozpoznawania twarzy oraz podstawowe struktury danych.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743079"
---
# <a name="face-recognition-concepts"></a>Pojęcia rozpoznawania twarzy

W tym artykule opisano pojęcia weryfikuj, znajdź podobne, grupuj i identyfikuj operacje rozpoznawania twarzy oraz podstawowe struktury danych. Ogólnie rzecz biorąc, uznanie opisuje pracę porównywania dwóch różnych twarzy, aby ustalić, czy są one podobne lub należą do tej samej osoby.

## <a name="recognition-related-data-structures"></a>Struktury danych związanych z rozpoznawaniem

Operacje rozpoznawania używają głównie następujących struktur danych. Obiekty te są przechowywane w chmurze i mogą się odwoływać za pomocą ich ciągów identyfikatorów. Ciągi identyfikatorów są zawsze unikatowe w ramach subskrypcji. Pola nazw mogą być duplikowane.

|Nazwa|Opis|
|:--|:--|
|Wykrytaface| Ta reprezentacja pojedynczej ściany jest pobierana przez operację [wykrywania twarzy.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) Jego identyfikator wygasa 24 godziny po jego utworzeniu.|
|PersistedFace (Nieutrzymywal się)| Gdy DetectedFace obiekty są dodawane do grupy, takich jak FaceList lub Osoba, stają się persistedFace obiektów. Można je pobrać w dowolnym [momencie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) i nie wygasają.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) lub [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Ta struktura danych jest różne listy PersistedFace obiektów. FaceList ma unikatowy identyfikator, ciąg nazwy i opcjonalnie ciąg danych użytkownika.|
|[Person (Osoba)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Ta struktura danych jest lista PersistedFace obiektów, które należą do tej samej osoby. Ma unikatowy identyfikator, ciąg nazwy i opcjonalnie ciąg danych użytkownika.|
|[Grupa osób](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) lub [grupa dużej osoby](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Ta struktura danych jest różne listy Person obiektów. Ma unikatowy identyfikator, ciąg nazwy i opcjonalnie ciąg danych użytkownika. PersonGroup musi być [przeszkolony,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) zanim będzie można go używać w operacjach rozpoznawania.|

## <a name="recognition-operations"></a>Operacje rozpoznawania

W tej sekcji opisano, jak cztery operacje rozpoznawania używać struktur danych wcześniej opisane. Aby uzyskać ogólny opis każdej operacji rozpoznawania, zobacz [Omówienie](../Overview.md).

### <a name="verify"></a>Weryfikuj

Verify [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) Operacji ma identyfikator twarzy z DetectedFace lub PersistedFace i inny identyfikator twarzy lub osoba obiektu i określa, czy należą one do tej samej osoby. Jeśli przekażesz w Person obiektu, można opcjonalnie przekazać w PersonGroup, do którego należy ta osoba w celu zwiększenia wydajności.

### <a name="find-similar"></a>Znajdź podobne

Operacja [Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) przyjmuje identyfikator twarzy z DetectedFace lub PersistedFace i FaceList lub tablicę innych identyfikatorów twarzy. W przypadku listy facelist zwraca mniejszą listę twarzy, która jest podobna do danej ściany. W przypadku tablicy identyfikatorów twarzy podobnie zwraca mniejszą tablicę.

### <a name="group"></a>Grupa

Operacja [Grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) przyjmuje tablicę różnych identyfikatorów twarzy z DetectedFace lub PersistedFace i zwraca te same identyfikatory pogrupowane w kilka mniejszych tablic. Każda tablica "grup" zawiera identyfikatory twarzy, które wyglądają podobnie. Pojedyncza tablica "messyGroup" zawiera identyfikatory twarzy, dla których nie znaleziono podobieństw.

### <a name="identify"></a>Identyfikacji

Operacja [Identyfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) przyjmuje jeden lub kilka identyfikatorów twarzy z DetectedFace lub PersistedFace i PersonGroup i zwraca listę Person obiektów, które każda ściana może należeć do. Zwrócona osoba obiekty są zawijane jako Candidate obiektów, które mają wartość zaufania przewidywania.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z następujących wskazówek, aby upewnić się, że obrazy wejściowe dają najdokładniejsze wyniki rozpoznawania:

* Obsługiwane formaty obrazów wejściowych to JPEG, PNG, GIF (pierwsza klatka), BMP.
* Rozmiar pliku obrazu nie powinien być większy niż 4 MB.
* Podczas tworzenia obiektów Osoby należy używać zdjęć o różnych kątach i oświetleniu.
* Niektóre twarze mogą nie być rozpoznawane z powodu problemów technicznych, takich jak:
  * Obrazy z ekstremalnym oświetleniem, na przykład, poważne podświetlenie.
  * Przeszkody, które blokują jedno lub oba oczy.
  * Różnice w typie włosów lub zarostu.
  * Zmiany w wyglądzie twarzy ze względu na wiek.
  * Ekstremalna mimika twarzy.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz pojęcia rozpoznawania twarzy, dowiedz się, jak napisać skrypt identyfikujący twarze względem uszkolonej grupy persongroup.

* [Identyfikowanie twarzy na obrazach](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)