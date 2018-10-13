---
title: Zamiana mowy na tekst — informacje
description: Omówienie funkcji zamiany mowy na tekst interfejsu API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: 3d03cd6a5cceb805ed8ee34548bd1a3973e9d78b
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167090"
---
# <a name="about-the-speech-to-text-api"></a>Zamiana mowy na tekst interfejsu API — informacje

**Zamiana mowy na tekst** API *transcribes* strumieni audio na tekst, który aplikacji można wyświetlić użytkownikowi lub działać jako polecenia dane wejściowe. Interfejsy API mogą służyć przy użyciu biblioteki klienta zestawu SDK (dla obsługiwanych platform i języków) lub interfejsu API REST.

**Zamiana mowy na tekst** interfejs API oferuje następujące funkcje:

- Zaawansowane technologii rozpoznawania mowy firmy Microsoft — taka sama używany przez Cortana, pakietu Office i innych produktów firmy Microsoft.

- W czasie rzeczywistym rozpoznawania ciągłe. **Zamiana mowy na tekst** umożliwia użytkownikom wykonać transkrypcji audio na tekst w czasie rzeczywistym. Obsługuje ona również otrzymywać wyniki pośrednie wyrazów, które zostały rozpoznane do tej pory. Usługa automatycznie rozpoznaje koniec mowy. Użytkownicy mogą także wybrać dodatkowych opcji formatowania, w tym wielkość liter i znaków interpunkcyjnych, wulgaryzmów maskowania i normalizacji odwrotność tekstu.

- Wyniki zwracane w formularzach zarówno Lexical, jak i wyświetlania (leksykalne wyników, zobacz DetailedSpeechRecognitionResult w przykładach lub interfejsu API).

- Obsługa wielu języków i dialekty. Aby uzyskać pełną listę obsługiwanych języków w trybie rozpoznawania, zobacz [obsługiwane języki](language-support.md#speech-to-text).

- Dostosowane modeli języka i akustyczne, dzięki czemu można dostosować aplikację do słownictwa wyspecjalizowane domeny użytkowników, wypowiedzi środowiska i sposób wypowiedzi.

- Interpretacja języka naturalnego. Dzięki integracji z usługą [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), użytkownik może dziedziczyć intencje i podmioty mowy. Użytkownicy nie muszą znać słownictwa aplikacji, ale można opisać mają własnymi słowami.

- Współczynnik ufności jest zwracany odpowiedź od usługi, jeśli określisz szczegółowych danych wyjściowych na obiekt konfiguracji mowy (właściwość SpeechConfig.OutputFormat). Następnie możesz użyć jednej z metod Best() wynik lub pobrać go bezpośrednio wynik w JSON zwrócony przez usługę (podobnie do wyniku. Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>Funkcje interfejsu API

Niektóre z możliwości **zamiana mowy na tekst** interfejsu API — zwłaszcza w części dotyczącej dostosowywania — są dostępne za pośrednictwem interfejsu REST. Poniższa tabela zawiera podsumowanie możliwości każdej metody uzyskiwania dostępu do interfejsu API. 

| Przypadek użycia | REST | Zestawy SDK |
|-----|-----|-----|----|
| Transkrypcja krótka wypowiedź, takich jak polecenia (długość < 15 s); nie wyników pośrednich | Yes | Yes |
| Transkrypcja dłużej wypowiedź (> 15 s) | Nie | Yes |
| Transkrypcja przesyłania strumieniowego audio z opcjonalnych wyników pośrednich | Nie | Yes |
| Zrozumieć intencje osoby mówiącej za pośrednictwem usługi LUIS | Brak\* | Yes |
| Utwórz testy dokładności | Yes | Nie |
| Przekaż zestawy danych dla adaptacja modelu | Yes | Nie |
| Tworzenie i zarządzanie modelami mowy | Yes | Nie |
| Tworzenie i zarządzanie wdrożeniami modelu | Yes | Nie |
| Zarządzanie subskrypcjami | Yes | Nie |
| Tworzenie i zarządzanie wdrożeniami modelu | Yes | Nie |
| Tworzenie i zarządzanie wdrożeniami modelu | Yes | Nie |

> [!NOTE]
> Interfejs API REST implementuje, że limity żądań interfejsu API 25 na 5-sekundowego ograniczania przepływności. Hearders komunikat informuje o limicie

\* *Usługa LUIS intencje i podmioty mogą być uzyskane przy użyciu oddzielnej subskrypcji usługi LUIS. Z tą subskrypcją zestawu SDK można wywołać usługi LUIS i zawierają jednostki i przeznaczenie wyniki, a także transkrypcje mowy. Za pomocą interfejsu API REST, można wywołać usługi LUIS samodzielnie do wyprowadzenia intencje i podmioty ze swoją subskrypcją usługi LUIS.*

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Szybki Start: Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
* [Zobacz, jak rozpoznać intencje z wypowiedzi w języku C#](how-to-recognize-intents-from-speech-csharp.md)
