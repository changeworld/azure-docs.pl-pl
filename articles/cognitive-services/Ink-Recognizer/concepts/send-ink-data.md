---
title: Wyślij dane pisma odręcznego do interfejsu API rozpoznawania pisma odręcznego
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej o dla różnych aplikacji podczas wywoływania analizatora API pisma odręcznego
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5a212c7332d085c15baabef8650572162c47903d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027096"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Wyślij dane pisma odręcznego do interfejsu API rozpoznawania pisma odręcznego 

Cyfrowy pisma odręcznego odnosi się do technologii, która umożliwia cyfrowe reprezentujących dane wejściowe, takie jak pisma ręcznego i rysunki. Zazwyczaj jest to osiągane przy użyciu dyskretyzatora, która przechwytuje przemieszczania danych wejściowych urządzeń, takich jak pióro. Jak urządzenia nadal włączyć zaawansowane cyfrowego pisma odręcznego, sztucznej inteligencji i uczenia maszynowego umożliwia rozpoznawanie napisane kształty i tekst w dowolnym kontekście. Interfejs API rozpoznawania pisma odręcznego umożliwia wysyłanie pociągnięć odręcznych i uzyskać szczegółowe informacje na ich temat. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>A interfejs API rozpoznawania pisma odręcznego Usługi optyczne rozpoznawanie znaków

Interfejs API rozpoznawania pisma odręcznego nie używa optyczne Recognition(OCR) znaków. Optyczne rozpoznawanie znaków usług przetwarzania danych pikseli z obrazów w celu zapewnienia rozpoznawania pisma ręcznego i tekst. Jest to czasem nazywane rozpoznawania w trybie offline. Zamiast tego interfejs API rozpoznawania pisma odręcznego wymaga cyfrowy atrament obrysu dane, które są przechwytywane w przypadku użycia urządzenia wejściowego. Przetwarzanie danych cyfrowy atrament w ten sposób można utworzyć bardziej precyzyjne wyniki rozpoznawania w porównaniu do usługi optyczne rozpoznawanie znaków. 

## <a name="sending-ink-data"></a>Wysyłanie danych pisma odręcznego

Interfejs API rozpoznawania pisma odręcznego wymaga współrzędne X i Y, które reprezentują pociągnięć odręcznych utworzone przez urządzenie wejściowe, od momentu styka się kiedy zostało zniesione powierzchnię wykrywania. Punkty każdego obrysu musi być ciągiem o wartości rozdzielane przecinkami i być zapisany w formacie JSON, jak w poniższym przykładzie. Ponadto każdy pociągnięcia odręczne musi mieć unikatowy identyfikator w każdym żądaniu. Jeśli identyfikator jest powtarzany w tym samym żądaniu, interfejs API zwróci błąd. Aby uzyskać najbardziej dokładne wyniki rozpoznawania ma co najmniej ośmiu cyfr po punkcie dziesiętnym. Punkt początkowy (0,0) kanwy zakłada się, że lewym górnym rogu obszaru roboczego pisma odręcznego.

> [!NOTE]
> Poniższy przykład nie jest prawidłowym plikiem JSON. Możesz znaleźć pełny żądania JSON rozpoznawania pisma odręcznego na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Odpowiedź rozpoznawania pisma odręcznego

Interfejs API rozpoznawania pisma odręcznego zwraca odpowiedź analizy dotyczące obiektów, który rozpoznał z pismo odręczne. Odpowiedź zawiera jednostki rozpoznawania, które opisywania relacji między różnych odręczne. Na przykład kreślone tworzyć kształty distinct, oddzielną będzie znajdować się w różnych jednostkach. Każda jednostka zawiera szczegółowe informacje na temat jego pociągnięcia odręczne, w tym obiektu rozpoznane, współrzędnych i inne atrybuty rysowania.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Kształty rozpoznana przez interfejs API rozpoznawania pisma odręcznego

Interfejs API rozpoznawania pisma odręcznego można zidentyfikować najczęściej używanych kształty w notatek. Poniższy obraz przedstawia niektóre podstawowe przykłady. Aby uzyskać pełną listę kształty i inne pisma odręcznego zawartości został rozpoznany przez interfejs API, zobacz [artykuł dokumentacji interfejsu API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Na liście kształtów rozpoznana przez interfejs API rozpoznawania pisma odręcznego](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Zaleca się wywołanie wzorców

Zgodnie z aplikacji, można wywołać interfejsu API REST rozpoznawania pisma odręcznego w różnych wzorców. 

### <a name="user-initiated-api-calls"></a>Użytkownik zainicjował wywołań interfejsu API

Jeśli tworzysz aplikację, która akceptuje dane wejściowe użytkownika (na przykład Uwaga pozyskiwanie lub adnotacji aplikacji), warto nadać im kontroli nad kiedy i które pisma odręcznego jest wysyłany do interfejsu API rozpoznawania pisma odręcznego. Ta funkcja jest szczególnie przydatne, gdy tekstowe i kształty są obecne na kanwie, a użytkownicy chcą wykonywać różne akcje dla każdego. Rozważ dodanie funkcje wybór (na przykład lasso lub inne narzędzia zaznaczania geometrycznych), które umożliwiają użytkownikom na wybór, co jest wysyłany do interfejsu API.  

### <a name="app-initiated-api-calls"></a>Aplikacja zainicjowane wywołań interfejsu API

Może również mieć wywołanie aplikacji interfejsu API rozpoznawania pisma odręcznego, po upływie limitu czasu. Jako są tworzone przy jednoczesnym zwiększeniu czasu odpowiedzi interfejsu API, wysyłając rutynowo bieżącego pociągnięć odręcznych do interfejsu API, można przechowywać wyniki rozpoznawania. Na przykład użytkownik może wysyłać wiersz tekstu odręcznego z interfejsem API po wykryciu użytkownika została zakończona go. 

Z wyprzedzeniem o wyniki rozpoznawania zapewnia informacje o charakterystykę pociągnięcia odręczne odnoszących się do siebie nawzajem. Na przykład linie, które są grupowane w celu utworzenia tego samego programu word, wiersz, listy, akapitu lub kształtu. Te informacje mogą poprawić funkcji wybór pisma odręcznego swojej aplikacji dzięki możliwości wybrać grupy pociągnięć jednocześnie, na przykład.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integracja aparatu rozpoznawania pisma odręcznego interfejsu API z usługą Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) oferuje narzędzia i technologie, które pozwalają cyfrowego pisma odręcznego na szereg różnych urządzeń. Platforma Windows Ink można połączyć z interfejsu API rozpoznawania pisma odręcznego do tworzenia aplikacji, które są wyświetlane i interpretować pociągnięć odręcznych cyfrowych.

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest interfejs API rozpoznawania pisma odręcznego?](../overview.md)
* [Dokumentacja interfejsu API REST rozpoznawania pisma odręcznego](https://go.microsoft.com/fwlink/?linkid=2089907)

* Rozpocznij wysyłanie danych obrysu cyfrowy atrament za pomocą:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)