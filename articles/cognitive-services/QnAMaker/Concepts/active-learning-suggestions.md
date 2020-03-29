---
title: Aktywne propozycje edukacyjne - QnA Maker
description: Aktywne sugestie dotyczące uczenia się pozwalają poprawić jakość bazy wiedzy, proponując alternatywne pytania, na podstawie zgłoszeń użytkowników, do pary pytań i odpowiedzi.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: af4f6b399bfd537b38ea741d03e59371ee81e588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053155"
---
# <a name="active-learning-suggestions"></a>Aktywne sugestie dotyczące uczenia się

Funkcja _Aktywnych sugestii edukacyjnych_ pozwala poprawić jakość bazy wiedzy, proponując alternatywne pytania, na podstawie zgłoszeń użytkowników, do pary pytań i odpowiedzi. Przeglądasz te sugestie, dodając je do istniejących pytań lub odrzucając je.

Baza wiedzy nie zmienia się automatycznie. Aby zmiany weszły w życie, musisz zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.

## <a name="what-is-active-learning"></a>Co to jest aktywne uczenie się?

QnA Maker uczy się nowych odmian pytań z niejawnymi i jawnymi opiniami.

* [Niejawna opinia](#how-qna-makers-implicit-feedback-works) — ranker rozumie, gdy pytanie użytkownika ma wiele odpowiedzi z wynikami, które są bardzo blisko i traktuje to jako opinię. Nie musisz nic robić, aby tak się stało.
* [Jawna opinia](#how-you-give-explicit-feedback-with-the-train-api) — gdy wiele odpowiedzi z niewielkimi różnicami w wynikach są zwracane z bazy wiedzy, aplikacja kliencka pyta użytkownika, które pytanie jest poprawne pytanie. Wyraźne opinie użytkownika są wysyłane do programu QnA Maker za pomocą [interfejsu API pociągu.](../How-to/improve-knowledge-base.md#train-api)

Obie metody zapewniają ranker z podobnych zapytań, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak działa aktywne uczenie się

Aktywne uczenie się jest wyzwalane na podstawie wyników kilku najlepszych odpowiedzi zwróconych przez QnA Maker. Jeśli różnice wyników między zestawami QnA, które pasują do kwerendy, mieszczą się w małym zakresie, kwerenda jest uważana za możliwą sugestię (jako pytanie alternatywne) dla każdej z możliwych par QnA. Po zaakceptowaniu sugerowanego pytania dla konkretnej pary QnA, zostanie ono odrzucone dla innych par. Musisz pamiętać, aby zapisać i trenować, po zaakceptowaniu sugestii.

Aktywne uczenie daje najlepsze możliwe sugestie w przypadkach, gdy punkty końcowe są coraz rozsądną ilość i różnorodność zapytań użycia. Gdy 5 lub więcej podobnych zapytań jest klastrowanych, co 30 minut program QnA Maker sugeruje projektantowi bazy wiedzy pytania oparte na użytkowniku, aby je zaakceptował lub odrzucił. Wszystkie sugestie są grupowane razem według podobieństwa, a najlepsze sugestie dotyczące pytań alternatywnych są wyświetlane na podstawie częstotliwości określonych zapytań przez użytkowników końcowych.

Gdy pytania zostaną zasugerowane w portalu QnA Maker, musisz przejrzeć i zaakceptować lub odrzucić te sugestie. Nie ma interfejsu API do zarządzania sugestiami.

## <a name="turn-on-active-learning"></a>Włączanie aktywnego uczenia się

Domyślnie aktywne uczenie się jest **wyłączone**.
Aby korzystać z aktywnego uczenia się:
* Musisz [włączyć aktywne uczenie się,](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) aby QnA Maker zbierał alternatywne pytania dla twojej bazy wiedzy.
* Aby wyświetlić sugerowane pytania alternatywne, [użyj opcji widoku](../How-To/improve-knowledge-base.md#view-suggested-questions) na stronie Edycja.

## <a name="how-qna-makers-implicit-feedback-works"></a>Jak działają niejawne opinie QnA Maker

Niejawna sprzężenie zwrotne QnA Maker używa algorytmu do określenia bliskości wyników, a następnie wprowadza aktywne sugestie dotyczące uczenia się. Algorytm do określania bliskości nie jest proste obliczenie. Zakresy w poniższym przykładzie nie są przeznaczone do ustalenia, ale powinny być używane jako przewodnik, aby zrozumieć wpływ tylko algorytmu.

Gdy wynik pytania jest bardzo pewny siebie, na przykład 80%, zakres wyników, które są uważane za aktywne uczenie się są szerokie, w przybliżeniu w granicach 10%. Wraz ze spadkiem wyniku zaufania, na przykład o 40%, zmniejsza się również zakres wyników, w przybliżeniu w granicach 4%.

W następującej odpowiedzi JSON z kwerendy do QnA Maker generateAnswer, wyniki dla A, B i C są blisko i będą traktowane jako sugestie.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker nie wie, która odpowiedź jest najlepszą odpowiedzią. Skorzystaj z listy sugestii portalu QnA Maker, aby wybrać najlepszą odpowiedź i ponownie trenować.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Jak dajesz wyraźne opinie za pomocą interfejsu API train

QnA Maker potrzebuje wyraźnej opinii na temat odpowiedzi, która z odpowiedzi była najlepszą odpowiedzią. Jak najlepsza odpowiedź jest określana zależy od Ciebie i może zawierać:

* Opinie użytkowników, wybierając jedną z odpowiedzi.
* Logika biznesowa, na przykład określanie dopuszczalnego zakresu wyników.
* Połączenie opinii użytkowników i logiki biznesowej.

Użyj [interfejsu API pociągu,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) aby wysłać poprawną odpowiedź do programu QnA Maker, po wybraniu go przez użytkownika.

## <a name="next-step"></a>Następny krok

> [!div class="nextstepaction"]
> [Zapytanie o bazę wiedzy](query-knowledge-base.md)