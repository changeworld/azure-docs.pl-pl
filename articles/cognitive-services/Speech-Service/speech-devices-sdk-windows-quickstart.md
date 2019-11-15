---
title: 'Szybki Start: uruchamianie zestawu Speech Devices SDK w systemie Windows-Speech Service'
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK urządzeń z funkcją rozpoznawania mowy systemu Windows.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: erhopf
ms.openlocfilehash: e59cfaa1260cd33c8912437d56bbbb2ace2f43ed
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090450"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Szybki Start: uruchamianie przykładowej aplikacji zestawu Speech Devices SDK w systemie Windows

W tym przewodniku szybki start dowiesz się, jak za pomocą zestawu SDK usługi Speech dla systemu Windows utworzyć produkt z obsługą mowy lub użyć go jako urządzenia [transkrypcji konwersacji](conversation-transcription-service.md) . W przypadku transkrypcji konwersacji jest obsługiwana tylko [usługa Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) . W przypadku innych funkcji rozpoznawania mowy obsługiwane są tablice mikrofonu liniowego, które zapewniają geometrię macierzy mikrofonu.

Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE środowiska Java (v4) w systemie 64-bitowym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga konta usługi [Azure Cognitive Services](get-started.md) z zasobem usługi Speech Services. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy [przykładowej aplikacji](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu Speech Devices SDK. Warto również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowy system Windows
* Tablica mikrofonu, taka jak [Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* Tylko [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* [Pakiet Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) dla języka Java i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Plik JRE-Sample-Release. zip zawiera przykładową aplikację JRE, a w tym przewodniku szybki start założono, że aplikacja została wyodrębniona do C:\SDSDK\JRE-Sample-Release

Transkrypcja konwersacji jest obecnie dostępna tylko dla "en-US" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

Jeśli planujesz korzystać z intencji, musisz mieć subskrypcję [usługi Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz Rozpoznawanie założeń [mowy C#przy użyciu Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Dla tej aplikacji jest dostępny [przykładowy model Luis](https://aka.ms/sdsdk-luis) .

## <a name="create-and-configure-the-project"></a>Tworzenie i Konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W obszarze **roboczym** **zaćmienie**w środowisku IDE wprowadź nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](media/speech-devices-sdk/eclipse-launcher.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu zaćmienie Utwórz nowy projekt, wybierając kolejno pozycje **plik** > **Nowy** > **projekt Java**. W przypadku niedostępności wybierz **projekt** , a następnie **projekt Java**.

1. Zostanie uruchomiony Kreator **nowego projektu Java** . **Przeglądaj** w poszukiwaniu lokalizacji przykładowego projektu. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](media/speech-devices-sdk/eclipse-new-java-project.png)

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven). Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu narzędzia Package Explorer](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Otwórz plik pliku pom. XML i edytuj go.

    Na końcu pliku przed tagiem zamykającym `</project>`Utwórz elementy `repositories` i `dependencies`, jak pokazano poniżej, i upewnij się, że `version` jest zgodna z bieżącą wersją:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.7.0</version>
        </dependency>
    </dependencies>
   ```

1. Skopiuj zawartość **systemu Windows — x64** do lokalizacji projektu Java, np. **C:\SDSDK\JRE-Sample-Release**

1. Kopiuj `kws.table`, `participants.properties` i `Microsoft.CognitiveServices.Speech.extension.pma.dll` do folderu projektu **target\classes**

## <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Dodaj swój klucz subskrypcji mowy do kodu źródłowego. Do wypróbowania rozpoznawanie intencji, również należy dodać swoje [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klucz subskrypcji i aplikacji identyfikatora.

   W przypadku mowy i LUIS informacje są wprowadzane do `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    W przypadku korzystania z transkrypcji konwersacji informacje o kluczu mowy i regionie są również potrzebne w `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Słowo kluczowe default (słowo kluczowe) to "Computer". Możesz również wypróbować jeden z innych podanych słów kluczowych, takich jak "Machine" lub "Assistant". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w zestawie SDK urządzeń mowy w folderze słów kluczowych. Na przykład `C:\SDSDK\JRE-Sample-Release\keyword\Computer` zawiera pliki używane dla słowa kluczowego "Computer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo kluczowe](speech-devices-sdk-create-kws.md).

    Aby użyć nowego słowa kluczowego, zaktualizuj Poniższy wiersz w `FunctionsList.java`i skopiuj słowo kluczowe do aplikacji. Na przykład, aby użyć słowa kluczowego "Machine" z pakietu słowa kluczowego `machine.zip`:

   * Skopiuj plik `kws.table` z pakietu zip do folderu **docelowego/klas**projektu.

   * Zaktualizuj `FunctionsList.java` przy użyciu nazwy słowa kluczowego:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z poziomu programu zaćmienie

1. Na pasku menu przezaćmienia **Uruchom** polecenie > **Uruchom jako** > **aplikację Java**. Następnie wybierz pozycję **FunctionsList** i **przycisk OK**.

   ![Zrzut ekranu przedstawiający wybraną aplikację Java](media/speech-devices-sdk/eclipse-run-sample.png)

1. Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

   ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](media/speech-devices-sdk/java-sample-app-windows.png)

1. Wypróbuj nową wersję demonstracyjną **transkrypcji konwersacji** . Uruchom jego przepisywania z **sesją** **startową** > . Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosu uczestnika, można je umieścić w pliku `participants.properties` w **obiekcie docelowym/klasach**projektu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demonstracyjnej](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Tworzenie i uruchamianie aplikacji autonomicznej

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Wybierz **wyeksportować**. 

1. Zostanie wyświetlone okno **eksport** . Rozwiń węzeł **Java** i wybierz pozycję **możliwy do uruchomienia plik JAR** , a następnie wybierz pozycję **Next (dalej**).

   ![Zrzut ekranu okna eksportowania](media/speech-devices-sdk/eclipse-export-windows.png) 

1. Zostanie wyświetlone okno **Eksportuj plik możliwy do uruchomienia jar** . Wybierz **lokalizację docelową eksportu** aplikacji, a następnie wybierz pozycję **Zakończ**.
 
   ![Zrzut ekranu przedstawiający Eksport pliku JAR możliwy do uruchomienia](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Umieść `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` i `Microsoft.CognitiveServices.Speech.extension.pma.dll` w folderze docelowym wybranym powyżej, ponieważ te pliki są wymagane przez aplikację.

1. Aby uruchomić aplikację autonomiczną

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
