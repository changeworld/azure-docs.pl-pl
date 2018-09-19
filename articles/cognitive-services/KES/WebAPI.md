---
title: Interfejs API sieci Web — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API sieci web, aby utworzyć środowisko rozbudowanych semantycznej wyszukiwania w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 5be39e8dce6aeeef32d20273c56650620d6fe986
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122029"
---
# <a name="web-api-interface"></a>Interfejs API sieci Web

Model kompilowaną przez Knowledge Exploration Service można obsługiwane i dostępne za pośrednictwem zestawu interfejsów API sieci web.  Interfejsy API może być hostowana na komputerze lokalnym przy użyciu [ `host_service` ](CommandLine.md#host_service-command) polecenie lub można wdrożyć usługi platformy Azure w chmurze przy użyciu [ `deploy_service` ](CommandLine.md#deploy_service-command) polecenia.  Obu tych technik ujawnić następujące punkty końcowe interfejsu API:

* [*Interpretowanie* ](interpretMethod.md) — interpretuje ciąg zapytania w języku naturalnym. Zwraca interpretacje z adnotacjami, aby zapewnić rozbudowane środowisko automatycznego uzupełniania w polu wyszukiwania, które przewiduje wpisywany tekst.
* [*Ocena* ](evaluateMethod.md) — Evaluates i zwraca wyniki wyrażeń zapytań ze strukturą.
* [*calchistogram* ](calchistogramMethod.md) — oblicza histogram wartości atrybutu dla obiektów zwróconych przez wyrażeń zapytań ze strukturą.

Razem te metody interfejsu API pozwala na tworzenie zaawansowanych semantycznego funkcji wyszukiwania.  Podany ciąg zapytania w języku naturalnym, *interpretacji* metoda zapewnia adnotacjami wersje zapytanie wejściowe za pomocą wyrażeń zapytań ze strukturą, oparte na danych bazowych Gramatyka języka i indeksem.  *Oceny* metoda oblicza wyrażenie zapytań ze strukturą i zwraca obiekty indeksu pasujące do wyświetlania.  *Calchistogram* metoda oblicza dystrybucje wartość atrybutu umożliwiające filtrowanie i Uszczegółowienie.

**Przykład**

W domenie akademickich publikacje, jeśli użytkownik wpisze ciąg "s ukryte" *interpretacji* metoda może dostarczyć zestaw rangi interpretacji, sugerujące, że użytkownik może wyszukiwanie — słowo kluczowe "ukryte analizy semantycznej" Tytuł "analiza struktury ukryte", lub innych wyrażeń, począwszy od "ukryte s".  Te informacje mogą służyć do szybko Przewodnik po użytkownik żądanych wyników wyszukiwania.

Dla tej domeny *oceny* metoda może służyć do pobierania zestawu pasujących publikacji z indeksu akademickich oraz *calchistogram* metody mogą być używane do obliczania dystrybucji atrybutu wartości dla publikacji dopasowania, które mogą służyć do dalszego filtrowania i zawęzić wyniki wyszukiwania.

Należy pamiętać, aby poprawić czytelność przykłady, wywołań interfejsu API REST zawiera znaki (takie jak spacje), które nie zostały zakodowane w adresie URL. Kod, należy zastosować odpowiednie kodowania adresu URL.
