---
title: 'Szybki Start: Rozpoznawanie mowy, Java (Windows, Linux, macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak utworzyć prostą aplikację Java, która przechwytuje i przekształca mowę użytkownika z mikrofonu komputera.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9438cd1345d324b068db8837216afb39241cb2a2
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802469"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Szybki Start: Rozpoznawanie mowy przy użyciu zestawu Speech SDK dla języka Java

Przewodniki Szybki Start są również dostępne w przypadku [tłumaczenia mowy na mowę](quickstart-translate-speech-java-jre.md), [wirtualnego asystenta głosowego](quickstart-virtual-assistant-java-jre.md)i [syntezy mowy](quickstart-text-to-speech-java-jre.md).

W razie potrzeby wybierz inny język programowania i/lub środowisko:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule opisano tworzenie aplikacji konsolowej Java przy użyciu [zestawu Speech SDK](speech-sdk.md). Zamiana mowy na tekst w czasie rzeczywistym transkrypcja z mikrofonu na komputerze. Aplikacja została skompilowana z pakietem Maven zestawu mowy SDK, a w przypadku 64-bitowego systemu Windows (v 4.8) — * 16,04 64, Ubuntu 18,04, Debian 9) lub w macOS 10,13 lub nowszym. Działa on w 64-bitowym środowisku środowiska uruchomieniowego Java 8 (JRE).

> [!NOTE]
> Aby zapoznać się z zestawem SDK usługi Speech Devices i urządzeniem roobo, zobacz temat [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga:

* System operacyjny: 64-bitowy Windows, 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) lub macOS 10,13 lub nowszy
* [Zaćmienie IDE Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi mowy. [Uzyskaj bezpłatnie](get-started.md).

Jeśli używasz systemu Linux, upewnij się, że te zależności są zainstalowane przed rozpoczęciem przeszukania.

* w Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* W programie Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Jeśli korzystasz z systemu Windows (64-bitowego), upewnij się, że zainstalowano pakiet redystrybucyjny Microsoft Visual C++ dla danej platformy.
* [Pobierz pakiet redystrybucyjny Microsoft Visual C++ dla programu visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Tworzenie i Konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Aby dodać nową pustą klasę do projektu Java, wybierz pozycję **plik** > **New** > **class**.

1. W **nowym oknie klasy Java** wprowadź **speechsdk. szybkiego startu** w polu **pakiet** , a następnie pozycję **Main** w polu **Nazwa** .

   ![Zrzut ekranu przedstawiający nowe okno klasy Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Zastąp cały kod w `Main.java` następującym fragmentem kodu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` dla subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz polecenie **uruchom** > **Debug**.
Kolejne 15 sekund wejścia mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym rozpoznaniu](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, takie jak odczytywanie mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Poznaj przykłady języka Java w witrynie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki Start: Tłumaczenie mowy, Java (Windows, Linux, macOS)](quickstart-translate-speech-java-jre.md)
- [Szybki Start: synteza mowy, Java (Windows, Linux, macOS)](quickstart-text-to-speech-java-jre.md)
- [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md)
- [Dostosuj modele języka](how-to-customize-language-model.md)
