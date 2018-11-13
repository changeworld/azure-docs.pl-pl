---
title: 'Szybki start: rozpoznawanie mowy w języku Java w systemie Android przy użyciu zestawu SDK usługi Mowa'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Java w systemie Android przy użyciu zestawu SDK usługi Mowa
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 0785383d8b5a8ab282d8097d5229fa5a8aade06f
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219430"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Przewodnik Szybki start: Rozpoznawanie mowy w języku Java w systemie Android przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Z tego artykułu dowiesz się, jak utworzyć aplikację języka Java dla systemu Android przy użyciu zestawu Speech SDK usługi Cognitive Services, aby wykonać transkrypcję mowy na tekst.
Aplikacja jest oparta na pakiecie Maven zestawu Speech SDK usługi Microsoft Cognitive Services w wersji 1.1.0 oraz systemie Android Studio 3.1.
Zestaw Speech SDK jest obecnie zgodny z urządzeniami z systemem Android oraz 32 lub 64-bitowymi procesorami ARM i procesorami x86 lub x64 firmy Intel.

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia kroków tego przewodnika Szybki start potrzebujesz klucza subskrypcji usługi rozpoznawania mowy. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje, zobacz temat [Try the Speech service for free](get-started.md) (Wypróbuj bezpłatnie usługę rozpoznawania mowy).

## <a name="create-and-configure-a-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom program Android Studio, a następnie w oknie powitalnym wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).

    ![Zrzut ekranu okna powitalnego programu Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zostanie wyświetlony kreator **Create New Project** (Tworzenie nowego projektu). W oknie **Create Android Project** (Tworzenie projektu Android) wprowadź **Quickstart** jako **nazwę aplikacji**, **samples.speech.cognitiveservices.microsoft.com** jako **domenę firmy** i wybierz katalog projektu. Pozostaw pola wyboru C++ i Kotlin niezaznaczone i naciśnij przycisk **Next** (Dalej).

   ![Zrzut ekranu kreatora Create New Project (Tworzenie nowego projektu)](media/sdk/qs-java-android-02-create-android-project.png)

1. W oknie **Target Android Devices** (Docelowe urządzenia Android) wybierz tylko pozycję **Phone and Tablet** (Telefon i tablet). Na liście rozwijanej poniżej wybierz pozycję **API 23: Android 6.0 (Marshmallow)** i naciśnij przycisk **Next** (Dalej).

   ![Zrzut ekranu kreatora Create New Project (Tworzenie nowego projektu)](media/sdk/qs-java-android-03-target-android-devices.png)

1. W oknie **Add an Activity to Mobile** (Dodawanie działania do urządzenia przenośnego) wybierz pozycję **Empty Activity** (Puste działanie) i kliknij przycisk **Next** (Dalej).

   ![Zrzut ekranu kreatora Create New Project (Tworzenie nowego projektu)](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. W oknie **Configure Activity** (Konfigurowanie działania) użyj **MainActivity** jako nazwy działania oraz **activity\_main** jako nazwy układu. Zaznacz oba pola wyboru, a następnie wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu kreatora Create New Project (Tworzenie nowego projektu)](media/sdk/qs-java-android-05-configure-activity.png)

Przygotowanie nowego projektu Android w programie Android Studio zajmie trochę czasu. Następnie skonfiguruj projekt, aby rozpoznawał zestaw Speech SDK i używał języka Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.1.0`.

Zestaw Speech SDK dla systemu Android znajduje się w pakiecie pod nazwą [AAR (biblioteka Android)](https://developer.android.com/studio/projects/android-library) i zawiera niezbędne biblioteki oraz uprawnienia systemu Android wymagane do ich używania.
Jest hostowany w repozytorium Maven znajdującym się pod adresem https://csspeechstorage.blob.core.windows.net/maven/.

Skonfiguruj projekt do korzystania z zestawu Speech SDK. Otwórz okno Project Structure (Struktura projektu), wybierając kolejno pozycje **File (Plik)** > **Project Structure (Struktura projektu)** z paska menu programu Android Studio. W oknie Project Structure (Struktura projektu), wprowadź następujące zmiany: 

1. Na liście po lewej stronie okna wybierz pozycję **Project** (Project). Edytuj ustawienia **Default Library Repository** (Domyślne repozytorium biblioteki), dodając przecinek i adres URL repozytorium Maven ujęty w pojedynczy cudzysłów. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](media/sdk/qs-java-android-06-add-maven-repository.png)

1. W tym samym oknie po lewej stronie wybierz pozycję **app** (aplikacja). Następnie wybierz kartę **Dependencies** (Zależności) w górnej części okna. Wybierz zielony znak plus (+) i z menu rozwijanego wybierz pozycję **Library dependency** (Zależność biblioteki).

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję zestawu Speech SDK dla systemu Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.1.0`. Następnie wybierz przycisk **OK**.
   Teraz należy dodać zestaw Speech SDK do listy zależności, jak przedstawiono poniżej:

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wybierz kartę **Properties** (Właściwości). Dla obu pozycji **Source Compatibility** (Zgodność kodu źródłowego) i **Target Compatibility** (Zgodność kodu docelowego) wybierz **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Naciśnij przycisk **OK**, aby zamknąć okno Project Structure (Struktura projektu) i zastosować zmiany do projektu.

## <a name="create-user-interface"></a>Tworzenie interfejsu użytkownika

Utworzymy podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ zawiera pasek tytułu z nazwą aplikacji i element TextView zawierający tekst „Hello World!”

* Kliknij element TextView. Zmień atrybut ID w prawym górnym rogu na `hello`.

* Z palety w lewym górnym rogu okna `activity_main.xml` przeciągnij przycisk do pustego miejsca nad tekstem.

* W atrybutach przycisku po prawej stronie w polu wartości atrybutu `onClick` wprowadź `onSpeechButtonClicked`. Napiszemy metodę o tej nazwie do obsługi zdarzenia przycisku.  Zmień atrybut ID w prawym górnym rogu na `button`.

* Użyj ikony różdżki u góry projektanta na potrzeby wnioskowania ograniczeń układu.

  ![Zrzut ekranu przedstawiający ikonę różdżki](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i graficzne przedstawienie interfejsu użytkownika powinno teraz wyglądać następująco.

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

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

Naciśnij przycisk w aplikacji, aby przejść do sekcji rozpoznawania mowy. Kolejne 15 sekund mowy w języku angielskim zostanie wysłanych do usługi rozpoznawania mowy i poddanych transkrypcji. Wynik zostanie wyświetlony w aplikacji Android i w oknie programu logcat w programie Android Studio.

![Zrzut ekranu aplikacji Android](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w folderze `quickstart/java-android`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Recognize intents from speech by using the Speech SDK for Java](how-to-recognize-intents-from-speech-java.md) (Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK mowy dla języka Java)

## <a name="see-also"></a>Zobacz też

- [Translate speech with the Cognitive Services Speech SDK for C#](how-to-translate-speech-csharp.md) (Tłumaczenie mowy za pomocą zestawu Speech SDK usługi Cognitive Services dla języka C#)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
