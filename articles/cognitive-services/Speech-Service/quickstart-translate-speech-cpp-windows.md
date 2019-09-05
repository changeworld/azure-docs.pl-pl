---
title: 'Szybki start: Tłumaczenie mowy, C++ (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz C++ aplikację, aby przechwycić mowę użytkownika, przetłumaczyć ją na inny język i wyprowadził tekst w wierszu polecenia. Ten przewodnik jest przeznaczony dla użytkowników systemu Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382676"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Szybki start: Tłumaczenie mowy w C++ systemie Windows przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-cpp-windows.md) i [syntezy mowy](quickstart-text-to-speech-cpp-windows.md).

W tym przewodniku szybki start utworzysz C++ aplikację, która przechwytuje mowę użytkownika z mikrofonu komputera, przekształci mowę i przekształca przetłumaczony tekst do wiersza polecenia w czasie rzeczywistym. Ta aplikacja jest zbudowana z [pakietem NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019 (w dowolnej wersji).

Aby uzyskać pełną listę języków dostępnych na potrzeby tłumaczenia mowy, zapoznaj się z [listą obsługiwanych języków](language-support.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz klucza subskrypcji usługi Speech Services. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje [, zobacz Wypróbuj bezpłatne usługi mowy](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **Kompiluj** > kompilacje**rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Wypowiedz zwrot lub zdanie w języku angielskim. Aplikacja przesyła mowę do usług mowy, które tłumaczą i przekształca na tekst (w tym przypadku do języka francuskiego i niemieckiego). Usługi mowy wysyłają następnie tekst z powrotem do aplikacji do wyświetlenia.

   ![Dane wyjściowe konsoli po pomyślnym przetłumaczeniu mowy](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, takie jak odczytywanie mowy z pliku dźwiękowego lub przekształcanie tekstu na mowę, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C++ w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Uczenie modelu dla Custom Speech](how-to-custom-speech-train-model.md)
