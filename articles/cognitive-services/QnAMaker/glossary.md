---
title: Słownik — QnA Maker
titleSuffix: Azure Cognitive Services
description: Słownik
services: cognitive-services
author: tulasim88
manager: cjgronlund
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 65f2aea6ca7b92a1f7d58c607138bbd3388ad30a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954572"
---
# <a name="glossary"></a>Słownik

## <a name="qna-maker-service"></a>Usługa QnA Maker
Usługa QnA Maker to wymaganie wstępne, aby rozpocząć korzystanie z usługi QnA Maker. Zakup warstwy usługi QnA Maker konfiguruje zasoby w subskrypcji platformy Azure do tworzenia i zarządzania bazy wiedzy. Każde konto użytkownika usługi QnA Maker można utworzyć wiele usługi QnA Maker w ramach swojej subskrypcji platformy Azure.

## <a name="knowledge-base"></a>Baza wiedzy
Baza wiedzy jest repozytorium pytania i odpowiedzi na utworzone, przechowywane i używane za pośrednictwem usługi QnA Maker. Każda warstwa usługi QnA Maker może służyć do wielu baz wiedzy.

## <a name="endpoint"></a>Endpoint
Punkt końcowy oparty na protokole REST protokołu HTTP, obsługi zawartości bazy wiedzy knowledge base, które można zintegrować aplikację często czatbot. 

## <a name="test-knowledge-base"></a>Testowanie bazy wiedzy
Baza wiedzy ma dwa stany - Test opublikowane. Baza wiedzy testu jest wersji, która jest edytowany, zapisane i przetestowane dla dokładności i kompletności odpowiedzi. Zmiany wprowadzone w bazie wiedzy knowledge base test nie wpływają na użytkownika końcowego bota aplikacji/rozmowy.

## <a name="published-knowledge-base"></a>Opublikowane bazy wiedzy
Baza wiedzy ma dwa stany - Test i opublikowaną.  Opublikowane baza wiedzy jest wersji, który jest używany podczas rozmowy aplikację/bota. Akcja publikowanie bazy wiedzy umieszcza zawartość bazy wiedzy testu w opublikowaną wersję elementu w bazie wiedzy knowledge base. Ponieważ opublikowanych wiedzy wersję, która korzysta z aplikacji za pośrednictwem punktu końcowego, należy z rozwagą aby upewnić się, że zawartość jest poprawna i dobrze przetestowanych.

## <a name="query"></a>Zapytanie
Kwerenda użytkownika to kwestia z pytaniem, użytkownikowi końcowemu lub tester bazy wiedzy knowledge base. Zapytanie jest często w postaci języka naturalnego lub kilka słów kluczowych, które reprezentują pytanie.

## <a name="response"></a>Odpowiedź
Odpowiedź jest odpowiedzi, pobierane z bazy wiedzy, w oparciu o najlepsze dopasowanie dla zapytania danego użytkownika.

## <a name="confidence-score"></a>Współczynnik ufności
Współczynnik ufności odpowiedzi jest wartością liczbową pomiędzy 0 a 100, 100, które są zgodne dokładnie zapytania zapytanie użytkownika z zapytania w bazie wiedzy, które obsługiwane odpowiedzi jest prawidłowe, odpowiednie odpowiedzi na kwerendę danego użytkownika. Odpowiedzi zwykle są uporządkowane według współczynnik ufności i z wyższym współczynnik ufności jest obsługiwany jako domyślny.
