---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: e1f9b561efb1fd88fe2dd00f8c175f1ebae848d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391725"
---
1. Uruchom program Android Studio, a następnie w oknie powitalnym wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).

    ![Zrzut ekranu okna powitalnego programu Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zostanie wyświetlony Kreator **Wybierz projekt** , wybierz pozycję **telefon i tablet** i **puste działanie** w polu wyboru działania. Wybierz opcję **Dalej**.

   ![Zrzut ekranu przedstawiający kreatora wyboru projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na ekranie **Konfigurowanie projektu** wprowadź **Szybki Start** jako **nazwę**, **Samples.Speech.cognitiveservices.Microsoft.com** jako **nazwę pakietu**, a następnie wybierz katalog projektu. W przypadku **minimalnego poziomu interfejsu API** wybierz **interfejs API 23: Android 6,0 (Marshmallow)** pozostaw wszystkie inne pola wyboru niezaznaczone i wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający Kreatora konfigurowania projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Przygotowanie nowego projektu Android w programie Android Studio zajmie trochę czasu. Następnie skonfiguruj projekt, aby rozpoznawał zestaw Speech SDK i używał języka Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.7.0`.

Zestaw Speech SDK dla systemu Android znajduje się w pakiecie o nazwie [AAR (biblioteka Android)](https://developer.android.com/studio/projects/android-library) i zawiera niezbędne biblioteki oraz wymagane uprawnienia systemu Android.
Jest ona hostowana w repozytorium Maven przy użyciu protokołu https: \//csspeechstorage. blob. Core. Windows. NET/Maven/.

Skonfiguruj projekt do korzystania z zestawu Speech SDK. Otwórz okno Project Structure (Struktura projektu), wybierając kolejno pozycje **File (Plik)**  > **Project Structure (Struktura projektu)** z paska menu programu Android Studio. W oknie Project Structure (Struktura projektu), wprowadź następujące zmiany:

1. Na liście po lewej stronie okna wybierz pozycję **Project** (Project). Edytuj ustawienia **Default Library Repository** (Domyślne repozytorium biblioteki), dodając przecinek i adres URL repozytorium Maven ujęty w pojedynczy cudzysłów. "https: \//csspeechstorage. blob. Core. Windows. NET/Maven/"

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. W tym samym oknie po lewej stronie wybierz pozycję **app** (aplikacja). Następnie wybierz kartę **Dependencies** (Zależności) w górnej części okna. Wybierz zielony znak plus (+) i z menu rozwijanego wybierz pozycję **Library dependency** (Zależność biblioteki).

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję zestawu Speech SDK dla systemu Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`. Następnie wybierz przycisk **OK**.
   Teraz należy dodać zestaw Speech SDK do listy zależności, jak przedstawiono poniżej:

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wybierz kartę **Właściwości** . W przypadku **zgodności źródłowej** i **zgodności docelowej**wybierz pozycję **1,8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Naciśnij przycisk **OK**, aby zamknąć okno Project Structure (Struktura projektu) i zastosować zmiany do projektu.
