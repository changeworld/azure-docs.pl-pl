---
title: O mowy na tekst | Dokumentacja firmy Microsoft
description: Omówienie funkcji mowy API tekstu.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349744"
---
# <a name="about-the-speech-to-text-api"></a>O mowy na tekst interfejsu API

**Mowy na tekst** interfejsu API *transcribes* strumieni audio w aplikacji można wyświetlić dla użytkownika lub obsługiwanie jako tekst polecenia danych wejściowych. Interfejsy API mogą służyć jako biblioteki klienta SDK (dla obsługiwanych platform i języków) lub interfejsu API REST.

**Mowy na tekst** interfejsu API oferuje następujące funkcje:

- Zaawansowane technologii rozpoznawania mowy firmy Microsoft — taka sama używany przez usługę Cortana, pakietu Office i innych produktów firmy Microsoft.

- Rozpoznawanie ciągłej w czasie rzeczywistym. **Mowy na tekst** umożliwia użytkownikom wykonać transkrypcji audio na tekst w czasie rzeczywistym. Obsługuje ona również otrzymywać wyniki pośrednie wyrazy, które zostały uznane wykonanej do tej pory. Usługa automatycznie rozpoznaje koniec mowy. Użytkownicy mogą także wybrać dodatkowe opcje formatowania, w tym wielkość liter i znaków interpunkcyjnych, niestosownych wyrażeń maskowania i normalizacji tekstu.

- Zoptymalizowane **mowy na tekst** wyniki dla interaktywnego, konwersacji, dyktowania scenariusze i. 

- Obsługa wielu języków w wielu dialekty języka. Aby uzyskać pełną listę obsługiwanych języków w każdym trybie rozpoznawania zobacz [obsługiwanych języków](supported-languages.md#speech-to-text).

- Dostosowany języka i akustycznego modeli, można dostosować sposób użytkowników mówiąc, mówiąc środowiska i specjalistycznego słownictwa przez aplikację.

- Opis języka naturalnego. Dzięki integracji z [opis języka](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), może pochodzić intencje i jednostki z mowy. Użytkownicy nie trzeba znać słownictwa aplikacji, ale można opisać mają własnymi słowami.

## <a name="api-capabilities"></a>Funkcje interfejsu API

Niektóre możliwości **mowy na tekst** interfejsu API nie są dostępne za pośrednictwem interfejsu REST. Poniższa tabela zawiera podsumowanie możliwości poszczególnych metod uzyskiwania dostępu do interfejsu API.

| Przypadek użycia | REST | Zestawy SDK |
|-----|-----|-----|----|
| Wykonać transkrypcji krótki utterance, takich jak polecenia (długość < 15 s); nie wyników pośrednich | Yes | Yes |
| Wykonać transkrypcji utterance dłużej (> 15 s) | Nie | Yes |
| Wykonać transkrypcji przesyłania strumieniowego audio z wyników pośrednich opcjonalne | Nie | Yes |
| Zrozumienie intencje prelegenta za pośrednictwem LUIS | Brak\* | Yes |

\* *LUIS intencje i jednostek mogą być uzyskane przy użyciu oddzielnej subskrypcji LUIS. Z tą subskrypcją zestawu SDK można wymagać LUIS i udostępniać jednostki i przeznaczeniu wyników, a także transcriptions mowy. Przy użyciu interfejsu API REST, należy wywołać LUIS samodzielnie pochodzić intencje i jednostki z subskrypcją LUIS.*

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
