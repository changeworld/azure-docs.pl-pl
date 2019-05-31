---
title: Konfigurowanie środowiska wyszukiwania niestandardowego Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: W tym artykule opisano sposób tworzenia witryn i usług wyszukiwania pionowego
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: e4799ca099d608c3b8ecd16612b790f5654df7dd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390414"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurowanie środowiska wyszukiwania niestandardowego Bing

Wystąpienia wyszukiwania niestandardowego pozwala dostosować środowisko wyszukiwania, aby uwzględnić tylko zawartość z witryn sieci Web przez użytkownika, interesujące. Zamiast przeprowadzania wyszukiwania w sieci web, Bing, wyszukiwanie tylko wycinków sieci web, które Cię interesują. Aby utworzyć niestandardowy widok internetowy, użyj [portalu](https://customsearch.ai) usługi wyszukiwania niestandardowego Bing.

Portal umożliwia tworzenie wystąpienia wyszukiwania, który określa wycinków sieci web: domen, niezwykle i stron sieci Web, które Bing, aby przeprowadzić wyszukiwanie, oraz te, których nie chcesz, aby wyszukać. Portal może także podpowiedzieć zawartości, który chcesz dołączyć.

Podczas definiowania wycinków, sieci Web, należy użyć następującego:

| Nazwa fragmentu | Opis                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Wycinek domeny obejmuje wszystkie zawartości znajdującej się w obrębie domeny internetowej. Na przykład `www.microsoft.com`. Pominięcie `www.` powoduje, że Bing można także przeszukać poddomeny w domenie. Na przykład, jeśli określisz `microsoft.com`, Wyszukiwarka Bing zwraca także wyniki z `support.microsoft.com` lub `technet.microsoft.com`. |
| Podstrony    | Wycinek podstrony zawiera całą zawartość w podstrony i ścieżki poniżej. W ścieżce można określić maksymalnie dwóch podstrony. Na przykład: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Strony sieci Web    | Wycinek strony sieci Web mogą zawierać tylko tej strony sieci Web w polu wyszukiwania niestandardowego. Opcjonalnie możesz określić, czy dołączać podstrony.                                                                                                                                                                                  |

> [!IMPORTANT]
> Wszystkich domen, podstrony i stron sieci Web, który określisz musi być indeksowane przez usługę Bing i publicznych. Jeśli jesteś właścicielem publicznej witryny, które mają zostać uwzględnione w wyszukiwaniu i Bing nie zindeksował go, zobacz Bing [dokumentacji webmastera](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) szczegółowe informacje na temat pobierania Bing, aby je indeksować. Zobacz też w dokumentacji webmastera, aby uzyskać szczegółowe informacje o pobieraniu Bing zaktualizować przeszukanych witryny, jeśli indeks jest nieaktualna.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Dodaj wycinków sieci web do Twojego wystąpienia wyszukiwania niestandardowego

Podczas tworzenia wystąpienia wyszukiwania niestandardowego, można określić wycinków sieci web: domen, podstrony i stron sieci Web, które mają być dołączone lub zablokowane z wyników wyszukiwania. 

Jeśli znasz wycinków, które chcesz uwzględnić w ramach wystąpienia wyszukiwania niestandardowego, dodać je do swojego wystąpienia **Active** listy. 

Jeśli nie masz pewności, wycinki, aby uwzględnić, możesz wysłać zapytania wyszukiwania do usługi Bing w **Podgląd** okienka, a następnie wybierz wycinków, które chcesz. W tym celu: 

1. Wybierz pozycję "Bing" z listy rozwijanej w okienku podglądu, a następnie wprowadź zapytanie wyszukiwania

2. Kliknij przycisk **Dodaj witrynę** obok wyników, które chcesz dołączyć. Kliknij przycisk OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Dostosuj swoje środowisko wyszukiwania przy użyciu listy aktywnych i zablokowane 

Listę aktywnych i zablokowane wycinki mogą uzyskać dostęp, klikając **Active** i **zablokowane** kart w ramach wystąpienia wyszukiwania niestandardowego. Wycinki dodany do listy aktywnych zostaną uwzględnione w wyszukiwania niestandardowego. Zablokowane wycinki nie będą przeszukiwane, a nie będzie wyświetlane w wynikach wyszukiwania.

Aby określić wycinki ma Bing, wyszukiwanie w sieci web, kliknij przycisk **Active** karta i Dodaj jeden lub więcej adresów URL. Aby edytować lub usunąć adresy URL, należy użyć opcji w obszarze **formantów** kolumny. 

Podczas dodawania adresów URL do **Active** listy można dodać pojedynczych adresów URL, lub wiele adresów URL na raz, przekazując plik tekstowy, za pomocą ikony przekazywania.

![Na karcie aktywne wyszukiwanie niestandardowe Bing](media/file-upload-icon.png)

Aby przekazać plik, Utwórz plik tekstowy i określ jedną domenę, podstrony lub strony sieci Web w każdym wierszu. Plik zostanie odrzucone, jeśli nie jest poprawnie sformatowany.

> [!NOTE]
> * Możliwe jest jedynie przekazywanie plików do **Active** listy. Nie można go użyć wycinków, aby dodać **zablokowane** listy.  
> * Jeśli **zablokowane** lista zawiera domeny, podstrony lub strony sieci Web, który określono w pliku przekazywania zostaną usunięte z **zablokowane** listy i dodane do **Active** listy .
> * Zduplikowane wpisy w pliku przekazywania zostaną zignorowane przez wyszukiwanie niestandardowe Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Pobierz sugestie dotyczące witryny sieci Web, do własnych potrzeb wyszukiwania

Po dodaniu umożliwiająca **Active** listy, wyszukiwanie niestandardowe Bing portal wygeneruje witryny sieci Web i podstrony sugestie w dolnej części karty. Są to wycinków, które uzna za wyszukiwanie niestandardowe Bing, czy może chcesz dołączyć. Kliknij przycisk **Odśwież** Aby uzyskać sugestie zaktualizowane po zaktualizowaniu ustawień wystąpienia wyszukiwania niestandardowego. Ta sekcja jest widoczna tylko jeśli sugestie są dostępne.

## <a name="search-for-images-and-videos"></a>Wyszukiwanie obrazów i klipów wideo

Możesz wyszukać obrazów i filmów wideo w sposób podobny do zawartości sieci web za pomocą [interfejsu API wyszukiwania obrazów Bing Custom](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) lub [interfejsu API wyszukiwania wideo Bing Custom](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Można wyświetlić tych wyników z [hostowanych interfejsu użytkownika](hosted-ui.md), lub interfejsów API. 

Te interfejsy API są podobne do innych niestandardowych [wyszukiwania obrazów Bing](../Bing-Image-Search/overview.md) i [wyszukiwania wideo Bing](../Bing-Video-Search/search-the-web.md) interfejsów API, ale wyszukiwanie cała sieć web i nie wymagają `customConfig` parametr zapytania. Zobacz te zestawów dokumentacji, aby uzyskać więcej informacji na temat pracy z obrazów i filmów wideo. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testowanie wystąpienie usługi wyszukiwania z okienkiem (wersja zapoznawcza)

Wystąpienie usługi wyszukiwania można przetestować za pomocą okienka (wersja zapoznawcza) po prawej stronie w witrynie portal do przesyłania zapytań wyszukiwań i wyświetlić wyniki. 

1. Poniżej pola wyszukiwania, wybierz **Moje wystąpienia**. Możesz porównać wyniki ze środowiska wyszukiwania Bing, wybierając **Bing**. 
2. Wybieranie filtru bezpieczne wyszukiwanie i rynku do wyszukiwania (zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Wprowadź kwerendę i naciśnij klawisz enter lub kliknij ikonę wyszukiwania, aby wyświetlić wyniki z bieżącej konfiguracji. Można zmienić typu wyszukiwania wykonywania, klikając pozycję **Web**, **obraz**, lub **wideo** uzyskanie odpowiednich wyników. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Dostosuj ranga określone wyniki

Portal umożliwia dostosowanie klasyfikacji wyszukiwania zawartości z określonych domen, podstrony i stron sieci Web. Po wysłaniu zapytania wyszukiwania w okienku podglądu, każdy wynik zawiera listę dostosowania wprowadzone dla niego:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blokuj      | Przenosi domeny, podstrony lub strony sieci Web do listy zablokowanych. Bing wykluczy zawartości z wybranej lokacji były wyświetlane w wynikach wyszukiwania.                    |
| Zwiększenie wydajności      | Zwiększa zawartość z domeny lub podstrony mogą być wyższe w wynikach wyszukiwania.                                                                                        |
| Obniżenie poziomu     | Przenosi zawartość z domeny lub podstrony niżej w wynikach wyszukiwania. Zdecyduj, czy obniżyć zawartości z domeny lub podstrony, należy dla strony sieci Web. |
| Przypnij na górze | Przenosi domeny, podstrony lub strony sieci Web do **Pinned** listy. Zmusza to strona sieci Web, aby pojawiało się jako wyniki wyszukiwania górnego danemu wyszukiwaniu zapytania.                   |

Dostosowywanie ranga nie jest dostępna dla obrazu lub wyszukiwania wideo.

### <a name="boosting-and-demoting-search-results"></a>Ulepszanie i obniżanie poziomu wyników wyszukiwania

Można bardzo zwiększania, zwiększania, lub obniżenie poziomu dowolnej domeny lub podstrony w **Active** listy. Domyślnie wszystkie fragmenty są dodawane bez korekt klasyfikacji. Wycinków sieci web, które są bardzo wzmocnione lub Boosted są wyżej w wynikach wyszukiwania (przy użyciu klasyfikacji super boost jest wyższa niż boost). Elementy, które są obniżony sklasyfikowano w dolnym w wynikach wyszukiwania.

Super zwiększania, zwiększ lub obniżyć poziom elementów za pomocą **Ranking dostosować** kontrolki w **Active** listy, lub przez przy użyciu zwiększenia wydajności i obniżenie poziomu kontrolki w okienku podglądu. Usługa dodaje wycinka do listy aktywnych i dostosowuje klasyfikację odpowiednio.

> [!NOTE] 
> Ulepszanie i obniżanie poziomu domen i podstrony jest jednym z wielu metody, które używa usługa Bing Custom Search, aby określić kolejność wyników wyszukiwania. Ze względu na inne czynniki wpływające na klasyfikacji zawartości sieci web w innej efekty dostosowania ranga mogą się różnić. Okienko podglądu umożliwia testowanie skutków Dostosowywanie ranga wyników wyszukiwania. 

Super Zwiększ, Przyspiesz i obniżenie poziomu nie są dostępne dla obrazu i wyszukiwania wideo.

## <a name="pin-slices-to-the-top-of-search-results"></a>Wycinki kodu PIN na początku wyników wyszukiwania

Portal umożliwia także przypiąć adresy URL na początku wyników wyszukiwania dla określonych wyszukiwane terminy, za pomocą **Pinned** kartę. Wprowadź adres URL i zapytania do określania strony sieci Web, która będzie wyświetlana jako najlepszy wynik. Pamiętaj, że będzie możliwe przypięcie więcej niż jedną stronę sieci Web na zapytanie wyszukiwania tylko indeksowanych stron sieci Web pojawi się w wynikach wyszukiwania. Przypinanie wyników nie jest dostępna dla obrazu lub wyszukiwania wideo.

Możesz przypiąć stronę do góry na dwa sposoby:

* W **Pinned** wprowadź adres URL strony sieci Web, aby przypiąć do góry, a jego odnośnego zapytania.

* W **Podgląd** okienku, wprowadź zapytanie wyszukiwania i kliknij przycisk wyszukiwania. Znajdź stronę sieci Web, aby przypiąć dla zapytania, a następnie kliknij przycisk **numeru Pin do góry**. strony sieci Web i zapytania zostaną dodane do **Pinned** listy.

### <a name="specify-the-pins-match-condition"></a>Określ warunek dopasowania numeru pin

Domyślnie strony sieci Web są tylko przypięte do początku wyników wyszukiwania gdy ciąg zapytania użytkownika w dokładnie odpowiada jednej liście **Pinned** listy. To zachowanie można zmienić, określając jedną z następujących warunków dopasowania:

> [!NOTE]
> Wszystkie porównania między zapytania wyszukiwania użytkownika i numer pin zapytania wyszukiwania jest rozróżniana wielkość liter.

| Wartość | Opis                                                                          |
|---------------|----------------------------------------------------------------------------------|
| rozpoczyna się od | Numer pin jest zgodny, jeśli ciąg zapytania użytkownika, który rozpoczyna się od ciągu zapytania numeru pin |
| kończy się ciągiem   | Numer pin jest zgodny, jeśli ciąg zapytania użytkownika kończy się ciągiem zapytania numeru pin.  |
| zawiera    | Numer pin jest zgodny, jeśli ciąg zapytania użytkownika zawiera ciąg zapytania numeru pin.   |


Aby zmienić warunek dopasowania numeru pin, kliknij ikonę edycji kodu pin. W **warunek dopasowania zapytania** kolumny, kliknij listę rozwijaną i wybierz nowy warunek, którego chcesz użyć. Następnie kliknij przycisk Zapisz ikonę, aby zapisać zmiany.

### <a name="change-the-order-of-your-pinned-sites"></a>Zmiana kolejności przypiętych witryn

Aby zmienić kolejność swoje numery PIN, możesz przeciągnij i upuść je lub edytować ich liczbą porządkową, klikając ikonę "Edytuj" w **kontrolki** kolumny **Pinned** listy.

Jeśli wiele numerów PIN spełniają warunek dopasowania, usługa Bing Custom Search będzie używać co najwyżej na liście.

## <a name="view-statistics"></a>Wyświetlanie statystyk

Jeśli masz subskrypcję do wyszukiwania niestandardowego na odpowiednim poziomie (zobacz [stronach z cennikami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), **statystyki** karta zostanie dodany do Twojego wystąpienia produkcyjne. Karta Statystyki zawiera szczegółowe informacje o sposobie korzystania z punktami końcowymi usługi Custom Search są, w tym wolumin wywołań, najpopularniejsze zapytania, rozmieszczenie geograficzne, kody odpowiedzi i bezpiecznego wyszukiwania. Można filtrować za pomocą formantów podane szczegółowe informacje.

## <a name="usage-guidelines"></a>Wytyczne dotyczące użycia

- Dla każdego wystąpienia wyszukiwania niestandardowego, maksymalna liczba korekt klasyfikacji, które mogą wprowadzać **Active** i **zablokowane** wycinków jest ograniczona do 400.
- Dodawanie wycinka do karty aktywne, czy zablokowane jest liczone jako korekty jednej klasyfikacji.
- Ulepszanie i obniżanie poziomu liczba jako dwa dostosowania klasyfikacji.
- Dla każdego wystąpienia wyszukiwania niestandardowego maksymalną liczbę numerów PIN, które mogą powodować wynosi 200.

## <a name="next-steps"></a>Kolejne kroki

- [Wywoływanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](./hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
