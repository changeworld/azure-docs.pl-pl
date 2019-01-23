---
title: Opublikuj aplikację zarządzaną platformy Azure w portalu Azure Marketplace
description: Opublikuj aplikację zarządzaną platformy Azure w portalu Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6e5bcd6d9923b4051d44d51ff1a2534bc5e02f41
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445664"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Publikowanie aplikacji zarządzanych platformy Azure w witrynie Azure Marketplace 
========================================================

W tym artykule wymieniono różne etapy publikowanie oferty zarządzanej aplikacji do portalu Azure Marketplace.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Wymagania wstępne publikowania aplikacji zarządzanych 
---------------------------------------------------

Wymagania wstępne dotyczące list w portalu Azure Marketplace

1.  Techniczna

    -   [Tworzenie szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Szablony szybkiego startu platformy Azure:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   Tworzenie definicji interfejsu użytkownika

        -   [Utwórz plik definicji interfejsu użytkownika](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Nietechniczne (wymagania biznesowe)

    -   Siedziba firmy (lub firmy od niej zależnej) musi znajdować się w kraju pochodzenia sprzedaży obsługiwanym przez Portal Azure Marketplace

    -   Muszą mieć licencję produktu w sposób, który jest zgodny z modelami rozliczeń obsługiwanym przez Portal Azure Marketplace

    -   Odpowiedzialność za zapewnienie pomocy technicznej dla klientów: bezpłatna, płatną lub za pośrednictwem pomocy technicznej społeczności.

    -   Jesteś odpowiedzialny za Licencjonowanie oprogramowania i wszelkie zależności oprogramowania innych firm.

    -   Należy podać zawartość, która spełnia kryteria za ofertę użytkownika był wyświetlany w portalu Azure Marketplace i w portalu zarządzania systemu Azure

    -   Musisz wyrazić zgodę na warunki usługi Azure Marketplace — zasady udziału oraz Umowie wydawcy portalu

    -   Musisz wyrazić zgodę spełniają warunki użytkowania i zasady zachowania poufności informacji firmy Microsoft umowę programu certyfikat platformy Azure firmy Microsoft.

<a name="how-to-create-a-new-azure-application-offer"></a>Tworzenie nowej oferty aplikacji na platformie Azure 
-------------------------------------------

Gdy spełniono wszystkie wymagania wstępne, zostanie gotowe do rozpoczęcia tworzenia oferty aplikacji zarządzanej. Przed rozpoczęciem krótkie podsumowanie oferty i jednostki SKU

**Oferta**

Oferty aplikacji na platformie Azure odnosi się do klasy produktu, oferty od wydawcy. Jeśli masz nowe rozwiązanie/aplikację do opublikowania w portalu Azure Marketplace, nowa oferta nie jest Zdajemy sobie. Oferta jest kolekcją jednostek SKU. Co oferty pojawia się jako własny element w witrynie Azure Marketplace.

**SKU**

Jednostki SKU to oferta, za pomocą najmniejszej płatnej wersji jednostki. W ramach tej samej klasie produktu (oferty) jednostek SKU umożliwiają rozróżnienie różnych funkcji są obsługiwane, czy jest ona zarządzanych lub niezarządzanych i modelami rozliczeń.

Dodawanie wielu jednostek SKU, jeśli chcesz obsługiwać różne modele rozliczania: takich jak Bring Your Own License (BYOL), płatność zgodnie z rzeczywistym użyciem (PAYG), itp. 

Dodanie wielu jednostek SKU każdej jednostki SKU obsługuje inny zbiór funkcji i wycenione inaczej.

Jednostka SKU pojawia się w ramach oferty nadrzędne w witrynie Azure Marketplace podczas, gdy zostanie ona wyświetlona jako własny element możliwych do zakupienia w witrynie azure.com.

1.  Zaloguj się do [portalu dla partnerów w chmurze](http://cloudpartner.azure.com).

2.  Na pasku nawigacyjnym po lewej stronie kliknij pozycję \"+ nowa oferta\" i wybierz \"Azure Applications\".

    ![Nowa oferta](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Nowa oferta \"edytora\" widok jest teraz otwarte i możesz rozpocząć tworzenie.

4.  \"Formularzy\" które muszą być wypełnione są widoczne po lewej stronie w obrębie \"edytora\" widoku. Każdy \"formularza\" zawiera zestaw pól, które mają zostać wypełnione. Wymagane pola są oznaczone czerwoną gwiazdką (\*).

    > Istnieją 4 główne formularze do tworzenia zarządzanej aplikacji

    -   Ustawienia oferty

    -   Jednostki SKU

    -   Portal Marketplace

    -   Pomoc techniczna

<a name="how-to-fill-out-the-offer-settings-form"></a>Jak wypełnić formularz ustawień oferty 
---------------------------------------

Formularz ustawień oferty jest podstawowej postaci, aby określić ustawienia oferty.
Poniżej opisano różne pola.

**Identyfikator oferty**

Unikatowy identyfikator oferty w ramach profilu wydawcy.
Będzie ona widoczna w adresach URL produktu, szablonów usługi Resource Manager i raporty rozliczeń. Tylko małych znaków alfanumerycznych i kresek (-) są dozwolone. Go nie może kończyć się kreską lub przekracza maksymalnie 50 znaków. To pole jest zablokowane, gdy oferty przechodzi na żywo.

**Identyfikator wydawcy**

Tej listy rozwijanej można wybrać profil wydawcy, który chcesz opublikować tę ofertę, w obszarze. To pole jest zablokowane, gdy oferty przechodzi na żywo.

**Nazwa**

Nazwa wyświetlana oferty. Nazwa, która będzie wyświetlana w portalu Azure Marketplace i w witrynie Azure portal. Może zawierać maksymalnie 50 znaków. W tym miejscu są wskazówki do uwzględnienia rozpoznawalną nazwę markę produktu. Nie obejmują nazwę swojej firmy, chyba że jest, jak jest sprzedawane. Ta oferta jest marketingu w swojej własnej witryny sieci Web, upewnij się, czy nazwa jest dokładnie tak jak zostanie ona wyświetlona w witrynie sieci Web.

Kliknij pozycję \"Zapisz\" Aby zapisać postęp. Następnym krokiem powinno być można dodać jednostki SKU oferty.

<a name="how-to-create-skus"></a>Jak utworzyć jednostki SKU 
------------------

Kliknij pozycję \"jednostki SKU\" formularza. W tym miejscu zobaczysz opcję \"Dodaj jednostkę SKU\" kliknięcie przycisku, na którym będzie można wprowadzić \"identyfikator jednostki SKU\".

![Nowa oferta jednostki SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"Identyfikator jednostki SKU\" to unikatowy identyfikator dla jednostek SKU w ramach oferty. Ten identyfikator będzie widoczny w adresach URL produktu, szablonów ARM, i zgłasza rozliczeń. Może składać się tylko z małych znaków alfanumerycznych i łączników (-).
Go nie może kończyć się kreską i nie może przekraczać 50 znaków. To pole jest zablokowane, gdy oferty przechodzi na żywo. W obrębie oferty możesz mieć wiele jednostek SKU. Potrzebujesz jednostki SKU dla każdego obrazu, który zamierzasz opublikować.

Po dodaniu jednostki SKU pojawi się na liście jednostek SKU w ramach \"jednostki SKU\" formularza. Kliknij nazwę jednostki SKU, aby uzyskać szczegóły tej konkretnej jednostki SKU. Poniżej przedstawiono niektórych informacji dla niektórych pól.

Po kliknięciu \"nowej jednostki SKU\", musisz wypełnić następującą postać:

![Nowa oferta - nowej jednostki SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Sposób wypełniania szczegółowe informacje o jednostce Sku 

**Tytuł** — udostępnia tytuł dla tej jednostki Sku. Jest to, co pojawi się w galerii dla tego elementu.

**Podsumowanie** — zawiera krótkie podsumowanie dla tej jednostki sku. Ten tekst pojawi się bezpośrednio pod tytułem.

**Opis** — zawiera szczegółowy opis o jednostkę SKU.

**Typ jednostki SKU** — dozwolone wartości to \"zarządzanej aplikacji\" i \"szablony rozwiązań\". Dla tego przypadku wybrania \"zarządzanej aplikacji\".

### <a name="how-to-fill-package-details-section"></a>Sposób wypełniania sekcji Szczegóły pakietu 

Sekcja pakietu zawiera następujące pola, które muszą być wypełnione

![Nowa oferta — nowy pakiet jednostki SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Bieżąca wersja** — zawiera wersji pakietu, możesz przekazać.
Należy w formacie.

**Plik pakietu** -pakiet zawiera następujące pliki, które są kompresowane w pliku zip.

applianceMainTemplate.json - pliku szablonu wdrożenia, który jest używany do wdrożenia rozwiązanie/aplikację i utworzyć zasoby zdefiniowane w nim. Więcej szczegółów można znaleźć na temat tutaj — pliki szablonu wdrożenia autora <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json — ten plik jest używany przez Portal w witrynie azure.com do generowania interfejsu użytkownika do obsługi administracyjnej rozwiązania/aplikacji. Więcej szczegółów można znaleźć na temat tworzenia tego pliku w tym miejscu — <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json — plik szablonu, który zawiera tylko zasób Microsoft.Solution/appliances. Właściwości klucza obiektu tego zasobu, w których trzeba pamiętać są następujące:

-   \"rodzaj\" — wartość powinna być \"Marketplace\" w przypadku scenariusza aplikacji zarządzanych w witrynie Marketplace
-   \"ManagedResourceGroupId\": Grupa zasobów w kliencie\'subskrypcji s wdrożonym wszystkie zasoby, które są zdefiniowane w applianceMainTemplate.json.
-   \"Elementu PublisherPackageId\": ciąg, który unikatowo identyfikuje pakiet. 

Wartość musi być zbudowany w następujący — to połączenie \[publisherId\].\[ Identyfikatora oferty\]— wersja zapoznawcza\[SKUID\].\[ PackageVersion\].
Każda z tych wartości można uzyskać, jak pokazano poniżej.

Ten pakiet powinien zawierać zagnieżdżone szablony lub skrypty wymagane do świadczenia pomyślnie tej aplikacji. MainTemplate.json, applianceMainTemplate.json i applianceCreateUIDefinition.json musi być obecny w folderze głównym.

**Autoryzacje** — właściwość ta definiuje kto uzyskuje dostęp, a poziom dostępu do zasobów w subskrypcji klientów. Dzięki temu z wydawcą, aby zarządzać aplikacją w imieniu klienta.

-   Identyfikator PrincipalId — usługi Azure Active directory identyfikator użytkownika, grupy użytkowników lub aplikacji, która zostanie udzielone określone uprawnienia (zgodnie z opisem w definicji roli) dotyczące zasobów w subskrypcji klienta.

-   Definicja roli — lista wszystkie wbudowane role kontroli RBAC dostarczane przez usługę Azure AD. Możesz wybrać najbardziej odpowiedni roli, które umożliwią zarządzanie zasobami w imieniu klienta.

Można dodać wiele autoryzacji. Jednak zalecane jest aby utworzyć grupę użytkowników usługi Active Directory i określić jego Identyfikatora w \"PrincipalId\."  Umożliwi to dodanie większej liczby użytkowników do grupy użytkowników, bez konieczności aktualizowania jednostki SKU.

Szczegółowe informacje na temat RBAC można znaleźć tutaj — <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Formularz witryny Marketplace
----------------

Formularz portalu marketplace w ramach oferty usługi azure application poprosi o podanie pola, które będą widoczne na [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure Portal](https://portal.azure.com/). Poniżej przedstawiono niektórych informacji dla niektórych pól.

#### <a name="preview-subscription-ids"></a>Identyfikatory subskrypcji (wersja zapoznawcza)

Wprowadź listę identyfikatorów subskrypcji platformy Azure, który ma mieć dostęp do tej oferty, po opublikowaniu go w tym miejscu. Te subskrypcje wymienione biały umożliwia testowanie traktuje ofertę przed jej wprowadzeniem na żywo. Portal dla partnerów pozwala na liście dozwolonych maksymalnie 100 subskrypcji.

#### <a name="suggested-categories"></a>Sugerowane kategorie

Wybierz maksymalnie pięć kategorii z dostarczonej listy, który Twoja oferta może być najlepiej skojarzony z. Wybrane kategorie, będzie używany do mapowania oferty dostępne w kategorie produktów [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure Portal](https://portal.azure.com/).

Poniżej przedstawiono niektóre z miejsc, w których dane podane w tym formularzu zostaną wyświetlone w.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portal at azure.com

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Wytycznych dotyczących logo

Wszystkie logo, które są przekazywane w portalu Cloud Partner, należy przestrzegać poniższych wytycznych:

-   Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.

-   Kolory motywu portalu na stronie azure.com to biały i czarny. Należy unikać używania tych kolorów jako kolor tła logo usługi. Użyj niektóre koloru, która spowodowałaby wprowadzenie Twoje logo widocznym w portalu w witrynie azure.com.
    Zalecamy proste kolory podstawowe. **Jeśli korzystają z przezroczystym tłem, upewnij się, że logo i tekst nie są białe, czarne lub niebieski.**

-   Nie należy używać gradientu tła na logo.

-   Należy unikać wprowadzania tekstu, nawet firmy, lub oznaczyć nazwy, logo.
    Wygląd i działanie logo powinno być \'prostego\' i unikać gradientów.

-   Należy unikać rozciąganie logo.

##### <a name="hero-logo"></a>Hero Logo

Logo usługi Hero jest opcjonalne. Wydawcy mogą zrezygnować z Przekaż Hero logo. Jednak po przekazany, nie można usunąć ikonę elementu hero. W takim przypadku partnera musi postępuj zgodnie z zasadami portalu Azure Marketplace dla elementu Hero ikon.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Wytyczne dotyczące ikona logo Hero

-   Nazwa wyświetlana wydawcy, tytuł plan i ofertę, długie podsumowanie są wyświetlane w kolorze białym czcionki. Należy unikać, pamiętając wszelkie jasny kolor tła ikony elementów Hero. Czarny, biały i przezroczyste tło nie są dozwolone dla elementu Hero ikon.

-   Jeśli na liście pobiera oferty, nazwa wyświetlana wydawcy, tytuł planu, oferty, długie Podsumowanie i przycisk Utwórz programowo są osadzone wewnątrz Hero logo. Nie trzeba wprowadzić dowolny tekst podczas projektowania Hero logo. Pozostaw puste miejsca do magazynowania po prawej stronie dla wydawcy wyświetlane nazwy, tytułu planu, oferty długie Podsumowanie i itp. Zostaną one dołączone programowo.
    Puste miejsca do magazynowania dla tekst powinien być 415 x 100, po prawej stronie i przesunięcia w 370 pikseli od lewej strony.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Formularz pomocy technicznej
------------

Dalej formularz do wypełnienia jest formularz pomocy technicznej. Ten formularz prosi o pomocy technicznej Twojej firmy.  Niektóre przykłady są Twoje Inżynieria kontaktowe i informacje kontaktowe pomocy technicznej klienta.

<a name="how-to-publish-an-offer"></a>Jak opublikować ofertę
-----------------------

Teraz, gdy jest niegotowa oferty, następnym krokiem jest publikowanie oferty w portalu Azure Marketplace. Kliknij przycisk \"Publikuj\" przycisk Rozpocznij proces podejmowania oferty na żywo.
