---
title: Karty jednostki SKU maszyny wirtualnej w portalu Cloud Partner portalu Azure Marketplace
description: W tym artykule opisano na karcie jednostki SKU używanego podczas tworzenia oferty maszyny wirtualnej w witrynie Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: e8148e3a26a236039736dede5a7fbc79075731ce
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938144"
---
# <a name="virtual-machine-skus-tab"></a>Karty jednostki SKU maszyny wirtualnej

**Jednostki SKU** karcie **nowa oferta** strona pozwala na tworzenie jednego lub więcej jednostek SKU i kojarzyć je z nowej oferty.  Różne jednostki SKU, mogą oni odróżnić rozwiązania przez zestawy funkcji, typy obrazów maszyny Wirtualnej, przepustowości lub skalowalność, modelami rozliczeń lub pewne inne cechy.


## <a name="create-a-sku"></a>Tworzenie jednostki SKU

Początkowo nowa oferta nie będzie żadnych skojarzonych jednostek SKU, więc będzie można utworzyć, klikając **nowej jednostki SKU**.

![Przycisk Nowe jednostki SKU na karcie nowa oferta dla maszyn wirtualnych](./media/publishvm_005.png)

<br/>

**Nowej jednostki SKU** zostanie wyświetlone okno dialogowe.  Wprowadź identyfikator nowej jednostki SKU, a następnie kliknij przycisk **OK**. (Zobacz poniżej dotyczących konwencji nazewnictwa identyfikator).  **Jednostki SKU** kartę będą teraz wyświetlane pola, które są dostępne do edycji.    Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Karty jednostki SKU w formularzu nowa oferta dla maszyn wirtualnych](./media/publishvm_006.png)

W poniższej tabeli opisano przeznaczenie zawartości i formatowania z tych pól.  Wymagane pola są wskazanych przez znak gwiazdki (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
|  *Ustawienia jednostki SKU*   |    |
| **IDENTYFIKATOR JEDNOSTKI SKU\***       | Identyfikator dla tej jednostki SKU.  Ta nazwa może zawierać maksymalnie 50 znaków, składający się z małych znaków alfanumerycznych i kresek (-), ale nie może kończyć się kreską.  Nie można zmodyfikować, po opublikowaniu oferty.  |
|  *Szczegóły jednostki SKU*   |  |
| **Tytuł\***        | Przyjazna nazwa dla oferty do wyświetlenia w portalu marketplace. Składać z maksymalnie 50 znaków. |
| **Podsumowanie\***      | Zwięzły opis oferty do wyświetlenia w portalu marketplace. Składać z maksymalnie 100 znaków. |
| **Opis elementu\***  | Tekst opisu, który zawiera bardziej szczegółowy opis oferty.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ukryj tej jednostki SKU\*** | Wskazuje, czy jednostka SKU powinny być widoczne w portalu marketplace dla klientów.  Można ukryć jednostki SKU, jeśli chcesz tylko on dostępny tylko za pomocą szablonów rozwiązań, a nie do zakupu indywidualnie.  Mogą być również przydatne w początkowej fazie testowania lub tymczasowej lub sezonowych ofert. |
| **Dostępności w chmurze\*** | Określa, w których chmurach powinny być dostępne jednostki SKU.  Wartość domyślna to wersji publicznej platformy Azure.  Microsoft Azure dla instytucji rządowych to chmura dla instytucji rządowych i społeczności, przy użyciu kontrolowanego dostępu dla nas federalnych, stan, lokalne lub plemienne instytucje rządowe i ich certyfikowanych partnerów.  Aby uzyskać więcej informacji na temat chmury dla instytucji rządowych, zobacz [Witamy na platformie Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **To jest jednostka SKU prywatne?\*** | Wskazuje, czy jednostka SKU jest prywatny lub publiczny. Wartość domyślna to **nie** (publicznego).  Aby uzyskać więcej informacji, zobacz [publiczne i prywatne jednostki SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Dostępność Kraj/Region\*** | Określa, w jakich krajach lub regionach świata jednostki SKU będzie dostępna do zakupu. Wybierz co najmniej jeden region/kraj. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Cennik*   |  |
| **Model licencji\***| Standardowy model rozliczeń do użycia.  Jeśli wybierzesz **opartej na użyciu miesiąc rozliczane jednostki SKU**, kontrolki accordion sekcji zostanie otwarta umożliwiają określenie szczegółów cennika za rdzeń, i określ, czy chcesz oferuje bezpłatny okres próbny.  Ta sekcja umożliwia eksportowanie i importowanie ujęty w harmonogramie cen do programu Excel. Aby uzyskać więcej informacji, zobacz [opcje rozliczeń w witrynie Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Obrazy maszyn wirtualnych*   |  |
| **Rodzina systemów operacyjnych\*** | Wskazuje, czy jest rozwiązania maszyny Wirtualnej, systemem Windows lub Linux. |
| **Wybierz typ systemu operacyjnego** | Określonego dostawcy lub wersji określonego systemu operacyjnego. |
| **Przyjazna nazwa OS\*** | Nazwa systemu operacyjnego, który ma być wyświetlany dla klientów.  |
| **Zalecane rozmiary maszyn wirtualnych\*** | Umożliwia wybór maksymalnie sześć zalecane rozmiary maszyn wirtualnych z listy standardowych.  Te zalecenia są wyświetlane dla potencjalnych klientów, są one mogą określić dowolny rozmiar maszyny Wirtualnej, która jest zgodna z obrazem rozwiązania. | 
| **Otwieranie portów**| Porty, aby otworzyć i protokołu w celu obsługi dla jednostki SKU.  Te konfiguracje musi być zgodna sieć wirtualną, skonfigurowanych dla sieci maszyny Wirtualnej rozwiązania. Te ustawienia zaczną obowiązywać podczas wdrażania maszyny Wirtualnej. Jednak ustawienia portu można zmodyfikować po opublikowaniu jednostkę SKU. Aby uzyskać więcej informacji, zobacz [jak otwierać porty do maszyny wirtualnej w witrynie Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Następujące domyślne mapowania sieci są dodawane do wszystkich maszyn wirtualnych. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Wersje dyskowe**  | Rozwiązanie skojarzone maszyny Wirtualnej, określony przez numer wersji dysku i adres URL dysku. Wersja dysku musi mieć [wersji semantycznej](https://semver.org/) format: `<major>.<minor>.<patch>`.  Adres URL jest sygnatura dostępu współdzielonego, identyfikator URI utworzony dla wirtualnego dysku twardego systemu operacyjnego.  Mimo że można dodać maksymalnie osiem wersji dysków dla jednostek SKU, tylko najwyższy dysku numer wersji dla jednostki SKU pojawią się w witrynie Azure Marketplace. Inne wersje będzie widoczny tylko za pośrednictwem interfejsów API.  <!--TD: Add more specific link to API --> <br/> **Nowy dysk danych** kontrolki accordion sekcja umożliwia dołączanie do 15 dysków z danymi do maszyny Wirtualnej.  Po opublikowaniu jednostki SKU z daną wersją maszyny Wirtualnej i dysków z danymi skojarzonego nie można zmodyfikować tę konfigurację.  Jeśli dodatkowe wersje maszyny Wirtualnej poproś o dodanie Cię do jednostki SKU, musi również obsługiwać taką samą liczbę dysków z danymi. <br/> Twoje obrazy maszyny Wirtualnej systemu Azure nie została utworzona, można dodać później zaktualizować tego pola.  Aby uzyskać informacje dotyczące tworzenia skojarzony zasób maszynę Wirtualną, zobacz sekcję [Utwórz maszynę Wirtualną, zasoby techniczne](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kliknij przycisk **Zapisz** Aby zapisać postęp. Następna karta określisz w przypadku oferty obsługuje [wersji testowej](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Dodatkowe zagadnienia dotyczące cen

Modelu cen opisanego powyżej jest podstawowy opis.  Jego jest w trakcie zmiany i mogą mieć wpływ na lokalnych lub regionalnych podatku regulacjami i zasady ustalania firmy Microsoft. 

### <a name="simplified-currency-pricing"></a>Cennik uproszczone waluty

Począwszy od września 2018 r. 1, nową sekcję o nazwie **uproszczony ceny waluty** zostanie dodany do portalu. Microsoft jest usprawnienie działalności w portalu Azure Marketplace, należy włączyć bardziej przewidywalne ceny i kolekcje z klientami na całym świecie. To usprawnienie zostaną uwzględnione w zmniejszeniu liczby walut, w których firma Microsoft faktury dla odbiorców.  Aby uzyskać więcej informacji, zobacz [aktualizacji istniejącej maszyny Wirtualnej oferty w portalu Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Dodatkowe informacje na temat podatki i ceny

* Microsoft klasyfikuje niektóre kraje/regiony jako *podatku przekazana krajach*.  W tych krajach/regionach Microsoft podatków od klientów, a następnie zapłaci podatki (publiczna) dla instytucji rządowych.  W innych krajach/regionach partnerzy są zazwyczaj odpowiedzialny za zbieranie podatków od klientów i wypłaty podatku dla rządu. Jeśli zdecydujesz się na sprzedaż w ostatnim krajach/regionach, musi mieć możliwość obliczania i płatność według lokalnych podatków.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Ceny nie są zmieniane, gdy oferty przechodzi na żywo. Nadal możesz jednak dodać lub usunąć obsługiwanych regionów. 
* Firma Microsoft nalicza za klienta standardowa maszyna wirtualna platformy Azure opłaty za użycie oprócz Twoje zaplanowane opłat za jednostki SKU.
* Ceny są ustawiane dla wszystkich regionów w walucie lokalnej na dostępne walut w momencie ustalania cen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Można ustawić indywidualnie cena każdego regionu, można eksportować arkusz cen, zastosuj niestandardowe ceny, a następnie zaimportuj. 


## <a name="next-steps"></a>Kolejne kroki

Opcjonalnie można będzie określić [wersji testowej](./cpp-test-drive-tab.md) informacji jeśli obsługują tę funkcję; w przeciwnym razie należy podać [marketplace](./cpp-marketplace-tab.md) danych oferty.
