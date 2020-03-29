---
title: Konfigurowanie środowiska wyszukiwania niestandardowego Bing | Dokumenty firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Portal umożliwia utworzenie wystąpienia wyszukiwania, które określa wycinki sieci Web; domen, podstron i stron internetowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220216"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurowanie środowiska wyszukiwania niestandardowego usługi Bing

Wystąpienie wyszukiwania niestandardowego umożliwia dostosowanie środowiska wyszukiwania do zawartości tylko z witryn sieci Web, na których zależy użytkownikom. Zamiast przeprowadzać wyszukiwanie w sieci Web, bing przeszukuje tylko interesujące Cię wycinki sieci Web. Aby utworzyć niestandardowy widok internetowy, użyj [portalu](https://customsearch.ai) usługi wyszukiwania niestandardowego Bing.

Portal umożliwia utworzenie wystąpienia wyszukiwania określającego wycinki sieci Web: domeny, podstrony i strony sieci Web, które mają być przeszukiwane przez bing, oraz te, których nie chcesz wyszukiwać. Portal może również sugerować zawartość, którą możesz chcieć dołączyć.

Podczas definiowania wycinków sieci Web należy użyć następujących elementów:

| Nazwa plasterka | Opis                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Plasterek domeny zawiera całą zawartość znalezioną w domenie internetowej. Na przykład `www.microsoft.com`. Pominięcie `www.` powoduje, że bing również przeszukiwać poddomeny domeny. Na przykład, jeśli `microsoft.com`określisz , Bing zwraca również wyniki z `support.microsoft.com` lub `technet.microsoft.com`. |
| Podstrona    | Plasterek podstrony zawiera całą zawartość znajdującą się na podstronie i ścieżki pod nią. Można określić maksymalnie dwie podstrony w ścieżce. Na przykład: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Strony sieci web    | Wycinek strony sieci Web może zawierać tylko tę stronę sieci Web w wyszukiwaniu niestandardowym. Opcjonalnie można określić, czy mają być dołączane podstrony.                                                                                                                                                                                  |

> [!IMPORTANT]
> Wszystkie domeny, podstrony i strony sieci Web, które określisz, muszą być publiczne i indeksowane przez bing. Jeśli jesteś właścicielem witryny publicznej, która ma zostać uwzględniona w wyszukiwaniu, a usługa Bing nie została zindeksowana, zapoznaj się [z dokumentacją dla webmasterów](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bing, aby uzyskać szczegółowe informacje na temat uzyskiwania usługi Bing do indeksowania. Ponadto zapoznaj się z dokumentacją dla webmasterów, aby uzyskać szczegółowe informacje na temat uzyskiwania usługi Bing do aktualizacji indeksowanego serwisu, jeśli indeks jest nieaktualny.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Dodawanie plasterków sieci Web do niestandardowego wystąpienia wyszukiwania

Podczas tworzenia niestandardowego wystąpienia wyszukiwania można określić wycinki sieci Web: domeny, podstrony i strony sieci Web, które mają zostać uwzględnione lub zablokowane w wynikach wyszukiwania. 

Jeśli znasz wycinki, które chcesz uwzględnić w niestandardowym wystąpieniu wyszukiwania, dodaj je do listy **Aktywne** wystąpienie. 

Jeśli nie masz pewności, które plasterki należy uwzględnić, możesz wysłać zapytania wyszukiwania do bingu w **okienku Podgląd** i wybrać żądane plasterki. W tym celu: 

1. wybierz "Bing" z listy rozwijanej w okienku Podgląd i wprowadź zapytanie wyszukiwania

2. Kliknij **pozycję Dodaj witrynę** obok wyniku, który chcesz uwzględnić. Następnie kliknij przycisk OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Dostosowywanie środowiska wyszukiwania za pomocą list aktywnych i zablokowanych 

Dostęp do listy aktywnych i zablokowanych wycinków można uzyskać, klikając karty **Aktywne** i **Zablokowane** w niestandardowym wystąpieniu wyszukiwania. Plasterki dodane do aktywnej listy zostaną uwzględnione w wyszukiwaniu niestandardowym. Zablokowane wycinki nie będą przeszukiwane i nie pojawią się w wynikach wyszukiwania.

Aby określić wycinki sieci Web, które chcesz przeszukiwać bing, kliknij kartę **Aktywny** i dodaj jeden lub więcej adresów URL. Aby edytować lub usuwać adresy URL, użyj opcji w kolumnie **Formanty.** 

Podczas dodawania adresów URL do listy **Active** można dodać pojedyncze adresy URL lub wiele adresów URL jednocześnie, przesyłając plik tekstowy za pomocą ikony przekazywania.

![Karta Aktywny wyszukiwania niestandardowego Bing](media/file-upload-icon.png)

Aby przekazać plik, utwórz plik tekstowy i określ pojedynczą domenę, podstronę lub stronę sieci Web na wiersz. Plik zostanie odrzucony, jeśli nie zostanie poprawnie sformatowany.

> [!NOTE]
> * Plik można przekazać tylko na listę **Aktywna.** Nie można go używać do dodawania plasterków do listy **Zablokowane.**  
> * Jeśli lista **Zablokowane** zawiera domenę, podstronę lub stronę sieci Web określoną w pliku przekazywania, zostanie ona usunięta z listy Zablokowane i **dodana** do listy **Aktywne.**
> * Zduplikowane wpisy w pliku przekazywania zostaną zignorowane przez wyszukiwanie niestandardowe Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Uzyskaj sugestie dotyczące witryny sieci Web dla środowiska wyszukiwania

Po dodaniu plasterków sieci Web do listy **Aktywne** portal wyszukiwania niestandardowego Bing wygeneruje sugestie dotyczące witryny sieci Web i podstrony u dołu karty. Są to plasterki, które według usługi Bing Custom Search można uwzględnić. Kliknij **przycisk Odśwież,** aby uzyskać zaktualizowane sugestie po zaktualizowaniu ustawień niestandardowego wystąpienia wyszukiwania. Ta sekcja jest widoczna tylko wtedy, gdy sugestie są dostępne.

## <a name="search-for-images-and-videos"></a>Wyszukiwanie obrazów i filmów

Obrazy i filmy wideo można wyszukiwać podobnie jak w sieci Web za pomocą [interfejsu API wyszukiwania obrazów niestandardowych Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) lub interfejsu API [niestandardowego wyszukiwania wideo Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Można wyświetlić te wyniki za pomocą [hostowanego interfejsu użytkownika](hosted-ui.md)lub interfejsów API. 

Te interfejsy API są podobne do nieniestandardowych [bing wyszukiwania obrazów](../Bing-Image-Search/overview.md) i [bing wideo wyszukiwania](../Bing-Video-Search/search-the-web.md) interfejsów `customConfig` API, ale przeszukać całą sieć Web i nie wymagają parametru zapytania. Zobacz te zestawy dokumentacji, aby uzyskać więcej informacji na temat pracy z obrazami i filmami. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testowanie wystąpienia wyszukiwania za pomocą okienka Podgląd

Wystąpienie wyszukiwania można przetestować za pomocą okienka podglądu po prawej stronie portalu, aby przesłać zapytania wyszukiwania i wyświetlić wyniki. 

1. Poniżej pola wyszukiwania wybierz pozycję **Moje wystąpienie**. Wyniki wyszukiwania można porównać z bingiem, wybierając opcję **Bing**. 
2. Wybierz filtr bezpiecznego wyszukiwania i który rynek ma być wyszukiwany (zobacz [Parametry kwerendy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Wprowadź kwerendę i naciśnij klawisz Enter lub kliknij ikonę wyszukiwania, aby wyświetlić wyniki z bieżącej konfiguracji. Typ wyszukiwania można zmienić, klikając pozycję **Web**, **Image**lub **Video,** aby uzyskać odpowiednie wyniki. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Dostosowywanie rangi określonych wyników wyszukiwania

Portal umożliwia dostosowanie klasyfikacji wyszukiwania zawartości z określonych domen, podstron i stron sieci Web. Po wysłaniu zapytania wyszukiwania w okienku podglądu każdy wynik wyszukiwania zawiera listę dostosowań, które można wprowadzić:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blok      | Przenosi domenę, podstronę lub stronę sieci Web na listę Zablokowane. Bing wykluczy zawartość z wybranej witryny z wyświetlanej w wynikach wyszukiwania.                    |
| Zwiększyć      | Zwiększa zawartość z domeny lub podstrony, aby była wyższa w wynikach wyszukiwania.                                                                                        |
| Obniż poziom     | Obniża zawartość z domeny lub podstrony niższej w wynikach wyszukiwania. Wybierz, czy zawartość ma być obniżana z domeny, czy podstrony, do której należy strona sieci Web. |
| Przypnij do góry | Przenosi domenę, podstronę lub stronę sieci Web na listę **Przypięte.** Wymusza to wyświetlenie strony sieci Web jako najlepszego wyniku wyszukiwania dla danego zapytania wyszukiwania.                   |

Dostosowywanie rangi nie jest dostępne w przypadku wyszukiwań obrazów lub filmów.

### <a name="boosting-and-demoting-search-results"></a>Promowanie i obniżanie wyników wyszukiwania

Możesz zwiększyć, zwiększyć lub obniżyć dowolną domenę lub podstronę na liście **Aktywna.** Domyślnie wszystkie wycinki są dodawane bez korekty klasyfikacji. W wynikach wyszukiwania znajdują się w rankingu super boost (z rankingiem super boost wyższym niż boost). Elementy, które są obniżone są klasyfikowane niżej w wynikach wyszukiwania.

Możesz zwiększyć, zwiększyć lub obniżyć elementy za pomocą formantów **Dopasuj klasyfikację** na liście **Aktywna** lub za pomocą kontrolek Zwiększanie i obniżanie w okienku Podgląd. Usługa dodaje plasterek do listy Active i odpowiednio dostosowuje ranking.

> [!NOTE] 
> Promowanie i obniżanie poziomu domen i podstron jest jedną z wielu metod, których wyszukiwanie niestandardowe w użyczaniu Bing używa do określania kolejności wyników wyszukiwania. Ze względu na inne czynniki wpływające na ranking różnych treści internetowych, skutki dostosowywania rangi mogą się różnić. Za pomocą okienka Podgląd można przetestować efekty dostosowywania rangi wyników wyszukiwania. 

Super doładowanie, promowanie i obniżanie poziomu nie są dostępne dla wyszukiwań obrazów i filmów.

## <a name="pin-slices-to-the-top-of-search-results"></a>Przypinanie plasterków do górnej części wyników wyszukiwania

Portal umożliwia również przypinanie adresów URL do górnej części wyników wyszukiwania dla określonych wyszukiwanych haseł, używając karty **Przypięte.** Wprowadź adres URL i kwerendę, aby określić stronę sieci Web, która będzie wyświetlana jako wynik u góry. Należy pamiętać, że można przypiąć maksymalnie jedną stronę sieci Web na zapytanie wyszukiwania, a tylko indeksowane strony sieci Web będą wyświetlane w wynikach wyszukiwania. Przypinanie wyników nie jest dostępne w przypadku wyszukiwań obrazów lub filmów.

Stronę sieci Web można przypiąć na dwa sposoby:

* Na karcie **Przypięte** wprowadź adres URL strony sieci Web, aby przypiąć ją u góry, i odpowiadającą jej kwerendę.

* W okienku **Podgląd** wprowadź zapytanie wyszukiwania i kliknij pozycję Wyszukaj. Znajdź stronę sieci Web, którą chcesz przypiąć dla zapytania, a następnie kliknij pozycję **Przypnij do góry**. strona sieci Web i kwerenda zostaną dodane do listy **Przypięte.**

### <a name="specify-the-pins-match-condition"></a>Określ warunek dopasowania szpilki

Domyślnie strony sieci Web są przypięte do górnej części wyników wyszukiwania tylko wtedy, gdy ciąg zapytania użytkownika dokładnie odpowiada ciągowi wymienionej na liście **Przypięte.** To zachowanie można zmienić, określając jeden z następujących warunków dopasowania:

> [!NOTE]
> Wszystkie porównania między zapytaniem wyszukiwania użytkownika i kwerendą wyszukiwania pinezki są niewrażliwe na wielkości liter.

| Wartość | Opis                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Rozpoczyna się od | Pinezka jest zgodna, jeśli ciąg zapytania użytkownika rozpoczyna się od ciągu zapytania pinu |
| Kończy się na   | Pin jest dopasowanie, jeśli ciąg zapytania użytkownika kończy się ciąg kwerendy pinezki.  |
| Contains    | Pin jest dopasowanie, jeśli ciąg zapytania użytkownika zawiera ciąg zapytania pin.   |


Aby zmienić stan dopasowania pinezki, kliknij ikonę edycji pinezki. W kolumnie **Warunek dopasowania kwerendy** kliknij listę rozwijanej i wybierz nowy warunek do użycia. Następnie kliknij ikonę zapisz, aby zapisać zmianę.

### <a name="change-the-order-of-your-pinned-sites"></a>Zmienianie kolejności przypiętych witryn

Aby zmienić kolejność pinów, możesz je przeciągać i upuszczać lub edytować ich numer zamówienia, klikając ikonę "edytuj" w kolumnie **Formanty** na liście **Przypięte.**

Jeśli wiele pinów spełnia warunek dopasowania, wyszukiwanie niestandardowe Bing użyje tego najwyższego na liście.

## <a name="view-statistics"></a>Wyświetlanie statystyk

Jeśli subskrybujesz wyszukiwanie niestandardowe na odpowiednim poziomie (zobacz [strony cenowe),](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)karta **Statystyka** zostanie dodana do wystąpień produkcyjnych. Karta Statystyki zawiera szczegółowe informacje o sposobie wykorzystania punktów końcowych wyszukiwania niestandardowego, w tym liczbę połączeń, najlepsze zapytania, dystrybucję geograficzną, kody odpowiedzi i bezpieczne wyszukiwanie. Szczegóły można filtrować za pomocą podanych formantów.

## <a name="usage-guidelines"></a>Wskazówki dotyczące użytkowania

- Dla każdego wystąpienia wyszukiwania niestandardowego maksymalna liczba korekt klasyfikacji, które można wprowadzić w **aktywnych** i **zablokowanych** plasterkach, jest ograniczona do 400.
- Dodanie plasterka do kart Aktywne lub Zablokowane liczy się jako jedno dopasowanie klasyfikacji.
- Zwiększenie i obniżenie liczy się jako dwie korekty rankingu.
- Dla każdego wystąpienia wyszukiwania niestandardowego maksymalna liczba pinów, które można wprowadzić, jest ograniczona do 200.

## <a name="next-steps"></a>Następne kroki

- [Wywoływanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](../bing-web-search/hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
