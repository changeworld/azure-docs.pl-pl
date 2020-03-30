---
title: Karta Jednostki SKU maszyn wirtualnych w portalu partnerów w chmurze dla portalu Azure Marketplace
description: W tym artykule opisano kartę Jednostki SKU używane do tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 49f1de5128325b2884ea76b010727be45f1b195d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288890"
---
# <a name="virtual-machine-skus-tab"></a>Karta Jednostki SKU maszyny wirtualnej

Karta **Jednostki SKU** na stronie **Nowa oferta** umożliwia utworzenie co najmniej jednej jednostki SKU i skojarzenie ich z nową ofertą.  Różne jednostki SKU mogą rozróżniać rozwiązanie według zestawów funkcji, typów obrazów maszyn wirtualnych, przepływności lub skalowalności, modeli rozliczeń lub innej cechy.


## <a name="create-a-sku"></a>Tworzenie jednostki SKU

Początkowo nowa oferta nie będzie miała żadnych powiązanych jednostek SKU, więc utworzysz ją, klikając **pozycję Nowa jednostka SKU**.

![Przycisk Nowa jednostka SKU na karcie Nowa oferta dla maszyn wirtualnych](./media/publishvm_005.png)

<br/>

Zostanie wyświetlone okno dialogowe **Nowa jednostka SKU.**  Wprowadź identyfikator nowej jednostki SKU, a następnie kliknij przycisk **OK**. (Poniżej znajdują się konwencje nazewnictwa identyfikatorów).  Na karcie **Jednostki SKU** będą teraz wyświetlane pola dostępne do edycji.    Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest ona wymagana.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Karta SKU w formularzu Nowa oferta dla maszyn wirtualnych](./media/publishvm_006.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie tych pól.  Wymagane pola są oskarżane gwiazdką (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
|  *Ustawienia jednostki SKU*   |    |
| **Identyfikator jednostki SKU\***       | Identyfikator dla tej jednostki SKU.  Ta nazwa ma maksymalnie 50 znaków, składających się z małych liter alfanumeryczne znaki lub myślniki (-), ale nie może kończyć się kreską.  Nie można go zmodyfikować po opublikowaniu oferty.  |
|  *SKU Szczegóły*   |  |
| **Tytuł\***        | Przyjazna nazwa oferty do wyświetlenia na rynku. Maksymalna długość 50 znaków. |
| **Podsumowanie\***      | Zwięzły opis oferty do wyświetlenia na rynku. Maksymalna długość 100 znaków. |
| **Opis\***  | Opis tekstu, który zawiera bardziej szczegółowe wyjaśnienie oferty.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ukryj tę jednostkę SKU\*** | Wskazuje, czy jednostka SKU powinna być widoczna w portalu marketplace dla klientów.  Możesz chcieć ukryć jednostkę SKU, jeśli chcesz, aby była dostępna tylko za pośrednictwem szablonów rozwiązań, a nie do zakupu indywidualnie.  Może to być również przydatne w przypadku wstępnych testów lub ofert tymczasowych lub sezonowych. |
| **Dostępność chmury\*** | Określa, w których chmurach jednostka SKU powinna być dostępna.  Wartością domyślną jest publiczna wersja platformy Azure.  Microsoft Azure Government to chmura społeczności rządowej z kontrolowanym dostępem dla rządów federalnych, stanowych, lokalnych lub plemiennych stanów USA oraz ich certyfikowanych partnerów.  Aby uzyskać więcej informacji na temat chmury rządów, zobacz [Zapraszamy do platformy Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Czy jest to prywatna jednostka SKU?\*** | Wskazuje, czy jednostka SKU jest prywatna czy publiczna. Wartość domyślna to **Nie** (publiczne).  Aby uzyskać więcej informacji, zobacz [jednostki SKU publiczne i prywatne](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Dostępność kraju/regionu\*** | Określa, w których krajach lub regionach świata jednostka SKU będzie dostępna do zakupu. Wybierz co najmniej jeden region/kraj. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Cennik*   |  |
| **Model licencji\***| Standardowy model rozliczeń do użycia.  Jeśli wybierzesz **jednostkę SKU rozliczaną miesięcznie opartą na użyciu,** zostanie otwarta sekcja akordeonu, aby określić szczegóły cen dla rdzenia i czy chcesz zaoferować bezpłatny okres próbny.  Ta sekcja umożliwia również eksportowanie i importowanie tego harmonogramu cenowego do programu Excel. Aby uzyskać więcej informacji, zobacz [Opcje rozliczeń w portalu Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Obrazy maszyn wirtualnych*   |  |
| **Rodzina systemów operacyjnych\*** | Wskazuje, czy maszyna wirtualna rozwiązania jest oparta na systemie Windows lub Linux. |
| **Wybierz typ systemu operacyjnego** | Określonego dostawcy lub zwolnienie określonego systemu operacyjnego. |
| **Nazwa przyjazna systemowi operacyjnym\*** | Nazwa systemu operacyjnego ma być wyświetlana klientom.  |
| **Zalecane rozmiary maszyn wirtualnych\*** | Umożliwia wybór maksymalnie sześciu zalecanych rozmiarów maszyn wirtualnych ze standardowej listy.  Ta lista jest przekazywana do witryny Azure portal i witryn firmy Microsoft.  Pierwszy rozmiar maszyny Wirtualnej na tej liście, który jest prawidłowy (dla tej subskrypcji klienta, regionu, strefy itp.) jest ustawiony jako domyślny dla tego potencjalnego klienta.  Użytkownik może zmienić ten rozmiar na dowolny zgodny z obrazem rozwiązania. | 
| **Otwarte porty**| Porty do otwarcia i protokół do obsługi jednostki SKU.  Te konfiguracje muszą być zgodne z siecią wirtualną skonfigurowany dla sieci maszyny Wirtualnej rozwiązania. Te ustawienia wchodzą w życie podczas wdrażania maszyny Wirtualnej. Jednak ustawienia portu można zmodyfikować po opublikowaniu jednostki SKU. Aby uzyskać więcej informacji, zobacz [Jak otwierać porty do maszyny wirtualnej z portalu Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Następujące domyślne mapowania sieci są dodawane do wszystkich maszyn wirtualnych. &emsp;Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Wersja dysku**  | Skojarzona maszyna wirtualna rozwiązania, określona przez numer wersji dysku i adres URL dysku. Wersja dysku musi być w formacie wersji `<major>.<minor>.<patch>` [semantycznej:](https://semver.org/) .  Adres URL jest identyfikatorem URI sygnatury dostępu współdzielonego utworzonego dla vhd systemu operacyjnego.  Chociaż można dodać maksymalnie osiem wersji dysku na jednostkę SKU, tylko najwyższy numer wersji dysku dla jednostki SKU pojawi się w portalu Azure Marketplace. Pozostałe wersje będą widoczne tylko za pośrednictwem interfejsów API.  <!--TD: Add more specific link to API --> <br/> Sekcja **Akordeon nowego dysku danych** umożliwia podłączenie do 15 dysków danych do maszyny Wirtualnej.  Po opublikowaniu jednostki SKU z daną wersją maszyny Wirtualnej i skojarzonymi dyskami danych nie można zmodyfikować tej konfiguracji.  Jeśli do jednostki SKU zostaną dodane dodatkowe wersje maszyn wirtualnych, muszą one również obsługiwać taką samą liczbę dysków z danymi. <br/> Jeśli nie utworzono obrazów maszyn wirtualnych opartych na platformie Azure, możesz dodać to pole później.  Aby uzyskać informacje dotyczące tworzenia skojarzonego zasobu maszyny Wirtualnej, zobacz sekcję [Tworzenie zasobów technicznych maszyn wirtualnych](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kliknij **przycisk Zapisz,** aby zapisać swoje postępy. W następnej karcie określ, czy oferta obsługuje [jazdę próbną](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Dodatkowe kwestie cenowe

Model cenowy opisany powyżej jest podstawowym opisem.  Zmiany mogą ulec zmianie i mogą mieć na nie wpływ lokalne lub regionalne przepisy podatkowe oraz zasady cenowe firmy Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Dodano nowe rozmiary rdzeni 7/2/2019

Wydawcy maszyn wirtualnych zostali powiadomieni 2 lipca 2019 r. o dodaniu nowych cen nowych rozmiarów maszyn wirtualnych platformy Azure (na podstawie liczby rdzeni).  Nowe ceny są dla podstawowych rozmiarów 10, 44, 48, 60, 120, 208 i 416.  W przypadku istniejących ofert maszyn wirtualnych nowe ceny dla tych rozmiarów rdzeni zostały automatycznie obliczone na podstawie cen bieżących.  Wydawcy mają czas do 1 sierpnia 2019 r., aby przejrzeć dodatkowe ceny i wprowadzić wszelkie pożądane zmiany.  Po tej dacie, jeśli wydawca nie został jeszcze ponownie opublikowany, automatycznie obliczone ceny dla tych nowych rozmiarów rdzenia zostaną zastosowane.


### <a name="simplified-currency-pricing"></a>Uproszczone ceny walut

Od 1 września 2018 r. do portalu zostanie dodana nowa sekcja o nazwie **Uproszczone ceny walut.** Firma Microsoft usprawnia działalność portalu Azure Marketplace, umożliwiając bardziej przewidywalne ceny i kolekcje od klientów z całego świata. Usprawnienie to będzie obejmować zmniejszenie liczby walut, w których wystawiamy faktury klientom.  Aby uzyskać więcej informacji, zobacz [Aktualizowanie istniejącej oferty maszyn wirtualnych w portalu Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Dodatkowe informacje na temat podatków i cen

* Firma Microsoft klasyfikuje niektóre kraje/regiony jako kraje, w których *podatek został nadajony.*  W takich krajach/regionach firma Microsoft pobiera podatki od klientów, a następnie płaci (przekazuje) podatki rządowi.  W innych krajach/regionach partnerzy są zazwyczaj odpowiedzialni za pobieranie podatków od swoich klientów i płacenie podatków rządowi. Jeśli zdecydujesz się na sprzedaż w tych ostatnich krajach/regionach, musisz mieć możliwość obliczania i płacenia podatków lokalnych.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Ceny nie są zmienne po uruchomieniu oferty. Jednak nadal można dodawać lub usuwać obsługiwane regiony. 
* Firma Microsoft pobiera od klienta standardowe opłaty za użycie maszyny wirtualnej platformy Azure oprócz zaplanowanych opłat za jednostkę SKU.
* Ceny są ustalane dla wszystkich regionów w walucie lokalnej według dostępnych kursów walut w momencie ustalania cen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Aby ustawić cenę każdego regionu indywidualnie, eksportuj arkusz kalkulacyjny cen, zastosuj ceny niestandardowe, a następnie zaimportuj. 


## <a name="next-steps"></a>Następne kroki

Opcjonalnie określ informacje [o dysku testowym,](./cpp-test-drive-tab.md) jeśli obsługujesz tę funkcję; w przeciwnym razie podajesz dane [rynkowe](./cpp-marketplace-tab.md) dla swojej oferty.
