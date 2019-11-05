---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C++ (Windows)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku C++ na komputerze z systemem Windows przy użyciu zestawu SDK usługi Mowa
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 1a08a5413ae41ef8c17e817a01be9e9efcb7ff99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503640"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Wypowiedz zwrot lub zdanie w języku angielskim. Aplikacja przesyła mowę do usług mowy, które przekształca do tekstu i wysyła je z powrotem do aplikacji do wyświetlenia.

   ![Dane wyjściowe konsoli po pomyślnym rozpoznaniu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]