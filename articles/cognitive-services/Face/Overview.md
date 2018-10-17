---
title: Co to jest usługa interfejsu API rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: W glosariuszu opisano terminy, które możesz napotkać podczas pracy z usługą interfejsu API rozpoznawania twarzy.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123474"
---
# <a name="what-is-the-face-api-service"></a>Co to jest usługa interfejsu API rozpoznawania twarzy?

Witamy w usłudze interfejsu API rozpoznawania twarzy — usłudze w chmurze, która udostępnia najbardziej zaawansowane algorytmy przetwarzania obrazów twarzy. Interfejs API rozpoznawania twarzy oferuje dwie główne funkcje: wykrywanie twarzy za pomocą atrybutów i rozpoznawanie twarzy.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API rozpoznawania twarzy umożliwia wykrycie do 64 ludzkich twarzy, lokalizując je z dużą dokładnością na obrazie. Obraz można określić jako bajty w pliku lub prawidłowy adres URL.

![Przegląd — wykrywanie twarzy](./Images/Face.detection.jpg)

Dla każdej wykrytej twarzy jest zwracany prostokąt twarzy (lewy górny róg, szerokość i wysokość) wskazujący położenie twarzy na obrazie. Opcjonalnie funkcja wykrywania twarzy wyodrębnia szereg atrybutów dotyczących twarzy, takich jak wyraz, płeć, wiek, ułożenie głowy, zarost i okulary. Aby uzyskać więcej informacji, zobacz [Twarz — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Rozpoznawanie twarzy

Funkcja rozpoznawania twarzy jest powszechnie używana w wielu scenariuszach, w tym dotyczących zabezpieczeń, naturalnego interfejsu użytkownika, analizy zawartości obrazu i zarządzania nią, aplikacji mobilnych i robotyki. Są dostępne cztery funkcji rozpoznawania twarzy: weryfikacja twarzy, wyszukiwanie podobnych twarzy, grupowanie twarzy i identyfikacja osoby.

### <a name="face-verification"></a>Weryfikacja twarzy

Funkcja weryfikacji interfejsu API rozpoznawania twarzy wykonuje uwierzytelnianie względem dwóch wykrytych twarzy lub uwierzytelnianie jednego obiektu osoby na podstawie jednej wykrytej twarzy. Aby uzyskać szczegółowe informacje, zobacz [Twarz — weryfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Wyszukiwanie podobnych twarzy

Po określeniu docelowej wykrytej twarzy oraz zestawu twarzy do przeszukania usługa znajduje mały zbiór twarzy, które są najbardziej podobne do twarzy docelowej. Są obsługiwane dwa tryby pracy: `matchFace` i `matchPerson`. Tryb `matchPerson` powoduje zwrócenie podobnych twarzy po zastosowaniu wartości progu dotyczącej danej osoby i pochodzącej z funkcji [Twarz — weryfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb `matchFace` ignoruje wartość progu danej osoby i zwraca najbardziej podobne z przeszukiwanych twarzy. Zobacz poniższy przykład, twarze do przeszukania są wyświetlone.
![Przegląd — wyszukiwanie podobnych twarzy](./Images/FaceFindSimilar.Candidates.jpg) A wyszukiwana twarz to ![Przegląd — wyszukiwanie podobnych twarzy](./Images/FaceFindSimilar.QueryFace.jpg)

Aby znaleźć cztery podobne twarze, tryb `matchPerson` zwraca obrazy (a) i (b), które przedstawiają tę samą osobę, do której należy wyszukiwana twarz. Tryb `matchFace` zwraca (a), (b), (c) i (d) — dokładnie cztery wybrane obrazy, nawet jeśli podobieństwo jest niewielkie. Aby uzyskać więcej informacji, zobacz [Twarz — wyszukiwanie podobnych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Grupowanie twarzy

Mając określony zbiór nieznanych twarzy, interfejs API grupowania twarzy automatycznie dzieli go na kilka grup na podstawie podobieństwa poszczególnych twarzy. Każda grupa to rozłączny podzbiór właściwy oryginalnego zbioru nieznanych twarzy zawierający podobne twarze. Wszystkie twarze w danej grupie mogą być traktowane jako należące do tego samego obiektu osoby. Aby uzyskać więcej informacji, zobacz [Twarz — grupowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identyfikacja twarzy

Interfejs API rozpoznawania twarzy umożliwia identyfikowanie osób za pomocą wykrytej twarzy i bazy danych osób (zdefiniowanej za pomocą elementu LargePersonGroup/PersonGroup). Utwórz tę bazę wcześniej, aby edytować ją z upływem czasu.

Następująca ilustracja przedstawia przykład elementu LargePersonGroup/PersonGroup o nazwie „myfriends”. Każda grupa może zawierać maksymalnie 1 000 000/10 000 obiektów osób. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Przegląd — element LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Po utworzeniu i przeprowadzeniu uczenia elementu LargePersonGroup/PersonGroup można identyfikować nowo wykrytą twarz względem grupy. Jeśli twarz zostanie zidentyfikowana jako obiekt osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikowania osób, zobacz następujące przewodniki interfejsu API:

[Twarz — identyfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[Element PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[Element PersonGroup osoby — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[Element PersonGroup — uczenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[Element LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[Element LargePersonGroup osoby — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[Element LargePersonGroup — uczenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Magazyn twarzy

Magazyn twarzy umożliwia subskrypcji w warstwie Standardowa przechowywanie dodatkowych utrwalonych twarzy w przypadku korzystania z obiektów osoby LargePersonGroup/PersonGroup([Element PersonGroup osoby — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[Element LargePersonGroup osoby — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) lub elementów LargeFaceLists/FaceLists ([Element FaceList — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[Element LargeFaceList — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) na potrzeby identyfikacji lub dopasowywania podobieństwa przy użyciu interfejsu API rozpoznawania twarzy. Opłata za przechowywane obrazy jest naliczana przy użyciu stawki wynoszącej 0,5 USD za 1000 twarzy proporcjonalnie do liczby dni. Subskrypcje w warstwie Bezpłatna są bezpłatne, lecz ograniczone do łącznie 1000 osób.

Cena za magazyn twarzy jest naliczana proporcjonalnie do liczby dni. Jeśli na przykład konto korzystało z 10 000 przechowywanych twarzy w każdym dniu pierwszej połowy miesiąca i nie korzystało z twarzy w drugiej połowie miesiąca, opłata zostanie naliczona za następującą liczbę twarzy: 10 000 pomnożone przez liczbę dni przechowywania. Inny przykład: jeśli każdego dnia miesiąca kilka twarzy, powiedzmy 1000, jest przechowywanych przez kilka godzin, a następnie są one nocą usuwane, nadal będą naliczane opłaty za przechowywanie 1000 twarzy każdego dnia.

## <a name="getting-started-tutorials"></a>Samouczki Wprowadzenie

Następujące samouczki przedstawiają podstawowe funkcje interfejsu API rozpoznawania twarzy i procesy subskrypcji:

- [Samouczek: wprowadzenie do interfejsu API rozpoznawania twarzy w języku CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Samouczek: wprowadzenie do interfejsu API rozpoznawania twarzy w języku Java dla systemu Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Samouczek: wprowadzenie do interfejsu API rozpoznawania twarzy w języku Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Przykładowe aplikacje

Zapoznaj się z tymi przykładowymi aplikacjami, które korzystają z interfejsu API rozpoznawania twarzy.

- [Interfejs API rozpoznawania twarzy firmy Microsoft: biblioteka i przykład klienta systemu Windows](https://github.com/Microsoft/Cognitive-Face-Windows)
  - Przykładowa aplikacja WPF demonstrująca kilka scenariuszy wykrywania, analizy i identyfikacji twarzy.
- [Aplikacja platformy UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Przykładowa aplikacja platformy uniwersalnej systemu Windows (UWP) pokazująca użycie funkcji rozpoznawania mowy, asystentki Cortana, funkcji pisma odręcznego i obsługi kamery za pomocą scenariusza udostępniania notatki rodzinie.
- [Przykład analizy klatek wideo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Przykładowa aplikacja platformy uniwersalnej systemu Windows (UWP) pokazująca analizowanie strumieni wideo na żywo prawie w czasie rzeczywistym za pomocą interfejsów API rozpoznawania twarzy, przetwarzania obrazów i rozpoznawania emocji.

## <a name="related-topics"></a>Tematy pokrewne

- [Informacje o wersji interfejsu API rozpoznawania twarzy w wersji 1.0](ReleaseNotes.md)
- [Jak wykrywać twarze na obrazie](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Jak identyfikować twarze na obrazie](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
