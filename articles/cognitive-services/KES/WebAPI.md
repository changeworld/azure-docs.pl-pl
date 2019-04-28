---
title: Interfejs API sieci Web — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API sieci web, aby utworzyć środowisko rozbudowanych semantycznej wyszukiwania w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814430"
---
# <a name="web-api-interface"></a>Interfejs API sieci Web

Model kompilowaną przez Knowledge Exploration Service można obsługiwane i dostępne za pośrednictwem zestawu interfejsów API sieci web.  Interfejsy API może być hostowana na komputerze lokalnym przy użyciu [ `host_service` ](CommandLine.md#host_service-command) polecenie lub można wdrożyć usługi platformy Azure w chmurze przy użyciu [ `deploy_service` ](CommandLine.md#deploy_service-command) polecenia.  Obu tych technik ujawnić następujące punkty końcowe interfejsu API:

* [*Interpretowanie* ](interpretMethod.md) — interpretuje ciąg zapytania w języku naturalnym. Zwraca interpretacje z adnotacjami, aby zapewnić zaawansowaną obsługę automatycznego uzupełniania w polu wyszukiwania, które przewiduje wpisywany tekst.
* [*Ocena* ](evaluateMethod.md) — Evaluates i zwraca wyniki wyrażeń zapytań ze strukturą.
* [*calchistogram* ](calchistogramMethod.md) — oblicza histogram wartości atrybutu dla obiektów zwróconych przez wyrażeń zapytań ze strukturą.

Razem te metody interfejsu API pozwala na tworzenie zaawansowanych semantycznego funkcji wyszukiwania.  Podany ciąg zapytania w języku naturalnym, *interpretacji* metoda zapewnia adnotacjami wersje zapytanie wejściowe za pomocą wyrażeń zapytań ze strukturą, oparte na danych bazowych Gramatyka języka i indeksem.  *Oceny* metoda oblicza wyrażenie zapytań ze strukturą i zwraca obiekty indeksu pasujące do wyświetlania.  *Calchistogram* metoda oblicza dystrybucje wartość atrybutu umożliwiające filtrowanie i Uszczegółowienie.

**Przykład**

W domenie akademickich publikacje, jeśli użytkownik wpisze ciąg "s ukryte" *interpretacji* metoda może dostarczyć zestaw rangi interpretacji, sugerujące, że użytkownik może wyszukiwanie — słowo kluczowe "ukryte analizy semantycznej" Tytuł "analiza struktury ukryte", lub innych wyrażeń, począwszy od "ukryte s".  Na podstawie tych informacji można udostępnić użytkownikowi wskazówki szybko prowadzące do żądanych wyników wyszukiwania.

Dla tej domeny *oceny* metoda może służyć do pobierania zestawu pasujących publikacji z indeksu akademickich oraz *calchistogram* metody mogą być używane do obliczania dystrybucji atrybutu wartości dla publikacji dopasowania, które mogą służyć do dalszego filtrowania i zawęzić wyniki wyszukiwania.

Aby poprawić czytelność przykładów, wywołania interfejsu API REST zawierają znaki (takie jak spacje), które nie zostały zakodowane w adresie URL. We własnym kodzie należy stosować odpowiednie kodowania adresów URL.
