---
title: Co to jest aparat rozpoznawania pisma odręcznego? -Interfejs API rozpoznawania pisma odręcznego
titleSuffix: Azure Cognitive Services
description: Aparat rozpoznawania pisma odręcznego Integrowanie aplikacji, witryn sieci Web, narzędzi i inne rozwiązania, aby zezwolić na dane pociągnięcia pisma odręcznego identyfikować i używane jako dane wejściowe.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 95121523c510e72894086740ad340bdfd33f3c32
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721405"
---
# <a name="what-is-the-ink-recognizer-api"></a>Co to jest interfejs API rozpoznawania pisma odręcznego?


Usługa Cognitive aparatu rozpoznawania pisma odręcznego zapewnia oparte na chmurze interfejs API REST do analizowania i rozpoznawać zawartość cyfrowy atrament. W przeciwieństwie do usług, które używają funkcji optycznego rozpoznawania znaków (OCR) interfejs API wymaga cyfrowy atrament obrysu danych jako dane wejściowe. Pociągnięcia odręczne cyfrowe to uporządkowanej w czasie zestawy 2D punkty (współrzędne), które reprezentują ruchu danych wejściowych narzędzi, takich jak pióra cyfrowe lub palców. Następnie rozpoznaje kształty i pisma odręcznego zawartości z danych wejściowych i zwraca odpowiedź JSON zawierający wszystkie rozpoznawanym jednostkę.

![Schemat blokowy opisujący wysyłania pociągnięcia odręczne do interfejsu API](media/ink-recognizer-pen-graph.png)

## <a name="features"></a>Funkcje

Za pomocą interfejsu API rozpoznawania pisma odręcznego możesz łatwo rozpoznać pisma odręcznego zawartości w aplikacjach. 

|Cecha  |Opis  |
|---------|---------|
| Rozpoznawanie pisma odręcznego | Rozpoznawanie pisma odręcznego zawartość 63 core [języków i ustawień regionalnych](language-support.md). | 
| Rozpoznawanie układu | Uzyskaj strukturalnych informacji o zawartości cyfrowy atrament. Podziel zawartość na zapisywanie regionów, akapitów, linii, słów, list punktowanych. Aplikacje mogą następnie umożliwia tworzenie dodatkowych funkcji, takich jak formatowanie automatyczne listy informacji o układzie oraz kształt wyrównania. |
| Rozpoznawanie kształtów | Rozpoznaje najczęściej używanych [kształtów geometrycznych](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) robiąc uwagi. |
| Połączone kształty i rozpoznawanie tekstu | Rozpoznawanie pisma odręcznego, które pociągnięć należą do kształtów lub pisma odręcznego zawartości, a następnie oddzielnie ich klasyfikacji.|

## <a name="workflow"></a>Przepływ pracy

Interfejs API rozpoznawania pisma odręcznego jest to usługa sieci web typu RESTful, co ułatwia wywołać z dowolnego języka programowania, który może wysyłać żądania HTTP i Przeanalizuj dane JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Po zarejestrowaniu się:

1. Przenieś swoje dane pociągnięcia odręczne i [flagowana](concepts/send-ink-data.md#sending-ink-data) w prawidłowym kodem JSON.
1. Wyślij żądanie do interfejsu API rozpoznawania pisma odręcznego ze swoimi danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Wypróbuj Szybki Start w następujących językach, aby rozpocząć wykonywanie wywołań interfejsu API rozpoznawania pisma odręcznego.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w cyfrowych aplikacji pisma odręcznego, spójrz na następujące przykładowe aplikacje w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
