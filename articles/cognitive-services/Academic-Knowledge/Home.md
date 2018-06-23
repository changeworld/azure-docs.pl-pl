---
title: Academic Knowledge API uczelni Microsoft Graph | Dokumentacja firmy Microsoft
description: Academic Knowledge API umożliwia zinterpretować kwerend użytkowników i pobierania informacji sformatowanego z Academic wykresu w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: mvorvoreanu
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: e241f9a87cd58b62eafd754bd3cb4283aa0a1e92
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347513"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Witamy w Academic Knowledge API. Dzięki tej usłudze możesz interpretować zapytania użytkowników w celach akademickich i pobierać rozbudowane informacje z wykresu Microsoft Academic Graph (MAG). W bazie wiedzy MAGNETYCZNY jest wykres heterogenicznych jednostki skali sieci web składają się z jednostkami, którymi modelowania aktywności publikacjach naukowych: pole badań, autora instytucji, papieru, miejsce i zdarzeń. 

Dane MAGNETYCZNY zaminowanie z indeksem Bing sieci web, a także wewnętrznych wiedzy z usługi Bing. W wyniku w toku Bing indeksowania, tego interfejsu API będzie zawierać nowych informacji z sieci Web po odnajdywania i indeksowania przez Bing. W oparciu o ten zestaw danych, Academic Knowledge API umożliwia opartej na wiedzy, interaktywnego okna dialogowego bezproblemowo łączącą reaktywne wyszukiwania z sugestii aktywnego środowiska, wyniki wyszukiwania wykres papieru sformatowanego badań i podziału histogram wartości atrybutów zestawu dokumenty i powiązanych jednostek.

Aby uzyskać więcej informacji na wykresie Academic firmy Microsoft, zobacz [ http://aka.ms/academicgraph ](http://aka.ms/academicgraph).

Academic Knowledge API został przeniesiony z kognitywnych podglądu usług kognitywnych Labs usług. Nowe strony głównej projektu jest: [ https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge ](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Istniejący klucz interfejsu API będą nadal działać, dopóki 24 maja 2018. Po tej dacie Wygeneruj nowy klucz interfejsu API. Należy pamiętać, tym płatną Podgląd nie będą już dostępne po wygaśnięciu istniejącego klucza. Skontaktuj się z działu, jeśli warstwa bezpłatna interfejsu API nie jest wystarczające do własnych celów. 

## <a name="features"></a>Funkcje
Academic Knowledge API składa się z czterech powiązane punkty końcowe REST:  
  1. **Interpretowanie** — interpretacji języka naturalnego ciągu zapytania użytkownika. Zwraca interpretacje z adnotacjami, aby zapewnić rozbudowane środowisko automatycznego uzupełniania w polu wyszukiwania, które przewiduje wpisywany tekst.  
  2. **Ocena** — oblicza wyrażenie zapytania i zwraca wyniki jednostki Academic Knowledge.  
  3. **calchistogram** — oblicza histogram rozkład wartości atrybutów dla academic jednostek zwróconych przez wyrażenie zapytania, np. rozkład cytaty roku dla danego autora.  
  4. **Wykres wyszukiwania** — umożliwia wyszukiwanie podanym wykresem wzorca i zwraca wyniki dopasowane jednostki.

Razem te metody interfejsu API pozwala na tworzenie rozbudowanych wyszukiwanie semantycznego. Podany ciąg zapytania użytkownika **interpretowania** metoda zapewnia adnotacjami wersji zapytania i wyrażenia zapytania strukturalnych, podczas wykonywania opcjonalnie kwerenda użytkownika oparta na semantykę podstawowej uczelni dane. Na przykład, jeśli użytkownik wpisze ciąg *ukryty s*, **interpretowania** — metoda zapewnia zbiór uporządkowanej według rangi interpretacje, sugerujące, że użytkownik może wyszukiwanie pole badania  *ukryty semantyczny analizy*, papieru *analizy struktury ukryty*, lub inne wyrażenia jednostki, począwszy od *ukryty s*. Te informacje mogą służyć do szybko Podręcznik użytkownika żądanych wyników wyszukiwania.

**Oceny** metody można użyć do pobierania zestawu zgodnych jednostek papieru z academic knowledge base i **calchistogram** metody mogą być używane do obliczania rozkład wartości atrybutów dla zestawu jednostek papieru, które mogą być używane, aby dokładniej przefiltrować wyniki wyszukiwania.        

**Wyszukiwania wykres** metoda ma dwa tryby: *json* i *lambda*. *Json* trybie można wykonywać wykres wzorca dopasowania zgodnie z wzorcami wykres określonym przez obiekt JSON. *Lambda* trybie można wykonywać obliczenia po stronie serwera podczas traversals wykres zgodnie z wyrażenia lambda określone przez użytkownika.

## <a name="getting-started"></a>Wprowadzenie 
Zobacz tematy podrzędne po lewej stronie szczegółowa dokumentacja.  Należy pamiętać, aby zwiększyć czytelność przykłady, wywołania interfejsu API REST zawiera znaki (na przykład spacji), które nie zostały zakodowane w adresie URL.  Kod należy zastosować odpowiednie kodowania adresu URL.
