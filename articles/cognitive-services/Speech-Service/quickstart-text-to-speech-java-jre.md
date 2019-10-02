---
title: 'Szybki Start: synteza mowy, Java (Windows, Linux, macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak utworzyć prostą aplikację Java, która przechwytuje i syntezuje mowę z tekstu i odtwarza ją z domyślnym głośnikem.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 832525ae1441fca85f8df661b4a187c0be8d91dc
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803985"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-java"></a>Szybki Start: syntezowanie mowy przy użyciu zestawu Speech SDK dla języka Java

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-java-jre.md), [zamiany mowy na mowę i rozpoznawania](quickstart-translate-speech-java-jre.md)mowy dla sygnałów [głosowych](quickstart-virtual-assistant-java-jre.md).

W tym artykule utworzysz aplikację konsolową w języku Java przy użyciu [zestawu Speech SDK](speech-sdk.md). Można wypróbować mowę z tekstu i odtworzyć ją z domyślnym głośnikem komputera. Aplikacja została skompilowana z pakietem Maven zestawu mowy SDK, a w przypadku 64-bitowego systemu Windows (v 4.8) — * 16,04 64, Ubuntu 18,04, Debian 9) lub w macOS 10,13 lub nowszym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

> [!NOTE]
> W przypadku zestawu Speech Devices SDK i urządzenia Roobo zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowy Windows, 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) lub macOS 10,13 lub nowszy
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

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Aby dodać nową pustą klasę do projektu języka Java, wybierz kolejno pozycje **Plik** > **Nowy** > **Klasa**.

1. W oknie **Nowa klasa Java** wprowadź ciąg **speechsdk.quickstart** w polu **Pakiet** i **Main** w polu **Nazwa**.

   ![Zrzut ekranu okna Nowa klasa Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Zastąp cały kod w pliku `Main.java` następującym fragmentem kodu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz kolejno pozycje **Uruchom** > **Debugowanie**.
Wprowadź tekst podczas podwyższania poziomu, a w tym miejscu audio jest odtwarzany z domyślnego głośnika.

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, dotyczące np. sposobu odczytywania mowy z pliku audio, są dostępne w witrynie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki Start: Rozpoznawanie mowy, Java (Windows, Linux, macOS)](quickstart-java-jre.md)
- [Szybki Start: Tłumaczenie mowy, Java (Windows, Linux, macOS)](quickstart-translate-speech-java-jre.md)
- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
