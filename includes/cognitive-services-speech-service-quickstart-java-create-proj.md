---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 3c88b8cb40660327d2a4c28cc80b7ebd5956b189
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467262"
---
1. Uruchom środowisko Eclipse.

1. W programie Eclipse Launcher w polu **Workspace** (Obszar roboczy) wprowadź nazwę nowego katalogu roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran **powitalny** , jeśli jest obecny.

1. Na pasku menu programu Eclipse utwórz nowy projekt, wybierając kolejno pozycje **File** > **New** > **Project** (Plik > Nowy > Projekt).

1. Zostanie wyświetlone okno dialogowe **Nowy projekt**. Wybierz pozycję **Java Project** (Projekt języka Java) i wybierz pozycję **Next** (Dalej).

   ![Zrzut ekranu dialogowego New Project (Nowy projekt) z wyróżnioną pozycją Java Project (Projekt języka Java)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Zostanie uruchomiony Kreator **nowego projektu Java** . W polu **Project name** (Nazwa projektu) wprowadź ciąg **quickstart** i wybierz **JavaSE-1.8** jako środowisko wykonania. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Jeśli zostanie wyświetlone okno **Open Associated Perspective?** (Otworzyć skojarzoną perspektywę?), wybierz pozycję **Open Perspective** (Otwórz perspektywę).

1. W narzędziu **Package Explorer** kliknij prawym przyciskiem myszy projekt **quickstart**. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven).

   ![Zrzut ekranu narzędzia Package Explorer](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zostanie wyświetlone okno **Create new POM** (Tworzenie nowego modelu POM). W polu **Identyfikator grupy** wprowadź wartość *com. Microsoft. cognitiveservices. Speech. Samples*i w polu **Identyfikator artefaktu** wprowadź *Szybki Start*. Następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu okna Create new POM (Tworzenie nowego modelu POM)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otwórz plik *pom.xml* i edytuj go.

   * Na końcu pliku przed tagiem zamykającym `</project>` utwórz element `repositories` z odwołaniem do repozytorium narzędzia Maven dla zestawu Speech SDK, jak pokazano poniżej:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Dodaj również `dependencies` element z zestawem SDK mowy w wersji 1.7.0 jako zależność:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Zapisz zmiany.
