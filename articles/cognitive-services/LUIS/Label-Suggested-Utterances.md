---
title: Etykieta sugerowane zniesławiających z LUIS | Dokumentacja firmy Microsoft
description: Opis języka (LUIS) umożliwia etykiety sugerowane zniesławiających i zwiększanie wyniku uczenia maszynowego active pomóc.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: v-geberr
ms.openlocfilehash: 91a2fe738743d359677392bfb79aac885702b440
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356428"
---
# <a name="review-endpoint-utterances"></a>Przejrzyj zniesławiających punktu końcowego

Funkcja przełomowe LUIS jest [koncepcji](luis-concept-review-endpoint-utterances.md) active nauczania. Raz LUIS Twojego zapytania punktu końcowego, LUIS używa active uczenia aby poprawić jakość wyników. W procesie active learning LUIS sprawdza wszystkie zniesławiających punktu końcowego i wybiera zniesławiających, które jest nieznany. Jeśli etykieta te zniesławiających, nauczenia i opublikować, a następnie LUIS identyfikuje zniesławiających dokładniej. 

## <a name="filter-utterances"></a>Zniesławiających filtru
1. Otwórz aplikację (na przykład TravelAgent) przez wybranie jego nazwę na **Moje aplikacje** strony, a następnie wybierz **kompilacji** w górnym pasku.

2. W obszarze **poprawić wydajność aplikacji**, wybierz pozycję **Przejrzyj zniesławiających punktu końcowego**.

    ![Przejrzyj zniesławiających](./media/label-suggested-utterances/review.png)

3. Na **Przejrzyj zniesławiających punktu końcowego** wybierz opcję w **lista filtrów zamiar lub jednostki** pola tekstowego. Tej listy rozwijanej obejmuje wszystkie opcje w obszarze **INTENCJE** a wszystkimi jednostkami w obszarze **jednostek**.

    ![Filtr zniesławiających](./media/label-suggested-utterances/filter.png)

4. Wybierz kategorię (intencje lub jednostek) z listy rozwijanej i przejrzyj zniesławiających.

    ![Zniesławiających konwersji](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etykieta jednostek
LUIS zastępuje nazwy jednostek zaznaczone na niebiesko jednostki tokeny (słowa). Jeśli utterance ma bez etykiety jednostek, etykiety je w utterance. 

1. Wybierz na wyrazów w utterance. 

2. Wybierz jednostkę z listy.

    ![Etykieta jednostki](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Dopasuj pojedynczy utterance

Każdy utterance ma zamiar sugerowane, wyświetlane w **wyrównane zamiar** kolumny. 

1. Jeśli zgadzasz się z tym sugestii, wybierz na znacznik wyboru.

    ![Zachowaj opcje wyrównany](./media/label-suggested-utterances/align-intent-check.png)

2. Jeśli zgadzasz się z sugestii, z wyrównany konwersji listy rozwijanej wybierz prawidłowe opcje, a następnie wybierz polecenie Znacznik wyboru, aby celem wyrównany z prawej strony. 

    ![Dopasuj zamiar](./media/label-suggested-utterances/align-intent.png)

3. Po wybraniu na znacznik wyboru, utterance zostanie usunięty z listy. 

## <a name="align-several-utterances"></a>Dopasuj kilka zniesławiających

Aby wyrównać kilka zniesławiających, pole wyboru, aby z lewej strony zniesławiających, a następnie wybierz na **Dodaj wybrane** przycisku. 

![Dopasuj kilka](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Sprawdź opcje wyrównany
Możesz sprawdzić utterance został wyrównany z zamiarem poprawny, przechodząc do **intencje** strony, wybierz nazwę konwersji i recenzowania zniesławiających. Utterance z **Przejrzyj zniesławiających punktu końcowego** znajduje się na liście.

## <a name="delete-utterance"></a>Usuń utterance
Każdy utterance mogą zostać usunięte z listy przeglądu. Po usunięciu, nie zostanie wyświetlony na liście ponownie. Dotyczy to nawet wtedy, gdy użytkownik wprowadza tego samego utterance z punktu końcowego. 

Jeśli nie wiesz, czy należy usunąć utterance albo przenieś go do konwersji, None lub utworzyć nowe opcje, takie jak "inne" i przenieść utterance do tego celem. 

## <a name="delete-several-utterances"></a>Usuń kilka zniesławiających
Aby usunąć kilka zniesławiających, zaznacz każdy element, a następnie wybierz na Kosza z prawej strony **Dodaj wybrane** przycisku.

![Usunięcie kilku](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>Kolejne kroki

Aby sprawdzić, jak wydajności zwiększa po etykiecie sugerowane zniesławiających, można dostęp do konsoli testu wybierając **Test** w górnym panelu. Aby uzyskać instrukcje dotyczące testowania aplikacji przy użyciu konsoli testu, zobacz [pociągu i testowanie aplikacji](Train-Test.md).
