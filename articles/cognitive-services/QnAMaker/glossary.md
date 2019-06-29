---
title: Słownik — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker ma wiele nowych warunków z usługi machine learning i języka naturalnego warunki przetwarzania, a także specyficzne dla usługi. Ta lista może pomóc w zapoznaniu się z tymi warunkami.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c6b7e4ca2acc1416e19fc8b70f57aed82afa4e1e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446545"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>Słownik usługi QnA Maker wiedzy i usługi

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
Współczynnik ufności odpowiedzi jest wartością liczbową pomiędzy 0 a 100, 100, które są zgodne dokładnie zapytania zapytanie użytkownika z zapytania w bazie wiedzy, które obsługiwane odpowiedzi jest prawidłowe, odpowiednie odpowiedzi na kwerendę danego użytkownika. Odpowiedzi zwykle są uporządkowane według współczynnik ufności i z wyższym współczynnik ufności jest podawany jako [Domyślna odpowiedź](concepts/confidence-score.md#change-default-answer).
