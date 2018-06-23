---
title: Słownik - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Słownik
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348689"
---
# <a name="glossary"></a>Słownik

## <a name="qna-maker-service"></a>Usługa Maker — strona główna
Usługa Maker — strona główna jest warunek wstępny, aby rozpocząć korzystanie z Maker — strona główna. Zakup warstwy Maker — strona główna skonfigurowanie zasobów w Twojej subskrypcji platformy Azure do tworzenia i zarządzania bazy wiedzy. Wszystkie konta użytkowników Maker — strona główna można utworzyć wiele usług Maker — strona główna w swoich subskrypcji platformy Azure.

## <a name="knowledge-base"></a>Baza wiedzy
Baza wiedzy jest repozytorium pytania i odpowiedzi utworzone, przechowywane i używane za pośrednictwem Maker — strona główna. Każda warstwa Maker — strona główna może służyć do wielu baz wiedzy.

## <a name="endpoint"></a>Endpoint
Punkt końcowy HTTP opartego na interfejsie REST, obsługi zawartości wiedzy, które mogą być zintegrowane z aplikacji, często bot rozmów. 

## <a name="test-knowledge-base"></a>Testowanie bazy wiedzy
Baza wiedzy ma dwa stany — Test opublikowane. Baza wiedzy testu jest wersji, który jest edytowany, zapisane i przetestowane dla dokładność i kompletność odpowiedzi. Zmiany wprowadzone w bazie wiedzy testu nie wpływają na użytkownika końcowego bot Twojej aplikacji/rozmów.

## <a name="published-knowledge-base"></a>Opublikowane wiedzy
Baza wiedzy ma dwa stany - testu i opublikowaną.  Opublikowane baza wiedzy jest wersji, która jest używana w rozmów bot/aplikacji. Akcja publikowania bazy wiedzy zapisuje zawartość w bazie wiedzy testu w opublikowaną wersję elementu bazy wiedzy knowledge base. Ponieważ opublikowanych wiedzy jest wersja aplikacja wykorzystuje za pośrednictwem punktu końcowego, ostrożność aby upewnić się, czy zawartość jest poprawna i dobrze przetestowane.

## <a name="query"></a>Zapytanie
Kwerenda użytkownika pozostaje z pytaniem, użytkownikowi końcowemu lub tester bazy wiedzy knowledge base. Zapytanie jest często w formacie języka naturalnego lub kilka słów kluczowych, które reprezentują pytanie.

## <a name="response"></a>Odpowiedź
Odpowiedź jest odpowiedzi pobierane z bazy wiedzy knowledge base, oparte na najlepsze dopasowanie dla zapytania danego użytkownika.

## <a name="confidence-score"></a>Współczynnik ufności
Wynik zaufania odpowiedzi jest wartością liczbową pomiędzy 0 a 100, 100 jest dopasowanie dokładne zapytanie między kwerenda użytkownika i zapytania w bazie wiedzy, który obsłużył odpowiedzi jest poprawny, odpowiednie odpowiedzi na zapytanie danego użytkownika. Odpowiedzi, zazwyczaj są uporządkowane według wynik zaufanie i z większą liczbą punktów zaufania jest udostępniany jako domyślny.
