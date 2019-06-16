---
title: Przejrzyj wypowiedzi użytkowników
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aktywna nauka przechwytuje kwerendy punktu końcowego i wybiera wypowiedzi punktu końcowego użytkownika jest nieznany. Zapoznaj się z tych wypowiedzi wybierz intencji i oznaczanie jednostek, dla tych wypowiedzi odczytu świata. Zaakceptuj te zmiany w swoje wypowiedzi przykładu, a następnie uczenie i publikowanie. Usługa LUIS następnie identyfikuje wypowiedzi dokładniej.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195168"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Jak przeglądać wypowiedzi punktu końcowego w portalu usługi LUIS aktywne uczenie

[Aktywna nauka](luis-concept-review-endpoint-utterances.md) przechwytuje kwerendy punktu końcowego i wybiera wypowiedzi punktu końcowego użytkownika jest nieznany. Zapoznaj się z tych wypowiedzi wybierz intencji i oznaczanie jednostek, dla tych wypowiedzi odczytu świata. Zaakceptuj te zmiany w swoje wypowiedzi przykładu, a następnie uczenie i publikowanie. Usługa LUIS następnie identyfikuje wypowiedzi dokładniej.


## <a name="enable-active-learning"></a>Włącz aktywne uczenie

Aby włączyć aktywne uczenie, należy zalogować się zapytań użytkowników. Jest to realizowane przez ustawienie [kwerendy punktu końcowego](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) z `log=true` querystring parametru i wartości.

## <a name="disable-active-learning"></a>Wyłącz aktywne uczenie

Aby wyłączyć aktywne uczenie, nie rejestruj zapytań użytkowników. Jest to realizowane przez ustawienie [kwerendy punktu końcowego](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) z `log=false` querystring parametru i wartości.

## <a name="filter-utterances"></a>Filtruj wypowiedzi

1. Otwórz aplikację (na przykład TravelAgent), wybierając jego nazwę na **Moje aplikacje** stronie, a następnie wybierz **kompilacji** na górnym pasku.

1. W obszarze **lepsza wydajność aplikacji**, wybierz opcję **Przejrzyj wypowiedzi punktu końcowego**.

1. Na **Przejrzyj wypowiedzi punktu końcowego** wybierz opcję w **lista filtrów przeznaczenie lub jednostki** pola tekstowego. Tej listy rozwijanej zawiera wszystkie opcje w obszarze **INTENCJI** a wszystkimi jednostkami w obszarze **jednostek**.

    ![Filtr wypowiedzi](./media/label-suggested-utterances/filter.png)

1. Wybierz kategorię (intencje lub jednostek) z listy rozwijanej, a następnie przejrzyj wypowiedzi.

    ![Wypowiedzi intencji](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etykieta jednostki
Usługa LUIS zastępuje tokeny jednostki (słowa) z nazwami jednostek wyróżnione na niebiesko. Jeśli wypowiedź ma bez etykiety jednostek, etykiety je w wypowiedź. 

1. Wybierz na wyrazów w wypowiedź. 

1. Wybierz jednostkę z listy.

    ![Etykieta jednostki](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Wyrównaj do pojedynczego wypowiedź

Każdy wypowiedź ma sugerowane przeznaczenie, wyświetlana w **wyrównane intencji** kolumny. 

1. Jeśli zgadzasz się z tym sugestii, wybierz znacznik wyboru.

    ![Zachowaj wyrównany intencji](./media/label-suggested-utterances/align-intent-check.png)

1. Jeśli nie zgadzasz się z sugestiami z wyrównany opcji listy rozwijanej wybierz prawidłowe opcje, a następnie wybierz znacznik wyboru na prawo od intencji wyrównane. 

    ![Wyrównaj do intencji](./media/label-suggested-utterances/align-intent.png)

1. Po wybraniu znacznik wyboru wypowiedź zostanie usunięty z listy. 

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
