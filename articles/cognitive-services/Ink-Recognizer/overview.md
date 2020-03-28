---
title: Co to jest aparat rozpoznawania atramentu? - Interfejs API aparatu rozpoznawania atramentu
titleSuffix: Azure Cognitive Services
description: Zintegruj aparat rozpoznawania pisma odręcznego z aplikacjami, witrynami internetowymi, narzędziami i innymi rozwiązaniami, aby umożliwić identyfikację i użycie danych obrysu atramentowego jako danych wejściowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448151"
---
# <a name="what-is-the-ink-recognizer-api"></a>Co to jest interfejs API rozpoznawania pisma odręcznego?


Usługa Cognitive Service aparat rozpoznawania pisma odurzacza zapewnia oparty na chmurze interfejs API REST do analizowania i rozpoznawania zawartości atramentu cyfrowego. W przeciwieństwie do usług, które używają optycznego rozpoznawania znaków (OCR), interfejs API wymaga danych cyfrowego obrysu atramentu jako danych wejściowych. Cyfrowe pociągnięcia atramentu to uporządkowane w czasie zestawy punktów 2D (współrzędne X,Y), które reprezentują ruch narzędzi wejściowych, takich jak pióra cyfrowe lub palce. Następnie rozpoznaje kształty i zawartość odręczną z danych wejściowych i zwraca odpowiedź JSON zawierającą wszystkie rozpoznane jednostki.

![Schemat blokowy opisujący wysyłanie danych wejściowych obrysu odręcznego do interfejsu API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funkcje

Za pomocą interfejsu API aparatu rozpoznawania pisma odręcznego można łatwo rozpoznawać zawartość odręczną w aplikacjach. 

|Funkcja  |Opis  |
|---------|---------|
| Rozpoznawanie pisma ręcznego | Rozpoznawanie odręcznej zawartości w 63 podstawowych [językach i ustawieniach regionalnych](language-support.md). | 
| Rozpoznawanie układu | Uzyskaj informacje strukturalne o zawartości atramentu cyfrowego. Podziel zawartość na pisanie regionów, akapitów, wierszy, słów, list punktowanych. Aplikacje mogą następnie używać informacji o układzie do tworzenia dodatkowych funkcji, takich jak automatyczne formatowanie listy i wyrównanie kształtów. |
| Rozpoznawanie kształtów | Rozpoznaj najczęściej używane [kształty geometryczne](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) podczas robienia notatek. |
| Połączone kształty i rozpoznawanie tekstu | Rozpoznaj, które obrysy odręczne należą do kształtów lub zawartości odręcznej, i oddzielnie klasyfikuj je.|

## <a name="workflow"></a>Przepływ pracy

Interfejs API rozpoznawania pisma odurzacza jest usługą sieci web RESTful, dzięki czemu można łatwo dzwonić z dowolnego języka programowania, który może tworzyć żądania HTTP i analizować JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Po zarejestrowaniu się:

1. Zabierz dane obrysu odręcznego i [sformatować go](concepts/send-ink-data.md#sending-ink-data) w prawidłowy JSON.
1. Wyślij żądanie do interfejsu API aparatu rozpoznawania pisma odurzacza z danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Wypróbuj przewodnik Szybki start w następujących językach, aby rozpocząć nawiązywać połączenia z interfejsem API aparatu rozpoznawania pisma odurzającego.
* [C #](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [Javascript](quickstarts/javascript.md)

Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odurzającego w aplikacji do cyfrowego pisma odurzającego, zapoznaj się z następującymi przykładowymi aplikacjami w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
