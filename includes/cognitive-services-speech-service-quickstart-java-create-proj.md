---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: d6ee5f432321753b9a09749ccf45c9a5bda5300d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802466"
---
1. Rozpocznij przezaćmienie.

1. W obszarze **roboczym** zaćmienie Zanotuj nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Uruchom**.

   ![Zrzut ekranu uruchamiania programu zaćmienie](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. W chwili pojawi się okno główne środowiska IDE w języku zaćmienie. Zamknij ekran powitalny, jeśli jest obecny.

1. Na pasku menu zaćmienie Utwórz nowy projekt, wybierając **plik** > **Nowy** > **projektu**.

1. Pojawi się okno dialogowe **Nowy projekt** . Wybierz pozycję **projekt Java**, a następnie wybierz przycisk **dalej**.

   ![Zrzut ekranu przedstawiający okno dialogowe Nowy projekt z wyróżnionym projektem Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Zostanie uruchomiony Kreator nowego projektu Java. W polu **Nazwa projektu** wprowadź **Przewodnik Szybki Start**, a następnie wybierz pozycję **Java-1,8** jako środowisko wykonawcze. Wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający Kreatora nowego projektu Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Jeśli zostanie wyświetlone okno **Otwórz skojarzoną perspektywę?** , wybierz pozycję **Otwórz perspektywę**.

1. W **Eksploratorze pakietów**kliknij prawym przyciskiem myszy projekt **szybkiego startu** . Wybierz pozycję **konfiguruj** > **Konwertuj na projekt Maven** z menu kontekstowego.

   ![Zrzut ekranu przedstawiający Eksploratora pakietów](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zostanie wyświetlone okno **Utwórz nowy pliku pom** . W polu **Identyfikator grupy** wprowadź wartość **com. Microsoft. cognitiveservices. Speech. Samples**i w polu **Identyfikator artefaktu** wprowadź **Szybki Start**. Następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający tworzenie nowego okna pliku pom](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otwórz plik **pliku pom. XML** i edytuj go.

   * Na końcu pliku przed tagiem zamykającym `</project>` Utwórz element `repositories` z odwołaniem do repozytorium Maven dla zestawu Speech SDK, jak pokazano poniżej:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Dodaj również `dependencies` elementu z zestawem SDK mowy w wersji 1.7.0 jako zależność:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Zapisz zmiany.
