---
title: 'Cechy: Działanie i kontekst - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer wykorzystuje funkcje, informacje o działaniach i kontekście, aby lepiej sugestie rankingu. Funkcje mogą być bardzo ogólne lub specyficzne dla elementu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 408501232891a7971d03c89acc647d9ed19609b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77026153"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkcje to informacje o działaniach i kontekście

Usługa Personalizer działa, ucząc się, co aplikacja powinna być pokazana użytkownikom w danym kontekście.

Personalizer używa **funkcji**, czyli informacji o **bieżącym kontekście,** aby wybrać najlepszą **akcję.** Funkcje reprezentują wszystkie informacje, które ich zdaniem mogą pomóc w personalizacji w celu uzyskania wyższych nagród. Funkcje mogą być bardzo ogólne lub specyficzne dla elementu. 

Na przykład może być **wyposażony w funkcję** dotyczącą:

* Persona _użytkownika,_ takich `Sports_Shopper`jak . Nie powinien to być identyfikator indywidualnego użytkownika. 
* _Zawartość,_ taka jak, jeśli `Documentary`film `Movie`jest `TV Series`, a , lub , lub czy towar detaliczny jest dostępny w sklepie.
* _Bieżący_ okres czasu, taki jak dzień tygodnia.

Personalizer nie przepisuje, nie ogranicza ani nie naprawia funkcji, które można wysyłać do akcji i kontekstu:

* Możesz wysłać niektóre funkcje dla niektórych działań, a nie dla innych, jeśli ich nie masz. Na przykład seriale telewizyjne mogą mieć atrybuty, których filmy nie mają.
* Niektóre funkcje mogą być dostępne tylko w niektórych przypadkach. Na przykład aplikacja mobilna może dostarczyć więcej informacji niż strona internetowa. 
* Z biegiem czasu można dodawać i usuwać funkcje dotyczące kontekstu i akcji. Personalizer nadal uczy się na podstawie dostępnych informacji.
* Musi istnieć co najmniej jedna funkcja dla kontekstu. Personalizer nie obsługuje pustego kontekstu. Jeśli tylko wysłać stały kontekst za każdym razem, Personalizer wybierze akcję dla rankingów tylko dotyczące funkcji w działaniach.
* W przypadku operacji kategorii nie trzeba definiować możliwych wartości i nie trzeba wstępnie definiować zakresów dla wartości liczbowych.

## <a name="supported-feature-types"></a>Obsługiwane typy obiektów

Personalizator obsługuje funkcje typów ciągów, numerycznych i logicznych.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Jak wybór typu funkcji wpływa na uczenie maszynowe w personalizatorze

* **Ciągi:** Dla typów ciągów każda kombinacja klucza i wartości tworzy nowe wagi w modelu uczenia maszynowego personalizatora. 
* **Numeryczne**: Należy użyć wartości liczbowych, gdy liczba powinna proporcjonalnie wpływać na wynik personalizacji. Jest to bardzo zależne od scenariusza. W uproszczonym przykładzie np. Funkcje, które są oparte na jednostkach liczbowych, ale gdzie znaczenie nie jest liniowe — takie jak Wiek, Temperatura lub Wysokość osoby — najlepiej kodować jako ciągi, a jakość operacji można zazwyczaj poprawić za pomocą zakresów. Na przykład Age może być zakodowany jako "Age":"0-5", "Age":"6-10" itp.
* Wartości **logiczne** wysyłane z wartością "false" działają tak, jakby w ogóle nie zostały wysłane.

Funkcje, które nie są obecne powinny zostać pominięte w żądaniu. Należy unikać wysyłania funkcji z wartością null, ponieważ będą przetwarzane jako istniejące i z wartością "null" podczas szkolenia modelu.

## <a name="categorize-features-with-namespaces"></a>Kategoryzowanie obiektów za pomocą obszarów nazw

Personalizator przyjmuje funkcje zorganizowane w przestrzenie nazw. W aplikacji można określić, czy obszary nazw są używane i jakie powinny być. Przestrzenie nazw są używane do grupowanie funkcji o podobnym temacie lub funkcjach pochodzących z określonego źródła.

Poniżej przedstawiono przykłady obszarów nazw funkcji używanych przez aplikacje:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* Rozwiązanie wideo
* UserDeviceInfo
* Pogoda
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Obszary nazw funkcji można nazwać zgodnie z własnymi konwencjami, o ile są one prawidłowe klucze JSON. Przestrzenie nazw służą do organizowania obiektów w różne zestawy i do rozróżniania obiektów o podobnych nazwach. Obszary nazw można myśleć jako "prefiks", który jest dodawany do nazw funkcji. Nie można zagnieżdżać obszarów nazw.


W następujących JSON `user` `state`, `device` , i są obszary nazw funkcji. 

> [!Note]
> Obecnie zdecydowanie zaleca się używanie nazw dla obszarów nazw funkcji, które są oparte na UTF-8 i zaczynają się od różnych liter. Na przykład `user` `state`, `device` , `u`i `s`zacznij `d`od , i . Obecnie obszary nazw z tymi samymi pierwszymi znakami może spowodować kolizje w indeksach używanych do uczenia maszynowego.

JSON obiekty mogą zawierać zagnieżdżone obiekty JSON i proste właściwości/wartości. Tablica może być uwzględniona tylko wtedy, gdy elementy tablicy są liczbami. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Ograniczenia w zestawach znaków dla obszarów nazw

Ciąg używany do nazywania obszaru nazw musi być zgodny z pewnymi ograniczeniami: 
* Nie może być unicode.
* W przypadku nazw obszarów nazw można użyć niektórych symboli do wydrukowania z kodami < 256. 
* Nie można używać symboli z kodami < 32 (nie można drukować), 32 (spacja), 58 (dwukropek), 124 (potok) i 126–140.
* Nie należy zaczynać od podkreślenia "_" lub funkcja zostanie zignorowana.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Jak sprawić, by zestawy funkcji były bardziej skuteczne dla Personalizer

Dobry zestaw funkcji pomaga Personalizerowi nauczyć się przewidywać akcję, która będzie napędzać najwyższą nagrodę. 

Rozważ wysłanie funkcji do interfejsu API rangi personalizatora, które są zgodne z tymi zaleceniami:

* Istnieje wystarczająco dużo funkcji, aby zwiększyć personalizację. Im dokładniej ukierunkowane treści muszą być, tym więcej funkcji są potrzebne.

* Istnieje wystarczająco dużo cech o zróżnicowanej *gęstości.* Operacja jest *gęsta,* jeśli wiele elementów są zgrupowane w kilku zasobnikach. Na przykład tysiące filmów można sklasyfikować jako "Długie" (ponad 5 min) i "Krótkie" (poniżej 5 min). Jest to *bardzo gęsta* cecha. Z drugiej strony te same tysiące elementów może mieć atrybut o nazwie "Title", który prawie nigdy nie będzie miał tej samej wartości z jednego elementu do drugiego. Jest to bardzo niegęstą lub *rzadką* cechą.  

Posiadanie cech o wysokiej gęstości pomaga Personalizer ekstrapolować uczenia się z jednego elementu do drugiego. Ale jeśli istnieje tylko kilka funkcji i są one zbyt gęste, Personalizer spróbuje precyzyjnie kierować zawartość z zaledwie kilkoma wiadrami do wyboru.

### <a name="improve-feature-sets"></a>Ulepszanie zestawów funkcji 

Analizowanie zachowania użytkownika, wykonując ocenę w trybie offline. Dzięki temu możesz przyjrzeć się przeszłym danym, aby zobaczyć, jakie funkcje w dużym stopniu przyczyniają się do pozytywnych nagród w porównaniu z tymi, które przyczyniają się mniej. Możesz zobaczyć, jakie funkcje pomagają, i to do Ciebie i Twojej aplikacji, aby znaleźć lepsze funkcje, aby wysłać do Personalizer, aby poprawić wyniki jeszcze bardziej.

Te poniższe sekcje są typowe praktyki dotyczące poprawy funkcji wysyłanych do Personalizer.

#### <a name="make-features-more-dense"></a>Uaż, aby obiekty były bardziej gęste

Możliwe jest ulepszenie zestawów funkcji, edytując je, aby były większe i mniej lub bardziej gęste.

Na przykład sygnatura czasowa w dół do drugiego jest bardzo rzadką funkcją. Może być bardziej gęsty (skuteczny) poprzez klasyfikację czasów na "rano", "południe", "popołudnie" itp.

Informacje o lokalizacji również zazwyczaj korzystają z tworzenia szerszych klasyfikacji. Na przykład współrzędna szerokości geograficznej, taka jak Lat: 47.67402° N, Long: 122.12154° W jest zbyt precyzyjna i zmusza model do uczenia się szerokości i długości geograficznej jako odrębnych wymiarów. Gdy próbujesz spersonalizować na podstawie informacji o lokalizacji, pomaga to grupować informacje o lokalizacji w większych sektorach. Łatwym sposobem, aby to zrobić, jest wybranie odpowiedniej precyzji zaokrąglania dla liczb Lat-Long i połączenie szerokości i długości geograficznej w "obszary", czyniąc je w jeden ciąg. Na przykład dobrym sposobem na reprezentowanie 47.67402° N, Long: 122.12154° W w regionach o szerokości około kilku kilometrów będzie "location":"34.3 , 12.1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Rozwiń zestawy funkcji z ekstrapolowanymi informacjami

Możesz również uzyskać więcej funkcji, myśląc o niezbadanych atrybutach, które mogą pochodzić z informacji, które już masz. Na przykład w fikcyjnej personalizacji listy filmów, czy możliwe jest, że weekend vs dzień tygodnia wywołuje różne zachowania od użytkowników? Czas można rozszerzyć, aby mieć atrybut "weekend" lub "dzień tygodnia". Czy narodowe święta kultury przyśmiają uwagę niektórych typów filmów? Na przykład atrybut "Halloween" jest przydatny w miejscach, w których jest to istotne. Czy to możliwe, że deszczowa pogoda ma znaczący wpływ na wybór filmu dla wielu ludzi? Z czasem i miejscem usługa pogodowa może dostarczyć tych informacji i można dodać je jako dodatkową funkcję. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Rozszerzanie zestawów funkcji o sztuczną inteligencję i usługi kognitywne

Sztuczna inteligencja i gotowe do uruchomienia usługi Cognitive Services mogą być bardzo potężnym dodatkiem do Personalizera. 

Przygotowując swoje elementy za pomocą usług sztucznej inteligencji, można automatycznie wyodrębnić informacje, które mogą być istotne dla personalizacji.

Przykład:

* Plik filmu można uruchomić za pomocą [indeksatora wideo,](https://azure.microsoft.com/services/media-services/video-indexer/) aby wyodrębnić elementy sceny, tekst, tonację i wiele innych atrybutów. Te atrybuty mogą być następnie bardziej gęste, aby odzwierciedlić właściwości, które nie mają oryginalnych metadanych elementu. 
* Obrazy mogą być uruchamiane przez wykrywanie obiektów, twarze poprzez sentyment itp.
* Informacje w tekście można rozszerzyć, wyodrębniając jednostki, tonację, rozszerzając jednostki za pomocą wykresu wiedzy Bing itp.

Można użyć kilku innych [usług Azure Cognitive Services,](https://www.microsoft.com/cognitive-services)takich jak

* [Łączenie jednostek](../entitylinking/home.md)
* [Analiza tekstu](../text-analytics/overview.md)
* [Rozpoznawanie emocji](../emotion/home.md)
* [Wizja komputerowa](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Akcje reprezentują listę opcji

Każda akcja:

* Ma identyfikator _zdarzenia._ Jeśli masz już identyfikator zdarzenia, należy go przesłać. Jeśli nie masz identyfikatora zdarzenia, nie wysyłaj go, Personalizer tworzy jeden dla Ciebie i zwraca go w odpowiedzi na żądanie rangi. Identyfikator jest skojarzony ze zdarzeniem Rank, a nie z użytkownikiem. Jeśli utworzysz identyfikator, identyfikator GUID działa najlepiej. 
* Zawiera listę funkcji.
* Lista funkcji może być duża (setki), ale zalecamy ocenę skuteczności funkcji w celu usunięcia funkcji, które nie przyczyniają się do zdobywania nagród. 
* Funkcje w **akcjach** mogą lub nie mogą mieć żadnej korelacji z funkcjami w **kontekście** używanym przez Personalizer.
* Funkcje działań mogą być obecne w niektórych działaniach, a nie w innych. 
* Funkcje dla określonego identyfikatora akcji mogą być dostępne jeden dzień, ale później stają się niedostępne. 

Algorytmy uczenia maszynowego personalizatora będą działać lepiej, gdy istnieją stabilne zestawy funkcji, ale wywołania rangi nie zakończy się niepowodzeniem, jeśli zestaw funkcji zmieni się wraz z czasem.

Nie wysyłaj więcej niż 50 akcji podczas akcji rankingowych. Mogą to być te same 50 działań za każdym razem lub mogą ulec zmianie. Jeśli na przykład masz katalog produktów zawierający 10 000 elementów dla aplikacji e-commerce, możesz użyć aparatu rekomendacji lub filtrowania, aby określić 40 najlepszych produktów, które klient może poludzić, i użyć Personalizera, aby znaleźć ten, który wygeneruje największą nagrodę (np. , aby użytkownik dodał do koszyka) dla bieżącego kontekstu.


### <a name="examples-of-actions"></a>Przykłady działań

Akcje wysyłane do interfejsu API rangi będą zależeć od tego, co próbujesz spersonalizować.

Oto kilka przykładów:

|Przeznaczenie|Akcja|
|--|--|
|Spersonalizuj, który artykuł jest wyróżniony na stronie z wiadomościami.|Każde działanie jest potencjalnym artykułem prasowym.|
|Optymalizuj miejsce docelowe reklam w witrynie.|Każda akcja będzie układem lub regułami tworzenia układu reklam (na przykład u góry, po prawej stronie, małymi obrazami, dużymi obrazami).|
|Wyświetlanie spersonalizowanego rankingu polecanych produktów na stronie internetowej zakupów.|Każda akcja jest konkretnym produktem.|
|Zasugeruj elementy interfejsu użytkownika, takie jak filtry, aby zastosować je do określonego zdjęcia.|Każda akcja może być inny filtr.|
|Wybierz odpowiedź czatu, aby wyjaśnić intencję użytkownika lub zaproponować działanie.|Każda akcja jest opcją interpretacji odpowiedzi.|
|Wybieranie treści, które mają być wyświetlane u góry listy wyników wyszukiwania|Każda akcja jest jednym z niewielu najlepszych wyników wyszukiwania.|


### <a name="examples-of-features-for-actions"></a>Przykłady funkcji dla działań

Poniżej przedstawiono dobre przykłady funkcji dla akcji. Będą one w dużej mierze zależeć od każdej aplikacji.

* Cechy z charakterystyką działań. Na przykład, czy jest to film lub serial?
* Funkcje dotyczące sposobu, w jaki użytkownicy mogli wchodzić w interakcje z tą akcją w przeszłości. Na przykład ten film jest najczęściej postrzegany przez osoby w demografii A lub B, zazwyczaj jest odtwarzany nie więcej niż jeden raz.
* Funkcje dotyczące cech, jak użytkownik *widzi* akcje. Na przykład, czy plakat filmu wyświetlany w miniaturze zawiera twarze, samochody lub krajobrazy?

### <a name="load-actions-from-the-client-application"></a>Akcje ładowania z aplikacji klienckiej

Funkcje z akcji zazwyczaj mogą pochodzić z systemów zarządzania zawartością, katalogów i systemów polecania. Aplikacja jest odpowiedzialna za ładowanie informacji o działaniach z odpowiednich baz danych i systemów, które masz. Jeśli akcje nie zmieniają się lub ich ładowania za każdym razem ma niepotrzebny wpływ na wydajność, można dodać logikę w aplikacji do pamięci podręcznej tych informacji.

### <a name="prevent-actions-from-being-ranked"></a>Zapobieganie klasyfikowaniu akcji

W niektórych przypadkach istnieją akcje, które nie mają być wyświetlane użytkownikom. Najlepszym sposobem, aby zapobiec akcji z rankingu jako najwyższej jest nie doliczyć go do listy akcji do interfejsu API rangi w pierwszej kolejności.

W niektórych przypadkach można określić tylko później w logice biznesowej, jeśli wynikowe _działanie_ wywołania interfejsu API rangi ma być wyświetlane użytkownikowi. W takich przypadkach należy użyć _nieaktywnych zdarzeń_.

## <a name="json-format-for-actions"></a>Format JSON dla akcji

Podczas wywoływania rangi wysyłasz wiele akcji do wyboru:

JSON obiekty mogą zawierać zagnieżdżone obiekty JSON i proste właściwości/wartości. Tablica może być uwzględniona tylko wtedy, gdy elementy tablicy są liczbami. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Przykłady informacji kontekstowych

Informacje dotyczące _kontekstu_ zależą od każdej aplikacji i przypadku użycia, ale zazwyczaj mogą zawierać informacje, takie jak:

* Dane demograficzne i profil użytkownika.
* Informacje wyodrębnione z nagłówków HTTP, takich jak agent użytkownika, lub pochodzące z informacji HTTP, takich jak odwrotne wyszukiwania geograficzne oparte na adresach IP.
* Informacje o aktualnym czasie, takim jak dzień tygodnia, weekend lub nie, rano lub po południu, pora świąteczna lub nie, itp.
* Informacje pobierane z aplikacji mobilnych, takich jak lokalizacja, ruch lub poziom naładowania baterii.
* Historyczne agregaty zachowania użytkowników - takie jak jakie są gatunki filmowe, które ten użytkownik oglądał najczęściej.

Aplikacja jest odpowiedzialna za ładowanie informacji o kontekście z odpowiednich baz danych, czujników i systemów, które mogą mieć. Jeśli informacje kontekstowe nie ulegnie zmianie, można dodać logikę w aplikacji do pamięci podręcznej tych informacji, przed wysłaniem go do interfejsu API rangi.

## <a name="json-format-for-context"></a>Format JSON dla kontekstu 

Kontekst jest wyrażony jako obiekt JSON, który jest wysyłany do interfejsu API rangi:

JSON obiekty mogą zawierać zagnieżdżone obiekty JSON i proste właściwości/wartości. Tablica może być uwzględniona tylko wtedy, gdy elementy tablicy są liczbami. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

[Uczenie przez wzmacnianie](concepts-reinforcement-learning.md) 
