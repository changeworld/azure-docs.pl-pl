---
title: Przejrzyj wypowiedzi punktu końcowego na korzystanie z aktywnej nauki w Language Understanding (LUIS) — Azure | Dokumentacja firmy Microsoft
description: Użyj funkcji aktywne uczenie o nazwie "Przejrzyj wypowiedzi punktu końcowego" Aby poprawić wydajność prognozy szybciej.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 4144db7474f20f464d14722431d5d4a2546a8c5a
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949853"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Włącz aktywne uczenie, przeglądając wypowiedzi punktu końcowego
Aktywna nauka jest jednym z trzech strategii w celu zwiększenia dokładności prognozy i najłatwiejsza do wdrożenia. 

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka
Aktywna nauka jest procesem dwuetapowym. Po pierwsze LUIS wybiera wypowiedzi otrzymywanych w punkcie końcowym aplikacji, które wymagają weryfikacji. Drugi etap odbywa się przez właściciela aplikacji lub Współautor, który można zweryfikować wybranego wypowiedzi dla [Przejrzyj](luis-how-to-review-endoint-utt.md), w tym poprawny intencji i wszystkie jednostki w ramach intencji. Po zapoznaniu się z wypowiedzi, szkolenie i ponownie Opublikuj aplikację. 

## <a name="which-utterances-are-on-the-review-list"></a>Które wypowiedzi znajdują się na liście przeglądu
LUIS wypowiedzi są dodawane do listy przeglądu, jeśli u góry, wyzwalania intencji znajduje się niskim wynikiem lub zbyt Zamknij oceny najważniejszych dwóch intencji. 

## <a name="single-pool-for-utterances-per-app"></a>Jednej puli dla wypowiedzi na aplikację
**Przejrzyj wypowiedzi punktu końcowego** listy nie zmienia się zależnie od wersji. Brak pojedynczej puli wypowiedzi, aby zobaczyć, niezależnie od tego, która wersja wypowiedź aktywnie edytowania lub która wersja aplikacji została opublikowana w punkcie końcowym. 

## <a name="where-are-the-utterances-from"></a>Gdzie są wypowiedzi z
Punkt końcowy wypowiedzi są pobierane z zapytań przez użytkownika końcowego na punkt końcowy HTTP w aplikacji. Jeśli Twoja aplikacja nie został opublikowany lub nie otrzymał trafień jeszcze, nie masz wypowiedzi, aby przejrzeć. Odebranie nie trafienia punktu końcowego dla określonych przeznaczenie lub jednostki nie masz wypowiedzi do przeglądania, zawierające je. 

## <a name="schedule-review-periodically"></a>Okresowo przeglądu harmonogramu
Przeglądania sugerowanych wypowiedzi nie musi odbywać się codziennie, ale powinna być częścią Twojego regularnej konserwacji usługi LUIS. 

## <a name="delete-review-items-programmatically"></a>Usuń elementy przeglądu programowe
Jeśli aplikacja jest duża, można przejrzeć niektóre wypowiedzi i programowo usunąć rest z listy. Jest to realizowane przez pierwszy [wprowadzenie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) listy i następnie [usuwanie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) wypowiedzi według identyfikatora.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Przejrzyj](luis-how-to-review-endoint-utt.md) wypowiedzi punktu końcowego
