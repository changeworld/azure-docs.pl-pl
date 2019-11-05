---
title: Przeglądanie użytkownika wyrażenia długości — LUIS
titleSuffix: Azure Cognitive Services
description: Usługa Active Learning przechwytuje zapytania punktów końcowych i wybiera punkt końcowy użytkownika wyrażenia długości, że nie ma pewności. Te wyrażenia długości są przeglądane w celu wybrania zamiar i oznaczenie jednostek dla tych wyrażenia długości do odczytu. Zaakceptuj te zmiany w przykładzie wyrażenia długości, a następnie Wyszkol i Opublikuj. LUIS następnie identyfikuje wyrażenia długości dokładniej.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499067"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Jak przejrzeć program Endpoint wyrażenia długości w portalu LUIS na potrzeby aktywnego uczenia

[Usługa Active Learning](luis-concept-review-endpoint-utterances.md) przechwytuje zapytania punktów końcowych i wybiera punkt końcowy użytkownika wyrażenia długości, że nie ma pewności. Te wyrażenia długości są przeglądane w celu wybrania zamiar i oznaczenie jednostek dla tych wyrażenia długości do odczytu. Zaakceptuj te zmiany w przykładzie wyrażenia długości, a następnie Wyszkol i Opublikuj. LUIS następnie identyfikuje wyrażenia długości dokładniej.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Włącz aktywną naukę

Aby włączyć funkcję aktywnego uczenia, rejestruj zapytania użytkownika. Jest to realizowane przez ustawienie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) z `log=true` ciągu QueryString i wartością.

## <a name="disable-active-learning"></a>Wyłącz aktywną naukę

Aby wyłączyć aktywną naukę, nie Rejestruj zapytań użytkownika. Jest to realizowane przez ustawienie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) z `log=false` ciągu QueryString i wartością.

## <a name="filter-utterances"></a>Filtr wyrażenia długości

1. Otwórz aplikację (na przykład TravelAgent), wybierając jej nazwę na stronie **Moje aplikacje** , a następnie wybierz opcję **Kompiluj** na górnym pasku.

1. W obszarze **Zwiększ wydajność aplikacji**wybierz pozycję **Przejrzyj punkt końcowy wyrażenia długości**.

1. Na stronie **Przegląd wyrażenia długości punktu końcowego** zaznacz w polu tekstowym **Filtruj według zamiaru lub jednostki** . Ta lista rozwijana zawiera wszystkie intencje w obszarze **intencje** i wszystkie jednostki w **jednostkach**.

    ![Filtr wyrażenia długości](./media/label-suggested-utterances/filter.png)

1. Wybierz kategorię (intencje lub jednostki) na liście rozwijanej i przejrzyj wyrażenia długości.

    ![Wyrażenia długości zamiaru](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etykiety jednostek
LUIS zastępuje tokeny jednostki (słowa) nazwami jednostek wyróżnionymi na niebiesko. Jeśli wypowiedź ma nieoznaczone jednostki, oznacz je w wypowiedź. 

1. Wybierz wyrazy w wypowiedź. 

1. Wybierz jednostkę z listy.

    ![Etykieta — jednostka](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Wyrównaj pojedyncze wypowiedź

Każdy wypowiedź ma sugerowane zamierzone w kolumnie **cel** . 

1. Jeśli zgadzasz się z tą sugestią, zaznacz pole wyboru.

    ![Zachowaj wyrównany cel](./media/label-suggested-utterances/align-intent-check.png)

1. Jeśli nie zgadzasz się z sugestią, wybierz odpowiednią opcję z listy rozwijanej założeń, a następnie zaznacz pole wyboru na prawo od dopasowanego zamiaru. 

    ![Dopasuj cel](./media/label-suggested-utterances/align-intent.png)

1. Po wybraniu znacznika wyboru wypowiedź zostanie usunięty z listy. 

## <a name="align-several-utterances"></a>Wyrównaj kilka wyrażenia długości

Aby wyrównać kilka wyrażenia długości, zaznacz pole wyboru po lewej stronie wyrażenia długości, a następnie wybierz przycisk **Dodaj wybrane** . 

![Wyrównaj kilka](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Weryfikuj wyrównany cel

Możesz sprawdzić, czy wypowiedź został wyrównany z poprawnym zamiarem, przechodząc do strony **intencje** , wybierz nazwę celu i przeglądając wyrażenia długości. Wypowiedź z **punktu końcowego przeglądu wyrażenia długości** znajduje się na liście.

## <a name="delete-utterance"></a>Usuń wypowiedź

Każdy wypowiedź można usunąć z listy przegląd. Po usunięciu nie zostanie on wyświetlony na liście ponownie. Jest to prawdziwe, nawet jeśli użytkownik wprowadzi ten sam wypowiedź z punktu końcowego. 

Jeśli nie masz pewności, czy należy usunąć wypowiedź, przenieś je do zamiaru brak lub Utwórz nowe zamierzenie, takie jak "różne" i Przenieś wypowiedź do tego celu. 

## <a name="delete-several-utterances"></a>Usuń kilka wyrażenia długości

Aby usunąć kilka wyrażenia długości, zaznacz każdy element i wybierz pozycję w koszu po prawej stronie przycisku **Dodaj wybrane** .

![Usuń kilka](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Następne kroki

Aby sprawdzić, jak wydajność zwiększa się po oznaczeniu sugerowanych wyrażenia długości, możesz uzyskać dostęp do konsoli testowej, wybierając **test** w górnym panelu. Aby uzyskać instrukcje dotyczące testowania aplikacji za pomocą konsoli testowej, zobacz [uczenie i testowanie aplikacji](luis-interactive-test.md).
