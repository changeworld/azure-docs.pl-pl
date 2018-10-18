---
title: 'Szybki start: rozpoznawanie mowy w języku Java (system Windows lub Linux)'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Java (system Windows lub Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: c0963fa8453dd51dfb05af20a5468e1eed1fc7cf
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885222"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku Java w systemie Windows lub Linux przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację konsolową w języku Java przy użyciu [zestawu Speech SDK](speech-sdk.md). Transkrypcja mowy na tekst będzie odbywać się w czasie rzeczywistym z mikrofonu komputera. Aplikacja jest kompilowana przy użyciu pakietu Maven zestawu Speech SDK i środowiska IDE Eclipse Java (wersja 4.8) w systemie Windows w wersji 64-bitowej lub Ubuntu Linux 16.04. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia kroków tego przewodnika Szybki start potrzebujesz klucza subskrypcji usługi rozpoznawania mowy. Możesz go uzyskać bezpłatnie. Zobacz [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md), aby uzyskać szczegółowe informacje.


## <a name="create-and-configure-project"></a>Tworzenie i konfigurowanie projektu

W przypadku korzystania z systemu Ubuntu 16.04 przed uruchomieniem środowiska Eclipse uruchom poniższe polecenia, aby upewnić się, że wymagane pakiety zostały zainstalowane.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Uruchom środowisko Eclipse.

1. W programie Eclipse Launcher w polu **Workspace** (Obszar roboczy) wprowadź nazwę nowego katalogu roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu programu Eclipse utwórz nowy projekt, wybierając kolejno pozycje **File** > **New** > **Project** (Plik > Nowy > Projekt).

1. Zostanie wyświetlone okno dialogowe **Nowy projekt**. Wybierz pozycję **Java Project** (Projekt języka Java) i wybierz pozycję **Next** (Dalej).

   ![Zrzut ekranu dialogowego New Project (Nowy projekt) z wyróżnioną pozycją Java Project (Projekt języka Java)](media/sdk/qs-java-jre-02-select-wizard.png)

1. Zostanie uruchomiony kreator nowego projektu języka Java. W polu **Project name** (Nazwa projektu) wprowadź ciąg **quickstart** i wybierz **JavaSE-1.8** jako środowisko wykonania. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](media/sdk/qs-java-jre-03-create-java-project.png)

1. Jeśli zostanie wyświetlone okno **Open Associated Perspective?** (Otworzyć skojarzoną perspektywę?), wybierz pozycję **Open Perspective** (Otwórz perspektywę).

1. W narzędziu **Package Explorer** kliknij prawym przyciskiem myszy projekt **quickstart**. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven).

   ![Zrzut ekranu narzędzia Package Explorer](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zostanie wyświetlone okno **Create new POM** (Tworzenie nowego modelu POM). W polu **Group Id** (Identyfikator grupy) wprowadź ciąg **com.microsoft.cognitiveservices.speech.samples**, a w polu **Artifact Id** (Identyfikator artefaktu) — ciąg **quickstart**. Następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu okna Create new POM (Tworzenie nowego modelu POM)](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otwórz plik **pom.xml** i edytuj go.

   * Na końcu pliku przed tagiem zamykającym `</project>` utwórz element `repositories` z odwołaniem do repozytorium narzędzia Maven dla zestawu Speech SDK, jak pokazano poniżej:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Dodaj również element `dependencies` z zestawem Speech SDK w wersji 1.0.0 jako zależność:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Zapisz zmiany.

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Aby dodać nową pustą klasę do projektu języka Java, wybierz kolejno pozycje **Plik** > **Nowy** > **Klasa**.

1. W oknie **Nowa klasa Java** wprowadź ciąg **speechsdk.quickstart** w polu **Pakiet** i **Main** w polu **Nazwa**.

   ![Zrzut ekranu okna Nowa klasa Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Zastąp cały kod w pliku `Main.java` następującym fragmentem kodu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz kolejno pozycje **Uruchom** > **Debugowanie**.
Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w folderze `quickstart/java-jre`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Recognize intents from speech by using the Speech SDK for Java](how-to-recognize-intents-from-speech-java.md) (Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK mowy dla języka Java)

## <a name="see-also"></a>Zobacz też

- [Translate speech with the Cognitive Services Speech SDK for C#](how-to-translate-speech-csharp.md) (Tłumaczenie mowy za pomocą zestawu Speech SDK usługi Cognitive Services dla języka C#)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
