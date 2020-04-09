---
title: Opis portalu Marketplace dla modułu usługi Azure IoT Edge | Azure Marketplace
description: Utwórz opis portalu marketplace dla modułu usługi IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: df51a408cc47908a240ceb66bf510fe3df3ce87f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983402"
---
# <a name="iot-edge-module-marketplace-tab"></a>Karta Marketplace modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami modułu IoT Edge do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty modułu IoT Edge,](https://aka.ms/AzureCreateIoT) aby zarządzać zmigrowanymi ofertami.

Karta **Marketplace** na stronie **Nowa oferta** umożliwia dostarczanie potencjalnym klientom informacji marketingowych, sprzedażowych i prawnych oraz umów oraz zarządzanie potencjalnymi klientami generowanymi z rynku. Ten długi formularz jest podzielony na cztery sekcje: **Przegląd**, **Artefakty marketingowe,** **Zarządzanie ołowiem**i **Prawne**.


## <a name="overview"></a>Omówienie

W tej sekcji należy wprowadzić ogólne informacje o ofercie portalu Azure Marketplace.  Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest ona wymagana.

![Sekcja Przegląd na karcie Marketplace w formularzu Nowa oferta dla modułów IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są oskarżane gwiazdką (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **Tytuł\***                 | Tytuł oferty. Będzie on wyświetlany w widocznym miejscu na rynku.  Maksymalna długość 50 znaków. <!--ADD PICTURE IN ACTION-->|
| **Podsumowanie\***               | Krótkie podsumowanie oferty. Maksymalna długość 100 znaków. <!--ADD PICTURE IN ACTION-->|
| **Długie podsumowanie\***          | Dłuższe podsumowanie oferty (choć może być takie samo jak **podsumowanie).**  Maksymalna długość 256 znaków. <!--ADD PICTURE IN ACTION-->|
| **Opis\***           | Opis oferty.  Maksymalna długość 3000 znaków, obsługuje proste formatowanie HTML.<br/> Musi zawierać *minimalne wymagania sprzętowe* akapit na dole. na przykład: <br/> <p><u>Minimalne wymagania sprzętowe:</u> Linux x64 i arm32 OS, 1 GB pamięci RAM, 500 Mb pamięci masowej</p>
| **Identyfikator marketingowy\***  | Unikatowy adres URL do skojarzenia z tą ofertą, zazwyczaj obejmuje nazwę organizacji i rozwiązania, maksymalną długość 50 znaków.  Przykład: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Identyfikatory subskrypcji w wersji zapoznawczej** | Dodaj jeden do 100 identyfikatorów subskrypcji podglądu. Te subskrypcje z białą listą będą miały dostęp do oferty po jej opublikowaniu, zanim zostanie ona wyeksponowana. |
| **Przydatne linki**          | Multi-wybór kategorii biznesowych i technicznych, które oferują mogą być najlepiej związane z.  Dozwolone jest maksymalnie 10. Pamiętaj, aby dodać co najmniej jedno łącze do dokumentacji i jedno łącze do zgodnych urządzeń usługi IoT Edge z [katalogu urządzeń Usługi Azure IoT](https://catalog.azureiotsolutions.com/). |
| **Sugerowane kategorie\*** | Wybierz do pięciu kategorii. Zostaną one wyświetlone na stronie szczegółów produktu. Na stronach przeglądania wszystkie moduły usługi IoT Edge są wyświetlane w kategorii *modułu Internet of Things \> usługi IoT Edge.*|
|  |  |


### <a name="offer-example"></a>Przykład oferty

 Poniższe przykłady pokazują, jak pola **Tytuł**oferty, **Podsumowanie,** **Opis,** **Logo**i Zrzuty ekranu są wyświetlane w różnych **widokach.**

 
#### <a name="on-the-azure-marketplace-website"></a>W witrynie sieci Web portalu Azure Marketplace:

- Podczas przeglądania ofert:

    ![Jak są wyświetlane oferty portalu Marketplace w witrynie sieci Web w portalu Azure Marketplace — przeglądaj](./media/iot-edge-module-ampdotcom-card.png)

- Patrząc na szczegóły oferty:

    ![Jak moduł IoT Edge pojawia się, gdy szuka szczegółów produktu na stronie internetowej](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>W witrynie sieci Web witryny sieci Web witryny azure portal:

- Podczas przeglądania ofert:

    ![Jak pokazuje się moduł usługi IoT Edge podczas przeglądania witryny Azure portal #1](./media/iot-edge-module-portal-browse.png)

    ![Jak pokazuje się moduł usługi IoT Edge podczas przeglądania witryny Azure portal #2](./media/iot-edge-module-portal-product-picker.png)

- Podczas wyszukiwania oferty:

    ![Jak pokazuje się moduł Usługi IoT Edge podczas przeszukiwania witryny Azure portal](./media/iot-edge-module-portal-search.png)

- Patrząc na szczegóły oferty:

    ![Jak pokazuje się moduł IoT Edge, gdy szuka się szczegółów produktu w portalu](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefakty marketingowe

W tej sekcji znajduje się podsekcja: **Logos**, **Screenshot**i **Filmy.** 

>[!Note]
>Logo są jedynymi wymaganymi artefaktami marketingowymi, jednak wszystkie są wysoce zalecane dla najlepszego odwołania klienta.

![Sekcja Artefakty marketingowe na karcie Marketplace w formularzu Nowa oferta dla maszyn wirtualnych](./media/publishvm_009.png)

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| *Logo*  | Zobacz poprzednie zrzuty ekranu, aby zobaczyć, jak i gdzie będą używane Twoje logo.  |
| **Small\***                 | Format PNG o rozmiarze 40x40 pikseli                                                     |
| **Medium\***                | Format PNG 90x90 pikseli                                                     |
| **Large\***                 | Format PNG o rozmiarze 115x115 pikseli                                                  |
| **Szeroki\***                  | Format PNG o 255 x 115 pikseli                                                   |
| **Bohater**                  | Format PNG o rozmiarze 815x290 pikseli.  Opcjonalnie jednak po przesłaniu ikony bohatera nie można usunąć. |
| *Zrzuty ekranu*  | Zrzuty ekranu są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem wizualnego komunikowania, co robi moduł IoT Edge i jak to działa. Można wyświetlić diagramy architektury lub ilustracje przypadków użycia na przykład. Opcjonalnie, ale maksymalnie pięć zrzutów ekranu na jednostkę SKU. |
| **Nazwa**                  | Nazwa lub tytuł. Maksymalna długość 100 znaków.                             |
| **Image (Obraz)**                 | Obraz przechwytywania ekranu w formacie PNG o rozmiarze 533 x 324 pikseli                               |
| *Filmy wideo*  | Filmy są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem wizualnego komunikowania, co robi moduł IoT Edge i jak to działa. |
| **Nazwa**                  | Nazwa lub tytuł. Maksymalna długość 100 znaków.                             |
| **Link**                  | Adres URL filmu, hostowany w YouTube lub Vimeo                                        |
| **Miniaturę**             | Format PNG o rozmiarze 533x324 pikseli                                                     |
|  |  |


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Wszystkie logo przesłane do portalu Cloud Partner Portal powinny być zgodne z wytycznymi:

*  Projekt platformy Azure ma prostą paletę kolorów. Zachowaj niską liczbę kolorów podstawowych i pomocniczych na logo.
*  Kolory motywu usługi Azure Portal są białe i czarne. Unikaj używania tych kolorów jako koloru tła logo. Użyj koloru, który sprawi, że twoje logo będzie widoczne w witrynie Azure portal. Zalecamy proste kolory podstawowe. Jeśli używasz przezroczystego tła, upewnij się, że logo/tekst nie są białe, czarne lub niebieskie.
*  Nie używaj gradientowego tła na logo.
*  Unikaj umieszczania na logo tekstu — nawet firmy lub marki. Wygląd i działanie logo powinno być "płaskie" i powinno unikać gradientów.
*  Nie rozciągaj logo.


#### <a name="hero-logo"></a>Logo Duży obraz

Logo Hero jest opcjonalne.

>[!Important]
>Po przesłaniu logo bohatera nie można go usunąć.

Użyj następujących wskazówek dotyczących logo bohatera: 

*  Czarne, białe i przezroczyste tła nie są dozwolone.
*  Unikaj używania światła jako tła dla logo.  Nazwa wyświetlana wydawcy, tytuł planu i długie podsumowanie oferty są wyświetlane w kolorze białej czcionki i muszą wyróżniać się na tle.
*  Unikaj używania większości tekstu podczas projektowania logo. Nazwa wydawcy, tytuł planu, długie podsumowanie oferty i przycisk tworzenia są osadzone programowo wewnątrz logo, gdy oferta jest wyświetlana na liście. 
* Dołącz nieużywane prostokątne miejsce po prawej stronie logo bohatera. To puste miejsce to 415x100 pikseli, a odsunięte od lewej o 370 pikseli.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Ta sekcja umożliwia skonfigurowanie opcji zbierania potencjalnych klientów generowanych z ofert portalu Azure Marketplace. Z listy rozwijanej można wybrać następujące opcje magazynowania.

* **Brak** — domyślne informacje o potencjalnych klientach nie są zbierane.
* Tabela platformy Azure — zapisywana w tabeli platformy Azure określona przez parametry połączenia.
* Dynamics CRM Online — zapisywane w wystąpieniu [usługi Microsoft Dynamics 365 Online,](https://dynamics.microsoft.com/) określonym przez adres URL i poświadczenia uwierzytelniania.
* Punkt końcowy HTTPS — zapisywany w określonym punkcie końcowym HTTPS jako ładunek JSON.
* Marketo - napisane do określonego wystąpienia [Marketo,](https://www.marketo.com/) określone przez identyfikator serwera, identyfikator munchkin i identyfikator formularza.
* Salesforce — zapisywane w bazie danych [Salesforce,](https://www.salesforce.com/) określone przez identyfikator obiektu.

Po pomyślnym opublikowaniu oferty połączenie potencjalnego klienta jest weryfikowane, a potencjalny klient testowy jest automatycznie wysyłany do skonfigurowanego miejsca docelowego. 

>[!Note]
>Informacje o potencjalnych klientach powinny być stale zarządzane, a te ustawienia powinny być niezwłocznie aktualizowane za każdym razem, gdy wprowadzane są zmiany w architekturze zarządzania klientami.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Informacje prawne

W tej sekcji można podać dwa dokumenty prawne, które są wymagane dla każdej oferty: Polityka prywatności i Warunki użytkowania.

|  **Pole**                    |     **Opis**                                                          |
|  ---------                    |     ---------------                                                          |
| **Adres URL polityki prywatności\***      | Adres URL do opublikowanej polityki prywatności                                            |
| **Użyj standardowego constractu\***  | Czy używać standardowego szablonu kontraktu firmy Microsoft.  Aby uzyskać więcej informacji, zobacz [Umowa standardowa](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Warunki użytkowania\***            | *Warunki użytkowania* jako wbudowany prosty kod HTML lub link do opublikowanej strony warunków użytkowania     |
|  |  |


## <a name="next-steps"></a>Następne kroki

Karta [Pomoc techniczna](./cpp-support-tab.md) umożliwia dostarczanie zasobów pomocy technicznej i pomocy technicznej dla swojej oferty.
