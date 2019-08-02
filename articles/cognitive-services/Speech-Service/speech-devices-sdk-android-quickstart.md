---
title: 'Szybki start: Uruchamianie zestawu Speech Devices SDK w usłudze Android-Speech'
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK urządzeń z funkcją rozpoznawania mowy dla systemu Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f7039b525cf0d52670b8d76a24d8ec3ea5115772
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559113"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Szybki start: Uruchamianie przykładowej aplikacji zestawu Speech Devices SDK w systemie Android

W tym przewodniku szybki start dowiesz się, jak utworzyć produkt z obsługą mowy przy użyciu zestawu Speech Devices SDK dla systemu Android lub użyć go jako urządzenia [transkrypcji konwersacji](conversation-transcription-service.md) .

Ten przewodnik wymaga konta usługi [Azure Cognitive Services](get-started.md) z zasobem usługi Speech Services. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy przykładowej aplikacji jest dołączone do zestawu Speech Devices SDK. Warto również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z zestawu SDK usługi Speech Devices należy:

* Aby włączyć urządzenie, postępuj [](get-speech-devices-sdk.md) zgodnie z instrukcjami dostarczonymi z Twoim zestawem deweloperskim.

* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download)i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Plik Android-Sample-Release. zip zawiera przykładową aplikację dla systemu Android, a w tym przewodniku szybki start założono, że aplikacja została wyodrębniona do C:\SDSDK\Android-Sample-Release

* Aby uzyskać [klucz subskrypcji platformy Azure dla usługi Speech Services](get-started.md)

* Jeśli planujesz korzystanie z transkrypcji konwersacji, musisz użyć okrągłego [urządzenia mikrofonu](get-speech-devices-sdk.md) , a ta funkcja jest obecnie dostępna tylko dla "en-us" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

* Jeśli planujesz używać usług mowy do identyfikowania intencji (lub działań) z wyrażenia długości użytkownika, będziesz potrzebować subskrypcji [usługi Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz Rozpoznawanie założeń [mowy C#przy użyciu Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Możesz [Tworzenie prostego modelu usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) lub użyj przykładu model usługi LUIS, example.json usługi LUIS. Przykładowy model usługi LUIS jest dostępny z [witryny pobierania zestawu Speech Devices SDK](https://aka.ms/sdsdk-luis). Można przekazać swój model pliku JSON do [portal usługi LUIS](https://www.luis.ai/home), wybierz opcję **importowania Nowa aplikacja**, a następnie wybierz plik JSON.

* Zainstaluj [programu Android Studio](https://developer.android.com/studio/) i [Vysor](https://vysor.io/download/) na komputerze.

## <a name="set-up-the-device"></a>Konfigurowanie urządzenia

1. Rozpocznij Vysor na tym komputerze.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Urządzenia powinny zostać wyświetlone w obszarze **wybierz urządzenie**. Wybierz **widoku** przycisk obok urządzenia.

1. Łączenie z siecią bezprzewodową, wybierając ikonę folderu, a następnie wybierz **ustawienia** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Jeśli Twoja firma ma zasady dotyczące podłączania urządzeń do sieci Wi-Fi systemu, należy uzyskać adres MAC, i skontaktuj się z działem IT o sposobie nawiązywania połączenia z sieci Wi-Fi firmy.
    >
    > Aby znaleźć adres MAC zestaw deweloperski, wybierz ikonę folderu plików na pulpicie zestawu dla deweloperów.
    >
    >  ![Folder plików Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Wybierz **ustawienia**. Wyszukaj "mac address", a następnie wybierz pozycję **adres Mac** > **zaawansowane WLAN**. Zanotuj adres MAC, który pojawia się w dolnej części okna dialogowego.
    >
    > ![Adres Vysor MAC](media/speech-devices-sdk/qsg-11.png)
    >
    > Niektóre firmy mogą mieć limit czasu na jak długo urządzenie może być podłączony do sieci Wi-Fi systemu. Może być konieczne rozszerzenie rejestracji zestawu dla deweloperów z sieci Wi-Fi systemu po określonej liczbie dni.

## <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby sprawdzić poprawność instalacji zestawu deweloperskiego, skompiluj i zainstaluj aplikację przykładową:

1. Uruchom program Android Studio.

1. Wybierz pozycję **Open an existing Android Studio project** (Otwórz istniejący projekt Android Studio).

   ![Android Studio — Otwórz istniejący projekt](media/speech-devices-sdk/qsg-5.png)

1. Przejdź do C:\SDSDK\Android-Sample-Release\example. Wybierz **OK** otworzyć projektu w przykładzie.

1. Dodaj swój klucz subskrypcji mowy do kodu źródłowego. Do wypróbowania rozpoznawanie intencji, również należy dodać swoje [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klucz subskrypcji i aplikacji identyfikatora.

   W przypadku mowy i LUIS informacje trafiają do MAINS. Java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Jeśli używasz transkrypcji konwersacji, informacje o kluczu mowy i regionie są również potrzebne w konwersacji. Java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Aktywujące domyślne (słowo kluczowe) jest "Computer". Można też spróbować jednego z innych podane wake słów, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych słów alternatywne wznawiania znajdują się w zestawu Speech Devices SDK, na w tym folderze — słowo kluczowe. Na przykład C:\SDSDK\Android-Sample-Release\keyword\Computer zawiera pliki, używany do wznawiania wyraz "Komputer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md).

    Aby użyć nowego wyrazu wznawiania, zaktualizuj następujące dwa wiersze w `MainActivity.java`programie i skopiuj pakiet wybudzenia programu Word do aplikacji. Na przykład, aby użyć programu Wake Word "Machine" z pliku Wake Word KWS-Machine. zip:

   * Skopiuj pakiet wznawiania tekstu do folderu "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * `MainActivity.java` Zaktualizuj za pomocą słowa kluczowego i nazwy pakietu:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Zaktualizuj następujące wiersze, które zawierają ustawienia geometrii, mikrofon tablicy:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ta tabela zawiera listę obsługiwanych wartości:

   |Zmienna|Znaczenie|Dostępne wartości|
   |--------|-------|----------------|
   |`DeviceGeometry`|Konfiguracji fizycznej mic|Aby uzyskać cykliczne zestaw deweloperski: `Circular6+1` |
   |||Aby uzyskać liniowej zestaw deweloperski: `Linear4`|
   |`SelectedGeometry`|Konfiguracja kontroli integralności uprawnień oprogramowania|Dla okręgu deweloperski, który używa mikrofonu wszystkich: `Circular6+1`|
   |||Aby uzyskać zestaw cykliczne deweloperów, która używa czterech mikrofonu: `Circular3+1`|
   |||Dla liniowych deweloperski, który używa mikrofonu wszystkich: `Linear4`|
   |||Dla zestawu SDK do dev liniowy, który używa dwóch mikrofonu: `Linear2`|

1. Aby skompilować aplikację, na **Uruchom** menu, wybierz opcję **Uruchom "aplikację"** . **Wybierz cel wdrożenia** pojawi się okno dialogowe.

1. Wybierz urządzenie, a następnie wybierz **OK** wdrożyć aplikację na urządzeniu.

    ![Wybierz cel wdrożenia — okno dialogowe](media/speech-devices-sdk/qsg-7.png)

1. Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

   ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](media/speech-devices-sdk/qsg-8.png)

1. Wypróbuj nową wersję demonstracyjną transkrypcji konwersacji. Rozpocznij jego przepisywania z opcją "Rozpocznij sesję". Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosu uczestników, można je umieścić w pliku `/video/participants.properties` na urządzeniu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demonstracyjnej](media/speech-devices-sdk/qsg-15.png)

1. Eksperyment!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

   Jeśli nie możesz nawiązać połączenia z urządzeniem mowy. Wpisz następujące polecenie w oknie wiersza polecenia. Zwróci listę urządzeń:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > To polecenie używa Android Debug Bridge, `adb.exe`, który jest częścią instalacji Android Studio. To narzędzie znajduje się w C:\Users\[nazwa_użytkownika] \AppData\Local\Android\Sdk\platform narzędzia. Ten katalog można dodać do swojej ścieżki, aby był bardziej wygodne do wywołania `adb`. W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdego polecenia, który wywołuje `adb`.
   >
   > Jeśli zobaczysz błąd `no devices/emulators found` , sprawdź, czy kabel USB jest podłączony, i upewnij się, że jest używany kabel wysokiej jakości.
   >

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
