---
title: 'Szybki Start: Rozpoznawanie mowy w języku Java (Windows lub Linux)'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku Java (Windows lub Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234334"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Szybki Start: Rozpoznawanie mowy w języku Java (Windows lub Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym dokumencie opisano sposób tworzenia aplikacji konsoli opartych na języku Java dla środowiska czasu wykonywania Java (JRE), który korzysta z zestawu SDK rozpoznawania mowy.
Ta aplikacja jest oparta na Microsoft Cognitive Services SDK pakiet Maven.
Używamy programu Eclipse jako rozwoju środowiska IDE (Integrated).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Komputer (Windows x64, Ubuntu 16.04 x64) stanie do uruchamiania środowiska Eclipse z mikrofonu pracy.
* 64-bitowego środowiska JRE/JDK języka Java 8.
* Wersja 4.8 [Eclipse](https://www.eclipse.org), 64-bitowej wersji.
* Na Ubuntu 16.04 uruchom następujące polecenia instalacji wymagane pakiety:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. Na ekranie uruchamiania środowiska Eclipse, wprowadź nazwę nowego katalogu w **obszaru roboczego** pola.
   Następnie kliknij przycisk **Uruchom**.

   ![Tworzenie obszaru roboczego środowiska Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Po krótkiej chwili w głównym oknie środowiska IDE Eclipse wyświetlane.
   Jeśli w niej jest ekran powitalny, zamknij go.

1. Wybierz **pliku** \> **nowe** \> **projektu**.

1. W **nowy projekt** kreatora, który pojawia się wybierz opcję **projektu w języku Java**i kliknij przycisk **dalej**.

   ![Wybierz kreatora](media/sdk/qs-java-jre-02-select-wizard.png)

1. W następnym oknie wprowadź **Szybki Start** jako projekt nazwę, a następnie wybierz **JavaSE 1.8** (lub) jako środowisko wykonawcze.
   Kliknij przycisk **Zakończ**.

   ![Wybierz kreatora](media/sdk/qs-java-jre-03-create-java-project.png)

1. Jeśli okno pod tytułem **Otwórz skojarzonej perspektywy?** punktów POP w górę, wybierz **Otwórz perspektywy**.

1. W **narzędziu Package explorer**, kliknij prawym przyciskiem myszy **Szybki Start** projektu, a następnie wybierz **Konfiguruj** \> **Konwertuj na projekt Maven**.

   ![Konwertuj na projekt Maven](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. W oknie wyskakującym wprowadź **com.microsoft.cognitiveservices.speech.samples** jako **identyfikator grupy** i **Szybki Start** jako **identyfikator artefaktu**. Wybierz pozycję **Finish** (Zakończ).

   ![Konfigurowanie POM narzędzia Maven](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Edytuj **pom.xml** pliku.

  * Na koniec pliku przed tagiem zamykającym `</project>`, utwórz sekcję repozytoriów z odwołaniem do repozytorium narzędzia Maven dla zestawu SDK usługi mowy:

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Ponadto później dodać sekcji dependencies z zestawem SDK w wersji 0.6.0 mowy jako zależność:

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Zapisz zmiany.

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Wybierz **pliku** \> **New** \> **klasy** Aby dodać nową pustą klasę do projektu języka Java.

1. W oknie **Nowa Klasa Java** wprowadź **speechsdk.quickstart** do **pakietu** pola i **Main** do **nazwy**  pola.

   ![Tworzenie klasy Main](media/sdk/qs-java-jre-06-create-main-java.png)

1. Zastąp cały kod w `Main.java` następującym fragmentem kodu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

Naciśnij klawisz F11, lub wybierz **Uruchom** \> **debugowania**.
Dalej 15 sekund wejście mowy z mikrofonu zostaną rozpoznane i rejestrowane w oknie konsoli.

![Dane wyjściowe konsoli po pomyślnym rozpoznawania](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/java-jre` folderu.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)
