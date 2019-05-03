---
title: Jak działa Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer korzysta z usługi machine learning dowiedzieć się, jakie działania do użycia w kontekście. Każdej pętli learning ma modelu, który jest uczony wyłącznie na dane, które zostało wysłane do niego za pośrednictwem rangę i wywołuje nagrody. Każdy pętla nauki jest całkowicie niezależna od siebie nawzajem.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027131"
---
# <a name="how-personalizer-works"></a>Jak działa Personalizer

Personalizer korzysta z usługi machine learning dowiedzieć się, jakie działania do użycia w kontekście. Każdej pętli learning ma modelu, który jest uczony wyłącznie na dane, które zostały wysłane do niego za pośrednictwem **ranga** i **nagradzania** wywołania. Każdy pętla nauki jest całkowicie niezależna od siebie nawzajem. Tworzenie pętli dla każdej części lub zachowanie aplikacji, który chcesz spersonalizować uczenia.

Dla każdej pętli **wywołania interfejsu API ranga z** na podstawie bieżącego kontekstu, za pomocą:

* Lista dostępnych akcji: zawartości elementów, z którego można wybrać akcję najważniejsze.
* Lista [funkcji kontekstu](concepts-features.md): kontekstowe odpowiednie dane, takie jak użytkownika, zawartości i kontekstu.

**Ranga** API zdecyduje się użyć:

* _Wykorzystać_: Bieżący model można określić najlepsze działanie na podstawie ostatnich danych.
* _Explore_: Wybierz inną akcję zamiast najważniejszych działań.

**Nagradzania** interfejsu API:

* Zbiera dane do nauczenia modelu, rejestrując funkcji i nagrody wyniki każdego wywołania rangi.
* Używa tych danych do aktualizacji modelu na podstawie ustawień określonych w _zasad Learning_.

## <a name="architecture"></a>Architektura

Na poniższej ilustracji przedstawiono przepływ architektury wywołania wywołania rangi i nagrody:

![tekst alternatywny](./media/how-personalizer-works/personalization-how-it-works.png "sposób działania personalizacji")

1. Personalizer używa wewnętrznego modeli sztucznej Inteligencji, aby określić pozycję akcji.
1. Usługa Określa, czy należy wykorzystać w bieżącym modelu lub Przejrzyj nowe opcje dla modelu.  
1. Wynik klasyfikacji są wysyłane do Centrum zdarzeń.
1. Gdy Personalizer otrzymuje nagrody, nagrody są wysyłane do Centrum zdarzeń. 
1. Ranga i osób trzecich są powiązane.
1. Model sztucznej Inteligencji jest aktualizowana, na podstawie wyników korelacji.
1. Aparat wnioskowania jest aktualizowany w nowym modelu. 

## <a name="research-behind-personalizer"></a>Badań za Personalizer

Personalizer opiera się na najnowocześniejsze nauki i badań w zakresie [uczenia przez wzmacnianie](concepts-reinforcement-learning.md) tym dokumenty, badania i bieżące obszary eksploracji w Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Nauka pętli**: Pętla learning można utworzyć dla każdej części aplikacji, który może być korzystne personalizacji. Jeśli masz więcej niż jednym środowisku spersonalizować, Utwórz pętli dla każdego. 

* **Akcje**: Akcje są elementy zawartości, takie jak produkty lub promocji do wyboru. Personalizer wybiera najważniejsze akcji do przedstawienia użytkownikom, nazywanej _nagradzaj akcji_, za pomocą interfejsu API rangi. Każda akcja może mieć funkcje przekazana z żądaniem rangi.

* **Kontekst**: Aby zapewnić bardziej precyzyjne rangę, zawierają informacje kontekstu, na przykład:
    * Użytkownika.
    * Urządzenie, które znajdują się na. 
    * Bieżąca godzina.
    * Inne dane dotyczące bieżącej sytuacji.
    * Historyczne dane dotyczące użytkownika lub kontekstu.

    Konkretnej aplikacji może być informacje w innym kontekście. 

* **[Funkcje](concepts-features.md)**: Jednostka informacje kontekstu użytkownika lub zawartości elementu.

* **Nagroda**: Miara jak użytkownika wysłanych jako odpowiedzi do interfejsu API ranga zwracane Akcja jako wynik od 0 do 1. Wartość od 0 do 1 jest ustawiana przez własną logikę biznesową, w oparciu o sposób wyboru pomogła osiągnąć cele biznesowe personalizacji. 

* **Eksploracja**: Usługa Personalizer rozważa, gdy zamiast zwracać najlepsze działanie, wybiera ona różne akcje dla użytkownika. Usługa Personalizer pozwala uniknąć odejściem od tego stanu stagnację i dostosowują się do zachowania użytkowników stałe, eksplorując szczegóły. 

* **Czas trwania eksperymentu**: Ilość czasu oczekiwania usługi Personalizer wynagrodzenia, już od momentu wywołania rangi wystąpiło dla tego zdarzenia.

* **Nieaktywne zdarzenia**: Nieaktywne zdarzenia jest jednym gdy wywołujesz rangę, ale nie masz pewności, że użytkownik nigdy nie zobaczy wynik, ze względu na decyzje dotyczące aplikacji klienta. Nieaktywne zdarzenia umożliwiają tworzenie i przechowywanie wyników personalizacji, a następnie zdecydować odrzucić je później, bez wywierania wpływu na model uczenia maszynowego.

* **Model**: Personalizer model przechwytuje wszystkie dane, które uzyskaliśmy informacje dotyczące zachowania użytkowników, pobieranie danych szkoleniowych z kombinacja argumentów, wysyłanych do rangi i nagrody wywołań i z zachowaniem szkolenia, określane przez polityki uczenia. 

## <a name="next-steps"></a>Kolejne kroki

Zrozumienie [którym używasz Personalizer](where-can-you-use-personalizer.md).