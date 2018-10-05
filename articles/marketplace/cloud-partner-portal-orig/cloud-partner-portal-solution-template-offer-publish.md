---
title: Publikowanie szablonu rozwiązania | Dokumentacja firmy Microsoft
description: Publikowanie szablonu rozwiązania w portalu Azure Marketplace.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: ded952ac6418ae3d9916b3ae8b8dbacb0c9c5a84
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810489"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Publikowanie szablonu rozwiązania w portalu Azure Marketplace

Ten artykuł zawiera kroki umożliwiające publikowanie szablonu rozwiązania oferty w portalu Azure Marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne pomoc techniczna i nietechniczna mają zastosowanie do wyświetlania listy szablon rozwiązania w portalu Azure Marketplace.

### <a name="technical"></a>Techniczna

- [Omówienie szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Szablony szybkiego startu platformy Azure:

    - [Dokumentacja szablonu szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/)

    - [Dokumentację Szybki Start platformy Azure w usłudze GitHub](https://github.com/azure/azure-quickstart-templates)

 - [Utwórz plik interfejsu użytkownika witryny Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>Nietechniczne (wymagania biznesowe)

-   Siedziba firmy (lub firmy od niej zależnej) musi znajdować się w sprzedaży w kraju obsługiwanym przez Portal Azure Marketplace.

-   Musi być licencjonowany produkt w sposób, który jest zgodny z modelami rozliczeń obsługiwanym przez Portal Azure Marketplace.

-   Jesteś odpowiedzialny udostępniania dla pomocy technicznej dla klientów w sposób rozsądny z komercyjnego punktu widzenia, czy bezpłatną, płatną lub za pośrednictwem pomocy technicznej społeczności.

-   Jesteś odpowiedzialny za Licencjonowanie oprogramowania oraz wszystkie zależności oprogramowania innych firm.

-   Podaj zawartość, która spełnia kryteria za ofertę użytkownika był wyświetlany w portalu Azure Marketplace i w portalu zarządzania Azure.

-   Zaakceptuj warunki Azure Marketplace — zasady udziału oraz Umowie wydawcy portalu.

-   Należy wyrazić zgodę na przestrzeganie Warunków użytkowania, Zasad zachowania poufności informacji firmy Microsoft i Umowy programu certyfikatu platformy Microsoft Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Po spełnieniu warunków wstępnych, możesz rozpocząć tworzenie oferty szablonu rozwiązania. Przed rozpoczęciem należy przejrzeć następujące oferty i informacje o jednostce SKU.

**Oferta**

Oferty aplikacji na platformie Azure odnosi się do klasy produktu, oferty od wydawcy. Jeśli masz nowy typ rozwiązania/aplikacji, który ma być dostępna w portalu Azure Marketplace, nowa oferta nie jest najlepszym rozwiązaniem. Oferta jest kolekcją jednostek SKU. Co oferty pojawia się jako własny element w witrynie Azure Marketplace.

**SKU**

Jednostka SKU to najmniejsza możliwa do kupienia jednostka oferty. W ramach tej samej klasie produktu (oferty) jednostek SKU umożliwiają rozróżnienia między różnych obsługiwanych funkcji. Na przykład oferty jest zarządzane lub niezarządzane, a różne modele rozliczeń są obsługiwane.

Dodaj wielu jednostek SKU w następujących scenariuszach:
- Chcesz obsługiwać różne modele rozliczania, takich jak Bring Your Own License (BYOL) lub płatność zgodnie z rzeczywistym użyciem (PAYG).
- Każda jednostka SKU obsługuje zestaw funkcji i każdy zestaw funkcji jest różne ceny.

Jednostka SKU pojawia się w ramach oferty nadrzędne w witrynie Azure Marketplace i jest wyświetlany jako swój własny płatnej wersji jednostki w witrynie Azure portal.

## <a name="to-create-a-new-offer"></a>Aby utworzyć nową ofertę

1.  Zaloguj się do [portalu dla partnerów w chmurze](http://cloudpartner.azure.com/).

2.  Na pasku nawigacyjnym po lewej stronie wybierz **+ nowa oferta**, a następnie wybierz pozycję **Azure Applications**.

    ![Utwórz nową ofertę](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  W obszarze **nowa oferta**, wybierz opcję **edytora**.

    ![Nowy edytor oferty](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  W obszarze **edytora**, udostępni informacje w następujących widokach:
    - Ustawienia oferty
    - Jednostki SKU
    - Portal Marketplace
    - Każdy widok zawiera zestaw pól dla Ciebie, wypełnij pomocy technicznej. Wymagane pola są oznaczone czerwoną gwiazdki (\*)

## <a name="to-configure-offer-settings"></a>Aby skonfigurować ustawienia oferty

1. Skonfiguruj następujące opcje **oferują tożsamości** pól w ustawieniach oferty.

    **Identyfikator oferty**

     Unikatowy identyfikator oferty w ramach profilu wydawcy. Ten identyfikator jest widoczny w adresach URL produktu, szablony ARM i raporty rozliczeń. Można używać tylko małych znaków alfanumerycznych i kresek (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. 
    >[!Note]
    >To pole jest zablokowane, gdy oferty przechodzi na żywo.

    **Identyfikator wydawcy**

    Na liście rozwijanej profilem wydawcy. Wybierz profil, który chcesz opublikować ofertę w obszarze. 
    >[!Note]
    >To pole jest zablokowane, gdy oferty przechodzi na żywo.

    **Nazwa**

    Nazwa wyświetlana oferty. Ta nazwa jest wyświetlana w portalu Azure Marketplace i w witrynie Azure Portal. Może zawierać maksymalnie 50 znaków. Użyj poniższych wskazówek Nazwa oferty:
    -  Warto umieścić tu rozpoznawalną nazwę marki produktu. 
    - Nie dołączaj nazwę swojej firmy, chyba że jak oferty są sprzedawane.
    - Jeśli ta oferta jest marketingu w swojej własnej witryny sieci Web, upewnij się, że nazwa jest taka sama jak nazwa w witrynie sieci Web.

2. Wybierz **Zapisz** zakończenie oferty ustawienia.
oferty.

## <a name="to-create-skus"></a>Aby utworzyć jednostki SKU
------------------

1. Wybierz **jednostki SKU**. 

    ![Nowej jednostki SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    Identyfikator jednostki SKU to unikatowy identyfikator dla jednostek SKU w ramach oferty. Ten identyfikator jest widoczny w adresach URL produktu, szablony ARM i raporty rozliczeń. Identyfikator jednostki SKU:
    - Obsługuje maksymalnie 50 znaków.
    - Można jedynie składa się z małych znaków alfanumerycznych i kresek (-).
    - Identyfikator nie może kończyć się łącznikiem.

    >[!Note]
    >Po dodaniu jednostki SKU pojawia się na liście jednostek SKU w widoku jednostek SKU. Aby wyświetlić szczegóły jednostki SKU, wybierz nazwę jednostki SKU. 

2. Wybierz **nowej jednostki SKU** podanie informacji o pokazano na poniższym zrzucie ekranu. 

    ![Szczegóły jednostki SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>Ustawienia jednostki SKU

Podaj następujące ustawienia jednostki SKU.

- **Tytuł** -tytułu dla jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.

- **Podsumowanie** — krótkie podsumowanie jednostki SKU. (Maksymalna długość wynosi 100 znaków).

- **Opis** — szczegółowy opis jednostki SKU.

- **Typ jednostki SKU** — na liście rozwijanej następującymi wartościami: "Zarządzana aplikacja (wersja zapoznawcza)" i "Szablon rozwiązania". W tym scenariuszu wybierz **szablon rozwiązania**.

- **Dostępność w chmurze** — Lokalizacja jednostki SKU. Wartość domyślna to publiczna platforma Azure.

### <a name="package-details"></a>Szczegóły pakietu

Po zakończeniu ustawienia jednostki SKU, należy podać następujące dane pakietu.

![Szczegóły pakietu](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Bieżąca wersja** — wersja pakietu, który trzeba będzie przekazać. Należy go w formacie -...

- **Plik pakietu** — ten pakiet zawiera następujące pliki, które są zapisywane w pliku zip.

    -   MainTemplate.json — pliki szablonu wdrożenia, który jest używany do wdrożenia rozwiązanie/aplikację i utworzyć zasoby definiowane dla rozwiązania. Aby uzyskać więcej informacji, zobacz [jak tworzyć pliki szablonów wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)

    -   createUIDefinition.json — ten plik jest używany przez witryny Azure Portal do wygenerowania interfejsu użytkownika do obsługi administracyjnej rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [interfejs użytkownika portalu tworzenie platformy Azure dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >Ten pakiet może zawierać innych zagnieżdżonych szablonów lub skrypty, które są potrzebne do aprowizowania tej aplikacji. MainTemplate.json i createUIDefinition.json muszą być w folderze głównym.

## <a name="to-configure-the-marketplace"></a>Aby skonfigurować w portalu Marketplace

Konfigurowanie pola, które są wyświetlane w celu skorzystania z oferty na przy użyciu widoku witryny Marketplace [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Identyfikatory subskrypcji (wersja zapoznawcza)

Lista identyfikatorów subskrypcji platformy Azure, który ma mieć dostęp do tej oferty, po opublikowaniu oferty. Te subskrypcje wymienione biały umożliwiają testowanie traktuje ofertę przed jej wprowadzeniem na żywo. Portal dla partnerów pozwala na liście dozwolonych maksymalnie 100 subskrypcji.

### <a name="suggested-categories"></a>Sugerowane kategorie

Wybierz maksymalnie 5 kategorie z podanej listy, które oferty można najlepiej skojarzony z. Wybrane kategorie, będzie używany do mapowania oferty dostępne w kategorie produktów [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [witryny Azure Portal](https://portal.azure.com/).

Poniższe przykłady przedstawiają informacje portalu marketplace w portalu Azure Marketplace i witryny Azure Portal.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Wytycznych dotyczących logo

Wykonaj te wytyczne dotyczące logo przekazany do portalu Cloud Partner:

-   Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.

-   Kolory motywu w witrynie Azure Portal są białe i czarne. Należy unikać używania tych kolorów jako kolor tła logo usługi. Użyj koloru, która spowodowałaby wprowadzenie Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe.

    >[!Note] 
    >Jeśli używasz przezroczyste tło, należy upewnić się, że logo/tekstu nie są białe, black lub niebieski.

-   Nie używaj w logo tła gradientowego.

-   Należy unikać wprowadzania tekstu na logo. Obejmuje to Twoja firma lub marką. Wygląd i działanie logo powinno być *prostego* i unikać gradientów.

-   Nie powinien być rozciągnięty logo.

#### <a name="hero-logo"></a>Hero Logo

Logo usługi Hero jest opcjonalne. Wydawcy mogą zrezygnować z Przekaż Hero logo. Jednak po przekazaniu logo jego usunięcie. Partner musi korzystać z portalu Azure Marketplace dotyczących ikony elementów Hero.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Wytyczne dotyczące ikona logo Hero

-   Nazwa wyświetlana wydawcy, tytuł plan i ofertę długie podsumowanie są wyświetlane przy użyciu biały kolor czcionki. Należy unikać wszelkich jasny kolor w tle. Czarny, biały i przezroczystego tła nie jest dozwolone dla elementu Hero ikon.

-   Nazwa wyświetlana wydawcy, plan, że tytuł, długie Podsumowanie oferty i przycisk Utwórz są osadzane programowo wewnątrz Hero logo gdy oferty na liście. Nie należy wprowadzić dowolny tekst, podczas projektowania Hero logo. Pozostaw puste miejsce po prawej stronie logo. Ta przestrzeń powinny być 415 x 100 pikseli i jest zwracana w 370 pikseli od lewej strony.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Konfigurowanie obsługi

Przy użyciu widoku pomocy technicznej należy podać następujące informacje:

- Kontakt z Twojej firmy, takich jak inżynierii z pomocą techniczną.
- Kontakty pomocy technicznej klienta.

## <a name="to-publish-the-offer"></a>Aby opublikować ofertę

Ostatnim krokiem jest, aby opublikować ofertę. Wybierz pozycję **Publikuj**.
