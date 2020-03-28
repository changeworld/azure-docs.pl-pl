---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 270a8316ee7c23ebb54c7d0633fb9159ef33acbd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383841"
---
1. Uruchom Android Studio i wybierz **pozycję Rozpocznij nowy projekt Android Studio** w oknie **powitalnym.**

    ![Zrzut ekranu okna powitalnego programu Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zostanie wyświetlony kreator **Wyboru projektu.** Wybierz **pozycję Telefon i tablet i** **pusta aktywność** w polu wyboru aktywności. Wybierz **pozycję Dalej**.

   ![Zrzut ekranu przedstawiający Kreatora wybierania projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na ekranie **Konfigurowanie projektu** wprowadź pozycję *Szybki start* jako **nazwa** i wprowadź *samples.speech.cognitiveservices.microsoft.com* jako **nazwę pakietu**. Następnie wybierz katalog projektu. Aby uzyskać **minimalny poziom interfejsu API,** wybierz **API 23: Android 6.0 (Marshmallow)**. Pozostaw wszystkie pozostałe pola wyboru wyczyść i wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający kreatora konfigurowania projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Przygotowanie nowego projektu Android w programie Android Studio zajmie trochę czasu. Następnie skonfiguruj projekt, aby wiedział o sdk mowy usług Azure Cognitive Services i używał oprogramowania Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja SDK mowy usług Cognitive Services jest 1.10.0.

Zestaw Speech SDK dla systemu Android znajduje się w pakiecie o nazwie [AAR (biblioteka Android)](https://developer.android.com/studio/projects/android-library) i zawiera niezbędne biblioteki oraz wymagane uprawnienia systemu Android.
Jest hostowany w repozytorium Maven pod\/adresem https: /csspeechstorage.blob.core.windows.net/maven/.

Skonfiguruj projekt do korzystania z zestawu Speech SDK. Otwórz okno **Struktura projektu,** wybierając**strukturę projektu** **pliku** > na pasku menu Programu Android Studio. W oknie **Struktura projektu** wykonuj następujące zmiany:

1. Na liście po lewej stronie okna wybierz pozycję **Project** (Project). Edytuj **domyślne ustawienia repozytorium biblioteki,** dołączając przecinek i adres URL repozytorium Maven ujęty w pojedynczy cudzysłów: 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na tym samym ekranie po lewej stronie wybierz pozycję **aplikacja**. Następnie wybierz kartę **Dependencies** (Zależności) w górnej części okna. Wybierz zielony znak**+** plus ( ) i wybierz z menu rozwijanego pozycję **Zależność biblioteki.**

   ![Zrzut ekranu przedstawiający zależność biblioteki](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję SDK mowy dla systemu Android, *com.microsoft.cognitiveservices.speech:client-sdk:1.10.0*. Następnie wybierz przycisk **OK**.
   SDK mowy powinny zostać dodane do listy zależności teraz, jak pokazano:

   ![Zrzut ekranu przedstawiający pakiet SDK mowy na liście zależności](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wybierz kartę **Właściwości.** Dla zgodności **źródła** i **zgodności docelowej**wybierz **1.9**.

   ![Zrzut ekranu przedstawiający zgodność źródła i zgodność docelową](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Wybierz **przycisk OK,** aby **zamknąć** okno Struktura projektu i zastosować zmiany do projektu.
