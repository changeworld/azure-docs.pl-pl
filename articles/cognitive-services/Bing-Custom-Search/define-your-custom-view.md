---
title: Zdefiniuj widok niestandardowy — Bing Custom Search
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano sposób tworzenia witryn i usług wyszukiwania pionowego
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a74e6a6a90a242d4d1b2fd71a5fc6cf949ea55cb
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815260"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurowanie środowiska wyszukiwania niestandardowego

Wystąpienia wyszukiwania niestandardowego pozwala dostosować środowisko wyszukiwania, aby uwzględnić tylko zawartość z witryn sieci Web przez użytkownika, interesujące. Zamiast przeprowadzania wyszukiwania w sieci web, Bing, wyszukiwanie tylko wycinek sieci web, który Cię interesuje. Aby utworzyć niestandardowy widok sieci web, użyj wyszukiwania niestandardowego Bing [portal](https://customsearch.ai). Aby uzyskać informacji na temat logowania się do portalu, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). 

Portal umożliwia tworzenie wystąpienia wyszukiwania, które określa domen, niezwykle i stron sieci Web, które Bing, wyszukiwanie i te, których nie chcesz, aby wyszukać. Oprócz określenia adresów URL informacji o zawartości, można również zadawać portalu Aby zasugerować zawartości, który chcesz dodać do widoku. 

Poniżej przedstawiono sposób zdefiniowania wycinek sieci web: 

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

Aby edytować lub usunąć wycinków, należy użyć opcji w obszarze **formantów** kolumny. 

Podobnie można dodać wycinki do listy zablokowanych (z wyjątkiem przekazywanie pliku nie można użyć do określenia wycinki).

## <a name="pinned-list"></a>Lista przypięte

Portal umożliwia także przypiąć konkretnej strony sieci Web na początku wyników wyszukiwania, jeśli użytkownik wprowadzi określonych wyszukiwany termin. **Pinned** karta zawiera listę par zapytania termin i stron sieci Web, które określają strony sieci Web, który jest wyświetlany jako najlepszy wynik dla określonej kwerendy. Aby uzyskać informacje o przypinaniu wyników, zobacz [dostosować ranga](#adjustrank).

Przypinanie wyników nie jest dostępna dla wyszukiwania obrazów i funkcji wyszukiwania wideo.

## <a name="website-suggestions"></a>Sugestie dotyczące witryny sieci Web

Po dodaniu wycinki do listy aktywnych, Usługa generuje witryny sieci Web i podstrony sugestie, które możesz zechcieć dodać do wyszukiwania. **Możesz zechcieć dodać** sekcja zawiera sugestie. Na stronie ustawień wystąpienie zawiera w tej sekcji, tylko wtedy, gdy dostępne są sugestie. 

Aby dodać sugestie do listy aktywnych, kliknij ikonę +.  Ponieważ usługa generuje sugestie na podstawie własnych ustawień, należy kliknąć przycisk **Odśwież** po dodaniu sugestie. 

## <a name="preview-pane"></a>Okienko podglądu

Możesz przetestować się wystąpienia wyszukiwania za pomocą okienka (wersja zapoznawcza) po prawej stronie do przesyłania zapytań wyszukiwań i wyświetlić wyniki. 

1. Wybierz **Moje wystąpienie**
2. Wybieranie filtru bezpieczne wyszukiwanie i co rynek do wyszukiwania (zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Wprowadź kwerendę i naciśnij klawisz enter lub kliknij ikonę wyszukiwania, aby wyświetlić wyniki z bieżącej konfiguracji. 

Aby wybrać typ wyszukiwania do wykonania, kliknij przycisk **sieci Web** można pobrać wyniki z Internetu, **obraz** Aby uzyskać wyniki obrazu lub **wideo** można pobrać wyników dotyczących wideo. 

Za pomocą okienka (wersja zapoznawcza), możesz również przejrzeć wyniki Bing, wybierając **Bing** zamiast **Moje wystąpienia**. Może to być przydatne porównać wyniki ze środowiska wyszukiwania na wynikach zwróconych przez usługę Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Dostosuj ranga

Portal pozwala dostosować, klasyfikacji do manipulowania wynikami, które zwraca Bing. W okienku podglądu wprowadź wyszukiwany termin, a następnie uruchomić zapytanie. W okienku podglądu listę wyników wyszukiwania dla zapytania. Z prawej strony każdego wyniku znajduje się lista dostosowania, możesz wprowadzić. 

- blok. Przenosi domeny, podstrony lub strony sieci Web do listy zablokowanych. Możesz wybrać poziom blokowania. Bing nie obejmuje zawartości z wybranej lokacji, w wynikach wyszukiwania.  
  
- Zwiększenie wydajności. Zwiększa zawartość z domeny lub podstrony wyższe w wynikach wyszukiwania. Zdecyduj, czy zwiększyć zawartości z domeny lub podstrony, należy dla strony sieci Web. [Dowiedz się więcej](#boosting-and-demoting)  
  
- Obniż poziom. Przenosi zawartość z domeny lub podstrony niżej w wynikach wyszukiwania. Zdecyduj, czy obniżyć zawartości z domeny lub podstrony, należy dla strony sieci Web. [Dowiedz się więcej](#boosting-and-demoting).  
  
- Numer PIN do góry. Zdefiniuj strony sieci Web, wyświetlany u góry strony wyników dopasowuje ciąg zapytania użytkownika czy numery PIN zapytania ciągu na podstawie warunku dopasowania numeru pin. Listę aktywnych ma zawierać strona sieci Web dla Ciebie przypiąć go. [Dowiedz się więcej](#pin-to-top).

Dostosowywanie ranga nie jest dostępna dla wyszukiwania obrazów i funkcji wyszukiwania wideo.

## <a name="boosting-and-demoting"></a>Ulepszanie i obniżanie poziomu

Można bardzo zwiększania, zwiększania, lub obniżenie poziomu dowolnej domeny lub podstrony liście Active. Domyślnie wszystkie wycinki zostaną dodane z tymi samymi wagami. Elementy, które są bardzo wzmocnione lub wzmocnione są wyżej w wynikach wyszukiwania (przy użyciu klasyfikacji super boost jest wyższa niż boost). Elementy, które są obniżony sklasyfikowano w dolnym w wynikach wyszukiwania.

Należy pamiętać, który super zwiększania, zwiększania i obniżenie poziomu warianty wagi zapewniają do domeny lub podstrony. Jest to tylko jedna wiele sygnałów, aby określić kolejność wyników posługują się oceniania. Oznacza to, że ich wpływ dla określonej kwerendy nie ma żadnej gwarancji zgodnie z wieloma innymi czynnikami mogą mieć wpływ na ogólną klasyfikacji wyniki z Internetu.  Można ustalić skutek możliwe tego zwiększania wyniku lub obniżania poziomu ma oceniania, test środowisko wyszukiwania przy użyciu okienka (wersja zapoznawcza).

Można bardzo zwiększania, zwiększania, lub obniżenie poziomu elementów, za pomocą kontrolki z klasyfikacji dostosować listę aktywnych lub za pomocą zwiększenia wydajności i obniżenie poziomu kontrolki w okienku podglądu. Usługa dodaje wycinka do listy aktywnych i dostosowuje klasyfikację odpowiednio.

Super Zwiększ, Przyspiesz i obniżenie poziomu nie są dostępne dla środowiska wyszukiwania obrazów i wideo.

## <a name="pin-to-top"></a>Przypnij na górze

Aby przypiąć stronę sieci Web na początku wyników wyszukiwania dla określonej kwerendy, należy wybrać jedną z następujących opcji:

1.  W **Pinned** wprowadź adres URL strony sieci Web Przypnij na górze wyników, a zapytanie, które wyzwala przypinania.  
  
2.  W **Podgląd** okienku, wprowadź termin zapytania i kliknij przycisk Wyszukaj. Następnie należy zidentyfikować strony sieci Web w wynikach, które chcesz przypiąć do pierwszych wyników, jeśli użytkownik wprowadzi tego samego zapytania. Następnie kliknij przycisk **numeru Pin do góry**. Usługa dodaje strony sieci Web i zapytania w celu **Pinned** listy. 

Możesz śledzić swoje numery PIN w **Pinned** kartę. Numery PIN są wyświetlane jako\<zapytania\>, \<strony sieci Web\>"pary. 

Dla określonej kwerendy możesz przypiąć maksymalnie jedną stronę sieci Web na górze wyników.

Numery PIN nie są dostępne dla wyszukiwania obrazów i funkcji wyszukiwania wideo.

### <a name="specifying-the-pins-match-condition"></a>Określanie warunków dopasowania numeru pin

Strony sieci Web jest przypięty do góry wyniki tylko wtedy, gdy ciąg zapytania użytkownika pasuje do ciągu zapytania numeru pin, na podstawie warunku dopasowania numeru pin. Domyślnie numer pin jest takie samo tylko wtedy, gdy ciąg zapytania numeru pin i ciąg zapytania użytkownika dokładnie pasować. Zachowanie domyślne można zmienić, określając jedną z następujących warunków dopasowania.

- Rozpoczyna się od &mdash; numer pin jest zgodny, jeśli ciąg zapytania użytkownika, który rozpoczyna się od ciągu zapytania numeru pin.
- Kończy się &mdash; numer pin jest zgodny, jeśli ciąg zapytania użytkownika kończy się ciągiem zapytania numeru pin.
- Zawiera &mdash; numer pin jest zgodny, jeśli ciąg zapytania użytkownika zawiera ciąg zapytania numeru pin.

Aby zmienić warunek dopasowania numeru pin, kliknij ikonę edycji kodu pin (wygląda ołówka). W **warunek dopasowania zapytania** kolumny, kliknij listę rozwijaną i wybierz nowy warunek, którego chcesz użyć. Następnie kliknij przycisk Zapisz ikonę, aby zapisać zmiany.

Wszystkie porównania jest rozróżniana wielkość liter.

### <a name="changing-the-order-of-the-pins"></a>Zmiana kolejności numerów PIN

Aby zmienić kolejność swoje numery PIN, można następnie przeciągnij i upuść numeru pin, lub można edytować numer pin i zmień jego numer zamówienia. Aby następnie przeciągnij i upuść numeru pin kliknij numer pin, na liście, aby przenieść Zachowaj przycisku myszy wciśnięte i przeciągnij numeru pin przez kod pin, który chcesz zamienić, a następnie zwolnij przycisk myszy. Należy zauważyć, że numer zamówienia zmienia się odpowiednio.

Można również edytować numer zamówienia. W **formantów** kolumny, kliknij ikonę edycji kodu pin (wygląda ołówka). Wprowadź liczbę porządkową, w której mają numer pin, aby widoczne na liście i naciśnij klawisz enter lub kliknij przycisk Zapisz ikony. Na przykład, jeśli chcesz, aby mieć drugi numer pin jest obecnie szóstego na liście, zmienić liczbę porządkową, do dwóch (2).

Jeśli wiele numerów PIN spełnia warunek dopasowania, kolejność numery PIN Określa numer pin, który korzysta z usługi Bing. Na przykład numery PIN, dwoma i trzema spełniają warunek dopasowania, usługa Bing używa dwóch numeru pin.

## <a name="use-bing-to-specify-slices"></a>Aby określić wycinki przy użyciu usługi Bing

Istnieje kilka sposobów, aby określić wycinków sieci web, które tworzą wyszukiwania niestandardowego. Jeśli znasz wycinków, które chcesz uwzględnić w wystąpieniu usługi, dodać je do swojego wystąpienia **Active** listy. Aby uzyskać informacje dotyczące dodawania elementów do **Active** listę samodzielnie, zobacz [listy aktywnych i zablokowane](#active-and-blocked-lists).

Ale jeśli nie masz pewności, wycinki, aby uwzględnić, możesz uruchomić Bing zapytania **(wersja zapoznawcza)** okienko i zobacz, Wyszukiwarka Bing zwróci. Następnie możesz wybrać wycinków, które chcesz uwzględnić w wyszukiwaniu niestandardowych. Prawdopodobnie należy uruchomić wiele terminów zapytania, aby upewnić się, że identyfikują wszystkie wycinki przeznaczone danego wystąpienia. 

Wykonaj następujące kroki, aby dodać wycinki wystąpienia usługi Custom Search przy użyciu usługi Bing. 

1.  Zaloguj się do usługi Bing Custom Search [portal](https://customsearch.ai).
2.  Utwórz wystąpienie lub wybierz wystąpienie do aktualizacji.
3.  W okienku podglądu po prawej stronie wybierz Bing z listy rozwijanej.
4.  W polu wyszukiwania wprowadź wyszukiwanego terminu, który jest odpowiedni dla Twojego wystąpienia.
5.  Kliknij przycisk **Dodaj witrynę** obok wyników, które chcesz dołączyć.
6.  Kliknij przycisk **OK**.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

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