---
title: 'Szybki start: Uruchamianie zestawu Speech Devices SDK w systemie Linux — usługa rozpoznawania mowy'
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK dla urządzeń z systemem Linux Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: 3ee39f4cf0c78850b0128fc5e497f2b2aee57bbc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559041"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Szybki start: Uruchamianie przykładowej aplikacji zestawu Speech Devices SDK w systemie Linux

W tym przewodniku szybki start dowiesz się, jak za pomocą zestawu Speech Devices SDK dla systemu Linux utworzyć produkt z obsługą mowy lub użyć go jako urządzenia [transkrypcji konwersacji](conversation-transcription-service.md) . Obecnie jest obsługiwana tylko [usługa Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE języka Java (v4) w systemie 64-bitowym Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga konta usługi [Azure Cognitive Services](get-started.md) z zasobem usługi Speech Services. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy [przykładowej aplikacji](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu Speech Devices SDK. Warto również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64 — bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)
* [Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* Tylko [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) dla języka Java i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Plik JRE-Sample-Release. zip zawiera przykładową aplikację JRE, a w tym przewodniku szybki start założono, że aplikacja została wyodrębniona do/home/wcaltest/JRE-Sample-Release

Przed rozpoczęciem przezaćmienia upewnij się, że te zależności są zainstalowane.

* W systemie Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* W programie Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Transkrypcja konwersacji jest obecnie dostępna tylko dla "en-US" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

Jeśli planujesz korzystać z intencji, musisz mieć subskrypcję [usługi Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz Rozpoznawanie założeń [mowy C#przy użyciu Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Dla tej aplikacji jest dostępny [przykładowy model Luis](https://aka.ms/sdsdk-luis) .

## <a name="create-and-configure-the-project"></a>Tworzenie i Konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W obszarze roboczym zaćmienie w środowisku  **IDE**wprowadź nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu przezaćmienie Utwórz nowy projekt, wybierając kolejno pozycje **plik** > **Nowy** > **projekt Java**. W przypadku niedostępności wybierz **projekt** , a następnie **projekt Java**.

1. Zostanie uruchomiony Kreator **nowego projektu Java** . **Przeglądaj** w poszukiwaniu lokalizacji przykładowego projektu. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven). Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu narzędzia Package Explorer](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Wybierz **Właściwości**, a następnie **Uruchom/Debuguj ustawienia** > **nowe...** > **Aplikacja Java**. 

1. Zostanie wyświetlone okno **Edytuj konfigurację** . W polu **Nazwa** wprowadź wartość **Main**, a następnie  Użyj opcji Wyszukaj **klasy głównej** , aby znaleźć i wybrać obiekt **com. Microsoft. cognitiveservices. Speech. Samples. FunctionsList**.

   ![Zrzut ekranu przedstawiający konfigurację uruchamiania edycji](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. W oknie **Edycja konfiguracji** wybierz stronę **środowisko** i **nową**. Zostanie wyświetlone okno **Nowa zmienna środowiskowa** . W polu **Nazwa** wprowadź **LD_LIBRARY_PATH** , a w polu **wartość** wprowadź folder zawierający pliki *. so, na przykład **/Home/wcaltest/JRE-Sample-Release**

1. Kopiuj `kws.table` i`participants.properties` do folderu projektu **Target/Classs**


## <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Dodaj swój klucz subskrypcji mowy do kodu źródłowego. Do wypróbowania rozpoznawanie intencji, również należy dodać swoje [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klucz subskrypcji i aplikacji identyfikatora.

   W przypadku mowy i LUIS informacje są umieszczane `FunctionsList.java`w:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    W przypadku korzystania z transkrypcji konwersacji informacje o kluczu mowy i regionie są również potrzebne w `Cts.java`programie:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Aktywujące domyślne (słowo kluczowe) jest "Computer". Można też spróbować jednego z innych podane wake słów, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych słów alternatywne wznawiania znajdują się w zestawu Speech Devices SDK, na w tym folderze — słowo kluczowe. Na przykład `/home/wcaltest/JRE-Sample-Release/keyword/Computer` zawiera pliki używane dla programu Wake Word "Computer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md).

    Aby użyć nowego wyrazu wznawiania, zaktualizuj następujące dwa wiersze w `FunctionsList.java`programie i skopiuj pakiet wybudzenia programu Word do aplikacji. Na przykład, aby użyć programu Wake Word "Machine" z pakietu `kws-machine.zip`programu Wake Word:

   * Skopiuj pakiet wybudzenia programu Word do **obiektu docelowego/klasy**folderu projektu.

   * `FunctionsList.java` Zaktualizuj za pomocą słowa kluczowego i nazwy pakietu:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z poziomu programu zaćmienie

1. Na pasku menu przezaćmienia **Uruchom** > polecenie**Uruchom** 

1. Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

   ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](media/speech-devices-sdk/java-sample-app-linux.png)

1. Wypróbuj nową wersję demonstracyjną **transkrypcji konwersacji** . Rozpocznij jego przepisywania z rozpoczęciem **sesji** > . Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosowe uczestnika, można je umieścić `participants.properties` w **obiekcie docelowym/Klasa**folderu projektu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demonstracyjnej](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Tworzenie i uruchamianie autonomicznej aplikacji

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Wybierz **wyeksportować**. 
1. Zostanie wyświetlone okno **eksport** . Rozwiń węzeł **Java** i wybierz pozycję **możliwy do uruchomienia plik JAR** , a następnie wybierz pozycję **Next (dalej**).

   ![Zrzut ekranu okna eksportowania](media/speech-devices-sdk/eclipse-export-linux.png) 

1. Zostanie wyświetlone okno **Eksportuj plik możliwy do uruchomienia jar** . Wybierz **lokalizację docelową eksportu** aplikacji, a następnie wybierz pozycję **Zakończ**.
 
   ![Zrzut ekranu przedstawiający Eksport pliku JAR możliwy do uruchomienia](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Umieść `kws.table` i`participants.properties` w folderze docelowym wybranym powyżej, ponieważ te pliki są zbędne przez aplikację.

1. Ustaw LD_LIBRARY_LIB do folderu zawierającego *. so plików

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Aby uruchomić aplikację autonomiczną

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
