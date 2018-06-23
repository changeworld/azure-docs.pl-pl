---
title: Słownik dla usługi interfejsu API krój | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Słownik opisano terminy, które można napotkać podczas pracy z usługą powierzchni interfejsu API.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347236"
---
# <a name="glossary"></a>Słownik

## <a name="a"></a>A

#### <a name="Attributes"></a>Atrybuty

Atrybuty są opcjonalne w [wykrywania](#Detection-Face-Detection) wyniki, takie jak [wieku](#Age-Attribute), [płci](#Gender-Attribute), [head ułożenia](#Head-Pose-Attribute), [twarzy włosów](#Facial-Hair-Attribute), [uśmiech](#Smile-Attribute).
Można je uzyskać z [wykrywania](#Detection-Face-Detection) interfejsu API, określając parametry zapytania: returnFaceAttributes. Atrybuty zapewniające dodatkowe informacje dotyczące wybranego [kroje](#Face); oprócz [stają przed Identyfikatorem](#Face-ID) i [prostokąt](#Face-Rectangle).

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Age-Attribute"></a>Wiek (atrybut)

Okres ważności jest jednym z [atrybuty](#Attributes) wiek danego krój, który opisuje. Atrybut wiek jest opcjonalne w [wykrywania](#Detection-Face-Detection) wyników i można sterować za pomocą [wykrywania](#Detection-Face-Detection) żądania, określając parametr returnFaceAttributes.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="Candidate"></a>Candidate

Kandydaci zasadniczo [identyfikacji](#Identification) wyniki (np. określone osoby i poziom zaufania wykryć). Kandydat jest reprezentowana przez [PersonID](#Person-ID) i [zaufania](#Confidence), wskazujący, że osoba jest identyfikowany wysokiego poziomu zaufania.

Więcej informacji można znaleźć w podręczniku [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="Confidence"></a>Zaufania

Zaufanie jest miary ujawniania podobieństwa między [kroje](#Face) lub [osoby](#Person) w wartości liczbowe — która jest używana w [identyfikacji](#Identification), i [ Weryfikacja](#Verification) wskazująca podobieństwa przeszukane, zidentyfikowane i zweryfikowane wyników.

Więcej informacji można znaleźć w następujących przewodnikach: [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"></a>Wykrywanie twarzy wykrywania

Wykrywanie twarzy na obrazie jest akcja lokalizowania kroje obrazów. Użytkownicy, można przekazać obrazu lub podaj adres URL obrazu w żądaniu. Wykryto kroje są zwracane z [stają przed identyfikatorów](#Face-ID) wskazujący unikatową tożsamość powierzchni interfejsu API. Prostokątów wskazać lokalizację krój obrazu w pikselach, jak również opcjonalne [atrybuty](#Attributes) dla każdej powierzchni, takich jak [wieku](#Age-Attribute), [płci](#Gender-Attribute), [head ułożenia ](#Head-Pose-Attribute), [twarzy włosów](#Facial-Hair-Attribute) i [uśmiech](#Smile-Attribute).

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>Krój

Powierzchnia jest ujednoliconego termin wyniki pochodzące z powierzchni interfejsu API powiązane z wykrytym kroje. Ostatecznie krój jest reprezentowana przez ujednoliconego tożsamości ([identyfikator krój](#Face-ID)), określonego regionu w obrazach ([prostokąt krój](#Face-Rectangle)) i dodatkowe powiązane [atrybuty](#Face-Attributes-Facial-Attributes), takich jak [wieku](#Age-Attribute), [płci](#Gender-Attribute), [punkty orientacyjne](#Face-Landmarks-Facial-Landmarks) i [head ułożenia](#Head-Pose-Attribute). Ponadto może zwracać kroje [wykrywania](#Detection-Face-Detection).

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-API"></a>Powierzchni interfejsu API

Powierzchni interfejsu API jest oparta na chmurze interfejs API, który zapewnia najbardziej zaawansowane algorytmy wykrywania twarzy na obrazie i rozpoznawania. Główne funkcji powierzchni interfejsu API można podzielić na dwie kategorie: krój [wykrywania](#Detection-Face-Detection) z [atrybuty](#Face-Attributes-Facial-Attributes)i [rozpoznawania](#Recognition).

Więcej informacji można znaleźć w następujących przewodnikach: [powierzchni interfejsu API omówienie](./Overview.md), [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="Face-Attributes-Facial-Attributes"></a>Atrybuty atrybuty/Maseczka krój

Zobacz [atrybutów](#Attributes).

#### <a name="Face-ID"></a>Identyfikator krój

Identyfikator krój jest pochodną [wykrywania](#Detection-Face-Detection) wyniki, w których ciąg reprezentuje [krój](#Face) w [powierzchni interfejsu API](#Face-API).

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-Landmarks-Facial-Landmarks"></a>Punkty orientacyjne punkty orientacyjne/Maseczka krój

Punkty orientacyjne są opcjonalne w [wykrywania](#Detection-Face-Detection) wyniki; semantycznego punktów twarzy, takich jak oczu, na dostaniem się do ust (przedstawiono na poniższym rysunku). Punkty orientacyjne można sterować za pomocą [wykrywania](#Detection-Face-Detection) żądanie logiczna returnFaceLandmarks numer. Jeśli returnFaceLandmarks jest ustawiony jako true, zwracane kroje ma atrybuty punkty.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>Prostokąt na powierzchni

Prostokąt krój jest pochodną [wykrywania](#Detection-Face-Detection) wyniki, co stanowi pionowo prostokąt (po lewej, top, szerokość, wysokość), w przypadku obrazów w pikselach. Lewego górnego rogu [krój](#Face) (po lewej, top), oprócz szerokość i wysokość, wskazuje krój rozmiary x i osi y, odpowiednio.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Facial-Hair-Attribute"></a>Włosów twarzy (atrybut)

Twarzy włosów jest jednym z [atrybuty](#Attributes) opisuje długość twarzy włosów kroje dostępne. Atrybut twarzy włosów jest opcjonalne w [wykrywania](#Detection-Face-Detection) wyników i można sterować za pomocą [wykrywania](#Detection-Face-Detection) żądania returnFaceAttributes. Jeśli returnFaceAttributes zawiera "facialHair", zwracane kroje ma atrybuty włosów twarzy.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="FaceList"></a>FaceList

FaceList jest kolekcją [PersistedFace](#PersistedFace) i to jednostka [Znajdź podobne](#Find-Similar). FaceList jest dostarczany z [identyfikator FaceList](#FaceList-ID)oraz innych atrybutów, takich jak [nazwa](#Name) i [dane użytkownika](#UserData-User-Data).

Więcej informacji można znaleźć w następujących przewodnikach: [FaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="FaceList-ID"></a>Identyfikator FaceList

Identyfikator FaceList jest używany jako identyfikator ciągu podane przez użytkownika [FaceList](#FaceList). Identyfikator FaceList musi być unikatowa w ramach subskrypcji.

Więcej informacji można znaleźć w następujących przewodnikach: [FaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="Find-Similar"></a>Znajdź podobne

Ten interfejs API jest używany do wyszukiwania podobne kroje oparte na kolekcji powierzchni. Powierzchni zapytania i kolekcje krój są reprezentowane jako [stają przed identyfikatorów](#Face-ID) lub [identyfikator FceList](#FaceList-ID)/[identyfikator LargeFaceList](#LargeFaceList-ID) w żądaniu. Zwrócone wyniki są przeszukiwane kroje podobne, reprezentowany przez [stają przed identyfikatorów](#Face-ID) lub [identyfikatorów PersistedFace](#PersistedFace-ID).

Więcej informacji można znaleźć w następujących przewodnikach: [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="Gender-Attribute"></a>Płci (atrybut)

Płci jest jednym z [atrybuty](#Attributes) opisuje płci z dostępnych powierzchni. Atrybut płci jest opcjonalne w [wykrywania](#Detection-Face-Detection) wyników i można sterować za pomocą [wykrywania](#Detection-Face-Detection) żądania returnFaceAttributes. Jeśli returnfaceAttributes zawiera "płci", zwracane kroje ma atrybuty płci.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Grouping"></a>Grupowanie

Krój grupowania jest grupowanie kolekcji kroje zgodnie z podobieństwa twarzy. Krój kolekcje są oznaczone krój kolekcje identyfikator w żądaniu. W wyniku grupowania, podobne kroje są zgrupowane [grup](#Groups), i kroje, które nie są podobne do innych powierzchni są scalane jako grupy lepiej. Jest co najwyżej jeden [grupy lepiej](#Messy-Group) w wyniku grupowania.

Więcej informacji można znaleźć w podręczniku [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="Groups"></a>Grupy

Grupy są uzyskiwane z [grupowanie](#Grouping) wyników. Każda grupa zawiera zbiór kroje podobne, gdzie są oznaczeni kroje [stają przed identyfikatorów](#Face-ID).

Więcej informacji można znaleźć w podręczniku [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>HEAD stanowić (atrybut)

Ułożenia HEAD jest jednym z [atrybuty](#Attributes) reprezentuje czoła orientacji w przestrzeni 3D zgodnie z kąty zbiorczego, wysokości i odchylenia, jak pokazano na poniższej ilustracji. Zakresy wartości przywracać i yaw są [-180, 180] i [-90, 90] stopni. W bieżącej wersji wartość wysokości zwrócona z wykrywania jest zawsze 0. Atrybut head ułożenia jest opcjonalne w [wykrywania](#Detection-Face-Detection) wyników i można sterować za pomocą [wykrywania](#Detection-Face-Detection) żądania przez parametr returnFaceAttributes. Jeśli parametr returnFaceAttributes zawiera "headPose", zwracane kroje będzie mieć head stanowić atrybutów.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>Identyfikacja

Aby zidentyfikować kroje co najmniej jeden z LargePersonGroup/PersonGroup jest identyfikacja.
A [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) jest kolekcją [osób](#Person).
Kroje i LargePersonGroup/PersonGroup są reprezentowane przez [stają przed identyfikatorów](#Face-ID) i [identyfikatorów LargePersonGroup](#LargePersonGroup-ID)/[identyfikatorów PersonGroup](#PersonGroup-ID) odpowiednio w żądanie.
Określone wyniki są [kandydatów](#Candidate), reprezentowanego przez [osób](#Person) bez obaw.
Wiele kroje w danych wejściowych są traktowane jako oddzielnie i każdej powierzchni mają zidentyfikowanych wyniku.

> [!NOTE]
> LargePersonGroup/PersonGroup powinny być uczony pomyślnie przed identyfikacji. Jeśli nie jest uczenia LargePersonGroup/PersonGroup, lub szkolenia [stan](#Status-Train) nie jest wyświetlany jako "succeeded" (np. "uruchomiona", "nie powiodło się" lub 'timeout'), odpowiedź na żądania wynosi 400.
> 

Więcej informacji można znaleźć w następujących przewodnikach: [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [ LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical jest polem z [weryfikacji](#Verification) wyniki wskazujące, czy obie strony należą do tej samej osoby.

Więcej informacji można znaleźć w podręczniku [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>tys.

## <a name="l"></a>L

#### <a name="landmarks"></a>Punkty orientacyjne

Zobacz [stają przed punkty orientacyjne](#Face-Landmarks-Facial-Landmarks).

#### <a name="LargeFaceList"></a>LargeFaceList

LargeFaceList jest kolekcją [PersistedFace](#PersistedFace) i to jednostka [Znajdź podobne](#Find-Similar). LargeFaceList jest dostarczany z [identyfikator LargeFaceList](#LargeFaceList-ID)oraz innych atrybutów, takich jak [nazwa](#Name) i [dane użytkownika](#UserData-User-Data).

Więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - krój listy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="LargeFaceList-ID"></a>Identyfikator LargeFaceList

Identyfikator LargeFaceList jest używany jako identyfikator ciągu podane przez użytkownika [LargeFaceList](#LargeFaceList). Identyfikator LargeFaceList musi być unikatowa w ramach subskrypcji.

Więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="LargePersonGroup"></a>LargePersonGroup

LargePersonGroup jest kolekcją [osób](#Person) i to jednostka [identyfikacji](#Identification). LargePersonGroup jest dostarczany z [identyfikator LargePersonGroup](#LargePersonGroup-ID)oraz innych atrybutów, takich jak [nazwa](#Name) i [dane użytkownika](#UserData-User-Data).

Więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [osoba LargePersonGroup — lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="LargePersonGroup-ID"></a>Identyfikator LargePersonGroup

Identyfikator LargePersonGroup jest używany jako identyfikator ciągu podane przez użytkownika [LargePersonGroup](#LargePersonGroup). Identyfikator LargePersonGroup musi być unikatowa w ramach subskrypcji.

Więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="Messy-Group"></a>Grupy lepiej

Grupy lepiej jest pochodną [grupowanie](#Grouping) wyniki; które zawiera kroje nie jest podobne do innych powierzchni. Każdą powierzchnię grupy lepiej jest określane przez [stają przed Identyfikatorem](#Face-ID).

Więcej informacji można znaleźć w podręczniku [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Nie

#### <a name="name-person"></a>Nazwa (osoba)

Nazwa jest przyjazną dla użytkownika ciąg opisujący [osoby](#Person). W odróżnieniu od [identyfikator osoby](#Person-ID), nazwę osoby można duplikować w obrębie grupy.

Więcej informacji można znaleźć w następujących przewodnikach: [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup - mieć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Pobierz osoba PersonGroup —](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Name"></a>Nazwa (LargePersonGroup/PersonGroup)

Nazwa jest również przyjazny dla użytkownika ciąg opisujący [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). W odróżnieniu od [identyfikator LargePersonGroup](#LargePersonGroup-ID)/[identyfikator PersonGroup](#PersonGroup-ID), nazwę LargePersonGroups/PersonGroups można duplikować, w ramach subskrypcji.

Więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [ PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace jest strukturą danych w interfejsie API twarzy na obrazie. PersistedFace jest dostarczany z [identyfikator PersistedFace](#PersistedFace-ID)oraz innych atrybutów, takich jak [nazwa](#Name), i [dane użytkownika](#UserData-User-Data).

Więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ Osoba PersonGroup — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person-ID"></a>Identyfikator osoby

Identyfikator osoby jest generowany po [PersistedFace](#PersistedFace) pomyślnie utworzony. Ciąg jest tworzony do reprezentowania tej powierzchni w [powierzchni interfejsu API](#Face-API).

Więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ Osoba PersonGroup — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person"></a>Osoby

Osoba jest strukturą danych zarządzanych powierzchni interfejsu API. Osoba jest dostarczany z [identyfikator osoby](#Person-ID)oraz innych atrybutów, takich jak [nazwa](#Name), Kolekcja [PersistedFace](#PersistedFace), i [dane użytkownika](#UserData-User-Data).

Więcej informacji można znaleźć w następujących przewodnikach: [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup - mieć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Pobierz osoba PersonGroup —](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Person-ID"></a>Identyfikator osoby

Identyfikator osoby jest generowany po [osoby](#Person) pomyślnie utworzony. Ciąg jest tworzony do reprezentowania tej osoby w [powierzchni interfejsu API](#Face-API).

Więcej informacji można znaleźć w następujących przewodnikach: [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup - mieć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Pobierz osoba PersonGroup —](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup jest kolekcją [osób](#Person) i to jednostka [identyfikacji](#Identification). PersonGroup jest dostarczany z [identyfikator PersonGroup](#PersonGroup-ID)oraz innych atrybutów, takich jak [nazwa](#Name) i [dane użytkownika](#UserData-User-Data).

Więcej informacji można znaleźć w następujących przewodnikach: [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [osoba PersonGroup — lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="PersonGroup-ID"></a>Identyfikator PersonGroup

Identyfikator PersonGroup jest używany jako identyfikator ciągu podane przez użytkownika [PersonGroup](#PersonGroup). Identyfikator grupy musi być unikatowa w ramach subskrypcji.

Więcej informacji można znaleźć w następujących przewodnikach: [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Stanowić (atrybut)

Zobacz [Head stanowić](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="Recognition"></a>Rozpoznawanie

Rozpoznawanie jest obszarem popularnych aplikacji krój technologii, takich jak [Znajdź podobne](#Find-Similar), [grupowanie](#Grouping), [Identyfikuj](#Identification),[weryfikowanie dwa skierowany takie same lub nie ](#Verification).

Więcej informacji można znaleźć w następujących przewodnikach: [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Prostokąt (krój)

Zobacz [prostokąt krój](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>Łuk w górę (atrybut)

Uśmiech jest jednym z [atrybuty](#Attributes) opisuje wyrażenia uśmiech dostępne powierzchni. Atrybut uśmiech jest opcjonalne w [wykrywania](#Detection-Face-Detection) wyników i można sterować za pomocą [wykrywania](#Detection-Face-Detection) żądania returnFaceAttributes. Jeśli returnFaceAttributes zawiera "łuk w górę", zwracane kroje ma atrybuty uśmiech.

Więcej informacji można znaleźć w podręczniku [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Wyszukiwanie podobnych twarzy

Zobacz [Znajdź podobne](#Find-Similar).

#### <a name="Status-Train"></a>Stan (pociąg)

Stan to ciąg używany do opisywania procedury [szkolenia LargeFaceList/LargePersonGroups/PersonGroups](#Train), łącznie z "notstarted", "uruchomiona", "powiodło się", "nie powiodło się".

Więcej informacji można znaleźć w podręczniku [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji jest ciągiem, który należy określić jako parametr ciągu zapytania, aby można było wywołać jakiegokolwiek powierzchni interfejsu API. Klucza subskrypcji można znaleźć na stronie Moje subskrypcje po zalogowaniu się do portalu kognitywnych usług firmy Microsoft. Będzie dwa klucze skojarzone z każdej subskrypcji: jeden klucz podstawowy i klucz pomocniczy jeden. Jednocześnie może służyć do wywołania interfejsu API tak samo. Należy zapewnić bezpieczeństwo kluczy subskrypcji i ponownie wygenerować klucze subskrypcję w dowolnym momencie ze strony Moje subskrypcje, a także.

## <a name="t"></a>T

#### <a name="Train"></a>Szkolenie (LargeFaceList LargePersonGroup/PersonGroup)

Ten interfejs API jest używany wstępnie przetworzyć [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) zapewnienie [znaleźć Podobne](#Find-Similar)/[identyfikacji](#Identification) wydajności. Jeśli szkolenia nie jest obsługiwane, lub [stan szkolenia](#Status-Train) nie jest wyświetlany jako zakończyło się pomyślnie, identyfikator dla tego PersonGroup wystąpi błąd.

Więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [stają przed — ustalenie ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>Danych użytkownika/dane użytkownika

Dane użytkownika są dodatkowe informacje skojarzone z [osoby](#Person) i [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Dane użytkownika ustawiono przez użytkowników ułatwiają danych użycia, zrozumieć i pamiętaj.

Więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [Osoba LargePersonGroup — aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup — aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Osoba PersonGroup — aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="Verification"></a>Weryfikacja

Ten interfejs API jest używany do sprawdzania, czy obie strony są takie same lub nie. Obie strony są reprezentowane jako stają przed identyfikatorów w żądaniu. Zweryfikować wyniki zawierają polem ([isIdentical](#Is-Identical)) takie same wskazująca, czy jest to true i pole Liczba ([zaufania](#Confidence)) wskazująca poziom zaufania.

Więcej informacji można znaleźć w podręczniku [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Tak

## <a name="z"></a>Z
