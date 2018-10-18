---
title: Co to jest interfejs Academic Knowledge API?
titlesuffix: Azure Cognitive Services
description: Interfejs Academic Knowledge API pozwala interpretować zapytania użytkowników i pobierać rozbudowane informacje z grafu Academic Graph.
services: cognitive-services
author: mvorvoreanu
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: overview
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: d08cd7124b232e50365e72753eba97c6309f401c
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901214"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Zapraszamy do korzystania z interfejsu Academic Knowledge API. Dzięki tej usłudze możesz interpretować zapytania użytkowników w celach naukowych i pobierać rozbudowane informacje z grafu Microsoft Academic Graph (MAG). Baza wiedzy MAG to heterogeniczny graf jednostek w skali Internetu, obejmujący jednostki modelujące działania związane z badaniami naukowymi: dziedzina badań, autor, instytucja, dokument, miejsce i wydarzenie. 

Dane grafu MAG są wyodrębniane z internetowego indeksu usługi Bing, a także z wewnętrznej bazy wiedzy z usługi Bing. Ten interfejs API zawiera aktualne informacje z Internetu dzięki ciągłemu indeksowaniu i odnajdywaniu informacji przez usługę Bing. Na podstawie tego zestawu danych interfejs Academic Knowledge API umożliwia prowadzenie interakcyjnego dialogu opartego na wiedzy, który bezproblemowo łączy reaktywne wyszukiwanie z proaktywną obsługą sugestii, bogatymi wynikami przeszukiwania grafu dokumentów dotyczących badań oraz rozkładem histogramu wartości atrybutów dla zestawu dokumentów i powiązanych jednostek.

Aby uzyskać więcej informacji o grafie Microsoft Academic Graph, zobacz [http://aka.ms/academicgraph](http://aka.ms/academicgraph).

Interfejs Academic Knowledge API został przeniesiony z usługi Cognitive Services w wersji zapoznawczej do usługi Cognitive Services Labs. Nowa strona główna projektu to [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Istniejący klucz interfejsu API będzie nadal działać do 24 maja 2018 roku. Po tej dacie wygeneruj nowy klucz interfejsu API. Pamiętaj, że płatna wersja zapoznawcza nie będzie już dostępna po wygaśnięciu istniejącego klucza. Skontaktuj się z naszym zespołem, jeśli bezpłatna warstwa interfejsu API jest niewystarczająca dla Twoich celów. 

## <a name="features"></a>Funkcje
Interfejs Academic Knowledge API obejmuje cztery powiązane punkty końcowe REST:  
  1. **interpret** — Interpretuje ciąg zapytania użytkownika w języku naturalnym. Zwraca interpretacje z adnotacjami, aby zapewnić zaawansowaną obsługę automatycznego uzupełniania w polu wyszukiwania, które przewiduje wpisywany tekst.  
  2. **evaluate** — Ocenia wyrażenie zapytania i zwraca wyniki z jednostkami Academic Knowledge.  
  3. **calchistogram** — Oblicza histogram rozkładu wartości atrybutów dla obiektów naukowych zwracanych przez wyrażenie zapytania, na przykład rozkład cytowań według roku dla danego autora.  
  4. **graph search** — Wyszukuje podany wzorzec grafu i zwraca dopasowane wyniki.

Wspólnie te metody interfejsu API umożliwiają tworzenie zaawansowanych funkcji wyszukiwania semantycznego. Na podstawie ciągu zapytania użytkownika metoda **interpret** udostępnia wersję tego zapytania z adnotacjami oraz strukturalne wyrażenie zapytania, opcjonalnie uzupełniając zapytanie użytkownika na podstawie semantyki bazowych danych akademickich. Jeśli na przykład użytkownik wpisze ciąg *latent s*, metoda **interpret** może udostępnić zestaw pozycjonowanych interpretacji, sugerując, że użytkownik mógł szukać dziedziny badań *latent semantic analysis*, dokumentu *latent structure analysis* lub innych wyrażeń jednostek zaczynających się od *latent s*. Na podstawie tych informacji można udostępnić użytkownikowi wskazówki szybko prowadzące do żądanych wyników wyszukiwania.

Metoda **evaluate** umożliwia pobieranie zestawu zgodnych jednostek dokumentów z bazy wiedzy naukowej, a metoda **calchistogram** umożliwia obliczanie rozkładu wartości atrybutów dla zestawu jednostek dokumentów, co pozwala dokładniej filtrować wyniki wyszukiwania.        

Metoda **graph search** ma dwa tryby: *json* i *lambda*. Tryb *json* umożliwia dopasowywanie wzorca grafu zgodnie z wzorcami grafu określonymi przez obiekt JSON. Tryb *lambda* umożliwia wykonywanie obliczeń po stronie serwera podczas przechodzenia przez graf zgodnie z wyrażeniami lambda określonymi przez użytkownika.

## <a name="getting-started"></a>Wprowadzenie 
Zobacz podtematy z lewej strony, aby zapoznać się ze szczegółową dokumentacją.  Aby poprawić czytelność przykładów, wywołania interfejsu API REST zawierają znaki (takie jak spacje), które nie zostały zakodowane w adresie URL.  We własnym kodzie należy stosować odpowiednie kodowania adresów URL.
