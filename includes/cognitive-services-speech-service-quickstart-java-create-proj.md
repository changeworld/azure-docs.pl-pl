---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 8a8611218f47e5600fc02efef24aa7912b630522
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010964"
---
1. Uruchom środowisko Eclipse.

1. W programie Eclipse Launcher w polu **Workspace** (Obszar roboczy) wprowadź nazwę nowego katalogu roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu programu Eclipse utwórz nowy projekt, wybierając kolejno pozycje **File** > **New** > **Project** (Plik > Nowy > Projekt).

1. Zostanie wyświetlone okno dialogowe **Nowy projekt**. Wybierz pozycję **Java Project** (Projekt języka Java) i wybierz pozycję **Next** (Dalej).

   ![Zrzut ekranu dialogowego New Project (Nowy projekt) z wyróżnioną pozycją Java Project (Projekt języka Java)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Zostanie uruchomiony kreator nowego projektu języka Java. W polu **Project name** (Nazwa projektu) wprowadź ciąg **quickstart** i wybierz **JavaSE-1.8** jako środowisko wykonania. Wybierz pozycję **Finish** (Zakończ).

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Jeśli zostanie wyświetlone okno **Open Associated Perspective?** (Otworzyć skojarzoną perspektywę?), wybierz pozycję **Open Perspective** (Otwórz perspektywę).

1. W narzędziu **Package Explorer** kliknij prawym przyciskiem myszy projekt **quickstart**. Z menu kontekstowego wybierz kolejno pozycje **Configure** > **Convert to Maven Project** (Konfiguruj > Konwertuj na projekt Maven).

   ![Zrzut ekranu narzędzia Package Explorer](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zostanie wyświetlone okno **Create new POM** (Tworzenie nowego modelu POM). W polu **Group Id** (Identyfikator grupy) wprowadź ciąg **com.microsoft.cognitiveservices.speech.samples**, a w polu **Artifact Id** (Identyfikator artefaktu) — ciąg **quickstart**. Następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu okna Create new POM (Tworzenie nowego modelu POM)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otwórz plik **pom.xml** i edytuj go.

   * Na końcu pliku przed tagiem zamykającym `</project>` utwórz element `repositories` z odwołaniem do repozytorium narzędzia Maven dla zestawu Speech SDK, jak pokazano poniżej:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Również dodać `dependencies` elementu z zestawem SDK w wersji 1.4.0 jako zależność mowy:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Zapisz zmiany.
