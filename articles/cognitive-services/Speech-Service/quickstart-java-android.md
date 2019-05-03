---
title: 'Szybki start: rozpoznawanie mowy, Java (Android) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: dabae62b9e308bc97850ee36fa735b8a11c4d6f0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020969"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku Java w systemie Android przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Z tego artykułu dowiesz się, jak utworzyć aplikację języka Java dla systemu Android przy użyciu zestawu Speech SDK usługi Cognitive Services, aby wykonać transkrypcję mowy na tekst.
Aplikacja zależy od pakietu Maven zestaw SDK rozpoznawania mowy, wersja 1.5.0 i Android Studio 3.3.
Zestaw Speech SDK jest obecnie zgodny z urządzeniami z systemem Android oraz 32 lub 64-bitowymi procesorami ARM i procesorami x86 lub x64 firmy Intel.

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Należy klucz subskrypcji usług przetwarzania mowy, aby ukończyć ten przewodnik Szybki Start. Możesz go uzyskać bezpłatnie. Zobacz [bezpłatnego wypróbowania usługi mowy](get-started.md) Aby uzyskać szczegółowe informacje.

## <a name="create-and-configure-a-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom program Android Studio, a następnie w oknie powitalnym wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).

    ![Zrzut ekranu okna powitalnego programu Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **Wybierz projekt** pojawi się Kreator, wybierz **telefony i tablety** i **puste działanie** w polu Wybieranie działania. Wybierz opcję **Dalej**.

   ![Zrzut ekranu przedstawiający Wybieranie Kreatora projektu](media/sdk/qs-java-android-02-target-android-devices.png)

1. W **Skonfiguruj projekt** ekranu, należy wprowadzić **Szybki Start** jako **nazwa**, **samples.speech.cognitiveservices.microsoft.com** jako **Nazwy pakietu**i wybierz katalog projektu. Aby uzyskać **poziom interfejsu API z co najmniej** wybierz **interfejsu API 23: System android 6.0 (Marshmallow)**, pozostaw wszystkie pozostałe pola wyboru nie zaznaczone i wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający Konfigurowanie kreatora projektu](media/sdk/qs-java-android-03-create-android-project.png)

Przygotowanie nowego projektu Android w programie Android Studio zajmie trochę czasu. Następnie skonfiguruj projekt, aby rozpoznawał zestaw Speech SDK i używał języka Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.5.0`.

Zestaw Speech SDK dla systemu Android znajduje się w pakiecie o nazwie [AAR (biblioteka Android)](https://developer.android.com/studio/projects/android-library) i zawiera niezbędne biblioteki oraz wymagane uprawnienia systemu Android.
Znajduje się w repozytorium Maven na https:\//csspeechstorage.blob.core.windows.net/maven/.

Skonfiguruj projekt do korzystania z zestawu Speech SDK. Otwórz okno Project Structure (Struktura projektu), wybierając kolejno pozycje **File (Plik)** > **Project Structure (Struktura projektu)** z paska menu programu Android Studio. W oknie Project Structure (Struktura projektu), wprowadź następujące zmiany:

1. Na liście po lewej stronie okna wybierz pozycję **Project** (Project). Edytuj ustawienia **Default Library Repository** (Domyślne repozytorium biblioteki), dodając przecinek i adres URL repozytorium Maven ujęty w pojedynczy cudzysłów. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](media/sdk/qs-java-android-06-add-maven-repository.png)

1. W tym samym oknie po lewej stronie wybierz pozycję **app** (aplikacja). Następnie wybierz kartę **Dependencies** (Zależności) w górnej części okna. Wybierz zielony znak plus (+) i z menu rozwijanego wybierz pozycję **Library dependency** (Zależność biblioteki).

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję zestawu Speech SDK dla systemu Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.5.0`. Następnie wybierz przycisk **OK**.
   Teraz należy dodać zestaw Speech SDK do listy zależności, jak przedstawiono poniżej:

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wybierz kartę **Properties** (Właściwości). Dla obu pozycji **Source Compatibility** (Zgodność kodu źródłowego) i **Target Compatibility** (Zgodność kodu docelowego) wybierz **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Naciśnij przycisk **OK**, aby zamknąć okno Project Structure (Struktura projektu) i zastosować zmiany do projektu.

## <a name="create-user-interface"></a>Tworzenie interfejsu użytkownika

Utworzymy podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ zawiera pasek tytułu z nazwą aplikacji i element TextView zawierający tekst „Hello World!”.

* Kliknij element TextView. Zmień atrybut ID w prawym górnym rogu na `hello`.

* Z palety w lewym górnym rogu okna `activity_main.xml` przeciągnij przycisk do pustego miejsca nad tekstem.

* W atrybutach przycisku po prawej stronie w polu wartości atrybutu `onClick` wprowadź `onSpeechButtonClicked`. Napiszemy metodę o tej nazwie do obsługi zdarzenia przycisku.  Zmień atrybut ID w prawym górnym rogu na `button`.

* Użyj ikony różdżki u góry projektanta na potrzeby wnioskowania ograniczeń układu.

  ![Zrzut ekranu przedstawiający ikonę różdżki](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i graficzna reprezentacja interfejsu użytkownika powinny teraz wyglądać następująco:

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy `MainActivity.java`. Zastąp cały zawarty w tym pliku kod poniższym kodem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` zawiera kod, który żąda uprawnień do mikrofonu i Internetu oraz inicjuje powiązanie z platformą natywną. Konfigurowanie powiązań z platformą natywną jest wymagane tylko raz. Należy to zrobić na początku, podczas inicjowania aplikacji.

   * Metoda `onSpeechButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku wyzwala transkrypcję mowy na tekst.

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że na urządzeniu włączono [tryb projektowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options).

1. Aby skompilować aplikację, naciśnij klawisze Ctrl + F9 lub wybierz na pasku menu pozycje **Build** > **Make Project** (Kompilacja) (Utwórz projekt).

1. Aby uruchomić aplikację, naciśnij klawisze Shift + F10 lub wybierz pozycje **Run** > **Run 'app'** (Uruchom) (Uruchom „aplikację”).

1. W oknie docelowym wdrożenia, które zostanie wyświetlone, wybierz urządzenie Android.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](media/sdk/qs-java-android-12-deploy.png)

Naciśnij przycisk w aplikacji, aby przejść do sekcji rozpoznawania mowy. Dalej 15 sekund mowy w języku angielskim, zostaną wysłane do usług przetwarzania mowy i transkrybowanego. Wynik zostanie wyświetlony w aplikacji Android i w oknie programu logcat w programie Android Studio.

![Zrzut ekranu aplikacji Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
