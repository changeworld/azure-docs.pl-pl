---
title: Tworzenie oferty maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Publikowanie maszyny wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a2512c488ac368205da11d79de479a888735729
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448707"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publikowanie maszyny wirtualnej w portalu Azure Marketplace

Ten artykuł zawiera kroki potrzebne do opublikowania oferty maszyny wirtualnej w portalu Azure Marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne pomoc techniczna i nietechniczna dotyczą publikowaniu maszyny wirtualnej w portalu Azure Marketplace

### <a name="technical"></a>Techniczna

-   [Techniczne wymagania wstępne dotyczące tworzenia obrazów maszyn wirtualnych w portalu Azure Marketplace](../cloud-partner-portal/virtual-machine/cpp-create-technical-assets.md)
-   [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
-   [& Utwórz test z obrazu Maszynę wirtualną systemu Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)
-   [Tworzenie i przekazywanie wirtualnego dysku twardego Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)
-   [& Utwórz test maszyny Wirtualnej z systemem Windows za pomocą obrazu](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)
-   [Jak rozwiązywać problemy z typowych problemów napotykanych podczas tworzenia wirtualnego dysku twardego](../cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues.md)
-   [Zalecenia dotyczące zabezpieczeń dla obrazów portalu Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Nietechniczne (wymagania biznesowe)

 -   Siedziba firmy (lub firmy od niej zależnej) znajduje się w kraju sprzedaży obsługiwanym przez Portal Azure Marketplace
-   Muszą mieć licencję produktu w sposób, który jest zgodny z modelami rozliczeń obsługiwanym przez Portal Azure Marketplace
-   Jesteś odpowiedzialny za wprowadzanie pomocy technicznej dostępne dla klientów w sposób rozsądny z komercyjnego punktu widzenia. Ta obsługa można bezpłatną, płatną lub zasadzie wsparcia społeczności.
-   Jesteś odpowiedzialny za Licencjonowanie oprogramowania oraz wszystkie zależności oprogramowania innych firm.
-   Możesz podać zawartość, która spełnia kryteria dotyczące oferty był wyświetlany w portalu Azure Marketplace i w witrynie Azure portal.
-   Wyrażasz zgodę na warunki usługi Azure Marketplace — zasady udziału oraz Umowie wydawcy portalu.
-   Użytkownik zobowiązuje się do przestrzegania [warunki użytkowania](https://azure.microsoft.com/support/legal/website-terms-of-use/) , [zasady zachowania poufności informacji firmy Microsoft](https://www.microsoft.com/privacystatement/default.aspx), i [umowę programu certyfikat platformy Azure Microsoft](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Po spełnieniu wszystkich wymagań wstępnych, możesz rozpocząć tworzenie oferty szablonu rozwiązania. Przed rozpoczęciem należy przejrzeć następujące oferty i informacje o jednostce SKU.

**Oferta**

Oferty aplikacji na platformie Azure odnosi się do klasy produktu, oferty od wydawcy. Jeśli masz nowy typ rozwiązania/aplikacji, który ma być dostępna w portalu Azure Marketplace, nowa oferta nie jest najlepszym rozwiązaniem. Oferta jest kolekcją jednostek SKU. Co oferty pojawia się jako własny element w witrynie Azure Marketplace.

**SKU**

Jednostka SKU to najmniejsza możliwa do kupienia jednostka oferty. W ramach tej samej klasie produktu (oferty) jednostek SKU umożliwiają rozróżnienia między różnych obsługiwanych funkcji. Na przykład oferty jest zarządzane lub niezarządzane, a różne modele rozliczeń są obsługiwane.

Dodaj wielu jednostek SKU w następujących scenariuszach:
- Chcesz obsługiwać różne modele rozliczania, takich jak Bring Your Own License (BYOL) lub płatność zgodnie z rzeczywistym użyciem (PAYG).
- Każda jednostka SKU obsługuje zestaw funkcji i każdy zestaw funkcji jest różne ceny.

Jednostka SKU pojawia się w ramach oferty nadrzędne w witrynie Azure Marketplace i jest wyświetlany jako swój własny płatnej wersji jednostki w witrynie Azure portal.

## <a name="to-create-a-new-offer"></a>Aby utworzyć nową ofertę

1.  Zaloguj się do [portalu Cloud partner](http://cloudpartner.azure.com/).

2.  Na pasku nawigacyjnym po lewej stronie wybierz **+ nowa oferta**, a następnie wybierz pozycję **maszyn wirtualnych**.

    ![Nowa oferta dla maszyn wirtualnych](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  W obszarze **nowa oferta**, wybierz opcję **edytora**.

![Nowa oferta edytora](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  W obszarze **edytora**, udostępni informacje w następujących widokach:
    - Ustawienia oferty
    - Jednostki SKU
    - Portal Marketplace
    - Każdy widok zawiera zestaw pól dla Ciebie, wypełnij pomocy technicznej. Wymagane pola są oznaczone czerwoną gwiazdką (\*)

## <a name="to-configure-offer-settings"></a>Aby skonfigurować ustawienia oferty

1. Skonfiguruj następujące opcje **oferują tożsamości** pól w ustawieniach oferty.

    **Identyfikator oferty**

     Unikatowy identyfikator oferty w ramach profilu wydawcy. Ten identyfikator jest widoczny w adresach URL produktu, szablony usługi Azure Resource Manager i raporty rozliczeń. Można używać tylko małych znaków alfanumerycznych i kresek (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. Na przykład, jeśli Wydawca **contoso** publikuje ofertę za pomocą Identyfikatora oferty **maszyny wirtualnej na przykład**, pojawia się w witrynie Azure marketplace jako **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >To pole jest zablokowane, gdy oferty przechodzi na żywo.

    **Identyfikator wydawcy**

    Na liście rozwijanej profilem wydawcy. Wybierz profil, który chcesz opublikować ofertę w obszarze. 
    >[!Note]
    >To pole jest zablokowane, gdy oferty przechodzi na żywo.

    **Nazwa**

    Nazwa wyświetlana oferty. Ta nazwa jest wyświetlana w portalu Azure Marketplace i w witrynie Azure portal. Może zawierać maksymalnie 50 znaków. Użyj poniższych wskazówek Nazwa oferty:
    -  Warto umieścić tu rozpoznawalną nazwę marki produktu. 
    - Nie dołączaj nazwę swojej firmy, chyba że jak oferty są sprzedawane.
    - Jeśli ta oferta jest marketingu w swojej własnej witryny sieci Web, upewnij się, że nazwa jest taka sama jak nazwa w witrynie sieci Web.

2. Wybierz **Zapisz** zakończenie oferty ustawienia.

## <a name="to-create-a-sku"></a>Aby utworzyć jednostkę SKU

1. Wybierz **jednostki SKU**. 
2. Wybierz **Dodaj jednostkę SKU** można wprowadzić identyfikator jednostki SKU. Identyfikator jednostki SKU to unikatowy identyfikator dla jednostek SKU w ramach oferty. Ten identyfikator jest widoczny w adresach URL produktu, szablony usługi Azure Resource Manager i raporty rozliczeń. Identyfikator jednostki SKU:
    - Obsługuje maksymalnie 50 znaków.
    - Można jedynie składa się z małych znaków alfanumerycznych i kresek (-).
    - Identyfikator nie może kończyć się łącznikiem.

    ![Dodaj jednostkę SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Konfigurowanie szczegółów jednostki SKU

Po dodaniu jednostki SKU, zostanie wyświetlona lista jednostek SKU w widoku jednostek SKU. Aby wyświetlić szczegóły jednostki SKU, wybierz nazwę jednostki SKU. Aby dodać informacje w następujących polach, można użyć w widoku szczegółów.

### <a name="hide-this-sku"></a>Ukryj tej jednostki SKU

To ustawienie umożliwia zarządzanie widoczność jednostki SKU. Jeśli "Ukryj ten jednostki SKU" jest wyłączona, jednostka SKU jest widoczna w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure portal](https://portal.azure.com/) dla klientów. Można ukryć jednostki SKU, jeśli chcesz tylko jego dostępnych za pośrednictwem szablony rozwiązań i nie zakupu indywidualnie.

### <a name="cloud-availability"></a>Dostępności w chmurze

To pole pozwala zdefiniować dostępności jednostki SKU w innej chmury platformy Azure.

**Publiczna platforma Azure**

Ta jednostka SKU można wdrożyć dla klientów we wszystkich publicznych regionach platformy Azure, które mają integrację portalu Marketplace.

**Chmura platformy Azure dla instytucji rządowych**

Ta jednostka SKU można wdrożyć w chmurze Azure Government. Przed opublikowaniem do [Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), firma Microsoft zaleca wydawców testowania i weryfikacji ich działa zgodnie z oczekiwaniami. Na etapie i testowania [żądania konta wersji próbnej](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>Microsoft Azure dla instytucji rządowych to chmura dla instytucji rządowych i społeczności, przy użyciu kontrolowanego dostępu dla klientów z USA federalnych, stan, lokalnej lub plemiennej, uprawnionych partnerów i do obsługi tych jednostek.

### <a name="countryregion-availability"></a>Dostępność Kraj/Region

To pole identyfikuje regionów, w których ma być dostępna do zakupu jednostki SKU. Należy rozważyć, którym udostępniasz od jednostek SKU. Niektóre kraje są klasyfikowane jako "Kraj przekazana podatkowych firmy Microsoft".

-   W "Microsoft podatku przekazana kraj" Firma Microsoft zbiera podatków od klientów i zapłaci podatki (publiczna) dla instytucji rządowych.

-   W innych krajach partnerów są odpowiedzialne za zbieranie klientów podatki i wypłaty podatku dla rządu. Jeśli chcesz sprzedawać w tych krajach, musi mieć możliwość obliczania i zapłacić podatki w nich.

![Wybierz kraj/Region dostępności](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Cennik

Dwa modele cen są obecnie obsługiwane.

#### <a name="bring-your-own-license-byol"></a>Bring-Your-Own-License (BYOL)

Możesz zarządzać licencjonowania oprogramowania działającego na maszynie Wirtualnej. Microsoft obciąża tylko koszty infrastruktury.

#### <a name="usage-based-monthly-billed-sku"></a>Użycie na podstawie miesięczne rozliczane jednostki SKU

Klienci jest naliczana na podstawie za godzinę, oparte na stawkach ustawione przez wydawców na rozmiary maszyn wirtualnych. Jeśli **rozliczeń godzinowych** model jest używany, cena łączna jest sumą kosztów oprogramowania pobieranych przez wydawcę i kosztów infrastruktury pobierane przez firmę Microsoft. Co godzinę i miesięczna cena to łączny koszt pojawi się do klienta, podczas biorąc pod uwagę zakupu. Rozliczenia będą w tym przypadku co miesiąc.

W ramach modelu na podstawie użycia istnieją dodatkowe ustawienia wymagane przez użytkownika.

**Bezpłatna wersja próbna**

Można określić, jeśli chcesz udostępnić bezpłatnej wersji próbnej dla swoich klientów.
W tym miejscu klienta nie naliczona koszt oprogramowania przez pierwsze 30/90 dni (w zależności od wyboru) po wdrożeniu maszyny Wirtualnej. Po upływie okresu bezpłatnej wersji próbnej one jest naliczana na podstawie za godzinę, oparte na stawkach ustawione przez wydawców w modelu co godzinę.

**Za rdzeń**

Możesz ustawić ceny na rdzeń dla danej jednostki SKU. Dla tej opcji wystarczy wprowadzić cena podstawowa dla pojedynczego rdzenia i firma Microsoft automatycznie compute ceny dla pozostałych rdzenie. Wprowadź ceny w USD w portalu, a firma Microsoft spowoduje automatyczne obliczenie cen dla innych regionach. Sprawdź ceny w innych regionach, za pomocą **Eksport danych ceny**

![Za rdzeń](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Cennik dyskretnych**

Możesz ustawić ceny dla każdego zestawu rdzeni pojedynczo, jeśli chcesz cena każdego rdzenia oddzielnie.

![Cennik dyskretnych](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Cennik eksportu / importu**

Zapewniają elastyczność do wyeksportowania, ceny, który został skonfigurowany za pośrednictwem portalu, aby wprowadzić zmiany za pomocą interfejsu programu excel. Ta opcja umożliwia zweryfikowanie ceny regionu i ceny w lokalnej walucie.
Kliknięcie **ceny eksportu** pobierze plik programu Excel z wstępnie uzupełnionym szczegóły cennika. Będzie można edytować te informacje w arkuszu, a następnie użyć **ceny importu** zaimportować zmiany, które zostały wprowadzone.
Zaimportowane ceny zostanie naliczona w portalu, jak również.

W tym cen w programie excel ceny dla różnych regionów są wyświetlane w walucie lokalnej. Kurs, której używamy są odświeżane raz dziennie.

![Ceny z przykładami kurs eksportu / importu](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   Ceny nie można zmienić po oferty przechodzi na żywo. Jednak nadal można dodać lub usunąć obsługiwanych regionów.
>-   Cena jest obciążany opłatą za użytkownika, oprócz [Azure\'cen maszyn wirtualnych s](https://aka.ms/vmpricingdetails).
>-   Ceny są ustawiane dla wszystkich regionów w walucie lokalnej przy użyciu stawek dostępnych waluty w chwili ustawienia ceny.
>-   Aby ustawić lub wyświetlić poszczególne cena każdego regionu, eksportować arkusz cen i zaimportuj z ceny niestandardowe.

## <a name="vm-images"></a>Obrazy maszyn wirtualnych

Następnej sekcji, aby ukończyć będzie sekcji obrazów maszyn wirtualnych. Przed przejściem do tej sekcji, musisz mieć wirtualny dysk twardy, który chcesz opublikować gotowy. Oto kilka linków, ułatwiając tworzenie wirtualnego dysku twardego:

-   [Techniczne wymagania wstępne dotyczące tworzenia obrazów maszyn wirtualnych w portalu Azure Marketplace](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md)
-   [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
-   [& Utwórz test z obrazu Maszynę wirtualną systemu Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)
-   [Tworzenie i przekazywanie wirtualnego dysku twardego Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)
-   [& Utwórz test maszyny Wirtualnej z systemem Windows za pomocą obrazu](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)
-   [Jak rozwiązywać problemy z typowych problemów napotykanych podczas tworzenia wirtualnego dysku twardego](../cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues.md)

Po utworzeniu możesz już wirtualny dysk twardy, możesz rozpocząć wypełniania tej sekcji.  Poniżej przedstawiono niektórych informacji dla niektórych pól.

### <a name="recommended-vm-sizes"></a>Zalecane rozmiary maszyn wirtualnych

Wybierz maksymalnie sześć zalecane rozmiary maszyn wirtualnych. Te zalecenia są wyświetlane dla klientów w witrynie Azure Marketplace i w bloku warstwa cenowa w portalu Azure, gdy będą rozważać zakup i wdrożenie Twojego obrazu. *Te rozmiary są jedynie zaleceniami. Klient jest w stanie wybrać dowolny rozmiar maszyny Wirtualnej, który obsługuje dyski określone w obrazie.*  Poniższy zrzut ekranu przedstawia zalecane rozmiary maszyn wirtualnych, które klient zostanie wyświetlony w witrynie Azure portal.


![Zalecane rozmiary maszyn wirtualnych](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Otwieranie portów

Określ porty, które chcesz wprowadzone otwarty i dostępny. Te porty są otwarte podczas tego wdrożenia maszyn wirtualnych.

### <a name="adding-vm-images"></a>Dodawanie obrazów maszyn wirtualnych

Następnym krokiem jest, aby dodać obraz maszyny Wirtualnej dla danej jednostki SKU. Możesz dodać maksymalnie osiem wersji dysków na jednostki SKU. Tylko najwyższy dysku numer wersji dla określonej jednostki SKU będą widoczne w portalu Azure Marketplace. Inne osoby będą widoczne za pośrednictwem interfejsów API.

W obszarze **wersje dyskowe**, wybierz opcję **+ nowa wersja**. Ta opcja umożliwia pokazanie następujące pola, które należy wypełnić.

#### <a name="vm-image-version"></a>Wersja obrazu maszyny Wirtualnej

Wersja obrazu maszyny Wirtualnej musi wykonać [wersji semantycznej](http://semver.org/) formatu. Wersje powinny być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Obrazy w innych jednostkach SKU może mieć różne wersje główne i pomocnicze. Wersje w ramach jednostki SKU należy tylko zmiany przyrostowe, które zwiększają wersja poprawki zabezpieczeń (Z z X.Y.Z).

#### <a name="os-vhd-url"></a>ADRES URL WIRTUALNEGO DYSKU TWARDEGO SYSTEMU OPERACYJNEGO

Wprowadź [identyfikatora URI sygnatury dostępu współdzielonego](../cloud-partner-portal/virtual-machine/cpp-get-sas-uri.md) utworzony dla wirtualnego dysku twardego systemu operacyjnego.

W przypadku dysków danych skojarzonych z tej jednostki SKU, możesz dodać te dyski, wybierając **+ nowy dysk danych** łącza. Ta akcja wyświetla dodatkowe pola, które należy wypełnić.

#### <a name="lun-vhd-url"></a>ADRES URL WIRTUALNEGO DYSKU TWARDEGO JEDNOSTKI LUN

Wprowadź [identyfikatora URI sygnatury dostępu współdzielonego](../cloud-partner-portal/virtual-machine/cpp-get-sas-uri.md) dla dysku z danymi.

#### <a name="lun-number"></a>Numer LUN

Przypisz tę jednostkę LUN numer. Ta liczba będzie zarezerwowana dla tego dysku danych w tej jednostce SKU.

>[!Note]
>Po opublikowaniu jednostki SKU z daną wersją maszyn wirtualnych i dysków z danymi, te zostaną zablokowane i nie można zmienić. Dla wszelkich dodatkowych wersji maszyny Wirtualnej, które poproś o dodanie Cię do jednostki SKU liczba dysków danych, których potrzebuje do obsługi nie można zmienić.

#### <a name="common-sas-url-issues--fixes"></a>Typowe problemy z adresu URL sygnatury dostępu Współdzielonego i poprawki

| Problem                                                                 | Komunikat                                                                           | Napraw                                                           |  Link do dokumentacji                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Wystąpił błąd podczas kopiowania obrazy — "?" nie zostanie odnaleziony w adresie url sygnatury dostępu Współdzielonego                | Błąd: Kopiowanie obrazów. Nie można pobrać obiekt blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.       | Zaktualizować adres Url sygnatury dostępu Współdzielonego za pomocą zalecane narzędzia                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Wystąpił błąd podczas kopiowania obrazy - parametrów "st" i "se" nie w url sygnatury dostępu Współdzielonego   | Błąd: Kopiowanie obrazów. Nie można pobrać obiekt blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.        | Zaktualizuj adres Url sygnatury dostępu Współdzielonego za pomocą daty rozpoczęcia i zakończenia na nim             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Wystąpił błąd podczas kopiowania obrazów — "sp = rl" nie w url sygnatury dostępu Współdzielonego                    | Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego         | Zaktualizuj adres Url sygnatury dostępu Współdzielonego z uprawnieniami ustawionymi jako "Odczyt" i "Lista     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Wystąpił błąd podczas kopiowania obrazów — adres url sygnatury dostępu Współdzielonego mają białych znaków w nazwie wirtualnego dysku twardego     | Błąd: Kopiowanie obrazów. Nie można pobrać obiekt blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.        | Aktualizacja adresu Url sygnatury dostępu Współdzielonego bez białych znaków                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Wystąpił błąd podczas kopiowania obrazów — błąd Autoryzacja adresów Url sygnatury dostępu Współdzielonego               | Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob ze względu na błąd autoryzacji     | Ponowne generowanie adresu Url sygnatury dostępu Współdzielonego                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Aby skonfigurować w portalu Marketplace

Konfigurowanie pola, które są wyświetlane w celu skorzystania z oferty na przy użyciu widoku witryny Marketplace [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Identyfikatory subskrypcji (wersja zapoznawcza)

Lista identyfikatorów subskrypcji platformy Azure, który ma mieć dostęp do tej oferty, po opublikowaniu oferty. Te subskrypcje wymienione biały umożliwiają testowanie traktuje ofertę przed jej wprowadzeniem na żywo. Portal dla partnerów pozwala na liście dozwolonych maksymalnie 100 subskrypcji.

### <a name="suggested-categories"></a>Sugerowane kategorie

Wybierz maksymalnie pięć kategorii z dostarczonej listy, który Twoja oferta może być najlepiej skojarzony z. Wybrane kategorie, będzie używany do mapowania oferty dostępne w kategorie produktów [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure portal](https://portal.azure.com/).

Poniższe przykłady przedstawiają informacje portalu marketplace w portalu Azure Marketplace i witryny Azure portal.

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>Wytycznych dotyczących logo

Wykonaj te wytyczne dotyczące logo przekazany do portalu Cloud Partner:

-   Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.

-   Kolory motywu w witrynie Azure Portal są białe i czarne. Należy unikać używania tych kolorów jako kolor tła logo usługi. Użyj koloru, która spowodowałaby wprowadzenie Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe.

    >[!Note] 
    >Jeśli używasz przezroczyste tło, należy upewnić się, że logo/tekstu nie są białe, black lub niebieski.

-   Nie używaj w logo tła gradientowego.

-   Unikaj umieszczania tekst, łącznie z firmy lub marki nazwy, logo.  Wygląd i działanie logo powinno być *prostego* i unikać gradientów.

-   Nie powinien być rozciągnięty logo.

#### <a name="hero-logo"></a>Hero Logo

Logo usługi Hero jest opcjonalne. Wydawcy mogą zrezygnować z Przekaż Hero logo. Jednak po przekazaniu logo jego usunięcie. Partner musi korzystać z portalu Azure Marketplace dotyczących ikony elementów Hero.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Wytyczne dotyczące ikona logo Hero

-   Nazwa wyświetlana wydawcy, tytuł plan i ofertę długie podsumowanie są wyświetlane przy użyciu czcionki o kolorze białym. Należy unikać wszelkich jasny kolor w tle. Czarny, biały i przezroczystego tła nie jest dozwolone dla elementu Hero ikon.

-   Nazwa wyświetlana wydawcy, plan, że tytuł, długie Podsumowanie oferty i przycisk Utwórz są osadzane programowo wewnątrz Hero logo gdy oferta znajduje się na liście. Nie należy wprowadzić dowolny tekst, podczas projektowania Hero logo. Pozostaw puste miejsce po prawej stronie logo. Ta przestrzeń powinny być 415 x 100 pikseli i jest zwracana w 370 pikseli od lewej strony.

![Przykład logo Duży obraz](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Aby skonfigurować ustawienia zarządzania potencjalnymi klientami, oferty, wykonaj [w instrukcjach](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Konfigurowanie obsługi

Przy użyciu widoku pomocy technicznej należy podać następujące informacje:

- Kontakt z Twojej firmy, takich jak inżynierii z pomocą techniczną.
- Kontakty pomocy technicznej klienta.

## <a name="to-publish-the-offer"></a>Aby opublikować ofertę

Ostatnim krokiem jest, aby opublikować ofertę. Postępuj zgodnie z [tych instrukcji do emisji na żywo z ofertą](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).
