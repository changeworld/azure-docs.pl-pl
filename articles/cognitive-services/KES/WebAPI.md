---
title: Interfejs API sieci Web interfejsu w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Interfejs API sieci web umożliwia utworzenie rozbudowane, semantycznego wyszukiwanie w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 16c5680eb4f249a5d37e6b90eea92cfff7090eef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346981"
---
# <a name="web-api-interface"></a>Interfejs API sieci Web
Model kompilowaną przez usługę eksploracji wiedzy można obsługiwana i dostępne za pośrednictwem zestaw interfejsów API sieci web.  Interfejsy API może znajdować się na komputerze lokalnym przy użyciu [ `host_service` ](CommandLine.md#host_service-command) polecenie lub została wdrożona do usługi chmury Azure przy użyciu [ `deploy_service` ](CommandLine.md#deploy_service-command) polecenia.  Obie techniki uwidacznia następujące punkty końcowe interfejsu API:
* [*Interpretowanie* ](interpretMethod.md) — interpretacji języka naturalnego ciągu zapytania. Zwraca interpretacje z adnotacjami, aby zapewnić rozbudowane środowisko automatycznego uzupełniania w polu wyszukiwania, które przewiduje wpisywany tekst.
* [*Ocena* ](evaluateMethod.md) — Evaluates i zwraca dane wyjściowe w wyrażeniu kwerendy strukturalnych.
* [*calchistogram* ](calchistogramMethod.md) — oblicza histogramu wartości atrybutów obiektów zwrócona przez wyrażenie structured query.

Razem te metody interfejsu API pozwala na tworzenie rozbudowanych wyszukiwanie semantycznego.  Podany ciąg zapytania języka naturalnego *interpretowania* metoda zapewnia adnotacjami wersje zapytanie wejściowe za pomocą wyrażeń structured query, oparte na danych gramatyki i indeksu.  *Oceny* metoda oblicza wyrażenie structured query i zwraca zgodnych obiektów indeksu do wyświetlenia.  *Calchistogram* metody oblicza dystrybucje wartość atrybutu aby umożliwić filtrowanie i uściślenia.

**Przykład**

W domenie academic publikacji, jeśli użytkownik wpisze ciąg "ukryty s" *interpretowania* — metoda zapewnia zbiór uporządkowanej według rangi interpretacje, sugerujące, że użytkownik może wyszukiwanie — słowo kluczowe "ukryty analizy semantyki" Tytuł "Analiza ukryty struktury", lub inne wyrażenia, począwszy od "ukryty s".  Te informacje mogą służyć do szybko Podręcznik użytkownika żądanych wyników wyszukiwania.

Dla tej domeny *oceny* metody można użyć do pobierania zestawu zgodnych publikacje z academic indeksu i *calchistogram* metody mogą być używane do obliczania dystrybucji atrybutu wartości zgodne publikacji, które mogą służyć do dalszego filtru i dostosować wyniki wyszukiwania.

Należy pamiętać, aby zwiększyć czytelność przykłady, wywołania interfejsu API REST zawiera znaki (na przykład spacji), które nie zostały zakodowane w adresie URL. Kod należy zastosować odpowiednie kodowania adresu URL.
