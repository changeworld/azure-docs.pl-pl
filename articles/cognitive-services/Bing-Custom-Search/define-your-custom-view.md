---
title: 'Wyszukiwanie niestandardowe Bing: Definiowanie widoku niestandardowego | Dokumentacja firmy Microsoft'
description: Opisuje sposób tworzenia witryn i usług wyszukiwania pionowego
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ef8aabc7363db88317a6428301512b0a0d4c055
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158776"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurowanie środowiska wyszukiwania niestandardowego
Wystąpienia wyszukiwania niestandardowego pozwala dostosować środowisko wyszukiwania, aby uwzględnić tylko zawartość z witryn sieci Web przez użytkownika, interesujące. Zamiast przeprowadzania wyszukiwania w sieci web, Bing, wyszukiwanie tylko wycinek sieci web, który Cię interesuje.
Aby utworzyć niestandardowy widok sieci web, użyj wyszukiwania niestandardowego Bing [portal](https://customsearch.ai). Aby uzyskać informacji na temat logowania się do portalu, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). Portal umożliwia tworzenie wystąpienia wyszukiwania, które określa domen, niezwykle i stron sieci Web, które Bing, wyszukiwanie i te, których nie chcesz, aby wyszukać. Oprócz określenia adresów URL informacji o zawartości, można również zadawać portalu Aby zasugerować zawartości, który chcesz dodać do widoku. Poniżej przedstawiono sposób zdefiniowania wycinek sieci web: 

1.  W domenie. Wycinek domeny obejmuje wszystkie zawartości znajdującej się w obrębie domeny internetowej. Na przykład www.microsoft.com. Pominięcie "www" powoduje, że Bing można także przeszukać poddomeny w domenie. Na przykład jeśli określisz microsoft.com, Bing zwraca również wartość wyniki w witrynie support.microsoft.com lub technet.microsoft.com.
2.  Podstrony. Wycinek podstrony zawiera całą zawartość w podstrony i ścieżki poniżej. W ścieżce można określić maksymalnie dwóch podstrony. Na przykład www.microsoft.com/en-us/windows/ 
3.  Strona sieci Web. Wycinek strony sieci Web mogą zawierać tylko tej strony sieci Web w polu wyszukiwania niestandardowego. Opcjonalnie możesz określić, czy dołączać podstrony.

Wszystkich domen, podstrony i stron sieci Web, który określisz musi być indeksowane przez usługę Bing i publicznych. Jeśli jesteś właścicielem publicznej witryny, które mają zostać uwzględnione w wyszukiwaniu i Bing nie zindeksował go, zobacz Bing [dokumentacji webmastera](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) szczegółowe informacje na temat pobierania Bing, aby je indeksować. Zobacz też w dokumentacji webmastera, aby uzyskać szczegółowe informacje o pobieraniu Bing zaktualizować przeszukanych witryny, jeśli indeks jest nieaktualna.

## <a name="adding-slices-to-your-custom-search"></a>Dodawanie wycinki wyszukiwania niestandardowego
Podczas definiowania wystąpienia wyszukiwania niestandardowego, można określić, aktywnych i zablokowanych domen, podstrony i stron sieci Web, którą chcesz wyszukiwania lub nie wyszukiwania.  

- Aktywność: Lista domen, podstrony lub stron sieci Web, aby uwzględnić w wyszukiwaniu. 
- Zablokowane: Lista podstrony, domeny lub stron sieci Web, które mają zostać wykluczone w wyniku wyszukiwania. Elementy, które możesz blok powinien być zawartości znajdują się w domenach i podstrony wymienione na liście aktywnych.

Aby uzyskać dostęp do każdej listy, kliknij na kartach aktywnych i zablokowane w ramach wystąpienia wyszukiwania niestandardowego. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktywne i zablokowane list 
Aby określić wycinek sieci web, które chcesz Bing, wyszukiwanie, kliknij przycisk **Active** kartę i wyświetlać listę domen, podstrony i stron sieci Web wyszukiwania. Można dodać wycinek bezpośrednio do listy lub Dodaj więcej niż jeden wycinek przez przekazanie pliku tekstowego za pomocą ikony przekazywania.

Szczegóły dotyczące przekazywania plików: 

- Przekazywanie pliku jest dostępna tylko w przypadku dodawania wycinki do listy aktywnych, nie umożliwia on dodawanie wycinki do listy zablokowanych. 
- Utwórz plik tekstowy i określ jedną domenę, podstrony lub strony sieci Web w każdym wierszu. Przekazywanie całego zostanie odrzucone, jeśli wystąpi błąd. 
- Jeśli na liście zablokowanych zawiera domeny, podstrony lub strony sieci Web, który określono w pliku przekazywania, usługi spowoduje usunięcie go z listy zablokowanych i dodaje go do listy aktywnych. 
- Usługa ignoruje duplikaty w pliku przekazywania.

Aby edytować lub usunąć wycinków, należy użyć opcji kontrolki w kolumnie. 

Podobnie można dodać wycinki do listy zablokowanych (z wyjątkiem przekazywanie pliku nie można użyć do określenia wycinki).

## <a name="pinned-list"></a>Lista przypięte
Portal umożliwia także przypiąć konkretnej strony sieci Web na początku wyników wyszukiwania, jeśli użytkownik wprowadzi określonych wyszukiwany termin. **Pinned** karta zawiera listę par zapytania termin i stron sieci Web, które określają strony sieci Web, który jest wyświetlany jako najlepszy wynik dla określonej kwerendy. Warunek kwerendy użytkownika musi dokładnie odpowiadać przypiętych wyszukiwanego terminu.
Aby uzyskać informacje o przypinaniu wyników, zobacz [dostosować ranga](#adjustrank).

Przypinanie wyników nie jest dostępna dla środowiska wyszukiwania obrazów.

## <a name="site-suggestions"></a>Propozycje dotyczące witryny
Po dodaniu wycinki do listy aktywnych, Usługa generuje witryny i podstrony sugestie, które możesz zechcieć dodać do wyszukiwania. **Możesz zechcieć dodać** sekcja zawiera sugestie. Na stronie ustawień wystąpienie zawiera w tej sekcji, tylko wtedy, gdy dostępne są sugestie. 

Aby dodać sugestie do listy aktywnych, kliknij ikonę +.  Ponieważ usługa generuje sugestie na podstawie własnych ustawień, należy kliknąć przycisk **Odśwież** po dodaniu sugestie. 

## <a name="preview-pane"></a>Okienko podglądu
Możesz przetestować się wystąpienia wyszukiwania za pomocą okienka (wersja zapoznawcza) po prawej stronie do przesyłania zapytań wyszukiwań i wyświetlić wyniki. Wybierz **Moje wystąpienia**, wybierz filtru bezpieczne wyszukiwanie i co rynek do wyszukiwania (zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Wprowadź kwerendę i naciśnij klawisz enter lub kliknij ikonę wyszukiwania, aby wyświetlić wyniki z bieżącej konfiguracji. Aby zobaczyć wyniki z Internetu kliknij **Web**, aby zobaczyć wyniki obrazu, kliknij przycisk **obrazu**. 

 Za pomocą okienka (wersja zapoznawcza), możesz również przejrzeć wyniki Bing, wybierając **Bing** zamiast **Moje wystąpienia**. Może to być przydatne porównać wyniki ze środowiska wyszukiwania na wynikach zwróconych przez usługę Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Dostosuj ranga
Portal pozwala dostosować, klasyfikacji do manipulowania wynikami, które zwraca Bing. W okienku podglądu wprowadź wyszukiwany termin, a następnie uruchomić zapytanie. W okienku podglądu listę wyników wyszukiwania dla zapytania. Z prawej strony każdego wyniku znajduje się lista dostosowania, możesz wprowadzić. 

- blok. Przenosi domeny, podstrony lub strony sieci Web do listy zablokowanych. Możesz wybrać poziom blokowania. Bing nie obejmuje zawartości z wybranej lokacji, w wynikach wyszukiwania. 
- Zwiększenie wydajności. Zwiększa zawartość z domeny lub podstrony wyższe w wynikach wyszukiwania. Zdecyduj, czy zwiększyć zawartości z domeny lub podstrony, należy dla strony sieci Web.
- Obniż poziom. Przenosi zawartość z domeny lub podstrony niżej w wynikach wyszukiwania. Zdecyduj, czy obniżyć zawartości z domeny lub podstrony, należy dla strony sieci Web. 
- Numer PIN do góry. Zdefiniuj stroną internetową, która pojawia się u góry strony wyników, jeśli użyto zapytania użytkownika termin dokładnie odpowiada wyszukiwanego terminu. Listę aktywnych ma zawierać strona sieci Web dla Ciebie przypiąć go. 

Dostosowywanie ranga nie jest dostępna dla środowiska wyszukiwania obrazów.

## <a name="boosting-and-demoting"></a>Ulepszanie i obniżanie poziomu
Można bardzo zwiększania, zwiększania, lub obniżenie poziomu dowolnej domeny lub podstrony liście Active. Domyślnie wszystkie wycinki zostaną dodane z tymi samymi wagami. Elementy, które są bardzo wzmocnione lub wzmocnione są wyżej w wynikach wyszukiwania (przy użyciu klasyfikacji super boost jest wyższa niż boost). Elementy, które są obniżony sklasyfikowano w dolnym w wynikach wyszukiwania.

Należy pamiętać, który super zwiększania, zwiększania i obniżenie poziomu warianty wagi zapewniają do domeny lub podstrony. Jest to tylko jedna wiele sygnałów, aby określić kolejność wyników posługują się oceniania. Oznacza to, że ich wpływ dla określonej kwerendy nie ma żadnej gwarancji zgodnie z wieloma innymi czynnikami mogą mieć wpływ na ogólną klasyfikacji wyniki z Internetu.  Można ustalić skutek możliwe tego zwiększania wyniku lub obniżania poziomu ma oceniania, test środowisko wyszukiwania przy użyciu okienka (wersja zapoznawcza).

Można bardzo zwiększania, zwiększania, lub obniżenie poziomu elementów, za pomocą kontrolki z klasyfikacji dostosować listę aktywnych lub za pomocą zwiększenia wydajności i obniżenie poziomu kontrolki w okienku podglądu. Usługa dodaje wycinka do listy aktywnych i dostosowuje klasyfikację odpowiednio.

Super Zwiększ, Przyspiesz i obniżenie poziomu zmiany są zapisywane automatycznie i natychmiast odzwierciedlać względem punktu końcowego usługi wyszukiwania niestandardowego. 

Super Zwiększ, Przyspiesz i obniżenie poziomu nie są dostępne dla środowiska wyszukiwania obrazów.

## <a name="pin-to-top"></a>Przypnij na górze
Aby przypiąć stronę sieci Web na początku wyników wyszukiwania dla określonej kwerendy, należy wybrać jedną z następujących opcji:

1.  Na karcie Pinned Podaj adres URL strony sieci Web Przypnij na górze wyników i dokładne zapytanie, które wyzwolą przypinania. 
2.  W okienku podglądu wprowadź termin zapytania, a następnie kliknij przycisk wyszukiwania. Następnie należy zidentyfikować strony sieci Web w wynikach, które chcesz przypiąć do pierwszych wyników, jeśli użytkownik wprowadzi tego samego zapytania. Następnie kliknij przycisk Przypnij do góry. Usługa strony sieci Web i zapytania są dodawane do listy Pinned. 

Aby śledzić swoje numery PIN, na karcie Pinned. Numery PIN są wyświetlane jako\<zapytania\>, \<strony sieci Web\>"pary. 

Strony sieci Web jest przypięty, tylko wtedy, gdy zapytanie użytkownika dokładnie odpowiada zapytaniu. 

Dla określonej kwerendy możesz przypiąć maksymalnie jedną stronę sieci Web na górze wyników.

Numery PIN nie są dostępne dla środowiska wyszukiwania obrazów.

## <a name="use-bing-to-specify-slices"></a>Aby określić wycinki przy użyciu usługi Bing
Istnieje kilka sposobów, aby określić wycinków sieci web, które tworzą wyszukiwania niestandardowego. Jeśli znasz wycinków, które mają zostać uwzględnione w wystąpieniu usługi, należy je dodać do listy aktywnego wystąpienia usługi. Aby uzyskać informacje dotyczące dodawania elementów do listy aktywnych samodzielnie, zobacz [listy aktywnych i zablokowane](#active-and-blocked-lists).
Jeśli nie masz pewności, wycinki, aby uwzględnić, można jednak uruchamiać zapytania w okienku podglądu, Bing i zobacz, Wyszukiwarka Bing zwróci. Następnie możesz wybrać wycinków, które chcesz uwzględnić w wyszukiwaniu niestandardowych. Prawdopodobnie należy uruchomić wiele terminów zapytania, aby upewnić się, że identyfikują wszystkie wycinki przeznaczone danego wystąpienia. 

Wykonaj następujące kroki, aby dodać wycinki wystąpienia usługi Custom Search przy użyciu usługi Bing. 
1.  Zaloguj się do usługi Bing Custom Search [portal](https://customsearch.ai).
2.  Utwórz wystąpienie lub wybierz wystąpienie do aktualizacji.
3.  W okienku podglądu po prawej stronie wybierz Bing z listy rozwijanej.
4.  W polu wyszukiwania wprowadź wyszukiwanego terminu, który jest odpowiedni dla Twojego wystąpienia.
5.  Kliknij przycisk **Dodaj witrynę** obok wyników, które chcesz dołączyć.
6.  Kliknij przycisk **OK**.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Wyświetlanie statystyk
Jeśli masz subskrypcję do wyszukiwania niestandardowego na odpowiednim poziomie (zobacz [stronach z cennikami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), **statystyki** karta zostanie dodany do Twojego wystąpienia produkcyjne. Karta Statystyki zawiera szczegółowe informacje o sposobie korzystania z punktami końcowymi usługi Custom Search są, w tym wolumin wywołań, najpopularniejsze zapytania, rozmieszczenie geograficzne, kody odpowiedzi i bezpiecznego wyszukiwania. Można filtrować za pomocą formantów podane szczegółowe informacje.

## <a name="understanding-quota"></a>Opis przydziału
- Dla każdego wystąpienia wyszukiwania niestandardowego, maksymalna liczba korekt klasyfikacji, które mogą wprowadzać **Active** i **zablokowane** wycinków jest ograniczona do 400.
- Dodawanie wycinka do karty aktywne, czy zablokowane jest liczone jako korekty jednej klasyfikacji.
- Ulepszanie i obniżanie poziomu liczba jako dwa dostosowania klasyfikacji.
- Dla każdego wystąpienia wyszukiwania niestandardowego maksymalną liczbę numerów PIN, które mogą powodować wynosi 200.

## <a name="next-steps"></a>Kolejne kroki

- [Wywołanie usługi wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)