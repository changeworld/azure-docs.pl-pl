---
title: Opis witryny Marketplace dla modułu usługi Azure IoT Edge | Portal Azure Marketplace
description: Utwórz opis witryny marketplace moduł usługi IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: aa0c908e18233926965d8dc3cd1b474f876b8dda
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942428"
---
# <a name="iot-edge-module-marketplace-tab"></a>Kartę Marketplace moduł usługi IoT Edge

**Marketplace** karcie **nowa oferta** strony umożliwia udostępnianie potencjalnych klientów. marketingu, sprzedaży i prawne informacji i umowy i zarządzanie potencjalnymi klientami wygenerowany na podstawie Portal Marketplace. Ta długich fragmentów jest podzielony na cztery sekcje: **Omówienie**, **marketingowych artefaktów**, **Zarządzanie potencjalnymi**, i **prawne**.


## <a name="overview"></a>Omówienie

W tej sekcji należy wprowadzić ogólne informacje o ofercie Azure Marketplace.  Znak gwiazdki (*) dodanym na końcu nazwy pola wskazuje, że jest to wymagane.

![Omówienie części karty portalu Marketplace w formularzu nowa oferta dla modułów usługi IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są wskazanych przez znak gwiazdki (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **Tytuł\***                 | Tytuł oferty. Będzie ona wyświetlana ważnej w portalu marketplace.  Składać z maksymalnie 50 znaków. <!--ADD PICTURE IN ACTION-->|
| **Podsumowanie\***               | Krótkie podsumowanie oferty. Składać z maksymalnie 100 znaków. <!--ADD PICTURE IN ACTION-->|
| **Długie podsumowanie\***          | Już podsumowanie ofertę (chociaż może być taka sama jak **podsumowania**).  Maksymalna długość 256 znaków. <!--ADD PICTURE IN ACTION-->|
| **Opis elementu\***           | Opis oferty.  Maksymalną długość znaków 3000 obsługuje prosty format HTML.<br/> Musi on zawierać *minimalne wymagania sprzętowe* akapitu na dole. Na przykład: <br/> <p><u>Minimalne wymagania sprzętowe:</u> X64 systemu Linux i OS arm32, 1 GB pamięci RAM, 500 Mb pamięci masowej</p>
| **Identyfikator marketingu\***  | Unikatowy adres URL, aby skojarzyć tę ofertę, obejmują zazwyczaj organizacji i nazwę rozwiązania, maksymalnej długości 50 znaków.  Na przykład: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Identyfikatory subskrypcji (wersja zapoznawcza)** | Dodaj jeden do 100 identyfikatorów subskrypcji podglądy. Te subskrypcje biały na liście mają dostęp do tej oferty, po opublikowaniu, zanim usługa zostanie wprowadzona na żywo. |
| **Przydatne linki**          | Wielokrotnego wyboru biznesowe i techniczne kategorie, które oferują może być najlepiej skojarzony.  Maksymalnie 10 dozwolone. Upewnij się dodać co najmniej jedno łącze do dokumentacji i jedno połączenie zgodne z urządzeniem usługi IoT Edge [Azure IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/). |
| **Sugerowane kategorie\*** | Wybierz maksymalnie pięć kategorii. Będą one wyświetlane na stronie szczegółów produktu. Na stronach Przeglądaj wszystkie moduły usługi IoT Edge zostaną wyświetlone w obszarze *Internet of Things \> moduł usługi IoT Edge* kategorii.|
|  |  |


### <a name="offer-example"></a>Przykład oferty

 W poniższych przykładach pokazano sposób oferty **tytuł**, **Podsumowanie**, **opis**, **logo**, i **zrzutyekranu** pola są wyświetlane w różnych widokach.

 
#### <a name="on-the-azure-marketplace-website"></a>W witrynie Azure Marketplace:

- Podczas przeglądania oferty:

    ![Jak oferty w witrynie marketplace są wyświetlane w witrynie Azure Marketplace — przejście](./media/iot-edge-module-ampdotcom-card.png)

- Przyglądając się szczegóły oferty:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas wyszukiwania szczegóły produktu w witrynie sieci Web](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>W witrynie Azure portal:

- Podczas przeglądania oferty:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas przeglądania portalu Azure #1](./media/iot-edge-module-portal-browse.png)

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas przeglądania portalu Azure #2](./media/iot-edge-module-portal-product-picker.png)

- Podczas wyszukiwania dla oferty:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas wyszukiwania w witrynie Azure portal](./media/iot-edge-module-portal-search.png)

- Przyglądając się szczegóły oferty:

    ![Jak moduł usługi IoT Edge zostaną wyświetlone podczas wyszukiwania szczegóły produktu w portalu](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marketing artefaktów

Ta sekcja zawiera następujące podsekcje: **Logo**, **zrzut ekranu**, i **wideo**. 

>[!Note]
>Logo są jedynymi wymagane marketingowych artefaktów, jednak wszystkie są zdecydowanie zaleca się najlepsze odwołania klienta.

![Marketing artefaktów części karty portalu Marketplace w formularzu nowa oferta dla maszyn wirtualnych](./media/publishvm_009.png)

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos*  | Zobacz, że przechwytuje poprzedni ekran, aby zobaczyć, jak i gdzie będą używane z logo.  |
| **Małe\***                 | format PNG 40 x 40 pikseli                                                     |
| **Średni\***                | format PNG 90 x 90 pikseli                                                     |
| **Duże\***                 | format PNG 115 x 115 pikseli                                                  |
| **Szerokie\***                  | format PNG 255 x 115 pikseli                                                   |
| **Element Hero**                  | format PNG 815 x 290 pikseli.  Opcjonalne, jednak po przekazany, nie można usunąć ikonę elementu hero. |
| *Zrzuty ekranu*  | Zrzuty ekranu są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne przedstawianie działanie modułu usługi IoT Edge i sposób jej działania. Można wyświetlić diagramy architektury, lub użyj ilustracje wielkości liter dla wystąpienia. Opcjonalne, ale maksymalnie pięć zrzuty ekranu na jednostki SKU. |
| **Nazwa**                  | Nazwa lub tytuł. Składać z maksymalnie 100 znaków.                             |
| **Obraz**                 | Obraz przechwytywania ekranie, format PNG 533 x 324 pikseli                               |
| *Filmy wideo*  | Filmy wideo są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne przedstawianie działanie modułu usługi IoT Edge i sposób jej działania. |
| **Nazwa**                  | Nazwa lub tytuł. Składać z maksymalnie 100 znaków.                             |
| **Link**                  | Adres URL filmu wideo, w serwisie YouTube lub Vimeo                                        |
| **Miniatura**             | format PNG 533 x 324 pikseli                                                     |
|  |  |


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Wszystkie logo, które są przekazywane do portalu Cloud Partner powinien postępuj zgodnie z wytycznymi:

*  Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.
*  Kolory motywu w witrynie Azure Portal są białe i czarne. Należy unikać używania tych kolorów jako kolor tła dla Twojego logo. Użyj koloru, który spowoduje, że Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe. Jeśli korzystasz z przezroczystym tłem, upewnij się, że logo/tekstu nie są białe, black lub niebieski.
*  Nie używaj gradientu tła na logo.
*  Należy unikać wprowadzania tekstu — nawet Twoja firma lub marką — na logo. Wygląd i działanie logo powinno być "płaską" i unikać gradientów.
*  Nie rozciąganie logo.


#### <a name="hero-logo"></a>Logo Duży obraz

Logo usługi Hero jest opcjonalne.

>[!Important]
>Po przekazaniu Hero logo, nie można usunąć.

Użyj następujących wytycznych dotyczących Hero logo: 

*  Czarny, biały i przezroczyste tło nie są dozwolone.
*  Należy unikać używania żadnych jasny kolor tła logo.  Nazwa wyświetlana wydawcy, plan tytuł oferty długie podsumowanie są wyświetlane w kolorze białym czcionki i musi być na tle.
*  Należy unikać większość tekstu podczas projektowania logo. Nazwa wydawcy, tytuł planu, oferty długie Podsumowanie i przycisk Utwórz są osadzane programowo wewnątrz logo gdy oferty na liście. 
* Obejmują nieużywane miejsce prostokątny po prawej stronie logo usługi hero. To puste miejsce jest 415 x 100 pikseli i przesunięcie od lewej w 370 pikseli.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Ta sekcja umożliwia skonfigurować opcje pobierania klient prowadzi wygenerowany z Twoich ofert w portalu Azure Marketplace. Można wybrać następujące opcje magazynu z listy rozwijanej.

* **Brak** — ustawienie domyślne, informacje o potencjalnym kliencie nie są zbierane.
* Tabela platformy Azure — zapisywane w tabeli platformy Azure, określona przez ciąg połączenia.
* Dynamics CRM Online — zapisane [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) wystąpienia, określonego przez adres URL i uwierzytelniania poświadczeń.
* Punkt końcowy HTTPS - zapisywane w określonym punkcie końcowym HTTPS jako ładunek JSON.
* Marketo - zapisywane do określonego [Marketo](https://www.marketo.com/) wystąpienia, określonego przez identyfikator serwera, identyfikator munchkin i identyfikator formularza.
* SalesForce — zapisane [Salesforce](https://www.salesforce.com/) określona baza danych, za pomocą identyfikatora obiektu.

Po pomyślnym opublikowaniu oferty, zweryfikowaniu połączenia potencjalnych klientów i potencjalnego klienta test jest automatycznie przesyłany do miejsca docelowego, który został skonfigurowany. 

>[!Note]
>Informacje o potencjalnych klientów powinny być stale zarządzane i te ustawienia powinny być na bieżąco aktualizowane przy każdym zmian do architektury zarządzania klienta.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Informacje prawne

Ta sekcja umożliwia dostarczanie dwa dokumenty prawne, które są wymagane dla każdej oferty: Zasady ochrony prywatności i warunki użytkowania.

|  **Pole**                    |     **Opis**                                                          |
|  ---------                    |     ---------------                                                          |
| **Adres URL zasad ochrony prywatności\***      | Adres URL do zasad zachowania opublikowane                                            |
| **Użyj standardowych Constract\***  | Określa, czy użyć standardowego szablonu umowy firmy Microsoft.  Aby uzyskać więcej informacji, zobacz [standardowej umowy](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Warunki użytkowania\***            | *Warunki użytkowania* jako inline proste HTML lub link do Twoich przesłanych warunków strony     |
|  |  |


## <a name="next-steps"></a>Kolejne kroki

Użyj [obsługuje](./cpp-support-tab.md) kartę, aby zapewnić technicznych i użytkownika, zasoby pomocy technicznej oferty.
