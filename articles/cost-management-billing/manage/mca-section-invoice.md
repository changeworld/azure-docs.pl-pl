---
title: Organizuj fakturę w zależności od potrzeb — Azure
description: Dowiedz się, jak organizować koszt na fakturze.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991063"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organizowanie kosztów przez dostosowanie konta rozliczeniowego

Konto rozliczeniowe dla umowy klienta firmy Microsoft zapewnia elastyczność organizowania kosztów w zależności od potrzeb niezależnie od tego, czy jest to przez dział, projekt czy środowisko programistyczne. 

W tym artykule opisano, jak można użyć Azure Portal do organizowania kosztów. Ma to zastosowanie do konta rozliczeniowego umowy klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Tworzenie struktury konta za pomocą profilów rozliczeń i sekcji faktur

Na koncie rozliczeń dla umowy klienta firmy Microsoft należy używać profilów rozliczeń i sekcji faktur do organizowania kosztów.

![Zrzut ekranu przedstawiający hierarchię rozliczeń umowy klienta firmy Microsoft](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Profil rozliczeniowy

Profil rozliczeniowy reprezentuje fakturę i powiązane informacje rozliczeniowe, takie jak formy płatności i adres rozliczeniowy. Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera opłaty za użycie platformy Azure i inne zakupy w poprzednim miesiącu.

Profil rozliczeniowy jest tworzony automatycznie wraz z kontem rozliczeniowym podczas tworzenia konta na platformie Azure. Można utworzyć dodatkowe profile rozliczeń w celu zorganizowania kosztów w wielu fakturach miesięcznych. 

> [!IMPORTANT]
>
> Tworzenie dodatkowych profilów rozliczeń może mieć wpływ na całkowity koszt. Aby uzyskać więcej informacji, zobacz [zagadnienia, które należy wziąć pod uwagę przy dodawaniu nowych profilów rozliczeń](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Sekcja faktury

Sekcja faktury reprezentuje grupowanie kosztów na fakturze. Sekcja faktury jest automatycznie tworzona dla każdego profilu rozliczania na Twoim koncie. Możesz utworzyć dodatkowe sekcje w celu zorganizowania kosztów na podstawie Twoich potrzeb. Każda sekcja faktury jest wyświetlana na fakturze z opłatami za ten miesiąc. 

Na poniższej ilustracji przedstawiono fakturę zawierającą dwie sekcje faktury — Inżynieria i marketing. Podsumowanie i szczegółowe opłaty dla każdej sekcji są wyświetlane na fakturze. Ceny pokazane na tym obrazie służą wyłącznie do celów i nie stanowią rzeczywistych cen usług platformy Azure. 

![Obraz przedstawiający fakturę z sekcjami](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Struktura konta rozliczeniowego dla typowych scenariuszy

W tej sekcji opisano typowe scenariusze organizowania kosztów i odpowiednich struktur kont rozliczeń:

|Scenariusz  |Struktura  |
|---------|---------|
|Podpisywanie gniazd na platformie Azure i wymaga pojedynczej faktury miesięcznej. | Sekcja profilu rozliczenia i faktury. Ta struktura jest automatycznie ustawiana dla gniazda podczas zalogowania się do platformy Azure i nie wymaga żadnych dodatkowych kroków. |

![Grafika informacyjna dotycząca prostego scenariusza rozliczania](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenariusz  |Struktura  |
|---------|---------|
|Firma Contoso to mała organizacja, która wymaga pojedynczej faktury miesięcznej, ale pogrupuje koszty według ich działów — Marketing i finanse.  | Profil rozliczeń dla firmy Contoso i sekcja faktury dla działu Marketing i finanse. |

![Grafika informacyjna dotycząca prostego scenariusza rozliczania](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenariusz  |Struktura  |
|---------|---------|
|Fabrikam to organizacja średniej wielkości, która potrzebuje oddzielnych faktur dla działów inżynieryjnych i marketingowych. W przypadku działu inżynierów chcą grupować koszty według środowisk — produkcja i programowanie.  | Profil rozliczeniowy każdy dla działów marketingu i finansów. Dla działu marketingu, sekcja faktury dla środowiska produkcyjnego i programistycznego. |

![Grafika informacyjna dotycząca prostego scenariusza rozliczania](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Utwórz nową sekcję faktury

Aby utworzyć sekcję faktury, musisz być **właścicielem profilu rozliczeniowego** lub **współautorem profilu rozliczeniowego**. Aby uzyskać więcej informacji, zobacz [Zarządzanie sekcjami faktury dla profilu rozliczania](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/mca-section-invoice/search-cmb.png)

3. Wybierz pozycję **Profile rozliczeń** z okienka po lewej stronie. Z listy wybierz profil rozliczania. Nowa sekcja zostanie wyświetlona na podstawie wybranej faktury profilu rozliczenia. 

   [Zrzut ekranu ![pokazujący listę profilów rozliczeń](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Wybierz **sekcje faktury** z okienka po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.

   [![zrzut ekranu, który pokazuje dodawanie sekcji faktury](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Wprowadź nazwę sekcji faktury. 

   [Zrzut ekranu ![pokazujący stronę tworzenia sekcji faktury](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Wybierz pozycję **Utwórz**.

## <a name="create-a-new-billing-profile"></a>Utwórz nowy profil rozliczeń

Aby utworzyć profil rozliczeń, musisz być **właścicielem konta rozliczeń** lub **współautorem konta rozliczeniowego**. Aby uzyskać więcej informacji, zobacz [Zarządzanie profilami rozliczeń dla konta rozliczeniowego](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Tworzenie dodatkowych profilów rozliczeń może mieć wpływ na całkowity koszt. Aby uzyskać więcej informacji, zobacz [zagadnienia, które należy wziąć pod uwagę przy dodawaniu nowych profilów rozliczeń](#things-to-consider-when-adding-new-billing-profiles).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/mca-section-invoice/search-cmb.png)

3. Wybierz pozycję **Profile rozliczeń** z okienka po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.

   [Zrzut ekranu ![pokazujący listę profilów rozliczeń](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Jeśli nie widzisz przycisku Dodaj na stronie profil rozliczania, ta funkcja nie jest dostępna dla Twojego konta. Obecnie jest ona dostępna tylko dla kont, które zostały skonfigurowane podczas pracy z przedstawicielem firmy Microsoft.

4. Wypełnij formularz i kliknij przycisk **Utwórz**.

   [Zrzut ekranu ![pokazujący stronę tworzenia profilu rozliczeń](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Pole  |Definicja  |
    |---------|---------|
    |Nazwa     | Nazwa wyświetlana, która pomaga w łatwym identyfikowaniu profilu rozliczeń w Azure Portal.  |
    |Numer zamówienia zakupu    | Opcjonalny numer zamówienia zakupu. Numer zamówienia zakupu będzie wyświetlany na fakturach wygenerowanych dla profilu rozliczania. |
    |Adres do faktury   | Adres rozliczeniowy będzie wyświetlany na fakturach wygenerowanych dla profilu rozliczania. |
    |Faktura e-mail   | Zaznacz pole Faktura e-mail, aby otrzymywać faktury dla tego profilu rozliczeń za pośrednictwem poczty e-mail. Jeśli nie zdecydujesz się na uczestnictwo w programie, możesz wyświetlać i pobierać faktury w Azure Portal.|

5. Wybierz pozycję **Utwórz**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Połącz opłaty z sekcjami faktur i profilami rozliczeń

Po dostosowaniu konta rozliczeniowego na podstawie Twoich potrzeb można połączyć subskrypcje i inne produkty z odpowiednią sekcją faktury i profilem rozliczeń.

### <a name="link-a-new-subscription"></a>Łączenie nowej subskrypcji

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wyszukaj pozycję **Subskrypcje**.

   [Zrzut ekranu ![, który pokazuje wyszukiwanie w portalu dla subskrypcji](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Wybierz pozycję **Dodaj** w górnej części strony.

   ![Zrzut ekranu przedstawiający przycisk Dodaj w widoku Subskrypcje](./media/mca-section-invoice/subscription-add.png)

4. Jeśli masz dostęp do wielu kont rozliczeń, wybierz konto rozliczeniowe umowy klienta firmy Microsoft.

   ![Zrzut ekranu przedstawiający przycisk Dodaj w widoku Subskrypcje](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Wybierz profil rozliczeń, dla którego zostanie naliczona opłata za użycie subskrypcji. Opłaty za użycie platformy Azure i inne zakupy dla tej subskrypcji będą naliczane na podstawie wybranej faktury profilu rozliczenia.

6. Wybierz sekcję faktura, aby połączyć opłaty za subskrypcję. Opłaty będą wyświetlane w tej sekcji na fakturze profilu rozliczenia.

7. Wybierz plan platformy Azure, a następnie wprowadź przyjazną nazwę subskrypcji. 

9. Kliknij przycisk **Utwórz**.  

### <a name="link-existing-subscriptions-and-products"></a>Łączenie istniejących subskrypcji i produktów

Jeśli masz istniejące subskrypcje platformy Azure lub inne produkty, takie jak Azure Marketplace i zasoby źródłowe aplikacji, możesz je przenieść z istniejącej sekcji faktury do innej sekcji faktury, aby zreorganizować koszty. 

> [!IMPORTANT]
>
> Subskrypcje i inne produkty można przenosić tylko między sekcjami faktur, które należą do tego samego profilu rozliczeń. Przeniesienie subskrypcji i produktów między sekcjami faktur w różnych profilach rozliczeń nie jest obsługiwane.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/mca-section-invoice/search-cmb.png)

3. Aby połączyć subskrypcję z nową sekcją faktury, wybierz opcję **subskrypcje platformy Azure** z lewej strony ekranu. W przypadku innych produktów, takich jak Azure Marketplace i zasoby źródłowe aplikacji, wybierz pozycję **opłaty cykliczne**.

   [![zrzut ekranu przedstawiający opcję zmiany sekcji faktury](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Na stronie kliknij wielokropek (trzy kropki) dla subskrypcji lub produktu, który chcesz połączyć z nową sekcją faktury. Wybierz **sekcję Zmień fakturę**.

5. Wybierz sekcję Nowa faktura z listy rozwijanej. Lista rozwijana będzie zawierać tylko sekcje faktur, które są skojarzone z tym samym profilem rozliczeń co istniejąca sekcja faktury.

    [![zrzut ekranu, który pokazuje Wybieranie nowej sekcji faktury](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Wybierz pozycję **Zapisz**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Kwestie, które należy wziąć pod uwagę przy dodawaniu nowych profilów rozliczeń

### <a name="azure-usage-charges-may-be-impacted"></a>Może to mieć wpływ na opłaty za korzystanie z platformy Azure

Na koncie rozliczeniowym dla umowy klienta firmy Microsoft użycie platformy Azure jest agregowane dla każdego profilu rozliczeń. Ceny dla zasobów platformy Azure z warstwowymi cenami są określane na podstawie użycia osobno dla każdego profilu rozliczania. Użycie nie jest agregowane w profilach rozliczeń podczas obliczania ceny. Może to mieć wpływ na ogólne koszty użycia platformy Azure dla kont z wieloma profilami rozliczeń.

Przyjrzyjmy się przykładowi, jak koszty różnią się w przypadku dwóch scenariuszy. Ceny używane w scenariuszach są przeznaczone wyłącznie do celów i nie reprezentują rzeczywistych cen usług platformy Azure.

#### <a name="you-only-have-one-billing-profile"></a>Masz tylko jeden profil rozliczania.

Załóżmy, że korzystasz z magazynu blokowych obiektów blob platformy Azure, który ma koszt USD. 00184 za GB dla pierwszych 50 terabajtów (TB), a następnie 00177 za GB dla następnych 450 terabajtów (TB). W subskrypcjach, które są rozliczane w ramach Twojego profilu rozliczeń, użyto 100 TB

|  Cennik warstwy (USD) |Ilość | Kwota (USD)|
|---------|---------|---------|
|1,84 za TB na pierwsze 50 TB/miesiąc    | 50 TB        | 92,0   |
|1,77 za TB na następne 450 TB/miesiąc    |  50 TB         | 88,5   |
|Łącznie     |     100 TB  | 180,5

Łączne opłaty za korzystanie z 100 TB danych w tym scenariuszu to **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Masz wiele profilów rozliczeń.

Załóżmy, że utworzono inny profil rozliczeń i wykorzystano 50 GB za pomocą subskrypcji, które są rozliczane do pierwszego profilu rozliczeń i 50 GB za pomocą subskrypcji, które są rozliczane w drugim profilu rozliczania.

`Charges for the first billing profile`

|  Cennik warstwy (USD) |Ilość | Kwota (USD)|
|---------|---------|---------|
|1,84 za TB na pierwsze 50 TB/miesiąc    | 50 TB        | 92,0  |
|1,77 za TB na następne 450 TB/miesiąc    |  0 TB         | 0.0  |
|Łącznie     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Cennik warstwy (USD) |Ilość | Kwota (USD)|
|---------|---------|---------|
|1,84 za TB na pierwsze 50 TB/miesiąc    | 50 TB        | 92,0  |
|1,77 za TB na następne 450 TB/miesiąc    |  0 TB         | 0.0  |
|Łącznie     |     50 TB  | 92,0 

Łączne opłaty za korzystanie z 100 TB danych w tym scenariuszu to **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Korzyści z rezerwacji na platformie Azure mogą nie dotyczyć wszystkich subskrypcji

Rezerwacje platformy Azure z zakresem udostępnionym są stosowane do subskrypcji w pojedynczym profilu rozliczania i nie są współużytkowane przez profile rozliczeń. 

![Grafika informacyjna dotycząca aplikacji rezerwacji dla różnej struktury konta rozliczeniowego](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Na powyższym obrazie Contoso ma dwie subskrypcje. Korzyść rezerwacji platformy Azure jest stosowana inaczej, w zależności od tego, jak konto rozliczeń ma strukturę. W scenariuszu po lewej stronie korzyść rezerwacji jest stosowana do obu subskrypcji, które są rozliczane w profilu rozliczeń inżynieryjnych. W tym scenariuszu korzyść rezerwacji zostanie zastosowana tylko do subskrypcji 1, ponieważ jedyną subskrypcją jest rozliczany profil rozliczeń inżynieryjnych. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie dodatkowej subskrypcji platformy Azure dla Umowy klienta firmy Microsoft](create-subscription.md)
- [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Uzyskiwanie własności rozliczeń subskrypcji platformy Azure od użytkowników z innych kont rozliczeniowych](mca-request-billing-ownership.md)
