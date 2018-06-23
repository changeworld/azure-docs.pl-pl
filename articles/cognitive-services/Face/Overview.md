---
title: Stają przed Omówienie usługi interfejsu API | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Słownik opisano terminy, które można napotkać podczas pracy z usługą powierzchni interfejsu API.
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bb3ee9431f24a51dfd3877f1c4d621f7ba633b96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347600"
---
# <a name="what-is-face-api"></a>Co to jest powierzchni interfejsu API?

Zapraszamy do powierzchni interfejsu API usługi Microsoft, usług chmurowych, który zapewnia najbardziej zaawansowane algorytmy krój. Powierzchni interfejsu API ma dwa główne funkcje: stają przed wykrywania z atrybutami i stają przed rozpoznawania.

## <a name="face-detection"></a>Wykrywanie twarzy

Powierzchni interfejsu API wykrywa maksymalnie 64 człowieka krojów z lokalizacją krój wysokiej precyzji obrazu. I obrazu może zostać określony w pliku w bajtach lub prawidłowy adres URL.

![Przegląd — wykrywania twarzy na obrazie](./Images/Face.detection.jpg)

Prostokąt twarzy na obrazie (po lewej, top, szerokość i wysokość) wskazujący lokalizację krój obrazu jest zwracana wraz z każdym wykryto krój. Opcjonalnie wykrywania twarzy na obrazie wyodrębnia szereg krój związane z atrybutów, takich jak ułożenia, płci, wieku, ułożenia head, włosów twarzy i szkła. Aby uzyskać więcej informacji, zobacz [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Rozpoznawanie twarzy na obrazie

Rozpoznawanie twarzy na obrazie jest powszechnie używany w wielu scenariuszach, w tym zabezpieczeń, interfejs użytkownika fizycznych analizy zawartości obrazu i zarządzania, aplikacje mobilne i robotics. Podano czterech funkcji rozpoznawania twarzy na obrazie: weryfikacji, znajdowanie kroje podobne, grupowanie krój i identyfikację osoby.

### <a name="face-verification"></a>Weryfikacja twarzy

Powierzchni interfejsu API weryfikacji wykonuje uwierzytelniania względem obie strony wykrytych lub z jednego krój wykryte do jednego obiektu osoby. Aby uzyskać szczegółowe informacje, zobacz [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Znajdowanie podobne krój

Podana krój docelowy wykryte i zestaw kroje kandydata do wyszukiwania z, usługa znajduje niewielki zestaw kroje, które najbardziej podobne do powierzchni docelowej. Dwa tryby pracy, `matchFace` i `matchPerson` są obsługiwane. `matchPerson` Funkcja podobne kroje po stosujące próg sama osoba pochodną [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). `matchFace` Tryb ignoruje próg tej samej osoby i zwraca górny podobne kroje kandydata. Następujący przykładowy, kroje candidate są wyświetlane.
![Przegląd — krój Znajdź podobne](./Images/FaceFindSimilar.Candidates.jpg) i powierzchnia zapytania jest ![Przegląd — krój Znajdź podobne](./Images/FaceFindSimilar.QueryFace.jpg)

Można znaleźć cztery kroje podobne, `matchPerson` zwraca tryb i b, które należą do tej samej osoby z powierzchni zapytania. `matchFace` Funkcja (), (b) (c) oraz (d), dokładnie czterech kandydatów podobieństwa nawet wtedy, gdy niski. Aby uzyskać więcej informacji, zobacz [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Grupowanie twarzy

Podana jeden zestaw kroje nieznany, krój grupowanie interfejsu API automatycznie dzieli je kilka grup na podstawie podobieństwa. Każda grupa jest rozłączna podzbiór właściwy oryginalnego powierzchni nieznany ustaw i zawiera podobne powierzchni. I mogą zostać uwzględnione wszystkie kroje w tej samej grupie należeć do tego samego obiektu osoby. Aby uzyskać więcej informacji, zobacz [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identyfikacja twarzy

Powierzchni interfejsu API może służyć do identyfikowania osób oparte na powierzchnię wykrytych i bazy danych osoby (zdefiniowany jako LargePersonGroup/PersonGroup). Wcześniej utworzyć tę bazę danych, które mogą być edytowane w czasie.

Poniższa ilustracja jest przykładem LargePersonGroup/PersonGroup o nazwie "myfriends". Każda grupa może zawierać obiekty osoby do 1 000 000/10 000. W tym samym czasie każdego obiektu osoba może mieć do 248 kroje zarejestrowany.

![Przegląd — LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Po utworzeniu i uczony LargePersonGroup/PersonGroup identyfikacji można wykonywać na grupie i nowych krój wykryte. Jeśli kroju została zidentyfikowana jako obiekt osoby w grupie, jest zwracany obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osoby zobacz następujące przewodniki interfejsu API:

[Stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 
 [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - pociągu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Magazyn twarzy

Magazyn krój umożliwia standardowe subskrypcji do przechowywania dodatkowe kroje utrwalonego, korzystając z obiektów osoby LargePersonGroup/PersonGroup ([PersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[osoby LargePersonGroup - Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) lub LargeFaceLists/FaceLists ([FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) do identyfikacji lub podobieństwa dopasowywania przy użyciu interfejsu API krój. Obrazy przechowywane są naliczane według $0,5 na powierzchni 1000, a ta stawka jest proporcjonalna codziennie. Warstwa bezpłatna subskrypcje są wolne, ale maksymalnie 1000 całkowita osób.

Cennik krój magazynu jest proporcjonalna codziennie. Na przykład, jeśli konto używane kroje utrwalonego 10 000 każdego dnia dla pierwszej połowy miesiąca i Brak drugiej połowie czy opłaty naliczane tylko w przypadku 10 000 kroje dni przechowywane. Alternatywnie Jeśli każdy dzień w miesiącu utrwalić 1000 kroje przez kilka godzin i usunąć je po każdej nocy, można będzie nadal opłaty będą naliczane za 1000 kroje utrwalonego każdego dnia.

## <a name="getting-started-tutorials"></a>Wprowadzenie samouczki

Następujące samouczki przykładem podstawowych funkcji powierzchni interfejsu API i procesy subskrypcji:

- [Wprowadzenie do korzystania z powierzchni interfejsu API w samouczku CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Wprowadzenie do korzystania z powierzchni interfejsu API w języku Java Samouczek systemu Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Wprowadzenie do korzystania z powierzchni interfejsu API w samouczku Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Przykładowe aplikacje

Spójrz na te przykładowe aplikacje korzystające z interfejsu API twarzy na obrazie.

- [Aplikacja FamilyNotes platformy uniwersalnej systemu Windows](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Uniwersalnych platformy systemu Windows (UWP) przykładowej aplikacji, pokazujący użycie mowy, Cortana odręczne i aparatu za pośrednictwem rodzina Pamiętaj scenariusza udostępniania.
- [Przykładowe analizy ramek wideo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Uniwersalnych platformy systemu Windows (UWP) przykładowej aplikacji, który zawiera analizowanie strumieni wideo na żywo w najbliższym czasie rzeczywistym za pomocą krój, wizji komputera i interfejsy API rozpoznawania emocji — warstwa.

## <a name="related-topics"></a>Tematy pokrewne

- [Informacje o wersji powierzchni interfejsu API w wersji 1.0](ReleaseNotes.md)
- [Jak wykryć skierowany w obrazie](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Jak zidentyfikować skierowany w obrazie](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
