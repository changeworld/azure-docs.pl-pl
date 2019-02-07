---
title: Słownik — Usługa interfejsu API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: W glosariuszu opisano terminy, które możesz napotkać podczas pracy z usługą interfejsu API rozpoznawania twarzy.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 06c385b1ca43277b59c337646c41cae360ce48a5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816564"
---
# <a name="glossary"></a>Słownik

## <a name="a"></a>A

#### <a name="attributes"></a>Atrybuty

Atrybuty są opcjonalne w wynikach wykrywania, takie jak [wiek](#Age-Attribute), [płeć](#Gender-Attribute), [poza siedzibę](#Head-Pose-Attribute), [zarost](#Facial-Hair-Attribute), [uśmiech ](#Smile-Attribute).
Można je uzyskać z interfejsu API wykrywania, określając parametry zapytania: returnFaceAttributes. Atrybuty zapewniają dodatkowe informacje dotyczące wybranego [twarzy](#Face); oprócz [face ID](#Face-ID) i [prostokąt](#Face-Rectangle).

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Wiek (atrybut)

Okres ważności jest jednym z [atrybuty](#Attributes) opisujący wiek konkretnej powierzchnię. Atrybut wiek jest opcjonalna w wynikach wykrywania i pozwala kontrolować, określając parametr returnFaceAttributes żądaniu wykrywania.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="candidate"></a>Release Candidate

Kandydaci to zasadniczo [identyfikacji](#Identification) wyników (np. określonych osób i poziom zaufania do wykrycia). Kandydat jest reprezentowany przez [PersonID](#Person-ID) i [ufności](#Confidence), wskazujący, że osoby jest identyfikowany za pomocą wysoki poziom zaufania.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Ufność

UFNOŚĆ miary, odsłaniając podobieństwa między [twarzy](#Face) lub [osoby](#Person) w wartości liczbowe — która zostanie użyta w [identyfikacji](#Identification), i [ Weryfikacja](#Verification) do wskazania podobieństwa wyniki wyszukiwanego, zidentyfikowany i zweryfikowane.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Twarzy — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [twarzy — weryfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Wykrywanie twarzy wykrywania

Wykrywanie twarzy jest to akcja lokalizowania twarze na obrazach. Użytkownicy mogą przekazać obraz lub określić adres URL obrazu w żądaniu. Wykryte twarze są zwracane z [twarzy identyfikatory](#Face-ID) wskazujący unikatową tożsamość w interfejsie API rozpoznawania twarzy. Prostokątów wskazania lokalizacji twarzy na obrazie w pikselach, jak również opcjonalne [atrybuty](#Attributes) każdej twarzy, takie jak [wiek](#Age-Attribute), [płeć](#Gender-Attribute), [poza siedzibę ](#Head-Pose-Attribute), [zarost](#Facial-Hair-Attribute) i [uśmiechające](#Smile-Attribute).

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="face"></a>Rozpoznawanie twarzy

Krój to ujednolicone termin określający wyniki pochodzące z interfejsu API rozpoznawania twarzy, związane z wykryte twarze. Ostatecznie twarzy jest reprezentowany przez ujednolicony tożsamości ([funkcji Face ID](#Face-ID)), określony region w obrazach ([obrys twarzy](#Face-Rectangle)) i dodatkowych twarzy powiązanych atrybutów, takich jak [wiek](#Age-Attribute), [płeć](#Gender-Attribute), charakterystycznych elementów krajobrazu i [poza siedzibę](#Head-Pose-Attribute). Ponadto twarzy mogą być zwracane z wykrywania.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Interfejs API rozpoznawania twarzy

Interfejs API rozpoznawania twarzy jest oparta na chmurze interfejs API, który udostępnia najbardziej zaawansowane algorytmy wykrywania twarzy i rozpoznawania. Główne funkcje interfejsu API rozpoznawania twarzy można podzielić na dwie kategorie: wykrywanie z atrybutami i twarzy twarzy [rozpoznawania](#Recognition).

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Omówienie interfejsu API twarzy](./Overview.md), [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [twarzy — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [twarzy — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ Twarzy — weryfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Atrybuty twarzy/atrybutów twarzy

Zobacz [atrybuty](#Attributes).

#### <a name="face-id"></a>Identyfikator twarzy

Funkcji Face ID jest tworzony na podstawie wyników wykrywania, w których ciąg reprezentuje [twarzy](#Face) w [interfejsu API rozpoznawania twarzy](#Face-API).

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>To punktów charakterystycznych elementów krajobrazu/twarzy charakterystycznych

Charakterystycznych elementów krajobrazu są opcjonalne w wynikach wykrywania; które są semantycznego punktów twarzy, takich jak Ty masz dostęp, czołowych i ujścia (zilustrowane na poniższym rysunku). Charakterystycznych elementów krajobrazu można kontrolować z żądaniem wykrywania, logiczna returnFaceLandmarks liczb. Jeśli returnFaceLandmarks jest ustawiona na wartość true, zwrócone twarzy będzie miał charakterystycznych elementów krajobrazu atrybutów.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Obrys twarzy

Obrys twarzy jest tworzony na podstawie wyników wykrywania, czyli prostokąt pionowo (po lewej stronie, top, szerokość, wysokość) w przypadku obrazów w pikselach. W lewym górnym rogu [twarzy](#Face) (lewo, top), oprócz szerokość i wysokość, wskazuje rozmiarów twarzy na platformie x i osi y, odpowiednio.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Zarost (atrybut)

Zarost jest jednym z [atrybuty](#Attributes) używane do opisywania długość zarost dostępne twarzy. Atrybut zarost jest opcjonalna w wynikach wykrywania i mogą być kontrolowane za pomocą żądania wykrywania returnFaceAttributes. Jeśli returnFaceAttributes zawiera "facialHair", zwracane twarzy będzie miał zarost atrybutów.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList to zbiór [PersistedFace](#PersistedFace) i jest jednostką [Znajdź podobne](#Find-Similar). Dołączono FaceList [identyfikator FaceList](#FaceList-ID), jak również inne atrybuty, takie jak nazwa i dane użytkownika.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [FaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>Identyfikator FaceList

Identyfikator FaceList jest ciągiem dostarczone przez użytkownika, używany jako identyfikator [FaceList](#FaceList). Identyfikator FaceList musi być unikatowa w ramach subskrypcji.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [FaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Znajdź podobne

Ten interfejs API jest używany do wyszukiwania podobnych twarzy na podstawie kolekcji twarzy. Twarzy kwerend i kolekcji twarzy, które są reprezentowane jako [twarzy identyfikatory](#Face-ID) lub [identyfikator FceList](#FaceList-ID)/[identyfikator LargeFaceList](#LargeFaceList-ID) w żądaniu. Zwrócone wyniki są przeszukiwane podobnych twarzy, reprezentowane przez [twarzy identyfikatory](#Face-ID) lub identyfikatory PersistedFace.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Twarzy — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Płeć (atrybut)

Płeć jest jednym z [atrybuty](#Attributes) używane do opisywania płci dostępne twarze. Atrybut płeć jest opcjonalna w wynikach wykrywania i mogą być kontrolowane za pomocą żądania wykrywania returnFaceAttributes. Jeśli returnfaceAttributes zawiera "płeć", zwracane twarzy będzie mieć atrybuty płci.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Grupowanie

Grupowanie twarzy jest grupowanie kolekcji zgodnie z twarzy podobieństwa. Kolekcje twarzy są wskazywane przez kolekcje identyfikator twarzy w żądaniu. W wyniku grupowanie podobnych twarzy są zgrupowane razem jako [grup](#Groups), i powierzchnie, które nie są podobne do innych twarzy są scalane jako grupę nieuporządkowane. Ma co najwyżej jeden [nieuporządkowane grupy](#Messy-Group) w wyniku grupowania.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Grupy

Grupy są uzyskiwane z [grupowanie](#Grouping) wyników. Każda grupa zawiera kolekcję podobnych twarzy, gdzie twarze są wskazywane przez [twarzy identyfikatory](#Face-ID).

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>HEAD stanowić (atrybut)

Poza HEAD jest jednym z [atrybuty](#Attributes) reprezentuje twarzy w przestrzeni 3D zgodnie z kąty wdrożenie, skoku i odchylenia, orientacji, jak pokazano na poniższej ilustracji. Zakresy wartości Przywracanie i yaw są [-180, 180] i [-90, 90] w stopniach. W bieżącej wersji pomysłu wartość zwracana z wykrywania zawsze wynosi 0. Atrybut ułożenia główny jest opcjonalny w wynikach wykrywania i mogą być kontrolowane za pomocą żądania wykrywania przez parametr returnFaceAttributes. Jeśli parametr returnFaceAttributes zawiera "headPose", zwracane twarzy będzie mieć główny każdego z atrybutów.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identyfikacja

Identyfikator jest identyfikowanie twarzy co najmniej jeden z LargePersonGroup/grupie.
A [grupie](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) to zbiór [osób](#Person).
Twarzy i LargePersonGroup/grupie są reprezentowane przez [twarzy identyfikatory](#Face-ID) i [identyfikatory LargePersonGroup](#LargePersonGroup-ID)/[identyfikatory grupie](#PersonGroup-ID) odpowiednio w żądanie.
Określone wyniki są [kandydatów](#Candidate), jest reprezentowana przez [osób](#Person) bez obaw.
Wiele powierzchni w danych wejściowych są traktowane jako oddzielnie i każdej twarzy zostaną zidentyfikowane wynik.

> [!NOTE]
> LargePersonGroup/grupie powinni przejść szkolenie dotyczące pomyślnie przed identyfikacji. Jeśli nie jest uczony LargePersonGroup/grupie, lub szkolenia [stan](#Status-Train) nie jest wyświetlany jako "Powodzenie" (czyli "uruchomiona", "failed" lub "timeout"), odpowiedzi na żądanie jest 400.
> 

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [grupie Osoba — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [grupie — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical jest polem z [weryfikacji](#Verification) wyniki wskazujące, czy dwie twarze należą do tej samej osoby.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — weryfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>tys.

## <a name="l"></a>L

#### <a name="landmarks"></a>Charakterystycznych elementów krajobrazu

Zobacz to punktów charakterystycznych.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList to zbiór [PersistedFace](#PersistedFace) i jest jednostką [Znajdź podobne](#Find-Similar). Dołączono LargeFaceList [identyfikator LargeFaceList](#LargeFaceList-ID), jak również inne atrybuty, takie jak nazwa i dane użytkownika.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - List twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList ID

Identyfikator LargeFaceList jest używany jako identyfikator typu ciąg podane przez użytkownika [LargeFaceList](#LargeFaceList). Identyfikator LargeFaceList musi być unikatowa w ramach subskrypcji.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup to zbiór [osób](#Person) i jest jednostką [identyfikacji](#Identification). Dołączono LargePersonGroup [identyfikator LargePersonGroup](#LargePersonGroup-ID), jak również inne atrybuty, takie jak nazwa i dane użytkownika.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [osoba LargePersonGroup — lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>Identyfikator LargePersonGroup

Identyfikator LargePersonGroup jest używany jako identyfikator typu ciąg podane przez użytkownika [LargePersonGroup](#LargePersonGroup). Identyfikator LargePersonGroup musi być unikatowa w ramach subskrypcji.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Grupa nieuporządkowane

Nieuporządkowane grupy jest tworzony na podstawie [grupowanie](#Grouping) wyniki; które zawiera twarzy nie jest podobne do innych twarzy. Każdej twarzy nieuporządkowane grupy jest wskazywany przez [face ID](#Face-ID).

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Nie

#### <a name="name-person"></a>Nazwa (osoba)

Nazwa jest przyjazny dla użytkownika ciąg opisujący [osoby](#Person). W odróżnieniu od [identyfikator osoby](#Person-ID), nazwa osoby mogą być zduplikowane w obrębie grupy.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [osoba grupie — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Nazwa (LargePersonGroup/grupie)

Nazwa jest także przyjazny dla użytkownika ciąg opisujący [LargePersonGroup](#LargePersonGroup)/[grupie](#PersonGroup). W odróżnieniu od [identyfikator LargePersonGroup](#LargePersonGroup-ID)/[identyfikator grupie](#PersonGroup-ID), mogą być zduplikowane nazwy grup LargePersonGroups/osób w ramach subskrypcji.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [grupie - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace jest strukturą danych w interfejsie API rozpoznawania twarzy. PersistedFace jest powiązana z Identyfikatorem PersistedFace, jak również inne atrybuty, takie jak nazwa i dane użytkownika.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [osoba LargePersonGroup — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [osoba grupie — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>Identyfikator osoby

Identyfikator osoby jest generowany podczas [PersistedFace](#PersistedFace) został utworzony pomyślnie. Ciąg jest tworzone do reprezentowania tej twarzy [interfejsu API rozpoznawania twarzy](#Face-API).

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [osoba LargePersonGroup — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [osoba grupie — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Person (Osoba)

Osoba jest strukturą danych zarządzanych w interfejsie API rozpoznawania twarzy. Osoby, który jest dostarczany z [identyfikator osoby](#Person-ID), jak również inne atrybuty, takie jak nazwa, zbiór [PersistedFace](#PersistedFace)i dane użytkowników.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [osoba grupie — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>Identyfikator osoby

Identyfikator osoby jest generowany podczas [osoby](#Person) został utworzony pomyślnie. Ciąg jest tworzone do reprezentowania tej osoby w [interfejsu API rozpoznawania twarzy](#Face-API).

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [osoba grupie — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

Grupie jest kolekcją [osób](#Person) i jest jednostką [identyfikacji](#Identification). Dołączono grupie [identyfikator grupie](#PersonGroup-ID), jak również inne atrybuty, takie jak nazwa i dane użytkownika.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [grupie - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [osoba grupie — lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>Identyfikator w grupie

Identyfikator grupie jest używany jako identyfikator typu ciąg podane przez użytkownika [grupie](#PersonGroup). Identyfikator grupy musi być unikatowa w ramach subskrypcji.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [grupie - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Stanowić (atrybut)

Zobacz [stanowić główny](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="recognition"></a>Rozpoznawanie

Rozpoznawanie jest obszar popularnych aplikacji w technologii rozpoznawania twarzy [Znajdź podobne](#Find-Similar), [grupowanie](#Grouping), [identyfikowanie](#Identification),[weryfikowanie dwa takie same twarzy, czy nie ](#Verification).

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [Twarzy — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [twarzy — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [twarzy — weryfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Prostokąt (twarzy)

Zobacz [obrys twarzy](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Wyszukiwanie podobnych twarzy

Zobacz [Znajdź podobne](#Find-Similar).

#### <a name="smile-attribute"></a>Uśmiech (atrybut)

Uśmiech jest jednym z [atrybuty](#Attributes) używane do opisywania wyrażenia uśmiech dostępne twarzy. Atrybut uśmiech jest opcjonalna w wynikach wykrywania i mogą być kontrolowane za pomocą żądania wykrywania returnFaceAttributes. Jeśli returnFaceAttributes zawiera "uśmiech", zwracane twarzy będzie miał uśmiech atrybutów.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Snapshot

Migawka jest tymczasowego magazynu zdalnego dla niektórych typów danych twarzy. Działa ona jako rodzaj Schowka do kopiowania danych z jednej subskrypcji do innego. Najpierw użytkownik "tworzy" migawkę danych w subskrypcji źródłowej, a następnie "odnoszą się" go do nowego obiektu danych w subskrypcji docelowej. 

Aby uzyskać więcej informacji, zobacz [Przewodnik po migracji twarzy](./face-api-how-to-topics/how-to-migrate-face-data.md) , jak również [wykonać migawki -](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/take) i [migawki — Zastosuj](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/apply) dokumentacji (REST).

#### <a name="status-train"></a>Stan (Train)

Ciąg używany do opisania procedury do szkolenia LargeFaceList/LargePersonGroups/grup osób, w tym stanie "notstarted", "uruchomiona", "Powodzenie", "nie powiodło się" jest w stanie.

Aby uzyskać więcej informacji można znaleźć w podręczniku [LargeFaceList — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [grupie — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji jest ciągiem, który należy określić jako parametr ciągu zapytania, aby można było wywołać dowolny interfejs API rozpoznawania twarzy. Klucz subskrypcji można znaleźć na stronie Moje subskrypcje, po zalogowaniu się do portalu usług Microsoft Cognitive Services. Będą istnieć dwa klucze skojarzone z każdą subskrypcję: jeden klucz podstawowy i klucz pomocniczy jeden. Jednocześnie może służyć do wywołania interfejsu API identycznie. Musisz zapewnić bezpieczeństwo kluczy subskrypcji i w dowolnym momencie na stronie Moje subskrypcje również można ponownie wygenerować klucze subskrypcji.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Szkolenie (LargeFaceList LargePersonGroup/grupie)

Ten interfejs API jest używany wstępnie przetworzyć [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[grupie](#PersonGroup) zapewnienie [Znajdź Podobne](#Find-Similar)/[identyfikacji](#Identification) wydajności. Jeśli nieobsługiwanymi szkolenia lub [stan szkolenia](#Status-Train) nie jest wyświetlany jako zakończyło się pomyślnie, identyfikacja dla tej grupie spowoduje błąd.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargeFaceList — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [grupie — szkolenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/User Data

Dane użytkownika są dodatkowe informacje związane z [osoby](#Person) i [grupie](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Dane użytkownika ustawiono przez użytkowników była łatwiejsza do używania, zrozumieć i Zapamiętaj danych.

Aby uzyskać więcej informacji można znaleźć w następujących przewodnikach: [LargePersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [osoba LargePersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [osoba LargePersonGroup — aktualizowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [Grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [grupie — aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [osoba grupie — aktualizowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Weryfikacja

Ten interfejs API jest używany do sprawdzenia, czy dwie twarze powodują takie same, czy nie. Obie strony są reprezentowane jako twarzy identyfikatorów w żądaniu. Zweryfikowano wyniki zawierają pola logicznych (isIdentical) wskazujący takie same, w przypadku opcji true i pole Liczba ([ufności](#Confidence)) wskazujący poziom zaufania.

Aby uzyskać więcej informacji można znaleźć w podręczniku [twarzy — weryfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Tak

## <a name="z"></a>Z
