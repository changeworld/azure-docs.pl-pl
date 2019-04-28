---
title: 'Samouczek: Wykrywanie i oznaczanie ramką twarzy na obrazie za pomocą zestawu Android SDK'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz prostą aplikację dla systemu Android, która korzysta z interfejsu API rozpoznawania twarzy do wykrywania i oznaczania ramką twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: 5c7f2e86d6fe63d309c74d7304f1c19a714b6471
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815499"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Samouczek: Tworzenie aplikacji dla systemu Android wykrywającej i oznaczającej ramką twarze na obrazie

W tym samouczku utworzysz prostą aplikację dla systemu Android, która używa interfejsu API rozpoznawania twarzy platformy Azure, za pośrednictwem zestawu SDK Java, do wykrywania twarzy na obrazie. Aplikacja wyświetla wybrany obraz i rysuje ramkę wokół każdej wykrytej twarzy.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji systemu Android
> - Instalowanie biblioteki klienta interfejsu API rozpoznawania twarzy
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy

![Zrzut ekranu z systemu Android ze zdjęciem, na którym widać twarze otoczone czerwoną ramką](../Images/android_getstarted2.1.PNG)

Kompletny przykładowy kod jest dostępny w repozytorium [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji interfejsu API rozpoznawania twarzy. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też wykonać instrukcje z tematu [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Tworzenie konta usług Cognitive Services), aby subskrybować usługę interfejsu API rozpoznawania twarzy i uzyskać klucz.
- Program [Android Studio](https://developer.android.com/studio/) z poziomem interfejsu API 22 lub nowszym (wymagane przez bibliotekę klienta rozpoznawania twarzy).

## <a name="create-the-android-studio-project"></a>Tworzenie projektu programu Android Studio

Wykonaj następujące kroki, aby utworzyć nowy projekt aplikacji dla systemu Android.

1. W programie Android Studio wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).
1. Na ekranie **Create Android Project** (Tworzenie projektu systemu Android) zmodyfikuj domyślną zawartość pól, jeśli to konieczne, a następnie kliknij przycisk **Next** (Dalej).
1. Na ekranie **Target Android Devices** (Docelowe urządzenia z systemem Android) użyj rozwijanego selektora, aby wybrać interfejs **API 22** lub nowszy, a następnie kliknij przycisk **Next** (Dalej).
1. Wybierz pozycję **Empty Activity** (Puste działanie), a następnie kliknij przycisk **Next** (Dalej).
1. Usuń zaznaczenie pola wyboru **Backwards Compatibility** (Zgodność z wcześniejszymi wersjami), a następnie kliknij przycisk **Finish** (Zakończ).

## <a name="add-the-initial-code"></a>Dodawanie początkowego kodu

### <a name="create-the-ui"></a>Tworzenie interfejsu użytkownika

Otwórz plik *activity_main.xml*. W edytorze układu Layout Editor wybierz kartę **Text** (Tekst), a następnie zastąp zawartość następującym kodem.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?range=1-18)]

### <a name="create-the-main-class"></a>Tworzenie głównej klasy

Otwórz plik *MainActivity.java* i zastąp istniejące instrukcje `import` następującym kodem.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=3-11)]

Następnie zastąp zawartość klasy **MainActivity** następującym kodem. Utworzy on procedurę obsługi zdarzeń dla elementu **Button**, która uruchamia nowe działanie pozwalające użytkownikowi wybrać obraz. Wyświetla obraz w widoku **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=29-68)]

### <a name="try-the-app"></a>Testowanie aplikacji

Oznacz jako komentarz wywołanie funkcji **detectAndFrame** w metodzie **onActivityResult**. Następnie naciśnij pozycję **Uruchom** w menu, aby przetestować aplikację. Gdy aplikacja zostanie otwarta, w emulatorze lub na podłączonym urządzeniu, kliknij pozycję **Przeglądaj** na dole. Powinno zostać wyświetlone okno dialogowe wyboru pliku urządzenia. Wybierz obraz i sprawdź, czy jest on wyświetlany w oknie. Następnie zamknij aplikację i przejdź do kolejnego kroku.

![Zrzut ekranu z systemu Android przedstawiający zdjęcie z twarzami](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Dodawanie zestawu SDK interfejsu API rozpoznawania twarzy

### <a name="add-the-gradle-dependency"></a>Dodawanie zależności narzędzia Gradle

W okienku **Project** (Projekt) wybierz za pomocą rozwijanego selektora pozycję **Android**. Rozwiń pozycję **Gradle Scripts** (Skrypty Gradle), a następnie otwórz plik *build.gradle (Module: app)*. Dodaj zależność dla biblioteki klienta rozpoznawania twarzy, `com.microsoft.projectoxford:face:1.4.3`, jak pokazano na poniższym zrzucie ekranu, a następnie kliknij pozycję **Sync now** (Synchronizuj teraz).

![Zrzut ekranu z programu Android Studio przedstawiający plik build.gradle aplikacji](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Dodawanie kodu projektu związanego z rozpoznawaniem twarzy

Wróć do pliku **MainActivity.java** i dodaj następujące instrukcje `import`:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=13-14)]

Następnie wstaw następujący kod w klasie **MainActivity** powyżej metody **onCreate**:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=17-27)]

Konieczne będzie zastąpienie elementu `<Subscription Key>` kluczem subskrypcji. Należy także zastąpić element `<API endpoint>` punktem końcowym interfejsu API rozpoznawania twarzy, używając odpowiedniego identyfikatora regionu dla swojego klucza (zobacz [dokumentację interfejsu API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), aby zapoznać się z listą wszystkich punktów końcowych regionów). Klucze subskrypcji bezpłatnej wersji próbnej są generowane w regionie **westus**.

W okienku **Project** (Projekt) rozwiń pozycje **app** i **manifests**, a następnie otwórz plik *AndroidManifest.xml*. Wstaw następujący element jako bezpośredni element podrzędny elementu `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?range=5)]

## <a name="upload-image-and-detect-faces"></a>Przekazywanie obrazu i wykrywanie twarzy

Aplikacja będzie wykrywała twarze, wywołując metodę **FaceServiceClient.detect**, która opakowuje interfejs API REST [wykrywania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) i zwraca listę wystąpień obiektu **Face**.

Każdy zwrócony element **Face** zawiera prostokąt wskazujący lokalizację twarzy wraz z serią opcjonalnych atrybutów twarzy. W tym przykładzie żądane są tylko prostokąty wskazujące lokalizację twarzy.

Wstaw następujące dwie metody do klasy **MainActivity**. Należy pamiętać, że po zakończeniu procesu wykrywania twarzy aplikacja wywołuje metodę **drawFaceRectanglesOnBitmap**, aby zmodyfikować widok **ImageView**. Zdefiniujesz tę metodę w następnej kolejności.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=70-150)]

## <a name="draw-face-rectangles"></a>Rysowanie prostokątów twarzy

Wstaw następującą metodę pomocniczą do klasy **MainActivity**. Ta metoda rysuje prostokąt wokół każdej wykrytej twarzy przy użyciu współrzędnych prostokąta każdego wystąpienia elementu **Face**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=152-173)]

Na koniec usuń znaczniki komentarza z wywołania metody **detectAndFrame** w metodzie **onActivityResult**.

## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom aplikację i znajdź obraz z twarzą. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Wokół każdej twarzy na obrazie powinien pojawić się czerwony prostokąt.

![Zrzut ekranu systemu Android przedstawiający twarze z czerwonymi prostokątami narysowanymi wokół nich](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono podstawowy proces korzystania z zestawu SDK języka Java interfejsu API rozpoznawania twarzy dla usługi rozpoznawania twarzy oraz utworzono aplikację do wykrywania i oznaczania ramkami twarzy na obrazie. Teraz dowiedz się więcej o szczegółach wykrywania twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
