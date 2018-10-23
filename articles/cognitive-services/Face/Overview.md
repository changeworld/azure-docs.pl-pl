---
title: Co to jest usługa interfejsu API rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: W tym temacie opisano usługę interfejsu API rozpoznawania twarzy oraz powiązane pojęcia.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310373"
---
# <a name="what-is-the-face-api-service"></a>Co to jest usługa interfejsu API rozpoznawania twarzy?

Usługa interfejsu API rozpoznawania twarzy to usługa oparta na chmurze, która udostępnia algorytmy do analizowania ludzkich twarzy na obrazach i w wideo. Interfejs API rozpoznawania twarzy oferuje dwie główne funkcje: wykrywanie twarzy za pomocą atrybutów i rozpoznawanie twarzy.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API rozpoznawania twarzy umożliwia wykrycie do 64 ludzkich twarzy na obrazie, lokalizując je z dużą dokładnością. Obraz można określić za pomocą pliku (strumienia bajtów) lub prawidłowego adresu URL.

![Przegląd — wykrywanie twarzy](./Images/Face.detection.jpg)

Dla każdej wykrytej twarzy jest zwracany prostokąt twarzy (lewy górny róg, szerokość i wysokość) wskazujący położenie twarzy na obrazie. Opcjonalnie funkcja wykrywania twarzy wyodrębnia szereg atrybutów dotyczących twarzy, takich jak wyraz, płeć, wiek, ułożenie głowy, zarost i okulary. Aby uzyskać więcej informacji, zobacz [Twarz — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Rozpoznawanie twarzy

Możliwość rozpoznawania ludzkich twarzy jest ważna w wielu scenariuszach, w tym dotyczących zabezpieczeń, naturalnego interfejsu użytkownika, analizy zawartości obrazu i zarządzania nią, aplikacji mobilnych i robotyki. Usługa interfejsu API rozpoznawania twarzy udostępnia cztery funkcje rozpoznawania twarzy: weryfikacja twarzy, wyszukiwanie podobnych twarzy, grupowanie twarzy i identyfikacja osoby.

### <a name="face-verification"></a>Weryfikacja twarzy

Funkcja weryfikacji twarzy wykonuje uwierzytelnianie względem dwóch wykrytych twarzy lub jednego obiektu osoby na podstawie jednej wykrytej twarzy. Aby uzyskać szczegółowe informacje, zobacz [Twarz — weryfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-faces"></a>Wyszukiwanie podobnych twarzy

Po określeniu docelowej wykrytej twarzy oraz zestawu twarzy do przeszukania usługa znajduje mały zbiór twarzy, które są najbardziej podobne do twarzy docelowej. Są obsługiwane dwa tryby pracy: **matchFace** i **matchPerson**. Tryb **matchPerson** powoduje zwrócenie podobnych twarzy po zastosowaniu wartości progu dotyczącej danej osoby i pochodzącej z funkcji [Twarz — weryfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb **matchFace** ignoruje wartość progu danej osoby i zwraca najbardziej podobne z przeszukiwanych twarzy. Zobacz poniższy przykład, twarze do przeszukania są wyświetlone.
![Przegląd — wyszukiwanie podobnych twarzy](./Images/FaceFindSimilar.Candidates.jpg) Szukana jest następująca twarz.
![Przegląd — wyszukiwanie podobnych twarzy](./Images/FaceFindSimilar.QueryFace.jpg)

W przypadku wyszukiwania czterech podobnych twarzy tryb **matchPerson** zwraca (a) i (b), czyli twarze należące do tej samej osoby co wyszukiwana twarz. Tryb **matchFace** zwraca (a), (b), (c) i (d) — dokładnie cztery wybrane obrazy, nawet jeśli podobieństwo jest niewielkie. Aby uzyskać więcej informacji, zobacz [Twarz — wyszukiwanie podobnych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Grupowanie twarzy

Mając określony zbiór nieznanych twarzy, interfejs API grupowania twarzy automatycznie dzieli go na kilka grup na podstawie podobieństwa poszczególnych twarzy. Każda grupa to rozłączny podzbiór właściwy oryginalnego zbioru nieznanych twarzy zawierający podobne twarze. Wszystkie twarze w danej grupie mogą być traktowane jako należące do tej samej osoby. Aby uzyskać więcej informacji, zobacz [Twarz — grupowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="person-identification"></a>Identyfikacja osoby

Interfejs API rozpoznawania twarzy umożliwia identyfikowanie osób za pomocą wykrytej twarzy i bazy danych osób. Utwórz tę bazę wcześniej, aby móc edytować ją z upływem czasu.

Następująca ilustracja przedstawia przykład bazy danych o nazwie „myfriends”. Każda grupa może zawierać maksymalnie 1 000 000/10 000 różnych obiektów osoby. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Przegląd — element LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Po utworzeniu i przeprowadzeniu uczenia bazy danych można identyfikować nowo wykrytą twarz względem grupy. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikowania osób, zobacz następujące przewodniki interfejsu API:

[Twarz — identyfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[Element PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[Element PersonGroup, osoba — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[Element PersonGroup — uczenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[Element LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[Element LargePersonGroup, osoba — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[Element LargePersonGroup — uczenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Magazyn twarzy i cennik

Magazyn twarzy umożliwia subskrypcji w warstwie Standardowa przechowywanie dodatkowych utrwalonych twarzy w przypadku korzystania z obiektów osoby LargePersonGroup/PersonGroup([Element PersonGroup osoby — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[Element LargePersonGroup osoby — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) lub elementów LargeFaceLists/FaceLists ([Element FaceList — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[Element LargeFaceList — dodawanie twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) na potrzeby identyfikacji lub dopasowywania podobieństwa przy użyciu interfejsu API rozpoznawania twarzy. Opłata za przechowywane obrazy jest naliczana przy użyciu stawki wynoszącej 0,50 USD za 1000 twarzy proporcjonalnie do liczby dni. Subskrypcje w warstwie Bezpłatna są ograniczone do łącznie 1000 osób.

Jeśli na przykład konto korzystało z 10 000 przechowywanych twarzy w każdym dniu pierwszej połowy miesiąca i nie korzystało z twarzy w drugiej połowie miesiąca, opłata zostanie naliczona za następującą liczbę twarzy: 10 000 pomnożone przez liczbę dni przechowywania. Inny przykład: jeśli każdego dnia miesiąca kilka twarzy, powiedzmy 1000, jest przechowywanych przez kilka godzin, a następnie są one nocą usuwane, nadal będą naliczane opłaty za przechowywanie 1000 twarzy każdego dnia.

## <a name="sample-apps"></a>Przykładowe aplikacje

Zapoznaj się z tymi przykładowymi aplikacjami, które korzystają z interfejsu API rozpoznawania twarzy.

- [Interfejs API rozpoznawania twarzy firmy Microsoft: biblioteka i przykład klienta systemu Windows](https://github.com/Microsoft/Cognitive-Face-Windows)
  - Przykładowa aplikacja WPF demonstrująca kilka scenariuszy wykrywania, analizy i identyfikacji twarzy.
- [Aplikacja platformy UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Przykładowa aplikacja platformy uniwersalnej systemu Windows (UWP) pokazująca użycie funkcji rozpoznawania mowy, asystentki Cortana, funkcji pisma odręcznego i obsługi kamery za pomocą scenariusza udostępniania notatki rodzinie.
- [Przykład analizy klatek wideo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Przykładowa aplikacja Win32 pokazująca analizowanie strumieni wideo na żywo prawie w czasie rzeczywistym za pomocą interfejsów API rozpoznawania twarzy, przetwarzania obrazów i rozpoznawania emocji.

## <a name="tutorials"></a>Samouczki
Następujące samouczki przedstawiają podstawowe funkcje interfejsu API rozpoznawania twarzy i procesy subskrypcji:
- [Samouczek: wprowadzenie do interfejsu API rozpoznawania twarzy w języku CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Samouczek: wprowadzenie do interfejsu API rozpoznawania twarzy w języku Java dla systemu Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Samouczek: wprowadzenie do interfejsu API rozpoznawania twarzy w języku Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>Następne kroki

Wypróbuj przewodnik Szybki start, aby zaimplementować prosty scenariusz obejmujący interfejs API rozpoznawania twarzy.
- [Szybki start: wykrywanie twarzy na obrazie przy użyciu języka C#](quickstarts/csharp.md) (inne języki są dostępne)
