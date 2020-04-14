---
title: Karta Marketplace maszyny wirtualnej w portalu partnerów w chmurze dla portalu Azure Marketplace
description: W tym artykule opisano kartę Marketplace używaną do tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 156a405d93aeac27f27fe617e1a5485044309846
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273821"
---
# <a name="virtual-machine-marketplace-tab"></a>Karta Marketplace maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty maszyny wirtualnej platformy Azure,](https://aka.ms/CreateAzureVMoffer) aby zarządzać zmigrowanymi ofertami.

Karta **Marketplace** na stronie **Nowa oferta** umożliwia dostarczanie potencjalnym klientom informacji marketingowych, sprzedażowych i prawnych oraz umów oraz zarządzanie potencjalnymi klientami generowanymi z rynku. Ten długi formularz jest podzielony na cztery sekcje: **Przegląd**, **Artefakty marketingowe,** **Zarządzanie ołowiem**i **Prawne**.


## <a name="overview-section"></a>Sekcja Omówienie
W tej sekcji należy wprowadzić ogólne informacje o ofercie portalu Azure Marketplace.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest ona wymagana.

![Omówienie sekcji na karcie Marketplace dla maszyn wirtualnych](./media/publishvm_008.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są oskarżane gwiazdką (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **Tytuł\***                 | Tytuł oferty, często długa, formalna nazwa. Ten tytuł będzie wyświetlany w widocznym miejscu na rynku.  Maksymalna długość 50 znaków. |
| **Podsumowanie\***               | Krótki cel lub funkcja rozwiązania.  Maksymalna długość 100 znaków. |
| **Długie podsumowanie\***          | Cel lub funkcja roztworu.  Maksymalna długość 256 znaków. |
| **Opis\***           | Opis roztworu.  Maksymalna długość 3000 znaków, obsługuje proste formatowanie HTML. |
| **Kanał sprzedawcy CSP firmy Microsoft\*** | Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](../../cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft. |
| **Identyfikator marketingowy\***  | Unikatowy adres URL do skojarzenia z tą ofertą, zazwyczaj obejmuje nazwę organizacji i rozwiązania, maksymalną długość 50 znaków.  Przykład: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Identyfikatory subskrypcji w wersji zapoznawczej\*** | Dodaj jeden do 100 identyfikatorów subskrypcji podglądu. Te subskrypcje z białą listą będą miały dostęp do oferty po jej opublikowaniu, zanim zostanie ona wyeksponowana. |
| **Przydatne linki**          | Dodaj adresy URL do dokumentacji, informacji o wersji, często zadawanych pytań i tak dalej. |
| **Sugerowane kategorie\*** | Wybierz maksymalnie dwie (2) kategorie, w tym kategorię podstawową i drugorzędną (opcjonalnie). Wybierz maksymalnie dwie (2) podkategorie dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli nie wybrano żadnej podkategorii, oferta będzie nadal wykrywalna tylko w wybranej kategorii. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sekcja Artefakty marketingowe

Ta druga sekcja jest podzielona na trzy podsekcje: **Logos**, **Screenshot**i **Filmy.** Logo są jedynymi wymaganymi artefaktami marketingowymi, jednak wszystkie są wysoce zalecane dla najlepszego odwołania klienta. 

![Sekcja Artefakty marketingowe na karcie Marketplace w formularzu Nowa oferta dla maszyn wirtualnych](./media/publishvm_009.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są oskarżane gwiazdką (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| *Logo*  |  |
| **Small\***                 | 40x40 pikseli .ico bitmapa                                                      |
| **Medium\***                | 90x90 pikseli .ico bitmapa                                                      |
| **Large\***                 | 115x115 pikseli .ico bitmapa                                                   |
| **Szeroki\***                  | 255x115 pikseli .ico bitmapa                                                    |
| **Bohater**                  | 815x290 bitmapa.  Opcjonalnie jednak po przesłaniu ikony bohatera nie można usunąć. |
| *Zrzuty ekranu*  | Opcjonalnie, ale maksymalnie pięć zrzutów ekranu na jednostkę SKU. |
| **Nazwa**                  | Nazwa lub tytuł <!-- TODO - max char length? none specified in UI -->                               |
| **Image (Obraz)**                 | Obraz przechwytywania ekranu, 533x324 pikseli                                         |
| *Filmy wideo*  |  |
| **Nazwa**                  | Nazwa lub tytuł  <!-- TODO - max char length? -->                              |
| **Link**                  | Adres URL filmu, hostowany w YouTube lub Vimeo                                        |
| **Miniaturę**             | Mapa bitowa 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Wszystkie logo przesłane do portalu Cloud Partner Portal powinny być zgodne z wytycznymi:

*  Projekt platformy Azure ma prostą paletę kolorów. Zachowaj niską liczbę kolorów podstawowych i pomocniczych na logo.
*  Kolory motywu witryny Azure portal są białe i czarne. Dlatego unikaj używania tych kolorów jako koloru tła logo. Użyj koloru, który sprawi, że twoje logo będzie widoczne w witrynie Azure portal. Zalecamy proste kolory podstawowe. Jeśli używasz przezroczystego tła, upewnij się, że logo/tekst nie są białe, czarne lub niebieskie.
*  Nie używaj gradientowego tła na logo.
*  Unikaj umieszczania na logo tekstu — nawet firmy lub marki. Wygląd i działanie logo powinno być "płaskie" i powinno unikać gradientów.
*  Nie rozciągaj logo.

#### <a name="hero-logo"></a>Logo Duży obraz

Logo Hero jest opcjonalne; jednak po przesłaniu ikona bohatera nie może zostać usunięta.  Ikona logo bohatera powinna być zgodna z wytycznymi:

*  Czarne, białe i przezroczyste tła nie są dozwolone dla ikon bohaterów.
*  Unikaj używania jakiegokolwiek jasnego koloru jako tła ikony bohatera.  Nazwa wyświetlana programu Publisher, tytuł planu i długie podsumowanie oferty są wyświetlane w kolorze białej czcionki i muszą wyróżniać się na tle.
*  Unikaj używania większości tekstu podczas projektowania logo bohatera.  Nazwa wydawcy, tytuł planu, długie podsumowanie oferty i przycisk tworzenia są osadzone programowo wewnątrz ikony bohatera, gdy oferta zostanie wyświetlona. 
* Dołącz nieużywany prostokąt po prawej stronie ikony bohatera o wymiarach 415x100 pikseli i przesunięcie 370 pikseli od lewej.  

Na przykład następująca ikona bohatera jest dla usługi Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Przykładowa ikona bohatera usługi Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Przykład informacji marketingowych 

Na poniższej ilustracji pokazano, jak informacje marketingowe są wyświetlane na stronie produktu głównego systemu Microsoft Windows Server.

![Przykładowa strona produktu dla systemu Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Sekcja Zarządzanie potencjalnymi klientami

Trzecia sekcja umożliwia zbieranie potencjalnych klientów potencjalnych klientów generowanych z ofert portalu Azure Marketplace. Oferuje następujące opcje magazynu (z listy rozwijanej) dla tych informacji o potencjalnych klientach.

* **Brak** — domyślne informacje o potencjalnych klientach nie są zbierane.
* Tabela platformy Azure — zapisywana w tabeli platformy Azure określona przez parametry połączenia.
* Dynamics CRM Online — zapisywane w wystąpieniu [usługi Microsoft Dynamics 365 Online,](https://dynamics.microsoft.com/) określonym przez adres URL i poświadczenia uwierzytelniania.
* Punkt końcowy HTTPS — zapisywany w określonym punkcie końcowym HTTPS jako ładunek JSON.
* Marketo - napisane do określonego wystąpienia [Marketo,](https://www.marketo.com/) określone przez identyfikator serwera, identyfikator munchkin i identyfikator formularza.
* Salesforce — zapisywane w bazie danych [Salesforce,](https://www.salesforce.com/) określone przez identyfikator obiektu.

Po pomyślnym opublikowaniu oferty połączenie potencjalnego klienta jest sprawdzane, a potencjalny klient testowy jest automatycznie wysyłany do skonfigurowanego miejsca docelowego. Informacje o potencjalnych klientach powinny być stale zarządzane, a te ustawienia powinny być niezwłocznie aktualizowane za każdym razem, gdy wprowadzane są zmiany w architekturze zarządzania klientami.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Sekcja prawna

Ta ostatnia sekcja umożliwia dostarczenie wymaganych dokumentów prawnych wymaganych dla każdej oferty.  

|  **Pole**                    |     **Opis**                                        |
|  ---------                    |     ---------------                                        |
| **Adres URL polityki prywatności\***      | Adres URL do opublikowanej polityki prywatności                          |
| **Czy korzystać ze standardowej umowy?\***  |   |
| **Warunki użytkowania\***            | jako zwykły tekst lub prosty kod HTML.                       |
|  |  |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie [Pomoc techniczna](./cpp-support-tab.md) udostępnisz zasoby pomocy technicznej i pomocy technicznej dla swojej oferty.
