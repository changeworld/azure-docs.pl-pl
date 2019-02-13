---
title: 'Szybki start: rozpoznawanie mowy, Java (Windows, Linux) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć prostą aplikację Java, która przechwytuje i transkrybuje mowę użytkownika z mikrofonu komputera.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 9185d8d236b2a3283cf5a542002adadd0ac74686
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820542"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Szybki start: rozpoznawanie mowy przy użyciu zestawu Speech SDK dla platformy Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację konsolową w języku Java przy użyciu [zestawu SDK usługi Mowa](speech-sdk.md). Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym z mikrofonu komputera. Aplikacja jest kompilowana przy użyciu pakietu Maven zestawu Speech SDK i środowiska IDE Eclipse Java (wersja 4.8) w systemie Windows w wersji 64-bitowej lub Ubuntu Linux 16.04 / 18.04 w wersji 64-bitowej. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: Windows (wersja 64-bitowa) lub Ubuntu Linux 16.04/18.04 (wersja 64-bitowa)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

Jeśli korzystasz z systemu Ubuntu 16.04/18.04, upewnij się, że przed uruchomieniem środowiska Eclipse zostały zainstalowane następujące zależności.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

Jeśli korzystasz z systemu Windows (64-bitowego), upewnij się, że masz zainstalowany pakiet Microsoft Visual C++ Redistributable dla swojej platformy.
* [Pobierz pakiet Microsoft Visual C++ Redistributable dla programu Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


## <a name="create-and-configure-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

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

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w serwisie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz też

- [Szybki start: tłumaczenie mowy, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
