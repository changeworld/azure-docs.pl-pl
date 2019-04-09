---
title: 'Szybki start: rozpoznawanie mowy, Java (Windows, Linux) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start dowiesz się utworzyć prostą aplikację Java, która przechwytuje i transcribes mowy użytkownika z mikrofonu tego komputera.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: fe565d63e72b5ec2798dde03ba4f4bd9ff4f48a7
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009403"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Szybki start: rozpoznawanie mowy przy użyciu zestawu Speech SDK dla platformy Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację konsolową w języku Java przy użyciu [zestawu Speech SDK](speech-sdk.md). Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym z mikrofonu komputera. Aplikacji został utworzony za pomocą pakietu Maven zestaw SDK rozpoznawania mowy i środowisko Eclipse IDE Java (v4.8) na Windows 64-bitowym, 64-bitowym systemem Ubuntu Linux 16.04 / 18.04 lub w systemie macOS 10.13 lub nowszej. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: Windows (64-bitowy), 16.04/18.04 Ubuntu Linux (64-bitowa) lub z systemem macOS 10.13 lub nowszy
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

Jeśli korzystasz z systemu Ubuntu 16.04/18.04, upewnij się, że przed uruchomieniem środowiska Eclipse zostały zainstalowane następujące zależności.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Jeśli korzystasz z Windows (64-bitowy) upewnij się, że zainstalowano program Microsoft Visual C++ Redistributable dla danej platformy.
* [Pobierz program Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


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

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Eksplorowanie przykładów w języku Java w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki start: Tłumaczenie mowy, języka Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md)
- [Dostosowywanie modeli językowych](how-to-customize-language-model.md)
