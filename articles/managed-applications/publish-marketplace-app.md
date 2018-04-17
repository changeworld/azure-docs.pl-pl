---
title: Azure zarządzanych aplikacji w witrynie Marketplace | Dokumentacja firmy Microsoft
description: W tym artykule opisano Azure zarządzane aplikacje, które są dostępne na rynku.
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/15/2018
ms.author: tomfitz
ms.openlocfilehash: 753e13ea0dbfc7fe0c74987e1383f71329b76a63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Azure zarządzanych aplikacji w witrynie Marketplace

Dostawców można używać platformy Azure zarządzanych aplikacji w celu zaoferowania ich rozwiązania dla wszystkich klientów w portalu Azure Marketplace. Dostawców mogą obejmować usługi zarządzane przez dostawców (MSPs), niezależnym dostawcom oprogramowania (ISV) i integratorów systemów. platforma (SIs). Zarządzane aplikacje zmniejszyć konserwacji i obsługi obciążenia dla klientów. Dostawców sprzedać infrastruktury i oprogramowaniem za pośrednictwem portalu marketplace. One dołączyć usług oraz obsługi do zarządzanych aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie aplikacji zarządzanych](overview.md).

W tym artykule opisano sposób publikowania aplikacji w portalu Marketplace i były szeroko dostępne dla klientów.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Wymagania wstępne dotyczące publikowania aplikacji zarządzanej

Aby ukończyć ten artykuł, trzeba już mieć plik zip definicji zarządzanej aplikacji. Aby uzyskać więcej informacji, zobacz [Utwórz aplikację usługi wykazu](publish-service-catalog-app.md).

Istnieją ponadto kilka biznesowe wymagania wstępne. Oto one:

* Firmy lub zależnemu musi znajdować się w danym kraju, w którym sprzedaży są obsługiwane przez witryny marketplace.
* W taki sposób, który jest zgodny z rozliczeń modeli obsługiwanych przez witryny marketplace muszą mieć licencję na produkt.
* Udostępnić pomocy technicznej dla klientów w sposób uzasadnione ekonomicznie. Obsługa można wolny, płatnej, lub za pośrednictwem społeczności obsługuje.
* Licencji oprogramowania oraz wszelkie zależności oprogramowania innych firm.
* Podaj zawartość, która spełnia kryteria dla Twojej oferty był wyświetlany w witrynie Marketplace oraz w portalu Azure.
* Akceptuję postanowienia Umowy o Azure Marketplace udział zasad i wydawcy.
* Zobowiązuje się do przestrzegania warunków użytkowania, zasady zachowania poufności informacji firmy Microsoft i certyfikowane umowę programu Microsoft Azure.

## <a name="become-a-publisher"></a>Stają się wydawca

Aby stać się wydawcy w portalu Azure Marketplace, należy:

1. Tworzenie Identyfikatora Microsoft - Tworzenie konta Microsoft przy użyciu adresu e-mail, który należy do domeny firmy, ale nie do jednej osoby. Ten adres e-mail jest używany zarówno dla Microsoft Developer Center, jak i w chmurze Portal dla partnerów. Aby uzyskać więcej informacji, zobacz [Azure Marketplace wydawca przewodnika](https://aka.ms/sellerguide).
1. Przedstawia [Azure Marketplace wyznaczenie formularza](https://aka.ms/ampnomination) — **rozwiązania, które mają zostać opublikowane?**, wybierz pozycję **zarządzanej aplikacji**. Po przesłaniu formularza zespół organizujący dołączanie Marketplace monitoruje aplikację i weryfikuje żądanie. Proces zatwierdzania może potrwać jednej do trzech dni. Po zatwierdzeniu Twoje wyznaczenie otrzymasz kod promocyjny odstąpienie opłata rejestracji dla Centrum deweloperów. Jeśli chcesz **nie** Wypełnij formularz wyznaczenie Marketplace, zostanie wyświetlona prośba o opłata rejestracji $99.
1. Zarejestruj w [Centrum deweloperów](http://dev.windows.com/registration?accountprogram=azure) -Microsoft sprawdza, czy Twoja organizacja jest prawidłowy firmy z Identyfikatorem prawidłowe podatku dla kraju, w którym jest on zarejestrowany. Proces zatwierdzania może zająć 5 – 10 dni. W celu uniknięcia opłat rejestracji, użyj kod promocyjny, otrzymany w wiadomości e-mail z nazwy procesu. Aby uzyskać więcej informacji, zobacz [Azure Marketplace wydawca przewodnika](https://aka.ms/sellerguide).
1. Zaloguj się do [portalu dla partnerów chmury](https://cloudpartner.azure.com) — w profilu wydawcy skojarzyć konto Centrum deweloperów z profilem wydawcy Marketplace. Aby uzyskać więcej informacji, zobacz [Azure Marketplace wydawca przewodnika](https://aka.ms/sellerguide).

## <a name="create-a-new-azure-application-offer"></a>Tworzenie nowej oferty aplikacji Azure

Po utworzeniu konta portalu partnerów, możesz utworzyć ofertę zarządzanej aplikacji.

### <a name="set-up-an-offer"></a>Konfigurowanie oferty

Oferta dla aplikacji zarządzanej odpowiada klasie produktu oferty od wydawcy. Jeśli nowy typ aplikacji, które mają być dostępne na rynku, można go skonfigurować jako nowej oferty. Oferta jest kolekcją jednostki SKU. Co oferta zostanie wyświetlony jako własny element w witrynie marketplace.

1. Zaloguj się do [portalu dla partnerów chmury](https://cloudpartner.azure.com/).

1. W okienku nawigacji po lewej stronie wybierz **+ nowe oferty** > **aplikacji Azure**.

1. W **edytor** widoku, zobacz wymagane formularzy. Każdy formularz jest opisane w dalszej części tego artykułu.

## <a name="offer-settings-form"></a>Formularz Ustawienia oferty

Pola **oferują ustawienia** są:

* **Identyfikator oferty**: ten unikatowy identyfikator identyfikuje oferta w profilu wydawcy. Ten identyfikator jest widoczny w adresach URL produktu, szablony usługi Resource Manager i raporty rozliczeń. Go mogą się składać tylko małe znaki alfanumeryczne i łączniki (-). Identyfikator nie może kończyć się łącznikiem. Jego ograniczone do maksymalnie 50 znaków. Po ofertę odbywa się na żywo, to pole jest zablokowane.
* **Identyfikator wydawcy**: Użyj tej listy rozwijanej, aby wybrać profil wydawcy, którą chcesz opublikować tej oferty, w obszarze. Po ofertę odbywa się na żywo, to pole jest zablokowane.
* **Nazwa**: Ta nazwa wyświetlana dla danej oferty pojawia się w witrynie Marketplace oraz w portalu. Może mieć maksymalnie 50 znaków. Obejmują rozpoznawalną nazwę markę produktu. Nie zawierają nazwę swojej firmy, chyba że jest sposób jest ona sprzedawane. Ta oferta jest marketingu na własne witryny sieci Web, upewnij się, że nazwa jest dokładnie sposobu jej wyświetlania w witrynie sieci Web.

Po zakończeniu wybierz **zapisać** Aby zapisać postęp.

## <a name="skus-form"></a>Formularz jednostki SKU

Następnym krokiem jest można dodać jednostki SKU dla danej oferty.

Jednostka SKU jest najmniejszej jednostki oferowane oferty. SKU w tej samej klasie produktu (Oferta) umożliwia rozróżnianie między:

* Różne funkcje, które są obsługiwane
* Określa, czy zarządzane lub niezarządzane oferty
* Modele rozliczeń, które są obsługiwane

Jednostka SKU jest wyświetlany w obszarze oferta nadrzędnego w witrynie marketplace. Wygląda na to jako własny element oferowane w portalu Azure.

1. Wybierz **jednostki SKU** > **nowe jednostki SKU**.

1. Wprowadź **identyfikator jednostki SKU**. Identyfikator jednostki SKU to unikatowy identyfikator dla jednostki SKU oferta. Ten identyfikator jest widoczny w adresach URL produktu, szablony usługi Resource Manager i raporty rozliczeń. Go mogą się składać tylko małe znaki alfanumeryczne i łączniki (-). Identyfikator nie może kończyć się kreską i ograniczone do maksymalnie 50 znaków. Po ofertę odbywa się na żywo, to pole jest zablokowane. Może mieć wiele jednostek SKU oferta. Należy jednostki SKU dla każdego obrazu, który chcesz opublikować.

1. Wypełnianie **szczegóły jednostki SKU** sekcji w następującej postaci:

   Wypełnij następujące pola:

   * **Tytuł**: Podaj tytuł tej jednostki SKU. Ten tytuł pojawi się w galerii dla tego elementu.
   * **Podsumowanie**: Podaj krótkie podsumowanie dla tej wersji produktu. Ten tekst jest wyświetlany pod tytułem.
   * **Opis elementu**: Podaj szczegółowy opis jednostki SKU.
   * **Typ jednostki SKU**: dozwolone wartości *zarządzanej aplikacji* i *szablony rozwiązań*. W tym przypadku wybierz *zarządzanej aplikacji*.
   * **Dostępność Kraj/Region**: Wybierz kraje, w którym znajduje się aplikacja zarządzana.
   * **Cennik**: przewidzieć cenę zarządzania aplikacji. Wybierz dostępny krajów przed ustawieniem cenę.

1. Dodaj nowy pakiet. Wypełnianie **informacje szczegółowe dotyczące pakietu** sekcji w następującej postaci:

   Wypełnij następujące pola:

   * **Bieżąca wersja**: wprowadź wersję pakietu przekazywania. Powinna być w formacie `{number}.{number}.{number}{number}`.
   * **Wybierz plik pakietu**: ten pakiet zawiera dwa wymagane pliki skompresowane do pakietu ZIP. Jeden plik jest szablonu usługi Resource Manager, który definiuje zasoby w celu wdrożenia dla aplikacji zarządzanej. Drugi plik definiuje [interfejsu użytkownika](create-uidefinition-overview.md) dla konsumentów wdrażanie zarządzanych aplikacji za pośrednictwem portalu. W interfejsie użytkownika należy określić elementy, które umożliwiają klientom podać wartości parametrów.
   * **PrincipalId**: Ta właściwość jest identyfikator usługi Azure Active Directory (Azure AD) użytkownika, grupy użytkowników lub aplikacji, który został udzielony dostęp do zasobów w subskrypcji klienta. Definicja roli Opisuje uprawnienia.
   * **Definicja roli**: Ta właściwość jest lista wszystkich kontroli dostępu opartej na rolach (RBAC) role wbudowane dostarczane przez usługę Azure AD. Możesz wybrać rolę, która jest najbardziej odpowiednia na potrzeby zarządzania zasobami w imieniu klienta.

Można dodać wiele zezwolenia. Zaleca się utworzenie grupy użytkowników usługi AD i określić jej identyfikator w **PrincipalId**. W ten sposób można dodać więcej użytkowników do grupy użytkowników, bez konieczności aktualizacji jednostki SKU.

Aby uzyskać więcej informacji o RBAC, zobacz [wprowadzenie RBAC w portalu Azure](../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formularz Marketplace

Formularz Marketplace wprowadza się pola, które są widoczne na [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i na [portalu Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Identyfikatory subskrypcji w wersji zapoznawczej

Wprowadź listę identyfikatorów, które mogą uzyskać dostęp do oferty po opublikowaniu subskrypcji platformy Azure. Te subskrypcje wymienione biały można użyć do testowania przeglądanego oferty, przed wprowadzeniem go na żywo. Można kompilować białą listę maksymalnie 100 subskrypcji w portalu dla partnerów.

### <a name="suggested-categories"></a>Kategorii sugerowanych

Wybierz kategorie do pięciu z Twoją ofertę może być najlepiej skojarzony z listy. Te kategorie są używane do mapowania kategorie produktów, które są dostępne w Twojej oferty [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [portalu Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Podsumowanie aplikacji zarządzanej wyświetla następujące pola:

![Podsumowanie witryny Marketplace](./media/publish-marketplace-app/publishvm10.png)

**Omówienie** karcie dla aplikacji zarządzanej wyświetla następujące pola:

![Omówienie portalu Marketplace](./media/publish-marketplace-app/publishvm11.png)

**Planów + cennik** karcie dla aplikacji zarządzanej wyświetla następujące pola:

![Plany Marketplace](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

Podsumowanie aplikacji zarządzanej wyświetla następujące pola:

![Portal podsumowania](./media/publish-marketplace-app/publishvm12.png)

Omówienie aplikacji zarządzanej wyświetla następujące pola:

![Przegląd portalu](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Wytycznych dotyczących logo

Skorzystaj z następujących wskazówek dla dowolnego logo, które można przekazać w portalu dla partnerów chmury:

*   Projekt platformy Azure ma paletę kolorów proste. Ogranicz liczbę podstawowy i pomocniczy kolorów w logo.
*   Kolorów motywu portalu są białe i czarne. Nie używaj kolorów te jako kolor tła dla logo. Użyj kolor, który sprawia, że logo jest widoczne w portalu. Zaleca się proste kolorów podstawowych. *Jeśli używasz przezroczyste tło, upewnij się, logo i tekst są białe, czarne lub niebieski.*
*   Nie używaj gradientu tła na logo.
*   Nie należy umieszczać na logo, nawet Twoja firma lub marką tekstu. Wygląd i działanie logo powinny być płaski i uniknąć gradienty.
*   Upewnij się, że nie jest rozciągana logo.

#### <a name="hero-logo"></a>Logo bohater

Logo bohater jest opcjonalna. Wydawcy można zrezygnować z przekazać logo bohater. Po przekazaniu ikona bohater nie można usunąć. W tym czasie partnera musi postępuj zgodnie z wytycznymi Marketplace, bohater ikon.

Skorzystaj z następujących wskazówek bohater ikony logo:

*   Nazwa wyświetlana wydawca, tytuł planu i długie Podsumowanie oferty są wyświetlane w biały. W związku z tym nie należy używać jasny kolor tła ikon bohater. Czarne, białe lub przezroczyste tło nie jest dozwolona dla bohater ikon.
*   Po oferty jest wyświetlana, elementy wewnątrz logo bohater są osadzone programowo. Osadzone elementy obejmują nazwę wyświetlaną wydawca, tytuł planu, długie Podsumowanie oferty i **Utwórz** przycisku. W rezultacie nie wprowadź tekst, podczas projektowania logo bohater. Pozostaw puste miejsce po prawej stronie, ponieważ tekst stanowi programowo, w tym miejscu. Puste miejsce na tekst powinien być 415 x 100 pikseli po prawej stronie. Jest on przesunięcia 370 pikseli z lewej strony.

    ![Przykład logo bohater](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Obsługa formularza

Wypełnianie **obsługuje** formularza z obsługą kontaktuje się z Twojej firmy. Te informacje mogą inżynierii, kontaktów i kontaktów pomocy technicznej klienta.

## <a name="publish-an-offer"></a>Publikowanie oferty

Po wypełnieniu wszystkie sekcje wybierz **publikowania** można uruchomić procesu, który udostępnia klientom ofertę.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Aby dowiedzieć się, jak publikowanie aplikacji zarządzanych katalogu usług, zobacz [Utwórz i publikowanie aplikacji katalogu usług zarządzanych](publish-service-catalog-app.md).