---
title: Co to jest interfejs Academic Knowledge API?
titlesuffix: Azure Cognitive Services
description: Interfejs Academic Knowledge API pozwala interpretować zapytania użytkowników i pobierać rozbudowane informacje z grafu Academic Graph.
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 305d73ba4b1b955b731e34ee5da0b1dced6acddc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704969"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Zapraszamy do korzystania z interfejsu Academic Knowledge API. Dzięki tej usłudze możesz interpretować zapytania użytkowników w celach naukowych i pobierać rozbudowane informacje z grafu Microsoft Academic Graph (MAG). Baza wiedzy MAG to heterogeniczny graf jednostek w skali Internetu, obejmujący jednostki modelujące działania związane z badaniami naukowymi: dziedzina badań, autor, instytucja, dokument, miejsce i wydarzenie. 

Dane grafu MAG są wyodrębniane z internetowego indeksu usługi Bing, a także z wewnętrznej bazy wiedzy z usługi Bing. Ten interfejs API zawiera aktualne informacje z Internetu dzięki ciągłemu indeksowaniu i odnajdywaniu informacji przez usługę Bing. Na podstawie tego zestawu danych interfejs Academic Knowledge API umożliwia prowadzenie interakcyjnego dialogu opartego na wiedzy, który bezproblemowo łączy reaktywne wyszukiwanie z proaktywną obsługą sugestii, bogatymi wynikami przeszukiwania grafu dokumentów dotyczących badań oraz rozkładem histogramu wartości atrybutów dla zestawu dokumentów i powiązanych jednostek.

Aby uzyskać więcej informacji o grafie Microsoft Academic Graph, zobacz [https://aka.ms/academicgraph](https://aka.ms/academicgraph).

Interfejs Academic Knowledge API został przeniesiony z usługi Cognitive Services w wersji zapoznawczej do usługi Cognitive Services Labs. Nowa strona główna projektu to [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Istniejący klucz interfejsu API będzie nadal działać do 24 maja 2018 roku. Po tej dacie wygeneruj nowy klucz interfejsu API. Pamiętaj, że płatna wersja zapoznawcza nie będzie już dostępna po wygaśnięciu istniejącego klucza. Skontaktuj się z naszym zespołem, jeśli bezpłatna warstwa interfejsu API jest niewystarczająca dla Twoich celów. 

## <a name="features"></a>Funkcje
Interfejs Academic Knowledge API obejmuje cztery powiązane punkty końcowe REST:  
  1. **interpret** — Interpretuje ciąg zapytania użytkownika w języku naturalnym. Zwraca interpretacje z adnotacjami, aby zapewnić zaawansowaną obsługę automatycznego uzupełniania w polu wyszukiwania, które przewiduje wpisywany tekst.  
  2. **evaluate** — Ocenia wyrażenie zapytania i zwraca wyniki z jednostkami Academic Knowledge.  
  3. **calchistogram** — Oblicza histogram rozkładu wartości atrybutów dla obiektów naukowych zwracanych przez wyrażenie zapytania, na przykład rozkład cytowań według roku dla danego autora.  
  
Wspólnie te metody interfejsu API umożliwiają tworzenie zaawansowanych funkcji wyszukiwania semantycznego. Na podstawie ciągu zapytania użytkownika metoda **interpret** udostępnia wersję tego zapytania z adnotacjami oraz strukturalne wyrażenie zapytania, opcjonalnie uzupełniając zapytanie użytkownika na podstawie semantyki bazowych danych akademickich. Jeśli na przykład użytkownik wpisze ciąg *latent s*, metoda **interpret** może udostępnić zestaw pozycjonowanych interpretacji, sugerując, że użytkownik mógł szukać dziedziny badań *latent semantic analysis*, dokumentu *latent structure analysis* lub innych wyrażeń jednostek zaczynających się od *latent s*. Na podstawie tych informacji można udostępnić użytkownikowi wskazówki szybko prowadzące do żądanych wyników wyszukiwania.

Metoda **evaluate** umożliwia pobieranie zestawu zgodnych jednostek dokumentów z bazy wiedzy naukowej, a metoda **calchistogram** umożliwia obliczanie rozkładu wartości atrybutów dla zestawu jednostek dokumentów, co pozwala dokładniej filtrować wyniki wyszukiwania.        

## <a name="getting-started"></a>Wprowadzenie 
Zobacz podtematy z lewej strony, aby zapoznać się ze szczegółową dokumentacją.  Aby poprawić czytelność przykładów, wywołania interfejsu API REST zawierają znaki (takie jak spacje), które nie zostały zakodowane w adresie URL.  We własnym kodzie należy stosować odpowiednie kodowania adresów URL.
