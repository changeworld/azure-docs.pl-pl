---
title: Wysyłanie danych pisma odręcznego do interfejsu API rozpoznawania pisma odręcznego
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o wywoływaniu interfejsu API analizatora pisma odręcznego dla różnych aplikacji
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221107"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Wysyłanie danych pisma odręcznego do interfejsu API rozpoznawania pisma odręcznego 

Cyfrowe pismo odręczne odnosi się do technologii, które umożliwiają tworzenie cyfrowych reprezentacji danych wejściowych, takich jak pismo ręczne i rysunki. Zazwyczaj jest to osiągane za pomocą digitizer, który przechwytuje ruchy urządzeń wejściowych, takich jak rysik. Urządzenia udostępniają zaawansowane środowiska cyfrowego pisma odręcznego, dlatego sztuczna inteligencja i systemy uczenia maszynowego umożliwiają rozpoznawanie napisanych kształtów i tekstu w dowolnym kontekście. Interfejs API aparatu rozpoznawania pisma odręcznego umożliwia wysyłanie pociągnięć odręcznych i uzyskanie szczegółowych informacji na ich temat. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Interfejs API aparatu rozpoznawania pisma od strony pisma od ink a usługi OCR

Interfejs API aparatu rozpoznawania pisma odurzającego nie używa optycznego rozpoznawania znaków (OCR). Usługi OCR przetwarzają dane pikseli z obrazów, aby zapewnić odręczne pisanie i rozpoznawanie tekstu. Jest to czasami nazywane rozpoznawaniem w trybie offline. Zamiast tego interfejs API rozpoznawania pisma odręcznego wymaga cyfrowych danych obrysu atramentu, które są przechwytywane jako używane urządzenie wejściowe. Przetwarzanie danych z atramentu cyfrowego w ten sposób może przynieść dokładniejsze wyniki rozpoznawania w porównaniu z usługami OCR. 

## <a name="sending-ink-data"></a>Wysyłanie danych pisma oduwu

Interfejs API aparatu rozpoznawania pisma odręcznego wymaga współrzędnych X i Y, które reprezentują pociągnięcia odręczne utworzone przez urządzenie wejściowe, od momentu dotknięcia powierzchni wykrywania do momentu jej podniesienia. Punkty każdego obrysu muszą być ciągiem wartości oddzielonych przecinkami i być sformatowane w JSON, tak jak w poniższym przykładzie. Ponadto każdy pociągnięcie odręczne musi mieć unikatowy identyfikator w każdym żądaniu. Jeśli identyfikator zostanie powtórzony w ramach tego samego żądania, interfejs API zwróci błąd. Aby uzyskać najdokładniejsze wyniki rozpoznawania, mieć co najmniej osiem cyfr po przecinku. Przyjmuje się, że początek (0,0) obszaru roboczego jest lewym górnym rogu kanwy pisma odurzowego.

> [!NOTE]
> Poniższy przykład nie jest prawidłowy JSON. Pełne żądanie JSON aparatu ink można znaleźć na [gitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Odpowiedź aparatu rozpoznawania pisma odurzacza od ink

Interfejs API aparatu rozpoznawania pisma odurzacza zwraca odpowiedź analityczną dotyczącą obiektów rozpoznawanych na podstawie zawartości pisma odurzacza. Odpowiedź zawiera jednostki rozpoznawania, które opisują relacje między różnymi pociągnięć odręcznych. Na przykład obrysy, które tworzą różne, oddzielne kształty, będą zawarte w różnych jednostkach. Każda jednostka zawiera szczegółowe informacje o pociągach odręcznych, w tym rozpoznany obiekt, jego współrzędne i inne atrybuty rysunku.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Kształty rozpoznawane przez interfejs API aparatu rozpoznawania pisma odurzacza

Interfejs API aparatu rozpoznawania pisma odurzacza może identyfikować najczęściej używane kształty podczas notatek. Poniższa ilustracja przedstawia kilka podstawowych przykładów. Aby uzyskać pełną listę kształtów i innych treści pisma odudowtowego rozpoznawanych przez interfejs API, zobacz [artykuł referencyjny interfejsu API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Lista kształtów rozpoznawanych przez interfejs API aparatu rozpoznawania pisma odurzacza](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Zalecane wzorce połączeń

Interfejs API REST aparatuwista odurzania od ink można wywołać w różnych wzorcach zgodnie z aplikacją. 

### <a name="user-initiated-api-calls"></a>Zainicjowane przez użytkownika wywołania interfejsu API

Jeśli budujesz aplikację, która pobiera dane wejściowe użytkownika (na przykład aplikacja do robienia notatek lub adnotacji), możesz dać im kontrolę nad tym, kiedy i który odręczny zostanie wysłany do interfejsu API rozpoznawania nazw odręcznych. Ta funkcja jest szczególnie przydatna, gdy tekst i kształty są obecne na kanwie, a użytkownicy chcą wykonywać różne akcje dla każdego. Należy rozważyć dodanie funkcji zaznaczenia (takich jak lasso lub inne narzędzie do zaznaczania geometrycznego), które umożliwiają użytkownikom wybranie, co zostanie wysłane do interfejsu API.  

### <a name="app-initiated-api-calls"></a>Wywołania interfejsu API inicjowane przez aplikację

Po upływie limitu czasu można również wywołać interfejs API aparatu pisma odczłowego. Wysyłając bieżące pociągnięcia odręczne do interfejsu API rutynowo, można przechowywać wyniki rozpoznawania, ponieważ są one tworzone przy jednoczesnym poprawie czasu odpowiedzi interfejsu API. Na przykład można wysłać wiersz odręcznego tekstu do interfejsu API po wykryciu, że użytkownik go ukończył. 

Posiadanie wyników rozpoznawania z wyprzedzeniem daje informacje o cechach pociągnięć odręcznych, ponieważ odnoszą się one do siebie nawzajem. Na przykład, które obrysy są grupowane w celu utworzenia tego samego wyrazu, wiersza, listy, akapitu lub kształtu. Te informacje mogą poprawić funkcje wyboru pisma odręcznego aplikacji, na przykład można wybrać grupy pociągnięć naraz.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integracja interfejsu API aparatu rozpoznawania pisma odurzacza z systemem Windows Ink

[Funkcja Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) udostępnia narzędzia i technologie umożliwiające cyfrowe odmładowywanie na różnych urządzeniach. Platformę Windows Ink można połączyć z interfejsem API rozpoznawania pisma odręcznego, aby tworzyć aplikacje wyświetlane i interpretujące cyfrowe pociągnięcia odręczne.

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API rozpoznawania pisma odręcznego?](../overview.md)
* [Odwołanie do interfejsu API REST aparatu pisma odczłowieczowego](https://go.microsoft.com/fwlink/?linkid=2089907)

* Zacznij wysyłać cyfrowe dane obrysu atramentu, korzystając z:
    * [C #](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [Javascript](../quickstarts/javascript.md)
