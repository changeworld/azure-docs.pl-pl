---
title: Omówienie przewodników Szybki start dotyczących przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: W tych przewodnikach Szybki start przeprowadzisz analizę obrazu, utworzysz miniaturę oraz wyodrębnisz tekst drukowany i odręczny za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8fd27fc0038e91531fbf05942336ce7d6fc34e37
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310019"
---
# <a name="quickstart-summary"></a>Szybki start: Podsumowanie

Te przewodniki Szybki start zawierają informacje oraz przykładowe kody, które umożliwią Ci szybkie rozpoczęcie pracy z usługą przetwarzania obrazów.

W tych przykładach wykonywane są bezpośrednie wywołania HTTP do interfejsu API przetwarzania obrazów. Zobacz sekcję *Zestawy SDK — przewodniki Szybki start*, aby zapoznać się z przykładowym kodem korzystającym z bibliotek klienta przetwarzania obrazów, które oferują wygodne metody opakowywania wywołań HTTP.

Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

Usługa przetwarzania obrazów umożliwia wykonywanie następujących zadań:

* Analizowanie zdalnego obrazu
* Analizowanie lokalnego obrazu
* Wykrywanie osobistości i charakterystycznych elementów krajobrazu (modele domeny)
* Inteligentne generowanie miniatur
* Wykrywanie i wyodrębnianie tekstu drukowanego (OCR) z obrazu
* Wykrywanie i wyodrębnianie tekstu odręcznego z obrazu

Kod w każdym przykładzie jest podobny. Jednak w każdym przykładzie przedstawiono inne funkcje przetwarzania obrazów oraz inne techniki wymiany danych z usługą, na przykład:

* Funkcja _Generowanie miniatur_ zwraca obraz w postaci tablicy bajtów w treści odpowiedzi.
* Funkcja _Analizowanie obrazu lokalnego_ wymaga dołączenia do żądania obrazu w formie tablicy bajtów.
* Funkcja _Wyodrębnianie tekstu odręcznego_ wymaga dwóch wywołań w celu pobrania tekstu.

## <a name="summary"></a>Podsumowanie

| Szybki start               | Parametry żądania                          | Odpowiedź          |
| ------------------------ | ------------------------------------------- | ----------------  |
| Analizowanie zdalnego obrazu   | visualFeatures=Categories,Description,Color | Ciąg JSON       |
| Analizowanie lokalnego obrazu    | data=image_data (tablica bajtów)                | Ciąg JSON       |
| Wykrywanie osobistości       | model=celebrities                           | Ciąg JSON       |
| Generowanie miniatur     | width=200&height=150&smartCropping=true     | tablica bajtów        |
| Wyodrębnianie tekstu drukowanego     | language=unk&detectOrientation=true         | Ciąg JSON       |
| Wyodrębnianie tekstu odręcznego | handwriting=true                            | Adres URL, ciąg JSON  |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego.

> [!div class="nextstepaction"]
> [Poznaj interfejsy API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
