---
title: 'Szybki start: Rozpoznawanie mowy, Java (Windows, Linux) — usługa mowy'
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
ms.openlocfilehash: 498e41b08133113be9789ef49291b8e2bb0f3705
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554108"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Szybki start: rozpoznawanie mowy przy użyciu zestawu Speech SDK dla platformy Java

Przewodniki Szybki Start są również dostępne w przypadku [funkcji zamiany mowy na mowę](quickstart-translate-speech-java-jre.md) i [głosowego asystenta wirtualnego](quickstart-virtual-assistant-java-jre.md).

W razie potrzeby wybierz inny język programowania i/lub środowisko:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację konsolową w języku Java przy użyciu [zestawu Speech SDK](speech-sdk.md). Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym z mikrofonu komputera. Aplikacja została skompilowana z pakietem Maven zestawu mowy SDK, a w przypadku 64-bitowego systemu Windows (v 4.8) — * 16,04 64, Ubuntu 18,04, Debian 9) lub w macOS 10,13 lub nowszym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowy system Windows, 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) lub macOS 10,13 lub nowszy
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

Jeśli używasz systemu Linux, upewnij się, że te zależności są zainstalowane przed rozpoczęciem przeszukania.

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

Jeśli korzystasz z systemu Windows (64-bitowego), upewnij się, że zainstalowano pakiet redystrybucyjny Microsoft Visual C++ dla danej platformy.
* [Pobierz pakiet redystrybucyjny Microsoft Visual C++ dla programu visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

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

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w serwisie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki start: tłumaczenie mowy, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
