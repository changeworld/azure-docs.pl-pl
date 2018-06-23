---
title: Jak używać alternatywnych danych wejściowych z konwersacji uczeń - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć alternatywnych danych wejściowych z uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2214436b193932e5b3b80c190f7754a0436b7ed8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348697"
---
# <a name="how-to-use-alternative-inputs"></a>Jak używać alternatywnej dane wejściowe

Ten samouczek przedstawia sposób użycia pola "alternatywnych dane wejściowe" dla danych wejściowych użytkownika w interfejsie nauczania.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły
"Alternatywnych dane wejściowe" są zniesławiających alternatywny użytkownika, które użytkownik może dostęp w określonym punkcie w oknie dialogowym szkolenia. Alternatywne dane wejściowe umożliwiają bardziej compactly określenie zmian co użytkownik może powiedzieć, bez konieczności listy każdej zmiany w oknie dialogowym oddzielne szkolenia.

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź AlternativeInputs. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź miasta.
3. Kliknij pozycję Utwórz.

### <a name="create-three-actions"></a>Utwórz trzy czynności

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi wpisz "Miasto, które chcesz?".
3. W dyskwalifikacji jednostek wprowadź $city.
3. Kliknięcie pozycji Utwórz

Następnie należy utworzyć drugą akcję:

1. Kliknij przycisk akcje, a następnie nowej akcji.
3. W odpowiedzi wpisz "pogody w $city jest prawdopodobnie słoneczna".
4. Wymagane jednostek, wprowadź $city.
4. Kliknij pozycję Utwórz.

Utwórz trzeci akcji:

1. Kliknij przycisk akcje, a następnie nowej akcji.
3. W odpowiedzi wpisz "Try z prośbą o pogodzie".
    - Będzie to w odpowiedzi na pytanie użytkownika takich jak "system czynności?"
4. W dyskwalifikacji jednostek wprowadź $city.
4. Kliknięcie pozycji Utwórz

Masz teraz trzy czynności.

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz "co to jest pogody".
3. Kliknij wynik akcji i wybierz pozycję "Miasto, które chcesz?"
2. Wprowadź "Zakopanego".
3. Kliknij dwukrotnie "Zakopanego", a następnie wybierz miasta.
    - To oznacza je jako element miasta.
5. Kliknij przycisk wynik akcji
    - Należy pamiętać, że Zakopanego znajduje się teraz w jednostce miasta. 
6. Wybierz opcję "pogody w $city jest prawdopodobnie słoneczna".
7. Kliknij przycisk Done nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij nową akcję, a następnie okno dialogowe nowego pociągu.
2. Wpisz "co można zrobić?".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Try z prośbą o pogodzie"
2. Wprowadź "Co to jest pogody w seattle".
3. Kliknij dwukrotnie "seattle" i wybierz miasta.
    - To oznacza je jako element miasta.
5. Kliknij przycisk wynik akcji
    - Należy pamiętać, że seattle znajduje się teraz w jednostce miasta. 
6. Wybierz opcję "pogody w $city jest prawdopodobnie słoneczna".
7. Kliknij przycisk Done nauczania.

Zobacz, co się stanie, jeśli użytkownik odpowie semantycznie podobny do powyższego:

1. Kliknij nową akcję, a następnie okno dialogowe nowego pociągu.
2. Wpisz "pomoc".
3. Kliknij przycisk wynik akcji.
    - Pamiętaj, że wyniki dla dwóch potencjalnych odpowiedzi jest bardzo Zamknij. Ta wartość informuje NAS, czy model jest pomylić o granicę między dwie akcje.
6. Kliknij przycisk porzucenia uczenie i potwierdź.

![](../media/tutorial8_closescores.png)

W takim przypadku pomoże dodać alternatywne wejścia do okien dialogowych. Możesz dodać je jako robią nauczanie. Można wrócić do poprzedniej strony i dodać je później.

2. Kliknij pozycję "Co można zrobić?" w oknach dialogowych pociągu.
2. W oknie dialogowym kliknij polecenie "co można zrobić?" Aby go wybrać.
    1. W prawym okienku w obszarze wykrywania jednostki w danych wejściowych alternatywnych Dodaj wprowadź kilka alternatyw:
    1. Wprowadź "jakie są moje opcje?"
    2. Wprowadź "Podaj mój wybór".
    3. Wprowadź "pomoc"
    1. Kliknij przycisk Prześlij zmiany.


![](../media/tutorial8_helpalternates.png)

2. Teraz kliknij "co to jest pogody w seattle".
    1. Dodaj alternatywne danych wejściowych wprowadź "prognozy dla seattle".
    2. Kliknij dwukrotnie "seattle" i wybierz miasta. Należy pamiętać, że jednostki dla danych wejściowych alternatywne powinny być obecne i mają ten sam zestaw jednostek. Jest poprawnie, jeśli różni się zawartości obiektów.
    3. Dodaj alternatywne danych wejściowych wprowadź "będzie ona fartuchów dzisiaj w Warszawie pojawią".
    4. Kliknij pozycję "Zakopanego", a następnie wybierz miasta.
    5. Kliknij przycisk Prześlij zmiany i gotowe.


Dodajmy alternatywnych danych wejściowych do okna dialogowego pierwszego:

1. Kliknij przycisk pociągu w oknach dialogowych.
2. Kliknij w oknie dialogowym, począwszy od "co to jest pogody".
2. Kliknij, aby w lewym okienku wybierz pozycję "co to jest pogody":
    1. Dodaj alternatywne danych wejściowych wprowadź "prognozie pogody".
    2. Wprowadź "będzie ona fartuchów?"
    3. Kliknij przycisk Zatwierdź zmiany.
4. Kliknij, aby wybrać "Zakopanego" w okienku po lewej stronie:
    1. Dodaj alternatywne danych wejściowych wprowadź "Zakopanego".
    2. Wprowadź "prognozy dla austin".
        - Należy pamiętać, że zostanie wyróżniona pełny zwrot. Polecenie frazy, a następnie czerwony znak x. Następnie wybierz austin i kliknij miasta.
        - Kliknij przycisk Zatwierdź zmiany
    1. Kliknij pozycję gotowe, który spowoduje, że model do retrain.

![](../media/tutorial8_altcities.png)

Poniżej przedstawiono różnice:

1. Kliknij przycisk Nowe okno dialogowe pociągu.
2. Wpisz "jakie mogą możliwości".
3. Kliknij przycisk wynik akcji.
    - Należy zauważyć, że wyniki teraz bardziej decydujący o następnej akcji, który wskazuje pewności modelu.
2. Wybierz opcję "Try z prośbą o pogodzie".
6. Kliknij przycisk Done nauczania

Masz teraz widziany wejść alternatywny sposób może służyć do wskazywać inne czynności, które użytkownik może dostęp. Pomagają uniknąć tworzenia wiele okien dialogowych, które są takie same, zwijanie ich w jednym oknie dialogowym i wyliczania co powiedzieć użytkownika na wiele sposobów.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Okna dialogowe dziennika](./9-log-dialogs.md)
