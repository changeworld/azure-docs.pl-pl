---
title: Przejrzyj zniesławiających punkt końcowy do użycia w języku opis (LUIS) - Azure active learning | Dokumentacja firmy Microsoft
description: Funkcja active learning o nazwie "Przejrzyj zniesławiających punktu końcowego" Aby poprawić wydajność prognoz szybciej.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356436"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Włącz active uczenia, przeglądając zniesławiających punktu końcowego
Active learning jest jednym z trzech strategii w celu zwiększenia dokładności prognozy i najłatwiejsza do zaimplementowania. 

## <a name="what-is-active-learning"></a>Co to jest aktywny learning
Active learning jest procesem dwuetapowym. Po pierwsze LUIS wybiera zniesławiających, który odbierze w punkcie końcowym aplikacji, które wymagają sprawdzania poprawności. Drugim krokiem jest wykonywane przez właściciela aplikacji lub współpracownika można sprawdzić poprawności wybranego zniesławiających dla [Przejrzyj](label-suggested-utterances.md), w tym poprawne zamiar oraz wszystkie elementy w ramach celem. Po przejrzeniu zniesławiających, szkolenie i ponownie opublikować aplikację. 

## <a name="which-utterances-are-on-the-review-list"></a>Zniesławiających, które znajdują się na liście przeglądu
LUIS dodaje zniesławiających do listy przeglądu u góry, wyzwalania zamiar znajduje się niskim wynikiem lub wyniki top dwie opcje są zbyt Zamknij. 

## <a name="where-are-the-utterances-from"></a>Gdzie są zniesławiających z
Punkt końcowy zniesławiających są pobierane z zapytań przez użytkownika końcowego na punkt końcowy HTTP w aplikacji. Jeśli aplikacja nie jest opublikowana lub nie otrzymał trafień jeszcze, nie masz żadnych zniesławiających, aby przejrzeć. Odebranie nie trafienia punktu końcowego dla określonych zamiar lub jednostki nie masz zniesławiających, aby przejrzeć zawierające je. 

## <a name="schedule-review-periodically"></a>Okresowo przeglądu harmonogramu
Przeglądanie sugerowane zniesławiających nie trzeba przeprowadzić każdego dnia, ale powinien być częścią Twojego regularnej konserwacji LUIS. 

## <a name="delete-review-items-programmatically"></a>Usuń elementy przeglądu programowo
Jeśli aplikacja jest duży, można przejrzeć niektórych zniesławiających i programistycznie usunąć pozostałe z listy. W tym celu należy najpierw [pobierania](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) listy, a następnie [usuwanie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) zniesławiających według identyfikatora.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Przejrzyj](Label-Suggested-Utterances.md) zniesławiających punktu końcowego
