---
title: Machine Learning - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Samouczek dotyczący uczenia maszynowego w usłudze Microsoft Azure decyzji niestandardowe, oparte na chmurze interfejsu API kontekstowe podejmowania decyzji.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh
ms.openlocfilehash: 50814d67ee39c6657954610358462d877843416e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349016"
---
# <a name="machine-learning"></a>Uczenie maszynowe

Ten samouczek dotyczy uczenia funkcjach w usłudze decyzji niestandardowe maszynowego zaawansowane. Samouczek składa się z dwóch części: [featurization](#featurization-concepts-and-implementation) i [funkcji specyfikacji](#feature-specification-format-and-apis). Featurization odwołuje się do reprezentujący dane jako "funkcji" do uczenia maszynowego. Specyfikacja funkcji obejmuje formatu JSON i dodatkowe interfejsy API do określania funkcji.

Domyślnie machine learning w usłudze decyzji niestandardowy jest niewidoczny dla klienta. Funkcje automatycznie korzystać z zawartości, i jest używany algorytm uczenia wzmocnienie standardowa. Funkcja wyodrębniania korzysta kilka innych kognitywnych usług Azure: [łączenie jednostki](../entitylinking/home.md), [Analiza tekstu](../text-analytics/overview.md), [emocji](../emotion/home.md), i [wizji komputera](../computer-vision/home.md). W tym samouczku można pominięte, jeśli tylko funkcje domyślne jest używany.

## <a name="featurization-concepts-and-implementation"></a>Featurization: pojęcia i implementacji

Usługa niestandardowa decyzji podejmowania decyzji w procesie jeden po drugim. Każdej decyzji obejmuje wybierania kilka rozwiązań alternatywnych, alias, akcje. W zależności od aplikacji decyzji mogą wybierać jednej akcji (short) listy uporządkowanej według rangi akcji.

Na przykład personalizowanie zaznaczenie artykułów na stronie frontonu witryny sieci Web. W tym miejscu akcji odpowiada artykułów i każdej decyzji jest artykuły, które do wyświetlenia dla danego użytkownika.

Każda akcja jest reprezentowana przez wektor właściwości odtąd o nazwie *funkcji*. Można określić nowe funkcje, oprócz funkcji wyodrębnione automatycznie. Można także skonfigurować niestandardowe decyzji usługi logowania niektórych funkcji, ale je zignorować do uczenia maszynowego.

### <a name="native-vs-internal-features"></a>Natywny, a funkcje wewnętrzne

Można określić funkcji w formacie najbardziej naturalny aplikacji, jest to numer, ciągu lub tablicy. Te funkcje są nazywane "funkcje native". Usługa niestandardowa decyzji tłumaczy każdej funkcji natywnego na jedną lub więcej funkcji liczbowego, nazywany "funkcji wewnętrznej."

Tłumaczenie na funkcji wewnętrznej jest następujący:

- Funkcje numeryczne pozostają takie same.
- tablica numeryczna umożliwia to kilka funkcji liczbowego, po jednej dla każdego elementu tablicy.
- Funkcja ciągami `"Name":"Value"` jest domyślnie przetłumaczyć funkcji o nazwie `"NameValue"` , a wartość 1.
- Opcjonalnie, ciąg może być reprezentowana jako [zbioru wyrazów](https://en.wikipedia.org/wiki/Bag-of-words_model). Wewnętrzna funkcja jest tworzona dla każdego wyrazu w ciągu, którego wartość jest liczbą wystąpień tego wyrazu.
- Funkcje wewnętrzne wartości zero zostały pominięte.

### <a name="shared-vs-action-dependent-features"></a>Udostępniony, a funkcje zależne od akcji

Niektóre funkcje odnoszą się do całego decyzji i są takie same dla wszystkich akcji. Nazywamy je *Funkcje współużytkowane*. Niektóre inne funkcje są specyficzne dla określonej akcji. Nazywamy je *funkcje zależne od akcji* (AD FS).

W tym przykładzie uruchomionych udostępnione funkcje można opisano użytkownika i/lub stan świata. Funkcje, takie jak używanie funkcji geolokalizacji, wieku i płci użytkownika oraz głównych zdarzenia są wykonywane w tej chwili. Funkcje zależne od akcji można opisać właściwości danego artykułu, takich jak tematy omówione w tym artykule.

### <a name="interacting-features"></a>Wchodzącymi w interakcje funkcji

Funkcje często "interakcji": efekt jednego zależy od innych użytkowników. Na przykład funkcja X jest Określa, czy użytkownik jest zainteresowana sportowych. Funkcja Y jest czy danym artykule o sportowych. Następnie wpływ funkcji Y jest wysoce zależna od funkcji X.

Aby uwzględnić interakcji między funkcjami X i Y, Utwórz *kwadratową* funkcji, którego wartość jest X\*Y. (Również mówi, "cross" X i Y.) Możesz wybrać przekroczeniu którego pary funkcji.

> [!TIP]
> Funkcja udostępnionego powinien przekroczony o funkcje zależne od akcji celu wpłynąć ich pozycja. Funkcji zależnych od akcji powinna przekroczony z funkcjami udostępnionego aby przyczynić się do personalizacji.

Innymi słowy funkcja udostępnionego nie przekroczyła z żadnych usług AD FS ma wpływ każdej akcji w taki sam sposób. ADF nie przekroczyła funkcją wspólną zbyt wpływ każdej decyzji. Tego typu funkcji może spowodować zmniejszenie wariancję szacuje osób trzecich.

### <a name="implementation-via-namespaces"></a>Implementacja za pośrednictwem przestrzeni nazw

Funkcje krzyżowej (a także innych koncepcji featurization) "VW wiersza polecenia" można zaimplementować w portalu. Składnia jest oparta na [Vowpal Wabbit](http://hunch.net/~vw/) wiersza polecenia.

Centralnej do implementacji jest pojęcie *przestrzeni nazw*: nazwany podzestaw funkcji. Każdej funkcji należy dokładnie jedną przestrzeń nazw. Przestrzeń nazw może zostać określona jawnie, gdy wartość funkcji jest przekazane do usługi decyzji niestandardowe. Jest jedynym sposobem, aby odwołać się do funkcji w wierszu polecenia VW.

Przestrzeń nazw jest "udostępniony" lub "działanie zależne od": składa się tylko z funkcji udostępnionych albo składa się tylko z funkcji działania zależne od tego samego działania.

> [!TIP]
> Należy dobrze zawijanie funkcje w jawnie określonych przestrzeni nazw. Grupowanie powiązanych funkcji w tej samej przestrzeni nazw.

Jeśli nie podano przestrzeni nazw, funkcję zostanie automatycznie przypisany do domyślnej przestrzeni nazw.

> [!IMPORTANT]
> Aby było spójne przez akcje nie są funkcje i przestrzenie nazw. W szczególności przestrzeni nazw może mieć różne funkcje dla różnych działań. Ponadto danej przestrzeni nazw można zdefiniować w przypadku niektórych działań, a nie inne.

Wiele wewnętrznych funkcji, które pochodzą z tej samej funkcji natywnego ciągami są zgrupowane w tej samej przestrzeni nazw. Dwie funkcje natywnego, które znajdują się w różnych przestrzeniach nazw są traktowane jako distinct, nawet jeśli ma taką samą nazwę funkcji.

> [!IMPORTANT]
> Identyfikatory długie, opisowe przestrzeni nazw są typowe, w wierszu polecenia VW nie rozróżnia przestrzeni nazw o identyfikatorze rozpoczyna się od tej samej litery. W jaki sposób identyfikatory przestrzeni nazw są jednej litery, takich jak `x` i `y`.

Szczegóły implementacji są następujące:

- Przestrzenie nazw przetnie `x` i `y`, zapisać `-q xy` lub `--quadratic xy`. Następnie każdej funkcji w `x` przekroczeniu z każdej funkcji w `y`. Użyj `-q x:` przetnie `x` z każdej przestrzeni nazw, i `-q ::` przetnie wszystkie pary przestrzeni nazw.

- Aby ignorować wszystkie funkcje w przestrzeni nazw `x`, zapis `--ignore x`.

Te polecenia są stosowane do każdej akcji oddzielnie, zawsze, gdy są zdefiniowane w przestrzeni nazw.

### <a name="estimated-average-as-a-feature"></a>Szacowany średni jako funkcja

Jako eksperyment myśl jakie byłyby średni nagrody danego działania, jeśli zostały wybrane dla wszystkich decyzji dotyczących? Takie średni osób trzecich mogą służyć jako środek "ogólną jakość" tej akcji. Nie wiadomo, dokładnie zawsze, gdy inne akcje zostały wybrane zamiast w kilku decyzji. Jednak można oszacować za pośrednictwem wzmocnienie uczenia technik. Jakość ta szacowana zwykle zwiększa się wraz z upływem czasu.

Możesz dołączyć "szacowany średni opłatą" jako funkcja dla danego działania. Następnie usługa decyzji niestandardowe automatycznie zaktualizuje ta szacowana jako dociera do nowych danych. Ta funkcja jest nazywana *brzegowego funkcji* tej akcji. Funkcje brzegowego może służyć do uczenia maszynowego i inspekcji.

Aby dodać funkcje brzegowego, zapisać `--marginal <namespace>` w wierszu polecenia VW. Zdefiniuj `<namespace>` w formacie JSON w następujący sposób:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Wstaw ten obszar nazw oraz inne funkcje zależne od akcji dla danej akcji. Podaj tej definicji dla każdej decyzji korzystającej z tego samego `mf_name` i `action_id` dla wszystkich decyzji dotyczących.

Dodawana funkcja brzegowego dla każdej akcji z `<namespace>`. `action_id` Może być dowolną nazwą funkcji, który unikatowo identyfikuje akcji. Nazwa funkcji jest równa `mf_name`. W szczególności marginal funkcji za pomocą różnych `mf_name` są traktowane jako różne funkcje — wagi różnych jest rozpoznawane dla każdego `mf_name`.

Użycie domyślnej jest to, że `mf_name` jest taka sama dla wszystkich akcji. Następnie jednej udostępnionej dla wszystkich funkcji brzegowych.

Można również określić wiele funkcji brzegowego na te same działania z tej samej wartości, ale nazw różnych funkcji.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>kodowanie 1 hot

Można reprezentować niektórych funkcji wektory bit, w którym każdy bit odpowiada zakresowi możliwych wartości. Ten bit jest ustawiony na wartość 1, tylko wtedy, gdy funkcja znajduje się w tym zakresie. W związku z tym istnieje jeden bit "gorących", która jest ustawiona na 1, a inne są ustawione na 0. Taka reprezentacja jest często nazywana *1 hot kodowanie*.

Kodowanie 1 hot jest typowa dla kategorii funkcje takie jak "region geograficzny", które nie mają z założenia znaczący numeryczna reprezentacja. Jest również zalecane dla liczbowe funkcje, których wpływ na nagrody jest mogą być inne. Na przykład danym artykule może być istotne dla określonej grupy wieku i nie ma znaczenia dla wszystkich starszych lub młodszych.

Dowolnej z ciągami funkcji jest 1 hot domyślnie zakodowane: różne funkcji wewnętrznej jest tworzona dla każdej możliwej wartości. 1 hot automatyczne kodowanie liczbowe funkcje i/lub zakresy niestandardowe nie są obecnie dostarczane.

> [!TIP]
> Algorytmów uczenia maszynowego w jednolity sposób traktować wszystkie możliwe wartości danej funkcji wewnętrznej: za pośrednictwem wspólnego "waga". Kodowanie 1 hot umożliwia oddzielne "waga" dla każdego zakresu wartości. Wprowadzania zakresy mniejszych prowadzi do lepszego korzyści po wystarczającej ilości danych zbieranych, ale może powodować zwiększenie ilości danych potrzebnych do zbieżne lepsze korzyści.

## <a name="feature-specification-format-and-apis"></a>Funkcja specyfikacji: format i interfejsów API

Można określić funkcji za pośrednictwem kilka dodatkowych interfejsów API. Wszystkie interfejsy API Użyj wspólnego formatu JSON. Poniżej przedstawiono interfejsów API i format na poziomie koncepcyjnego. Specyfikacja jest uzupełnione schematu struktury Swagger.

Podstawowe szablonu JSON w specyfikacji funkcja wygląda następująco:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

W tym miejscu `<name>` i `<value>` podstawa dla funkcji nazwą i wartością funkcji, odpowiednio. `<value>` może być ciągiem, liczbą całkowitą, zmiennoprzecinkową, wartością logiczną lub tablicy. Funkcja nie zostały opakowane w przestrzeni nazw zostanie automatycznie przypisany do domyślnej przestrzeni nazw.

Do reprezentowania ciągu jako zbioru słowa, Użyj specjalnych składni `"_text":"string"` zamiast `"<name>":<value>`. Ostatecznie osobnych funkcji wewnętrznej jest tworzony dla każdego wyrazu w ciągu. Jego wartość jest liczbą wystąpień tego wyrazu.

Jeśli `<name>` rozpoczyna się od "_" (i nie jest `"_text"`), funkcja jest ignorowane.

> [!TIP]
> Czasami scalania funkcji z wielu źródeł JSON. Dla wygody użytkownik może reprezentować je w następujący sposób:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

W tym miejscu `<features>` odwołuje się do wcześniej zdefiniowane specyfikacji podstawowych funkcji. Lepszy "zagnieżdżenia" dozwolona liczba poziomów to, zbyt. Usługa niestandardowa decyzji automatycznie znajdzie "najgłębszym" obiektów JSON, które mogą być interpretowane jako `<features>`.

#### <a name="feature-set-api"></a>Interfejs API zestawu funkcji

Funkcja API ustawić zwraca listę funkcji w formacie JSON opisanych powyżej. Można użyć kilku funkcji ustawić punkty końcowe interfejsu API. Każdy punkt końcowy jest identyfikowany przez identyfikator zestawu funkcji i adresu URL. Mapowanie funkcji Ustaw identyfikatory, a adresy URL w portalu.

Wywołania funkcji API ustawiony przez wstawienie odpowiedni identyfikator zestawu funkcji w odpowiednim miejscu w formacie JSON. Funkcje zależne od akcji Wywołanie jest automatycznie sparametryzowanych według identyfikatora akcji. Można określić wiele identyfikatorów zestaw funkcji dla tego samego działania.

#### <a name="action-set-api-json-version"></a>Akcja Ustaw API (wersja JSON)

Interfejs API akcji Ustaw ma wersję, w którym akcje i funkcje są określone w formacie JSON. Funkcji można określić jawnie i/lub za pośrednictwem interfejsów API funkcji ustawić. Wspólne funkcje można określić jeden raz dla wszystkich akcji.

#### <a name="ranking-api-http-post-call"></a>Klasyfikacja interfejsu API (wywołanie HTTP POST)

Klasyfikacja interfejsu API ma wersję, która używa wywołania metody POST protokołu HTTP. Treść tego wywołania Określa akcje i funkcje za pomocą elastycznych składni JSON.

Akcje mogą być jawnie określona i/lub za pośrednictwem akcji Ustaw identyfikatorów. Po napotkaniu identyfikator zestawu działań jest wykonywane wywołanie odpowiednich akcji Ustaw punkt końcowy interfejsu API.

Podobnie jak w przypadku akcji Ustaw API funkcji można określić jawnie i/lub za pośrednictwem interfejsów API funkcji ustawić. Wspólne funkcje można określić jeden raz dla wszystkich akcji.