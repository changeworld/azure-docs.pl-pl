---
title: Przegląd punktu końcowego wypowiedzi Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Funkcja przełomowe LUIS jest koncepcji aktywne uczenie. Gdy usługi LUIS ma punkt końcowy zapytań, aktywne uczenie zwiększa jakość wyników, wypowiedzi wybiera jest nieznany. Jeśli etykiety te wypowiedzi uczenie i publikowanie, a następnie LUIS identyfikuje wypowiedzi dokładniej.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 3ec791d534fb73a9d88f2dcdb81e445d6c26ab69
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057362"
---
# <a name="review-endpoint-utterances"></a>Przeglądanie wypowiedzi punktu końcowego

Funkcja przełomowe usługi Luis jest [koncepcji](luis-concept-review-endpoint-utterances.md) z aktywnej nauki. Po usługi LUIS ma punkt końcowy zapytań, LUIS jest używany aktywne uczenie się poprawić jakość wyników. W procesie aktywne uczenie LUIS bada wypowiedzi punktu końcowego i wybiera wypowiedzi jest nieznany. Jeśli etykiety te wypowiedzi uczenie i publikowanie, a następnie LUIS identyfikuje wypowiedzi dokładniej. 

## <a name="filter-utterances"></a>Filtruj wypowiedzi
1. Otwórz aplikację (na przykład TravelAgent), wybierając jego nazwę na **Moje aplikacje** stronie, a następnie wybierz **kompilacji** na górnym pasku.

2. W obszarze **lepsza wydajność aplikacji**, wybierz opcję **Przejrzyj wypowiedzi punktu końcowego**.

3. Na **Przejrzyj wypowiedzi punktu końcowego** wybierz opcję w **lista filtrów przeznaczenie lub jednostki** pola tekstowego. Tej listy rozwijanej zawiera wszystkie opcje w obszarze **INTENCJI** a wszystkimi jednostkami w obszarze **jednostek**.

    ![Filtr wypowiedzi](./media/label-suggested-utterances/filter.png)

4. Wybierz kategorię (intencje lub jednostek) z listy rozwijanej, a następnie przejrzyj wypowiedzi.

    ![Wypowiedzi intencji](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etykieta jednostki
Usługa LUIS zastępuje tokeny jednostki (słowa) z nazwami jednostek wyróżnione na niebiesko. Jeśli wypowiedź ma bez etykiety jednostek, etykiety je w wypowiedź. 

1. Wybierz na wyrazów w wypowiedź. 

2. Wybierz jednostkę z listy.

    ![Etykieta jednostki](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Wyrównaj do pojedynczego wypowiedź

Każdy wypowiedź ma sugerowane przeznaczenie, wyświetlana w **wyrównane intencji** kolumny. 

1. Jeśli zgadzasz się z tym sugestii, wybierz znacznik wyboru.

    ![Zachowaj wyrównany intencji](./media/label-suggested-utterances/align-intent-check.png)

2. Jeśli nie zgadzasz się z sugestiami z wyrównany opcji listy rozwijanej wybierz prawidłowe opcje, a następnie wybierz znacznik wyboru na prawo od intencji wyrównane. 

    ![Wyrównaj do intencji](./media/label-suggested-utterances/align-intent.png)

3. Po wybraniu znacznik wyboru wypowiedź zostanie usunięty z listy. 

## <a name="align-several-utterances"></a>Wyrównaj do kilku wypowiedzi

Aby wyrównać kilka wypowiedzi, pole wyboru, aby wypowiedzi po lewej stronie, a następnie wybierz na **Dodaj wybrane** przycisku. 

![Wyrównaj do kilku](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Sprawdź wyrównany intencji
Możesz sprawdzić wypowiedź był wyrównany z zamiarem poprawne, przechodząc do **intencji** stronie, wybierz nazwę intencji i przeglądanie wypowiedzi. Wypowiedź z **Przejrzyj wypowiedzi punktu końcowego** znajduje się na liście.

## <a name="delete-utterance"></a>Usuń wypowiedź
Każdy wypowiedź może być usunięty z listy przeglądu. Po usunięciu nie pojawi się na liście ponownie. Ta zasada obowiązuje, nawet wtedy, gdy użytkownik musi wprowadzić ten sam wypowiedź z punktu końcowego. 

Jeśli nie wiesz, jeśli należy usunąć wypowiedź, albo przenieść je do konwersji, None lub utworzyć nowe opcje takie jak "różne" i Przenieś wypowiedź tym przeznaczeniem. 

## <a name="delete-several-utterances"></a>Usuń kilka wypowiedzi
Aby usunąć kilka wypowiedzi, zaznacz każdy element, a następnie wybierz Kosz z prawej strony **Dodaj wybrane** przycisku.

![Usunięcie kilku](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>Kolejne kroki

Aby sprawdzić, jak wydajność poprawia po etykiety sugerowane wypowiedzi, możesz uzyskać dostęp w konsoli testów wybierając **Test** w górnym panelu. Aby uzyskać instrukcje na temat testowania aplikacji przy użyciu konsoli testów, zobacz [szkolenie i testowanie aplikacji](luis-interactive-test.md).
