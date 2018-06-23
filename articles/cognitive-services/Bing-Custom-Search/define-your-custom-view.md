---
title: 'Wyszukiwanie niestandardowe Bing: Zdefiniuj widok niestandardowy | Dokumentacja firmy Microsoft'
description: Opisuje sposób tworzenia lokacji i usług pionowy wyszukiwania
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ffe3087df398d6310828e41d0c6992199fafbed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347888"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurowanie środowiska wyszukiwania niestandardowego
Wystąpienie wyszukiwanie niestandardowe umożliwia dostosowywanie wyników wyszukiwania, aby uwzględnić zawartość tylko z interesujących użytkowników witryn sieci Web. Zamiast wyszukiwania w sieci web, usługi Bing wyszukuje tylko wycinka interesującego sieci web.
Aby utworzyć niestandardowy widok sieci web, użyj wyszukiwania usługi Bing niestandardowego [portal](https://customsearch.ai). Aby uzyskać informacje o zalogowaniu się do portalu, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). Portal umożliwia utworzenie wystąpienia wyszukiwania, które określa domen, niezwykle i stron sieci Web, które wyszukiwania usługi Bing i tych, których nie chcesz, aby wyszukać. Oprócz określania adresów URL informacji o zawartości, możesz również poprosić portalu, aby zasugerować zawartości, którą chcesz dodać do widoku. Poniżej przedstawiono sposoby zdefiniować wycinek sieci Web: 

1.  Domeny. Wycinek domeny obejmuje całą zawartość w domenie internetowej. Na przykład www.microsoft.com. Pominięcie "www" powoduje, że Bing można także przeszukać poddomen domeny. Na przykład jeśli określisz microsoft.com Bing zwraca również wartość wyników z support.microsoft.com lub technet.microsoft.com.
2.  Podstrony. Wycinek podstrony obejmuje całą zawartość w podstrony i ścieżki poniżej. W ścieżce można określić maksymalnie dwóch podstrony. Na przykład www.microsoft.com/en-us/windows/ 
3.  Strony sieci Web. Wycinek strony sieci Web może zawierać tylko tej strony sieci Web wyszukiwania niestandardowego. Opcjonalnie możesz określić, czy dołączać podstrony.

Wszystkich domen, podstrony i stron sieci Web, który określisz musi być indeksowane według Bing i publicznych. Właścicielem publicznej witryny, które chcesz uwzględnić w wyszukiwaniu, Bing nie indeksowane go, zobacz Bing [dokumentacji webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) szczegółowe informacje na temat uzyskiwania Bing, aby go. Zobacz też dokumentacji webmaster informacje na temat uzyskiwania Bing zaktualizować przeszukanych witryny, jeśli indeks jest nieaktualny.

## <a name="adding-slices-to-your-custom-search"></a>Dodawanie wycinków niestandardowe wyszukiwania
Podczas definiowania wystąpienia wyszukiwania niestandardowego określ domeny active i zablokowane, podstrony i stron sieci Web, który chcesz przeszukać lub Wyszukaj, nie.  

- Aktywny: Listę domen, podstrony lub stron sieci Web do wyszukiwania. 
- Zablokowane: Lista podstrony, domeny lub stron sieci Web, które mają zostać wykluczone z wyszukiwania. Elementy, które można bloku powinna być zawartości znajdują się w domenach i podstrony wymienione na liście aktywnych.

Dostęp do każdej listy, kliknij na kartach aktywnych i zablokowane w wystąpieniu wyszukiwania niestandardowego. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktywny i listy zablokowanych 
Aby określić wycinek sieci web, które chcesz Bing wyszukiwania, kliknij przycisk **Active** karcie i listy domen, podstrony i stron sieci Web do wyszukiwania. Można dodać wycinek bezpośrednio do listy lub Dodaj więcej niż jeden wycinek poprzez przekazanie pliku tekstowego za pomocą ikony przekazywania.

Szczegóły dotyczące przekazywania plików: 

- Przekazywanie pliku jest dostępna tylko w przypadku dodawania wycinków do listy aktywnych, nie można go użyć do dodania do listy zablokowanych wycinków. 
- Utwórz plik tekstowy i określ pojedynczą domenę, podstrony lub strony sieci Web w każdym wierszu. Przekazywanie całego został odrzucony, jeśli wystąpi błąd. 
- Jeśli lista zablokowanych zawiera domeny, podstrony lub określona w pliku przekazywania strony sieci Web, usługi spowoduje usunięcie jej z listy zablokowanych i dodaje go do listy aktywnych. 
- Usługa ignoruje duplikaty przekazywanego pliku.

Aby edytować lub usunąć wycinków, użyj opcji w kolumnie kontrolki. 

Podobnie można dodawanych do listy zablokowanych (z wyjątkiem pliku przekazywania nie można użyć do określenia wycinków).

## <a name="pinned-list"></a>Przypięte listy
Portal umożliwia także przypiąć określonej strony sieci Web na początku wyników wyszukiwania po wprowadzeniu terminu wyszukiwania określonych. **Pinned** karta zawiera listę pary terminu i strony sieci Web zapytania, które Określ strony sieci Web, który jest wyświetlany jako wynik top dla określonej kwerendy. Użytkownika wyszukiwanego terminu musi dokładnie odpowiadać przypiętych wyszukiwanego terminu.
Aby dowiedzieć się, jak przypinanie wyników, zobacz [pozycję Dostosuj](#adjustrank).

Przypinanie wyników nie jest dostępna dla środowiska wyszukiwania obrazu.

## <a name="site-suggestions"></a>Sugestie lokacji
Po dodaniu wycinków do listy aktywnych, Usługa generuje sugestie dotyczące lokacji i podstrony, które można dodać do wyszukiwania. **Ma zostać dodany** sekcja zawiera sugestie. Strona Ustawienia wystąpienia zawiera w tej sekcji tylko wtedy, gdy sugestie są dostępne. 

Aby dodać sugestie do listy aktywnych, kliknij pozycję + ikony.  Ponieważ usługa generuje sugestie na podstawie własnych ustawień, należy kliknąć przycisk **Odśwież** po dodaniu sugestie. 

## <a name="preview-pane"></a>Okienko podglądu
Można przetestować wystąpienia wyszukiwania za pomocą okienka podglądu po prawej stronie do przesyłania zapytań wyszukiwań i wyświetlenia wyników. Wybierz **Moje wystąpienia**, wybierz filtr wyszukiwania bezpieczne i co rynku wyszukiwania (zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Wprowadź kwerendę i naciśnij klawisz enter lub kliknij ikonę wyszukiwania, aby wyświetlić wyniki z bieżącej konfiguracji. Aby zobaczyć wyniki sieci web, kliknij przycisk **Web**, aby zobaczyć wyniki obrazu, kliknij przycisk **obrazu**. 

 Za pomocą okienka podglądu, można również przejrzeć wyniki Bing, wybierając **Bing** zamiast **Moje wystąpienia**. Może to być przydatne do porównywania wyników z środowiska wyszukiwania do wyników zwróconych przez usługi Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Dostosuj ranga
Portal służy do zmiany klasyfikacji do manipulowania wyniki, które zwraca Bing. W okienku podglądu wprowadź wyszukiwany termin, a następnie uruchom zapytanie. W okienku podglądu listę wyników wyszukiwania dla zapytania. Po prawej stronie każdy wynik jest listy korekt, możesz wprowadzić. 

- Blok. Lista zablokowanych przenosi domeny, podstrony lub strony sieci Web. Należy wybrać poziom, aby zablokować. Bing wyklucza zawartość z wybranej witryny w wynikach wyszukiwania. 
- Zwiększanie wyniku. Zwiększa zawartości z domeny lub podstrony wyżej w wynikach wyszukiwania. Zdecyduj, czy zwiększania zawartości znajdują się w domenie lub podstrony, należy dla strony sieci Web.
- Obniż poziom. Przenosi zawartość z domeny lub podstrony znajdujące się w wynikach wyszukiwania. Zdecyduj, czy obniżyć poziom zawartości znajdują się w domenie lub podstrony, należy dla strony sieci Web. 
- Kod PIN do góry. Zdefiniuj strony sieci Web, która pojawia się w górnej części wyników, jeśli kwerenda użytkownika terminu dokładnie odpowiada wyszukiwanego terminu. Listę aktywnych musi zawierać strona sieci Web można przypiąć go. 

Dopasowywanie rangę nie jest dostępna dla środowiska wyszukiwania obrazu.

## <a name="boosting-and-demoting"></a>Zwiększanie wyniku i obniżanie poziomu
Można super zwiększania, zwiększenia, lub obniżyć poziom wszystkich domen lub podstrony na liście aktywnych. Domyślnie wszystkie fragmenty są dodawane z tymi samymi wagami. Elementy, które są Super boosted lub Boosted są wyżej w wynikach wyszukiwania (o klasyfikacji super zwiększanie wyniku jest wyższy niż zwiększanie wyniku). Elementy, które są obniżenie poziomu niższe sklasyfikowano w wynikach wyszukiwania.

Należy pamiętać, że super zwiększania zwiększania i obniżyć poziom nadaj im wagi Variant do domeny lub podstrony. To jest tylko jeden z wielu sygnały używane przez ranker, aby określić kolejność wyników. Oznacza to, ich wpływ na określonej kwerendy nie jest gwarantowana jak wiele innych czynników wpływających na ogólnej klasyfikacji wyników sieci web.  Ustalenie możliwego wpływu tego zwiększania lub obniżania ma w teście ranker, wyników wyszukiwania za pomocą okienka podglądu.

Można super zwiększania, zwiększenia, lub obniżenia poziomu elementów za pomocą formantów Klasyfikacja dostosować na liście aktywnych lub za pomocą zwiększanie wyniku i obniżyć poziom formantów w okienku podglądu. Usługa dodaje go do listy aktywnych i dostosowuje klasyfikację odpowiednio.

Super zwiększania zwiększania i obniżyć poziom zmiany są automatycznie zapisywane i natychmiast odzwierciedlają względem punktu końcowego wyszukiwania niestandardowego. 

Super zwiększania zwiększania i obniżyć poziom nie są dostępne w celu uzyskania wyników wyszukiwania obrazu.

## <a name="pin-to-top"></a>Przypnij do góry
Aby przypiąć strony sieci Web na początku wyniki wyszukiwania dla określonej kwerendy, wybierz jedną z następujących opcji:

1.  Na karcie Pinned wprowadź adres URL strony sieci Web, aby przypiąć element do góry wyników i dokładne zapytanie, które wyzwoli przypinanie. 
2.  W okienku podglądu wprowadź wyszukiwanego terminu, a następnie kliknij przycisk wyszukiwania. Następnie należy zidentyfikować strony sieci Web w wynikach, które chcesz przypiąć do góry wyniki, jeśli użytkownik musi wprowadzić w jednym zapytaniu. Następnie kliknij polecenie Przypnij do góry. Usługa strony sieci Web i zapytania są dodawane do listy Pinned. 

Można śledzić z numerów PIN na karcie Pinned. Numery PIN są wyświetlane jako\<zapytania\>, \<strony sieci Web\>"pary. 

Strony sieci Web jest przypięty tylko, jeśli kwerenda użytkownika dokładnie odpowiada zapytaniu. 

Dla określonej kwerendy można przypiąć maksymalnie jeden strony sieci Web na początku wyników.

Numery PIN nie są dostępne w celu uzyskania wyników wyszukiwania obrazu.

## <a name="use-bing-to-specify-slices"></a>Aby określić wycinków przy użyciu usługi Bing
Istnieje kilka sposobów, aby określić wycinków sieci web, wchodzące w skład niestandardowe wyszukiwania. Jeśli znasz wycinków, które chcesz uwzględnić w wystąpieniu, dodaj je do listy aktywnych Twoje wystąpienie. Aby uzyskać informacje dotyczące dodawania elementów do listy aktywnych samodzielnie, zobacz [listy aktywnych i zablokowane](#active-and-blocked-lists).
Ale jeśli nie masz pewności, wycinki do uwzględnienia, można uruchomić kwerendy usługi Bing w okienku podglądu i sprawdzić, zwraca Bing. Następnie możesz wybrać wycinków, które chcesz uwzględnić w wyszukiwaniu niestandardowych. Prawdopodobnie należy uruchomić wiele terminów zapytania do upewnij się, że zidentyfikować wycinki dla wystąpienia. 

Wykonaj następujące kroki, aby dodać do Twojego wystąpienia wyszukiwanie niestandardowe wycinków przy użyciu usługi Bing. 
1.  Zaloguj się do wyszukiwania usługi Bing niestandardowe [portal](https://customsearch.ai).
2.  Utwórz wystąpienie lub wybierz wystąpienie do aktualizacji.
3.  W okienku podglądu po prawej stronie wybierz Bing z listy rozwijanej.
4.  W polu wyszukiwania wprowadź wyszukiwanego terminu, który jest odpowiedni dla wystąpienia.
5.  Kliknij przycisk **Dodawanie witryny** obok wyników, które chcesz dołączyć.
6.  Kliknij przycisk **OK**.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Wyświetlanie statystyk
Jeśli masz subskrypcję wyszukiwanie niestandardowe na odpowiednim poziomie (zobacz [cennik stron](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), **statystyki** kartę zostanie dodany do Twojego wystąpienia produkcji. Karta Statystyki zawiera szczegółowe informacje o sposobie korzystania punktami końcowymi wyszukiwanie niestandardowe, łącznie z wezwań, najważniejszych zapytań, rozmieszczenie geograficzne, kody odpowiedzi i bezpieczne wyszukiwania. Można filtrować za pomocą formantów podanych szczegółów.

## <a name="understanding-quota"></a>Opis przydziału
- Dla każdego wystąpienia wyszukiwania niestandardowego maksymalną liczbę korekt klasyfikacji, które mogą dokonać **Active** i **zablokowane** wycinków jest ograniczona do 400.
- Dodawanie wycinek do karty aktywny lub zablokowane traktowana jako korekty jeden klasyfikacji.
- Zwiększanie wyniku i obniżenie liczby jako dwa dostosowania klasyfikacji.
- Dla każdego wystąpienia wyszukiwania niestandardowego maksymalną liczbę numerów PIN, które mogą powodować jest ograniczona do 200.

## <a name="next-steps"></a>Kolejne kroki

- [Wywołanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)