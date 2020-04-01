---
title: 'Samouczek: wyodrębnianie danych strukturalnych za pomocą jednostki nauczanej maszynowo — LUIS'
description: Wyodrębnij dane strukturalne z wypowiedź przy użyciu jednostki nauczane maszynowo. Aby zwiększyć dokładność ekstrakcji, dodaj podskładniki z deskryptorami i wiązaniami.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: f521e634c129aaf03ee341328721bd89723e8264
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478347"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Samouczek: wyodrębnianie danych strukturalnych z wypowiedź użytkownika z jednostek nauczonych maszynowo w language understanding (LUIS)

W tym samouczku wyodrębnić dane strukturalne z wypowiedź przy użyciu jednostki maszyny nauczył.

Jednostka nauczona maszynowo obsługuje [koncepcję rozkładu modelu,](luis-concept-model.md#v3-authoring-model-decomposition) udostępniając jednostki podskładnikowe z ich deskryptorami i ograniczeniami.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Dodawanie jednostki nauczającej maszyną
> * Dodaj podskładła
> * Dodawanie deskryptora podskładnika
> * Dodawanie ograniczenia podskładła
> * Szkolenie aplikacji
> * Aplikacja testowa
> * Publikowanie aplikacji
> * Pobierz prognozę encji z punktu końcowego

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Dlaczego warto korzystać z jednostki nauczanych maszynowo?

W tym samouczku dodano jednostkę nauczaną maszyną, aby wyodrębnić dane z wypowiedź.

Jednostka definiuje dane, które mają być wyodrębniane z wypowiedź. Obejmuje to nadanie danych nazwę, typ (jeśli to możliwe), wszelkie rozwiązania danych, jeśli istnieją niejednoznaczności i dokładny tekst, który składa się na dane.

Aby zdefiniować jednostkę, należy utworzyć jednostkę, a następnie oznaczyć tekst reprezentujący jednostkę w przykładowych wypowiedziach we wszystkich intencjach. Te oznaczone przykłady uczą usługi LUIS, co jest jednostką i gdzie można znaleźć w wypowiedź.

## <a name="entity-decomposability-is-important"></a>Rozkład jednostek jest ważny

Rozkład jednostek jest ważne zarówno dla przewidywania intencji i wyodrębniania danych z jednostką.

Zacznij od jednostki nauczanych maszynowo, która jest jednostką początkową i najwyższego poziomu do wyodrębniania danych. Następnie rozłożyć jednostkę na części wymagane przez aplikację kliencką.

Chociaż możesz nie wiedzieć, jak szczegółowe chcesz jednostki po uruchomieniu aplikacji, najlepszym rozwiązaniem jest, aby rozpocząć od jednostki maszynowo nauczył, a następnie rozkładu z podskładnikami w aplikacji dojrzewa.

W tym utworzysz jednostkę nauczona maszynowo do reprezentowania zamówienia dla aplikacji do pizzy. Kolejność powinna mieć wszystkie części, które są niezbędne do fullfil zamówienia. Aby rozpocząć, jednostka wyodrębni tekst związany z zamówieniem, wyciągając rozmiar i ilość.

Wypowiedź `Please deliver one large cheese pizza to me` dla `one large cheese pizza` należy wyodrębnić jako `1` kolejność, a następnie również wyodrębnić i `large`.

Istnieje dalszy rozkład, który można dodać, takie jak tworzenie podskładniczków dla dodatków lub skorupy. Po tym samouczku powinieneś mieć pewność, dodając te `Order` podskładniki do istniejącej jednostki.

## <a name="import-example-json-to-begin-app"></a>Importowanie przykładu .json, aby rozpocząć aplikację

1.  Pobierz i zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Etykieta tekstu jako jednostek w wypowiedziach przykładowych

Aby wyodrębnić szczegółowe informacje o zamówieniu `Order` pizzy, utwórz jednostkę najwyższego poziomu, nauczona maszynowo.

1. Na stronie **Intencje** wybierz intencję **OrderPizza.**

1. Na przykładowej liście wypowiedzi wybierz następującą wypowiedź.

    |Wypowiedź przykładowa kolejność|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Rozpocznij zaznaczanie tuż przed `pickup` lewym tekstem (#1), a następnie wyjdź tuż poza tekst znajdujący się po prawej stronie `anchovies` (#2 - spowoduje to zakończenie procesu etykietowania). Zostanie wyświetlenie wyskakującego menu. W wyskakującym okienku wprowadź nazwę `Order` encji jako (#3). Następnie `Order - Create new entity` wybierz z listy (#4).

    ![Etykieta początkowa i końcowa tekstu dla pełnej kolejności](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Jednostka nie zawsze będzie cały wypowiedź. W tym konkretnym przypadku wskazuje, `pickup` w jaki sposób ma zostać odebrane zamówienie. Z koncepcyjnego `pickup` punktu widzenia powinien być częścią jednostki oznaczonej dla zamówienia.

1. W polu **Wybierz typ elementu** wybierz pozycję Dodaj **strukturę,** a następnie wybierz pozycję **Dalej**. Struktura jest niezbędna do dodania podskładów, takich jak rozmiar i ilość.

    ![Dodawanie struktury do encji](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. W polu **Utwórz jednostkę nauczona maszyny** `Size` w polu **Struktura** dodaj, a następnie wybierz pozycję Wprowadź.
1. Aby dodać **deskryptor** `+` , zaznacz w obszarze **Deskryptory,** a następnie wybierz pozycję **Utwórz nową listę fraz**.

1. W polu **Utwórz nowy deskryptor** `SizeDescriptor` listy fraz wprowadź `small` `medium`nazwę, `large`a następnie wprowadź wartości: , , i . Po wypełnieniu pola **Sugestie** `extra large`wybierz `xl`opcję , i . Wybierz **pozycję Gotowe,** aby utworzyć nową listę fraz.

    Ten deskryptor listy `Size` fraz pomaga podskładnikowi znaleźć wyrazy związane z rozmiarem, udostępniając mu przykładowe słowa. Ta lista nie musi zawierać wyrazu o każdym rozmiarze, ale powinna zawierać wyrazy, które powinny wskazywać rozmiar.

    ![Tworzenie deskryptora dla podskładnika rozmiaru](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. W oknie **Utwórz jednostkę nauczoną komputera** `Size` wybierz pozycję **Utwórz,** aby zakończyć tworzenie podskładła.

    Jednostka `Order` ze `Size` składnikiem jest tworzony, `Order` ale tylko jednostka została zastosowana do wypowiedź. Należy oznaczyć `Size` tekst jednostki w wypowiedź przykład.

1. W tym samym przykładzie **Size** wypowiedź etykiety `large` Size podskładnika, wybierając wyraz, a następnie wybierając **Size** jednostki z listy rozwijanej.

    ![Etykieta jednostki rozmiar tekstu w wypowiedź.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Wiersz jest stały pod tekstem, ponieważ zarówno etykietowanie, jak i przewidywanie są zgodne, ponieważ tekst został _jawnie_ oznaczony etykietą.

1. Etykieta `Order` jednostki w pozostałych wypowiedzi wraz z jednostki size. Nawiasy kwadratowe w tekście `Order` wskazują encję oznaczona etykietą i encję `Size` wewnątrz.

    |Kolejność wypowiedzi przykładowych|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Make jednostki i podskładności we wszystkich pozostałych wypowiedzi przykład.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Jak traktować dorozumiane `a` dane, takie jak list sugerujący jedną pizzę? Albo brak `pickup` i `delivery` wskazać, gdzie pizza jest oczekiwana? Lub brak rozmiaru, aby wskazać domyślny rozmiar mały lub duży? Należy rozważyć traktowanie dorozumianej obsługi danych jako część reguł biznesowych w aplikacji klienckiej zamiast lub oprócz usługi LUIS.

1. Aby wyszkolić aplikację, wybierz opcję **Pociąg**. Szkolenia stosuje zmiany, takie jak nowe jednostki i wypowiedzi oznaczone etykietą, do aktywnego modelu.

1. Po szkoleniu, dodać nowy wypowiedź przykład do intencji, aby zobaczyć, jak dobrze usługa LUIS rozumie jednostki maszyny.

    |Wypowiedź przykładowa kolejność|
    |--|
    |`pickup XL meat lovers pizza`|

    Ogólny element górny jest oznaczony etykietą, `Order` a `Size` podskładnik jest również oznaczony liniami kropkowanymi.

    ![Przewidywana wypowiedź z nowym przykładem za pomocą encji](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Linia kropkowana wskazuje przewidywanie.

1. Aby zmienić przewidywanie na encję z etykietą, zaznacz wiersz, a następnie wybierz pozycję **Potwierdź prognozowanie encji**.

    ![Zaakceptuj przewidywanie, wybierając pozycję Potwierdź przewidywanie jednostek.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    W tym momencie jednostka uczenia maszynowego działa, ponieważ można znaleźć jednostki w nowym wypowiedź przykład. Jak dodać wypowiedzi przykład, jeśli jednostka nie jest przewidywane poprawnie, etykiety jednostki i podskładności. Jeśli jednostka jest przewidywane poprawnie, upewnij się, aby potwierdzić prognoz.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Dodawanie wstępnie utworzonego numeru w celu zwiększenia ilości danych

Informacje o zamówieniu powinny również zawierać liczbę przedmiotów w zamówieniu, na przykład liczbę pizz. Aby wyodrębnić te dane, należy dodać nowy podskładnik uczenia maszynowego `Order` i ten składnik wymaga ograniczenia wstępnie utworzonego numeru. Ograniczając encję do wstępnie utworzonej liczby, jednostka znajdzie i wyodrębni liczby, `2`niezależnie od `two`tego, czy tekst jest cyfrą, czy tekstem.

Rozpocznij od dodania wstępnie utworzonej jednostki numeru do aplikacji.

1. Wybierz **elementy** z lewego menu, a następnie wybierz **+ Dodaj wstępnie utworzony element**.

1. W polu **Dodaj wstępnie utworzone elementy** wyszukaj i wybierz **numer,** a następnie wybierz pozycję **Gotowe**.

    ![Dodawanie wstępnie utworzonej encji](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Wstępnie skompilowana jednostka jest dodawana do aplikacji, ale nie jest jeszcze ograniczeniem.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Tworzenie jednostki podskładła z ograniczeniem w celu wyodrębniania danych

Jednostka `Order` powinna mieć `Quantity` podskładent, aby określić, ile towaru są w kolejności. Ilość powinna być ograniczona do liczby, tak aby wyodrębnione dane były natychmiast dostępne dla aplikacji klienckiej według nazwy.

Ograniczenie jest stosowane jako dopasowanie tekstu, z dokładnym dopasowaniem (na przykład jednostką listy) lub za pomocą wyrażeń regularnych (takich jak encja wyrażenia regularnego lub wstępnie utworzony element).

Za pomocą ograniczenia wyodrębniany jest tylko tekst zgodny z tym ograniczeniem.

1. Następnie wybierz pozycję `Order` **Elementy,** a następnie wybierz encję.
1. Wybierz **+ Dodaj komponent,** a następnie wprowadź nazwę, `Quantity` a następnie `Order` wybierz pozycję Enter, aby dodać nowy podskładnik do encji.
1. Po powiadomieniu o powodzenie w **opcji zaawansowanych**wybierz ołówek Wiązania.
1. Z listy rozwijanej wybierz wstępnie utworzony numer.

    ![Utwórz encję ilości z wstępnie utworzonym numerem jako ograniczeniem.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Encja `Quantity` jest stosowana, gdy tekst jest zgodny z wstępnie utworzonym elementem numer.

    Jednostka z ograniczeniem jest tworzony, ale nie zostały jeszcze zastosowane do wypowiedzi przykład.

    > [!NOTE]
    > Podskład nie może być zagnieżdżony w podskładnym do 5 poziomów. Chociaż nie jest to wyświetlane w tym artykule, jest on dostępny w portalu i interfejsu API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Wypowiedź przykładu etykiety, aby nauczyć usługi LUIS o jednostce

1. Wybierz **opcji intencji** z nawigacji po lewej stronie, a następnie wybierz **orderPizza** intencji. Trzy liczby w następujących wypowiedzi są oznaczone etykietą, ale `Order` są wizualnie poniżej wiersza jednostki. Ten niższy poziom oznacza, że jednostki są znalezione, ale nie są uważane za część jednostki. `Order`

    ![Wstępnie utworzony numer został znaleziony, ale nie jest jeszcze uważany za poza jednostką Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Etykieta numery z `Quantity` jednostki, `2` wybierając w wypowiedź `Quantity` przykład, a następnie wybierając z listy. Etykieta `6` i `1` w tym samym wypowiedź przykład.

    ![Etykietuj tekst encją ilości.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Trenuj aplikację, aby zastosować zmiany encji do aplikacji

Wybierz **train,** aby trenować aplikację z tych nowych wypowiedzi. Po treningu `Quantity` podskładnik jest `Order` poprawnie przewidywany w składniku. Ta poprawna prognoza jest wskazywana linią ciągłą.

![Trenuj aplikację, a następnie przejrzyj wypowiedzi przykładowe.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

W tym momencie zamówienie zawiera pewne szczegóły, które można wyodrębnić (rozmiar, ilość i całkowity tekst zamówienia). Istnieje dalsze rafinacji `Order` podmiotu, takich jak polewy do pizzy, rodzaj skorupy, i zamówień bocznych. Każdy z nich powinien być utworzony jako `Order` podskładne jednostki.

## <a name="test-the-app-to-validate-the-changes"></a>Przetestuj aplikację, aby zweryfikować zmiany

Przetestuj aplikację za pomocą interaktywnego panelu **testowego.** Ten proces umożliwia wprowadzenie nowego wypowiedź następnie wyświetlić wyniki prognozowania, aby zobaczyć, jak dobrze działa aktywna i przeszkolona aplikacja. Przewidywanie intencji powinno być dość pewne (powyżej 70%) a pobieranie podmiotu powinno podnieść co najmniej jednostkę. `Order` Szczegóły jednostki order może brakować, ponieważ 5 wypowiedzi nie są wystarczające do obsługi każdego przypadku.

1. Wybierz opcję **Test** (Testuj) w górnym menu nawigacyjnym.
1. Wprowadź wypowiedź `deliver a medium veggie pizza` i wybierz pozycję Enter. Aktywny model przewidywał prawidłową intencję z ponad 70% pewnością.

    ![Wprowadź nową wypowiedź, aby przetestować intencji.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Wybierz **sprawdź,** aby wyświetlić prognozy jednostki.

    ![Wyświetlanie prognoz jednostki w panelu testów interaktywnych.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Rozmiar został prawidłowo zidentyfikowany. Należy pamiętać, że wypowiedzi `OrderPizza` przykład w intencji nie `medium` mają przykład jako rozmiar, ale `SizeDescriptor` należy użyć deskryptora listy fraz, która zawiera medium.

    Ilość nie jest poprawnie przewidywana. Można rozwiązać ten problem w aplikacji klienckiej, domyślnie rozmiar do jednego (1), jeśli nie ma rozmiaru jest zwracany w przewidywaniu usługi LUIS.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikowanie aplikacji w celu uzyskania do niej dostępu z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Uzyskaj intencję i przewidywanie encji z punktu końcowego HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL w adresie i zastąp _YOUR_QUERY_HERE_ tą samą kwerendą, którą wprowadzono w interaktywnym panelu testowym.

    `deliver a medium veggie pizza`

    Ostatnim parametrem querystring jest `query` **kwerenda**wypowiedź .

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Samouczek - intencje](luis-quickstart-intents-only.md)
* [Concept - informacje](luis-concept-entity-types.md) koncepcyjne podmiotów
* [Concept - funkcje](luis-concept-feature.md) informacji koncepcyjnych
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja używa jednostki nauczane maszynowo, aby znaleźć intencji wypowiedzi użytkownika i wyodrębnić szczegóły z tej wypowiedź. Za pomocą jednostki uczenia maszynowego pozwala na rozkład szczegółów jednostki.

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki KeyPhrase](luis-quickstart-intent-and-key-phrase.md)
