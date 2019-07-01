---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485027"
---
1. Uruchom program Android Studio, a następnie w oknie powitalnym wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).

    ![Zrzut ekranu okna powitalnego programu Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **Wybierz projekt** pojawi się Kreator, wybierz **telefony i tablety** i **puste działanie** w polu Wybieranie działania. Wybierz opcję **Dalej**.

   ![Zrzut ekranu przedstawiający Wybieranie Kreatora projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. W **Skonfiguruj projekt** ekranu, należy wprowadzić **Szybki Start** jako **nazwa**, **samples.speech.cognitiveservices.microsoft.com** jako **Nazwy pakietu**i wybierz katalog projektu. Aby uzyskać **poziom interfejsu API z co najmniej** wybierz **interfejsu API 23: System android 6.0 (Marshmallow)** , pozostaw wszystkie pozostałe pola wyboru nie zaznaczone i wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający Konfigurowanie kreatora projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Przygotowanie nowego projektu Android w programie Android Studio zajmie trochę czasu. Następnie skonfiguruj projekt, aby rozpoznawał zestaw Speech SDK i używał języka Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.6.0`.

Zestaw Speech SDK dla systemu Android znajduje się w pakiecie o nazwie [AAR (biblioteka Android)](https://developer.android.com/studio/projects/android-library) i zawiera niezbędne biblioteki oraz wymagane uprawnienia systemu Android.
Znajduje się w repozytorium Maven na https:\//csspeechstorage.blob.core.windows.net/maven/.

Skonfiguruj projekt do korzystania z zestawu Speech SDK. Otwórz okno Project Structure (Struktura projektu), wybierając kolejno pozycje **File (Plik)**  > **Project Structure (Struktura projektu)** z paska menu programu Android Studio. W oknie Project Structure (Struktura projektu), wprowadź następujące zmiany:

1. Na liście po lewej stronie okna wybierz pozycję **Project** (Project). Edytuj ustawienia **Default Library Repository** (Domyślne repozytorium biblioteki), dodając przecinek i adres URL repozytorium Maven ujęty w pojedynczy cudzysłów. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. W tym samym oknie po lewej stronie wybierz pozycję **app** (aplikacja). Następnie wybierz kartę **Dependencies** (Zależności) w górnej części okna. Wybierz zielony znak plus (+) i z menu rozwijanego wybierz pozycję **Library dependency** (Zależność biblioteki).

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję zestawu Speech SDK dla systemu Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Następnie wybierz przycisk **OK**.
   Teraz należy dodać zestaw Speech SDK do listy zależności, jak przedstawiono poniżej:

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wybierz kartę **Properties** (Właściwości). Dla obu pozycji **Source Compatibility** (Zgodność kodu źródłowego) i **Target Compatibility** (Zgodność kodu docelowego) wybierz **1.8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Naciśnij przycisk **OK**, aby zamknąć okno Project Structure (Struktura projektu) i zastosować zmiany do projektu.
