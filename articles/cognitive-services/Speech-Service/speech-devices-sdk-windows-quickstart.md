---
title: 'Szybki start: Uruchom zestaw Speech Devices SDK na Windows — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dla klientów zaczynających się od zestawu Windows Speech Devices SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ac0ed218aa27a36b3b8cd8ed8123e2baef6948c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723384"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Szybki start: Uruchamianie przykładowej aplikacji zestawu Speech Devices SDK na Windows

W tym przewodniku Szybki Start dowiesz się, jak za pomocą mowy urządzeń zestawu SDK dla Windows tworzenia produktu z funkcją mowy, lub użyj go jako [transkrypcji konwersacji](conversation-transcription-service.md) urządzenia. Obecnie tylko [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) jest obsługiwana.

Aplikacja jest tworzone za pomocą pakietu SDK rozpoznawania mowy i środowisko Eclipse IDE Java (v4) na Windows 64-bitowych. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga [usług Azure Cognitive Services](get-started.md) konta z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy [Przykładowa aplikacja](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu Speech Devices SDK. Warto również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: Windows 64-bitowych
* [Usługa Azure DK Kinect](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) tylko.
* [Pakiet Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) dla języka Java i Wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Plik środowiska JRE — przykładowe-Release.zip zawiera środowisko JRE przykładową aplikację i ten przewodnik Szybki Start założono, że aplikacja jest wyodrębniany do C:\SDSDK\JRE-Sample-Release

Konwersacja transkrypcja jest obecnie dostępna tylko dla "en US" i "zh-CN", w regionach "centralus" i "Azja Wschodnia". Musi mieć klucz mowy w jednym z tych regionów, aby użyć transkrypcji konwersacji.

Jeśli planujesz używać intencji [usługa interpretacji języka (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) subskrypcji. Aby dowiedzieć się więcej na temat usługi LUIS i rozpoznawanie intencji, zobacz [rozpoznać intencje mowy z użyciem usługi LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). A [przykładowy model usługi LUIS](https://aka.ms/sdsdk-luis) jest dostępna dla tej aplikacji.

## <a name="create-and-configure-the-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W **uruchamianie środowiska IDE programu Eclipse**w **obszaru roboczego** wprowadź nazwę nowego katalogu roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](media/speech-devices-sdk/eclipse-launcher.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu programu Eclipse Utwórz nowy projekt, wybierając **pliku** > **New** > **projektu w języku Java**. Jeśli nie jest dostępny wybierz **projektu** i następnie **projektu w języku Java**.

1. **Nowy projekt języka Java** uruchamiany jest Kreator. **Przeglądaj** lokalizacji przykładowego projektu. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](media/speech-devices-sdk/eclipse-new-java-project.png)

1. W **narzędziu Package explorer**, kliknij prawym przyciskiem myszy projekt. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven). Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu narzędzia Package Explorer](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Kopiuj `kws.table`, `participants.properties` i `Microsoft.CognitiveServices.Speech.extension.pma.dll` na folder projektu **target\classes**

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

1. Aktywujące domyślne (słowo kluczowe) jest "Computer". Można też spróbować jednego z innych podane wake słów, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych słów alternatywne wznawiania znajdują się w zestawu Speech Devices SDK, na w tym folderze — słowo kluczowe. Na przykład `C:\SDSDK\JRE-Sample-Release\keyword\Computer` zawiera pliki, używany do wznawiania wyraz "Komputer".

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

1. Na pasku menu programu Eclipse **Uruchom** > **Uruchom jako** > **aplikacji Java**. Następnie wybierz pozycję **FunctionsList** i **OK**.

   ![Zrzut ekranu aplikacji wybierz opcję języka Java](media/speech-devices-sdk/eclipse-run-sample.png)

1. Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

   ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](media/speech-devices-sdk/java-sample-app-windows.png)

1. Wypróbuj nowy **transkrypcji konwersacji** pokaz. Rozpocznij przepisywania z **sesji** > **Start**. Domyślnie wszyscy jest gościa. Jednak w przypadku podpisów głosu uczestnika one można umieścić w pliku `participants.properties` w folderze projektu **klasdocelowych/** . Do generowania podpisu głosowej, Przyjrzyj się [transkrypcja rozmów (SDK)](how-to-use-conversation-transcription-service.md).

   ![Pokaz transkrypcji konwersacji aplikacji](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Tworzenie i uruchamianie aplikacji autonomicznej

1. W **narzędziu Package explorer**, kliknij prawym przyciskiem myszy projekt. Wybierz **wyeksportować**. 

1. **Wyeksportować** zostanie wyświetlone okno. Rozwiń **Java** i wybierz **pliku JAR możliwy do uruchomienia** , a następnie wybierz **dalej**.

   ![Zrzut ekranu przedstawiający okno eksportu](media/speech-devices-sdk/eclipse-export-windows.png) 

1. **Możliwy do uruchomienia JAR pliku eksportu** zostanie wyświetlone okno. Wybierz **miejsce docelowe eksportu** dla aplikacji, a następnie wybierz **Zakończ**.
 
   ![Zrzut ekranu przedstawiający eksportowanie pliku JAR możliwy do uruchomienia](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Umieść `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` i `Microsoft.CognitiveServices.Speech.extension.pma.dll` w folderze docelowym wybrany powyżej, ponieważ te pliki są wymagane przez aplikację.

1. Uruchamianie aplikacji autonomicznej

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
