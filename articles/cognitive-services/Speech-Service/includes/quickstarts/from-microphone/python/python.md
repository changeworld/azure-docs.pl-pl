---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 9002df0a596eec1dcf7aca425a45d806fb94805b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658933"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu mowy platformy Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="source-code"></a>Kod źródłowy

Utwórz plik o nazwie *quickstart.py* i wklej w nim następujący kod Języka Python.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Wyjaśnienie kodu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do testowania aplikacji i weryfikowania funkcji rozpoznawania mowy za pomocą usługi Mowy.

1. **Uruchom aplikację** — w wierszu polecenia wpisz:
    ```bash
    python quickstart.py
    ```
2. **Rozpocznij rozpoznawanie** - poprosi Cię o wymówienia frazy w języku angielskim. Mowa jest wysyłana do usługi Mowy, transkrybowana jako tekst i renderowana w konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](../footer.md)]
