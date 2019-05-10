---
title: 'Szybki start: tłumaczenie mowy, Java (Windows, Linux) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start utworzysz prostą aplikację Java do przechwytywania mowy użytkownika, tłumaczenia jej na inny język i przekazywania tekstu do wiersza polecenia. Ten przewodnik jest przeznaczony dla użytkowników systemów Windows i Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 69cb48df4e765250247605fb5c4e1f421554fd20
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467013"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Szybki start: Tłumaczenie mowy z zestawem SDK rozpoznawania mowy, języka Java

Przewodniki Szybki Start są również dostępne dla [mowy na tekst](quickstart-java-jre.md) i [głosu pierwszego wirtualnego Asystenta](quickstart-virtual-assistant-java-jre.md).

W tym przewodniku Szybki start utworzysz prostą aplikację Java, która przechwytuje mowę użytkownika z mikrofonu komputera, tłumaczy tę mowę i transkrybuje przetłumaczony tekst do wiersza polecenia w czasie rzeczywistym. Ta aplikacja jest przeznaczony do działania na Windows 64-bitowy lub 64-bitowych Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9) lub w systemie macOS 10.13 lub nowszej. Jest on oparty za pomocą pakietu Maven zestaw SDK rozpoznawania mowy i środowiska Eclipse IDE Java.

Aby uzyskać pełną listę języków dostępnych na potrzeby tłumaczenia mowy, zapoznaj się z [listą obsługiwanych języków](language-support.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowych Windows 64-bitowych systemu Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9) i macOS 10.13 lub nowszej
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

Jeśli korzystasz z systemu Linux, upewnij się, że te zależności są zainstalowane przed uruchomieniem programu Eclipse.

 * W systemie Ubuntu:

   ```sh
   sudo apt-get update
   sudo apt-get install libssl1.0.0 libasound2
   ```

 * On Debian 9:

   ```sh
   sudo apt-get update
   sudo apt-get install libssl1.0.2 libasound2
   ```

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Aby dodać nową pustą klasę do projektu języka Java, wybierz kolejno pozycje **Plik** > **Nowy** > **Klasa**.

1. W oknie **Nowa klasa Java** wprowadź ciąg **speechsdk.quickstart** w polu **Pakiet** i **Main** w polu **Nazwa**.

   ![Zrzut ekranu okna Nowa klasa Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Zastąp cały kod w pliku `Main.java` następującym fragmentem kodu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz kolejno pozycje **Uruchom** > **Debugowanie**.

Dane wejściowe mowy z mikrofonu zostaną przetranskrybowane na język niemiecki i zarejestrowane w oknie konsoli. Naciśnij klawisz Enter, aby zatrzymać przechwytywanie mowy.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, dotyczące między innymi odczytywania mowy z pliku audio i zwracania przetłumaczonego tekstu jako syntetyzowanej mowy, są dostępne w usłudze GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki start: rozpoznawanie mowy, Java (Windows, Linux)](quickstart-java-jre.md)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
