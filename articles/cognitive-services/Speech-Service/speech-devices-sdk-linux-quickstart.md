---
title: 'Szybki start: Uruchom zestaw Speech Devices SDK w systemie Linux — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dla klientów zaczynających się od zestawu Linux Speech Devices SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: d755f3388466369ee1edc3d9ff1e353173babc10
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723391"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Szybki start: Uruchamianie przykładowej aplikacji zestawu Speech Devices SDK w systemie Linux

W tym przewodniku Szybki Start dowiesz się, jak używać zestawu Speech Devices SDK dla systemu Linux do tworzenia produktu z funkcją mowy, lub użyj go jako [transkrypcji konwersacji](conversation-transcription-service.md) urządzenia. Obecnie tylko [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) jest obsługiwana.

Aplikacja jest skompilowana przy użyciu pakietu SDK rozpoznawania mowy i środowiska Eclipse IDE Java (v4) w systemie 64-bitowych Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga [usług Azure Cognitive Services](get-started.md) konta z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy [Przykładowa aplikacja](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu Speech Devices SDK. Warto również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowego systemu Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9)
* [Usługa Azure DK Kinect](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) tylko.
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) dla języka Java i Wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Plik środowiska JRE — przykładowe-Release.zip zawiera środowisko JRE przykładową aplikację i ten przewodnik Szybki Start założono, że aplikacja jest wyodrębniany do /home/wcaltest/JRE-Sample-Release

Upewnij się, że te zależności są zainstalowane przed uruchomieniem programu Eclipse.

* W systemie Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* On Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Konwersacja transkrypcja jest obecnie dostępna tylko dla "en US" i "zh-CN", w regionach "centralus" i "Azja Wschodnia". Musi mieć klucz mowy w jednym z tych regionów, aby użyć transkrypcji konwersacji.

Jeśli planujesz używać intencji [usługa interpretacji języka (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) subskrypcji. Aby dowiedzieć się więcej na temat usługi LUIS i rozpoznawanie intencji, zobacz [rozpoznać intencje mowy z użyciem usługi LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). A [przykładowy model usługi LUIS](https://aka.ms/sdsdk-luis) jest dostępna dla tej aplikacji.

## <a name="create-and-configure-the-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W **uruchamianie środowiska IDE programu Eclipse**w **obszaru roboczego** wprowadź nazwę nowego katalogu roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu programu Eclipse Utwórz nowy projekt, wybierając **pliku** > **New** > **projektu w języku Java**. Jeśli nie jest dostępny wybierz **projektu** i następnie **projektu w języku Java**.

1. **Nowy projekt języka Java** uruchamiany jest Kreator. **Przeglądaj** lokalizacji przykładowego projektu. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. W **narzędziu Package explorer**, kliknij prawym przyciskiem myszy projekt. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven). Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu narzędzia Package Explorer](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. W **narzędziu Package explorer**, kliknij prawym przyciskiem myszy projekt. Wybierz **właściwości**, następnie **ustawienia uruchomienia/debugowania** > **nowy...** > **Aplikacja Java**. 

1. **Edycja konfiguracji** zostanie wyświetlone okno. W **nazwa** pola wprowadź **Main**i użyj **wyszukiwania** dla **klasy Main** Aby znaleźć i wybrać  **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Zrzut ekranu przedstawiający konfigurację uruchamiania edycji](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Także z **Edycja konfiguracji** wybierz okno **środowiska** strony i **New**. **Zmienna środowiskowa nowe** zostanie wyświetlone okno. W **nazwa** pola wprowadź **LD_LIBRARY_PATH** i **wartość** pola wprowadź folder zawierający pliki *.so, na przykład   **/home/wcaltest / Środowisko JRE przykładowe zwalniania**

1. Kopiuj `kws.table` i `participants.properties` na folder projektu **klas/docelowych**


## <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Dodaj klucz subskrypcji mowy do kodu źródłowego. Do wypróbowania rozpoznawanie intencji, również należy dodać swoje [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klucz subskrypcji i aplikacji identyfikatora.

   Mowy i LUIS informacji przechodzi w stan `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Jeśli używasz konwersacji transkrypcja mowy informacji klucza i region również są potrzebne w `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Aktywujące domyślne (słowo kluczowe) jest "Computer". Można też spróbować jednego z innych podane wake słów, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych słów alternatywne wznawiania znajdują się w zestawu Speech Devices SDK, na w tym folderze — słowo kluczowe. Na przykład `/home/wcaltest/JRE-Sample-Release/keyword/Computer` zawiera pliki, używany do wznawiania wyraz "Komputer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md).

    Aby korzystać z nowego słowa wznawiania, należy zaktualizować następujące dwa wiersze w `FunctionsList.java`, a następnie skopiuj pakiet wznawiania programu word do swojej aplikacji. Na przykład, aby użyć aktywujące "Machine" za pomocą pakietu programu word wznawiania `kws-machine.zip`:

   * Skopiuj pakiet programu word wznawiania na folder projektu **klasdocelowych/** .

   * Aktualizacja `FunctionsList.java` za pomocą słowa kluczowego i nazwa pakietu:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z poziomu środowiska Eclipse

1. Na pasku menu programu Eclipse **Uruchom** > **uruchamiania** 

1. Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

   ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](media/speech-devices-sdk/java-sample-app-linux.png)

1. Wypróbuj nowy **transkrypcji konwersacji** pokaz. Rozpocznij przepisywania z **sesji** > **Start**. Domyślnie wszyscy jest gościa. Jednak w przypadku podpisów głosu uczestnika one można umieścić w `participants.properties` w folderze projektu **klasdocelowych/** . Do generowania podpisu głosowej, Przyjrzyj się [transkrypcja rozmów (SDK)](how-to-use-conversation-transcription-service.md).

   ![Pokaz transkrypcji konwersacji aplikacji](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Tworzenie i uruchamianie aplikacji autonomicznej

1. W **narzędziu Package explorer**, kliknij prawym przyciskiem myszy projekt. Wybierz **wyeksportować**. 
1. **Wyeksportować** zostanie wyświetlone okno. Rozwiń **Java** i wybierz **pliku JAR możliwy do uruchomienia** , a następnie wybierz **dalej**.

   ![Zrzut ekranu przedstawiający okno eksportu](media/speech-devices-sdk/eclipse-export-linux.png) 

1. **Możliwy do uruchomienia JAR pliku eksportu** zostanie wyświetlone okno. Wybierz **miejsce docelowe eksportu** dla aplikacji, a następnie wybierz **Zakończ**.
 
   ![Zrzut ekranu przedstawiający eksportowanie pliku JAR możliwy do uruchomienia](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Umieść `kws.table` i `participants.properties` w folderze docelowym wybrany powyżej, ponieważ te pliki są wymagane przez aplikację.

1. Ustaw LD_LIBRARY_LIB do folderu zawierającego pliki *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Uruchamianie aplikacji autonomicznej

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
