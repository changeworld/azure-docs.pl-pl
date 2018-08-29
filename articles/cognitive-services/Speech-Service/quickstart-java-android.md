---
title: 'Szybki Start: Rozpoznawanie mowy w języku Java w systemie Android przy użyciu Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku Java na systemu Android przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: 9f761fed46f0730a64a984111da1bae1229cc93d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127075"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w języku Java w systemie Android przy użyciu zestawu SDK rozpoznawania mowy

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację języka Java dla systemu Android przy użyciu Cognitive Services SDK mowy transkrypcja mowy na tekst.
Aplikacja zależy od Microsoft Cognitive Services mowy pakiet zestawu SDK narzędzia Maven, wersji 0.6.0 i Android Studio 3.1.

> [!NOTE]
> Dla zestawu Speech Devices SDK a urządzeniem Roobo, odwiedź [zestawu Speech Devices SDK](speech-devices-sdk.md) strony.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Komputer (Windows, Linux, Mac) może uruchomić programu Android Studio.
* Wersja 3.1 [programu Android Studio](https://developer.android.com/studio/).
* Na podstawie ARM urządzenia z systemem Android (interfejsu API 23: dla systemu Android Marshmallow 6.0 lub nowszej) [włączone dla rozwoju](https://developer.android.com/studio/debug/dev-options) z mikrofonu pracy.

## <a name="create-an-android-studio-project"></a>Tworzenie projektu programu Android Studio

Uruchom program Android Studio, a następnie wybierz pozycję **Utwórz nowy projekt programu Android Studio**.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

W **Utwórz nowy projekt** kreatora, który jest dostarczany w górę, wybierz następujące opcje:

1. W **Utwórz projekt dla systemu Android** ekranu, należy wprowadzić **Szybki Start** jako **Nazwa aplikacji**, **samples.speech.cognitiveservices.microsoft.com** jako **domeny firmowej**i wybierz lokalizację projektu. Pozostaw nie zaznaczone pola wyboru, a następnie kliknij przycisk **dalej**.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. W **docelowych urządzeń z systemem Android** ekranu wyboru **telefony i tablety** jako jedyną opcję Wybierz **interfejsu API 23: system Android 6.0 (Marshmallow)** z listy rozwijanej i kliknij przycisk **Dalej**.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. W **dodać działanie do Mobile** ekranu, wybierz opcję **puste działanie** i kliknij przycisk **dalej**.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. W **skonfigurować działanie** użyj **MainActivity** jako nazwę działania i **działania\_głównego** jako Nazwa układu. Zaznacz oba pola wyboru, a następnie kliknij przycisk **Zakończ**.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Po uruchomieniu przez jakiś czas, nowo utworzonego projektu programu Android Studio powinna pochodzić.

## <a name="configure-your-project-for-the-speech-sdk"></a>Konfigurowanie projektu dla zestawu SDK usługi mowy

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu SDK mowy usługi Cognitive Services to `0.6.0`.

Zestaw SDK rozpoznawania mowy dla systemu Android jest spakowany jako [AAR (biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), która obejmuje niezbędnych bibliotek, a także wymagane uprawnienia dla systemu Android dotyczące korzystania z niego.
Znajduje się w repozytorium narzędzia Maven w https://csspeechstorage.blob.core.windows.net/maven/.

Opisano poniżej sposób konfigurowania projektu, zestaw SDK rozpoznawania mowy.

Otwórz okno strukturę projektu w obszarze **pliku** \> **struktury projektu**.
W wyświetlonym oknie Utwórz następujące zmiany (kliknij **OK** tylko wtedy, gdy wykonać wszystkie czynności):

1. Wybierz **projektu**i edytować **domyślne biblioteki repozytorium** ustawienia, dodając przecinek i adresu URL repozytorium Maven ujęta w apostrofy `'https://csspeechstorage.blob.core.windows.net/maven/'`:

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. W tym samym ekranie po lewej stronie wybierz **aplikacji** moduł i w górnej części **zależności** kartę. Następnie kliknij zielony znak Plus w prawym górnym rogu i wybierz **zależności biblioteki**.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję nasz zestaw SDK rozpoznawania mowy dla systemów Android, `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`, następnie kliknij przycisk **OK**.
   Zestaw SDK rozpoznawania mowy powinna być dodana do listy zależności teraz, jak pokazano poniżej:

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. W górnym rogu, wybierz **właściwości** kartę. Wybierz **1.8** zarówno dla **źródła niezgodności** i **docelowe zgodności**.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Na koniec kliknij **OK** zamknąć **struktury projektu** systemu windows i zastosować wszystkie aktualizacje.

## <a name="create-a-minimal-ui"></a>Tworzenie minimalnego interfejsu użytkownika

Możesz edytować układu dla głównego działania `activity_main.xml`.
Domyślnie go należy skorzystać z paska tytułu, z nazwą Twojej aplikacji i TextView, który jest wyświetlany komunikat "Hello World!".

* Kliknij element TextView. Zmień jej atrybut ID w prawym górnym rogu, aby `hello`.

* Z palety w lewym górnym rogu usługi `activity_main.xml` okna, przeciągnij przycisk do pustego miejsca nad tekstem.

* W atrybutach przycisku po prawej stronie wartości `onClick` atrybutu, wprowadź `onSpeechButtonClicked`, który będzie nazwa naszej obsługi przycisku.
  Zmień jej atrybut ID w prawym górnym rogu, aby `button`.

* Użyj ikony Różdżka magic w górnej części projektanta, aby rozpoznać ograniczenia układu dla Ciebie.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i wersją graficznego interfejsu użytkownika powinien teraz wyglądać podobnie do poniższego:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[! code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Edytuj `MainActivity.java` pliku źródłowego i Zastąp kod następującym (poniżej zestawienie pakietu):

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` Metoda zawiera kod, który żąda uprawnienia Internet i mikrofon, a także inicjuje powiązania platformy natywnej. Konfigurowanie powiązań platformy natywnej jest tylko wymagane raz, oznacza to, należy to zrobić, wcześnie podczas inicjowania aplikacji.
   
   * Metoda `onSpeechButtonClicked` została wcześniej połączone się, jak program obsługi kliknięcia przycisku. Rozpoznawanie mowy rzeczywisty jest wyzwalana przez naciśnięcie przycisku.

1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

* Do kompilacji, naciśnij klawisze Ctrl + F9 lub wybierz **kompilacji** \> **projektu upewnij**.

* Połącz urządzenia z systemem Android na komputerze. Upewnij się, że masz [trybu opracowywania i debugowania USB włączone](https://developer.android.com/studio/debug/dev-options).

* Aby uruchomić aplikację, naciśnij klawisze Shift + F10 lub wybierz **Uruchom** \> **Uruchom "aplikację"**.

* W oknach docelowych wdrożenia, które funkcjonuje wybierz urządzenie z systemem Android.

  ![Uruchom aplikację do debugowania](media/sdk/qs-java-android-12-deploy.png)

* Aplikację należy uruchomić na urządzeniu z systemem.
  Po naciśnięciu przycisku, dalej 15 sekund zostaną rozpoznane i wyświetlane w interfejsie użytkownika (należy także mogli zobaczyć odpowiedź w oknie programu logcat w programie Android Studio):

  ![Interfejs użytkownika po pomyślnym rozpoznawania](media/sdk/qs-java-android-13-gui-on-device.png)

Przewodnika Szybki Start dla systemu Android kończy się w tym zrzucie ekranu. Można pobrać pełny projekt przykładowy kod z repozytorium przykładów.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/java-android` folderu.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)
