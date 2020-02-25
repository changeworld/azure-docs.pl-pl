---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 44ca89cb9d1ecb1a6b7fb91a146cd440f40bb303
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563220"
---
W tym przewodniku szybki start dowiesz się, jak utworzyć produkt z obsługą mowy przy użyciu zestawu Speech Devices SDK dla systemu Android lub użyć go jako urządzenia [transkrypcji konwersacji](../conversation-transcription-service.md) .

Ten przewodnik wymaga konta [Cognitive Services platformy Azure](../get-started.md) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy przykładowej aplikacji jest dołączone do zestawu Speech Devices SDK. Jest ona również [dostępna w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z zestawu SDK usługi Speech Devices należy:

- Aby włączyć urządzenie, postępuj zgodnie z instrukcjami dostarczonymi z Twoim [zestawem deweloperskim](../get-speech-devices-sdk.md) .

- Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download)i wyodrębnij plik zip do katalogu roboczego.

  > [!NOTE]
  > W tym przewodniku szybki start przyjęto założenie, że aplikacja została wyodrębniona do C:\SDSDK\Android-Sample-Release

- Aby uzyskać [klucz subskrypcji platformy Azure dla usługi mowy](../get-started.md)

- Jeśli planujesz korzystanie z transkrypcji konwersacji, musisz użyć [okrągłego urządzenia mikrofonu](../get-speech-devices-sdk.md) , a ta funkcja jest obecnie dostępna tylko dla "en-us" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

- Jeśli planujesz używać usługi mowy do identyfikowania intencji (lub działań) z wyrażenia długości użytkownika, będziesz potrzebować subskrypcji [usługi Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz Rozpoznawanie założeń [mowy C#przy użyciu Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Można [utworzyć prosty model Luis](https://docs.microsoft.com/azure/cognitive-services/luis/) lub użyć przykładowego modelu LUIS, Luis-example. JSON. Przykładowy model LUIS jest dostępny w [witrynie pobierania zestawu SDK urządzeń mowy](https://aka.ms/sdsdk-luis). Aby przekazać plik JSON modelu do [portalu Luis](https://www.luis.ai/home), wybierz pozycję **Importuj nową aplikację**, a następnie wybierz plik JSON.

- Zainstaluj [Android Studio](https://developer.android.com/studio/) i [Vysor](https://vysor.io/download/) na komputerze.

## <a name="set-up-the-device"></a>Konfigurowanie urządzenia

1. Rozpocznij Vysor na tym komputerze.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Urządzenie powinno być wyświetlane w obszarze **Wybierz urządzenie**. Wybierz przycisk **Widok** obok urządzenia.

1. Połącz się z siecią bezprzewodową, wybierając ikonę folderu, a następnie wybierz pozycję **ustawienia** > **WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Jeśli Twoja firma ma zasady dotyczące podłączania urządzeń do sieci Wi-Fi systemu, należy uzyskać adres MAC, i skontaktuj się z działem IT o sposobie nawiązywania połączenia z sieci Wi-Fi firmy.
   >
   > Aby znaleźć adres MAC zestaw deweloperski, wybierz ikonę folderu plików na pulpicie zestawu dla deweloperów.
   >
   > ![Folder plików Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Wybierz pozycję **Ustawienia**. Wyszukaj ciąg "adres MAC", a następnie wybierz pozycję **adres mac** > **zaawansowaną sieć WLAN**. Zanotuj adres MAC, który pojawia się w dolnej części okna dialogowego.
   >
   > ![Adres Vysor MAC](../media/speech-devices-sdk/qsg-11.png)
   >
   > Niektóre firmy mogą mieć limit czasu na jak długo urządzenie może być podłączony do sieci Wi-Fi systemu. Może być konieczne rozszerzenie rejestracji zestawu dla deweloperów z sieci Wi-Fi systemu po określonej liczbie dni.

## <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby sprawdzić poprawność instalacji zestawu deweloperskiego, skompiluj i zainstaluj aplikację przykładową:

1. Uruchom program Android Studio.

1. Wybierz pozycję **Open an existing Android Studio project** (Otwórz istniejący projekt Android Studio).

   ![Android Studio — Otwórz istniejący projekt](../media/speech-devices-sdk/qsg-5.png)

1. Przejdź do C:\SDSDK\Android-Sample-Release\example. Wybierz **przycisk OK** , aby otworzyć przykładowy projekt.

1. Skonfiguruj Gradle, aby odwoływać się do zestawu Speech SDK. Następujące pliki można znaleźć w obszarze **skrypty Gradle** w Android Studio.

    Zaktualizuj element **Build. Gradle (Project: example)** , blok allprojects powinien odpowiadać poniżej, dodając linie Maven.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Zaktualizuj program **Build. Gradle (module: App)** , dodając ten wiersz do sekcji zależności. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.9.0'
    ```
    
1. Dodaj swój klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz wypróbować funkcję rozpoznawania intencji, Dodaj również klucz subskrypcji [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) i identyfikator aplikacji.

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

1. Słowo kluczowe default ma wartość "Computer". Możesz również wypróbować jeden z innych podanych słów kluczowych, takich jak "Machine" lub "Assistant". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w zestawie SDK urządzeń mowy w folderze słów kluczowych. Na przykład C:\SDSDK\Android-Sample-Release\keyword\Computer zawiera pliki używane dla słowa kluczowego "Computer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo kluczowe](../speech-devices-sdk-create-kws.md).

   Aby użyć nowego słowa kluczowego, zaktualizuj następujące dwa wiersze w `MainActivity.java`i skopiuj pakiet słów kluczowych do aplikacji. Na przykład, aby użyć słowa kluczowego "Machine" ze słowa kluczowego KWS-Machine. zip:

   - Skopiuj pakiet słowa kluczowego do folderu "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Zaktualizuj `MainActivity.java` za pomocą słowa kluczowego i nazwy pakietu:

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

   | Zmienna | Znaczenie | Dostępne wartości |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfiguracji fizycznej mic | Dla cyklicznego zestawu deweloperskiego: `Circular6+1` |
   |          |         | W przypadku zestawu liniowego dla deweloperów: `Linear4` |
   | `SelectedGeometry` | Konfiguracja kontroli integralności uprawnień oprogramowania | Dla cyklicznego zestawu deweloperskiego, który używa wszystkich Mics: `Circular6+1` |
   |          |         | Dla cyklicznego zestawu deweloperskiego, który używa czterech Mics: `Circular3+1` |
   |          |         | W przypadku zestawu liniowego dev, który używa wszystkich Mics: `Linear4` |
   |          |         | W przypadku zestawu liniowego dev, który używa dwóch Mics: `Linear2` |

1. Aby skompilować aplikację, w menu **Uruchom** wybierz polecenie **Uruchom aplikację**. Zostanie wyświetlone okno dialogowe **Wybieranie celu wdrożenia** .

1. Wybierz urządzenie, a następnie wybierz przycisk **OK** , aby wdrożyć aplikację na urządzeniu.

   ![Wybierz cel wdrożenia — okno dialogowe](../media/speech-devices-sdk/qsg-7.png)

1. Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

   ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](../media/speech-devices-sdk/qsg-8.png)

1. Wypróbuj nową wersję demonstracyjną transkrypcji konwersacji. Rozpocznij jego przepisywania z opcją "Rozpocznij sesję". Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosu uczestników, można je umieścić w pliku `/video/participants.properties` na urządzeniu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demonstracyjnej](../media/speech-devices-sdk/qsg-15.png)

1. Eksperyment!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie możesz nawiązać połączenia z urządzeniem mowy. Wpisz następujące polecenie w oknie wiersza polecenia. Zwróci listę urządzeń:

```powershell
 adb devices
```

> [!NOTE]
> To polecenie używa Android Debug Bridge, `adb.exe`, który jest częścią instalacji Android Studio. To narzędzie znajduje się w lokalizacji C:\Users\[nazwa użytkownika] \AppData\Local\Android\Sdk\platform-tools. Możesz dodać ten katalog do ścieżki, aby uczynić go wygodniejszym do wywoływania `adb`. W przeciwnym razie należy określić pełną ścieżkę do instalacji programu ADB. exe w każdym poleceniu, które wywołuje `adb`.
>
> Jeśli zostanie wyświetlony komunikat o błędzie `no devices/emulators found`, sprawdź, czy kabel USB jest podłączony i czy jest używany kabel wysokiej jakości.
