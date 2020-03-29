---
title: Migracja do jednostki wyuczonej maszynowo w wersji 3
titleSuffix: Azure Cognitive Services
description: Tworzenie v3 zapewnia jeden nowy typ jednostki, jednostki maszynowo, wraz z możliwością dodawania relacji do jednostki nauczanych maszynowo i innych jednostek lub funkcji aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563835"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migracja do jednostki tworzenia w wersji 3

Tworzenie v3 zapewnia jeden nowy typ jednostki, jednostki maszynowo, wraz z możliwością dodawania relacji do jednostki nauczanych maszynowo i innych jednostek lub funkcji aplikacji.

## <a name="entities-are-decomposable-in-v3"></a>Elementy są rozkładane w wersji V3

Jednostki utworzone za pomocą interfejsów API tworzenia wersji 3 przy użyciu [interfejsów API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) lub [portalu podglądu](https://preview.luis.ai/)umożliwiają tworzenie modelu jednostki warstwowej z elementem nadrzędnym i elementami podrzędnymi. Jednostka nadrzędna jest znana jako **jednostka uczenia się maszyn,** a dzieci podrzędne są znane jako **podskładne jednostki** maszyny.

Każdy podskładniki jest również jednostką nauczoną maszyn, ale z dodanymi opcjami konfiguracji ograniczeń i deskryptorów.

* Ograniczenia są dokładne **reguły** dopasowywania tekstu, które gwarantują jednostki jest wyodrębniany, gdy pasuje do reguły. Reguła jest definiowana przez jednostkę dopasowywania dokładnie tekstu, obecnie: [wstępnie skompilowana encja,](luis-reference-prebuilt-entities.md) [encja wyrażenia regularnego](reference-entity-regular-expression.md)lub [encja listy](reference-entity-list.md).
* **Deskryptory** to [operacje,](luis-concept-feature.md)takie jak listy fraz lub encje, które są używane do zdecydowanego wskazania encji.

Tworzenie v3 zapewnia jeden nowy typ jednostki, jednostki maszynowo, wraz z możliwością dodawania relacji do jednostki nauczanych maszynowo i innych jednostek lub funkcji aplikacji.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Jak te nowe relacje porównać do tworzenia V2

Tworzenie wersji 2 dostarczyło jednostek hierarchicznych i złożonych wraz z rolami i funkcjami, aby wykonać to samo zadanie. Ponieważ jednostki, funkcje i role nie były jawnie powiązane ze sobą, trudno było zrozumieć, jak usługa LUIS implikowała relacje podczas przewidywania.

W wersji 3 relacja jest jawna i zaprojektowana przez autorów aplikacji. Pozwala to, jako autor aplikacji, na:

* Wizualnie zobacz, jak usługa LUIS przewiduje te relacje, w przykładowych wypowiedziach
* Przetestuj te relacje za pomocą [interaktywnego okienka testowego](luis-interactive-test.md) lub w punkcie końcowym
* Użyj tych relacji w aplikacji klienckiej za pośrednictwem dobrze ustrukturyzowanego, nazwanego, zagnieżdżonego [obiektu .json](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planowanie

Podczas migracji należy wziąć pod uwagę następujące kwestie w planie migracji:

* Utwórz zapasową aplikacji usługi LUIS i wykonaj migrację w osobnej aplikacji. Mając v2 i V3 aplikacja dostępna w tym samym czasie pozwala sprawdzić poprawność zmian wymaganych i wpływ na wyniki prognozowania.
* Przechwytywanie bieżących wskaźników powodzenia prognozowania
* Przechwytywanie bieżących informacji na pulpicie nawigacyjnym jako migawki stanu aplikacji
* Przeglądanie istniejących intencji, encji, list fraz, wzorców i testów wsadowych
* Następujące elementy można migrować **bez zmian:**
    * Intencje
    * Jednostki
        * Jednostka wyrażenia regularnego
        * Jednostka listy
    * Funkcje
        * Lista fraz
* Następujące elementy muszą zostać przeniesione **ze zmianami:**
    * Jednostki
        * Jednostka hierarchiczna
        * Jednostka złożona
    * Role — role można stosować tylko do jednostki nauczającej maszynowo (nadrzędnej). Ról nie można stosować do podskładów
    * Testy wsadowe i wzorce, które używają jednostek hierarchicznych i złożonych

Podczas projektowania planu migracji pozostaw czas na przejrzenie końcowych jednostek nauczonych maszynowo, po migracji wszystkich jednostek hierarchicznych i złożonych. Podczas gdy migracja prosta będzie działać, po dokonaniu zmiany i przejrzeniu wyników testów wsadowych i przewidywania JSON, bardziej ujednolicony JSON może prowadzić do wprowadzania zmian, więc ostateczne informacje dostarczane do aplikacji po stronie klienta jest zorganizowany inaczej. Jest to podobne do refaktoryzacji kodu i powinny być traktowane z tego samego procesu przeglądu organizacji ma w miejscu.

Jeśli nie masz testów wsadowych w miejscu dla modelu V2 i migracji testów wsadowych do modelu V3 w ramach migracji, nie będzie można sprawdzić, jak migracja wpłynie na wyniki przewidywania punktu końcowego.

## <a name="migrating-from-v2-entities"></a>Migracja z encji v2

Jak rozpocząć przejście do modelu tworzenia V3, należy rozważyć, jak przejść do jednostki maszyny dowiedział się i jego podskładników, w tym ograniczenia i deskryptorów.

W poniższej tabeli przedstawiono informacje, które jednostki muszą przeprowadzić migrację z projektu jednostki V2 do V3.

|Typ jednostki autoryzującej w wersji 2|Typ jednostki autoryzującej w wersji 3|Przykład|
|--|--|--|
|Jednostka złożona|Jednostka wyuczona maszynowo|[Dowiedz się więcej](#migrate-v2-composite-entity)|
|Jednostka hierarchiczna|Rola jednostki nauczającej maszyną|[Dowiedz się więcej](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrowanie encji Kompozytowe V2

Każdy element podrzędny kompozytu V2 powinien być reprezentowany z podskładłem jednostki uczenia maszynowego V3. Jeśli element podrzędny złożony jest wstępnie utworzonym wyrażeniem regularnym lub jednostką listy, należy je zastosować jako **ograniczenie** podskładnika reprezentującego element podrzędny.

Zagadnienia dotyczące planowania migracji jednostki złożonej do jednostki nauczającej maszynowo:
* Nie można używać elementów podrzędnych w wzorcach
* Jednostki podrzędne nie są już współużytkowane
* Jednostki podrzędne muszą być oznakowane, jeśli kiedyś nie były nauczone maszynowo

### <a name="existing-descriptors"></a>Istniejące deskryptory

Każda lista fraz używana do zwiększania wyrazów w jednostce złożonej powinna być stosowana jako deskryptor do jednostki nauczonej maszynowo (nadrzędnej), jednostki podskładnika (podrzędnej) lub intencji (jeśli lista fraz dotyczy tylko jednego zamiaru). Zaplanuj dodanie deskryptora do jednostki, która powinna zostać znacznie zwiększona. Nie należy dodawać deskryptora ogólnie do jednostki uczenia maszynowego (nadrzędnego), jeśli będzie to najbardziej znacząco zwiększyć przewidywanie podskładnika (podrzędnego).

### <a name="new-descriptors"></a>Nowe deskryptory

W obszarze tworzenia wersji 3 dodaj krok planowania, aby ocenić jednostki jako możliwe deskryptory dla wszystkich jednostek i intencji.

### <a name="example-entity"></a>Przykładowa encja

Ta jednostka jest tylko przykładem. Migracja własnej jednostki może wymagać innych względów.

Należy wziąć pod uwagę kompozyt V2 do modyfikowania pizzy, `order` która używa:
* prekompilowany datetimeV2 dla czasu dostawy
* listy fraz, aby zwiększyć niektóre słowa, takie jak pizza, ciasto, skorupa i polewa
* podmiotu do wykrywania dodatków, takich jak grzyby, oliwki, pepperoni.

Przykładowa wypowiedź dla tej jednostki jest:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

W poniższej tabeli przedstawiono migrację:

|Modele V2|Modele V3|
|--|--|
|Jednostka nadrzędna — encja komponentu o nazwie`Order`|Nadrzędny — jednostka nauczona maszynowo o nazwie`Order`|
|Dziecko — wstępnie utworzony datetimeV2|* Migruj wstępnie utworzone jednostki do nowej aplikacji.<br>* Dodaj ograniczenie dla rodzica dla wstępnie utworzonego datetimeV2.|
|Child - jednostka listy dodatków|* Migruj encję listy do nowej aplikacji.<br>* Następnie dodaj ograniczenie do elementu nadrzędnego dla encji listy.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrowanie jednostki hierarchicznej w wersji 2

W wersji 2 tworzenia hierarchicznej jednostki została dostarczona przed role istniejące w usłudze LUIS. Oba służyły ten sam cel wyodrębniania jednostek na podstawie użycia kontekstu. Jeśli masz hierarchiczne jednostki, można myśleć o nich jako proste jednostki z rolami.

W v3 tworzenia:
* Rolę można zastosować w jednostce nauczanych maszynowo (nadrzędnych).
* Nie można zastosować roli do żadnych podskładów.

Ta jednostka jest tylko przykładem. Migracja własnej jednostki może wymagać innych względów.

Należy wziąć pod uwagę jednostkę hierarchiczną V2 do modyfikowania pizzy: `order`
* w przypadku gdy każde dziecko określa oryginalną polewę lub

Przykładowa wypowiedź dla tej jednostki jest:

`Change the topping from mushrooms to olives`

W poniższej tabeli przedstawiono migrację:

|Modele V2|Modele V3|
|--|--|
|Jednostka nadrzędna — encja komponentu o nazwie`Order`|Nadrzędny — jednostka nauczona maszynowo o nazwie`Order`|
|Dziecko - Hierarchiczna jednostka z oryginalną i końcową polewą do pizzy|* Dodaj `Order` rolę do każdego polewa.|

## <a name="next-steps"></a>Następne kroki

* [Zasoby deweloperów](developer-reference-resource.md)
