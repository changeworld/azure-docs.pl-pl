---
title: I Praca z zakresów w usłudze Azure Cost Management | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia zapoznanie Pomoc dotycząca rozliczeń i zasobów zakresy zarządzania dostępne na platformie Azure i jak używać zakresów w Cost Management i interfejsów API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 717c0f110ebbeee53e2c9b9207350385288d57c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991378"
---
# <a name="understand-and-work-with-scopes"></a>Opis zakresów i praca z nimi

Ten artykuł ułatwia zapoznanie Pomoc dotycząca rozliczeń i zasobów zakresy zarządzania dostępne na platformie Azure i jak używać zakresów w Cost Management i interfejsów API.

## <a name="scopes"></a>Zakresy

A _zakres_ jest węzłem w hierarchii zasobów platformy Azure, w których użytkowników usługi Azure AD dostęp do usług i zarządzania nimi. Zasoby najbardziej platformy Azure są tworzone i wdrażane w grupach zasobów, które są dostępne w ramach subskrypcji. Firma Microsoft oferuje także dwoma hierarchiami powyżej subskrypcji platformy Azure, które mają wyspecjalizowane ról na potrzeby zarządzania danych dotyczących rozliczeń:
- Dane dotyczące rozliczeń, takich jak płatności i faktur
- Usługi w chmurze, takich jak zarządzanie kosztami i zasady

Zakresy są, gdzie możesz zarządzać danymi rozliczeń, ma specyficzne role płatności, wyświetlanie faktur i przeprowadzanie zarządzania kontami ogólnego. Kontami i rozliczeniami role są zarządzane oddzielnie od używanych do zarządzania zasobami, którego [Azure RBAC](../role-based-access-control/overview.md). Wyraźne rozróżnienie celem zakresy oddzielnych, włącznie z różnicami kontroli dostępu, są one określane jako _rozliczeń zakresy_ i _zakresy RBAC_odpowiednio.

## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management używa zakresów

Usługa Cost Management działa na wszystkich zakresów powyżej zasobów, aby umożliwić organizacjom zarządzanie kosztami na poziomie, w którym mają dostęp, czy to całe konto rozliczeniowe lub pojedynczą grupę zasobów. Mimo że rozliczeń zakresy zależą od umowie firmy Microsoft (typ subskrypcji), nie są zakresy RBAC.

## <a name="azure-rbac-scopes"></a>Zakresy Azure RBAC

Platforma Azure obsługuje trzy zakresy do zarządzania zasobami. Każdego zakresu obsługuje zarządzanie dostępu i zarządzania, w tym, ale nie wyłącznie, kosztów zarządzania.

- [**Grupy zarządzania** ](../governance/management-groups/index.md) — kontenery hierarchiczne, maksymalnie ośmiu poziomów, aby zorganizować subskrypcje platformy Azure.

    Typ zasobu: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Subskrypcje** -głównej kontenery dla zasobów platformy Azure.

    Typ zasobu: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupy zasobów** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -logiczne grupowanie powiązanych zasobów dla rozwiązania platformy Azure, które współużytkują ten sam cykl życia. Na przykład zasoby, które są wdrożone i usuwane razem.

    Typ zasobu: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Grupy zarządzania umożliwiają organizowanie subskrypcji do hierarchii. Na przykład może utworzyć hierarchię organizację logiczną przy użyciu grup zarządzania. Następnie należy przypisać subskrypcje zespołów dla celów produkcyjnych i obciążeń deweloperskich/testowych. A następnie utwórz grupy zasobów w ramach subskrypcji, aby zarządzać każdego podsystemu lub składnika.

Tworzenie hierarchii organizacyjnych umożliwia, koszt i zgodności z zasadami zbiorczy organizacji. Następnie każdy lidera można przeglądać i analizować swoje bieżące koszty. A następnie można utworzyć budżetów ograniczenia nieprawidłowych wzorców wydatków i optymalizować koszty przy użyciu zalecenia usługi Advisor na najniższym poziomie.

Udzielanie dostępu do wyświetlania kosztów i opcjonalnie Zarządzanie konfiguracją kosztów, takich jak budżetów i eksportuje, odbywa się na zakresy nadzoru przy użyciu RBAC platformy Azure. Udzielanie użytkownikom usługi Azure AD przy użyciu kontroli RBAC usługi Azure i grup dostępu do wykonania wstępnie zdefiniowany zestaw akcji, które są zdefiniowane w roli w określonym zakresie i poniżej. Na przykład rola przypisana do zakresu grupy zarządzania również daje takie same uprawnienia w zagnieżdżonych subskrypcji i grup zasobów.

Usługa Cost Management obsługuje następujące wbudowane role dla każdego z następujących zakresów:

- [**Właściciel** ](../role-based-access-control/built-in-roles.md#owner) — można wyświetlić koszty i zarządzać nimi, wszystko w tym konfiguracją kosztów.
- [**Współautor** ](../role-based-access-control/built-in-roles.md#contributor) — można wyświetlić koszty i zarządzać nimi, wszystko w tym konfiguracją kosztów, z wyłączeniem kontroli dostępu.
- [**Czytnik** ](../role-based-access-control/built-in-roles.md#reader) — może przeglądać wszystko, włącznie z danymi kosztów i konfiguracji, ale nie może wprowadzać żadnych zmian.
- [**Koszt Współautor zarządzania** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) — można wyświetlić koszty, zarządzanie konfiguracją kosztów oraz zalecenia.
- [**Koszt zarządzania czytnika** ](../role-based-access-control/built-in-roles.md#cost-management-reader) — mogą wyświetlać dane kosztów, konfiguracja kosztów i Wyświetl zalecenia.

Współautor zarządzania kosztami jest zalecaną najniższych rolą. Ona umożliwia użytkownikom dostęp do tworzenia i zarządzania nimi budżetów i eksportuje aby skutecznie, monitorowanie i raportowanie na kosztach. Współautorzy zarządzania kosztami może również wymagać dodatkowych ról do obsługi scenariuszy zarządzania kosztami end-to-end. Należy rozważyć następujące scenariusze:

- **Działanie po przekroczeniu budżetu** — koszt zarządzania współautorzy potrzebują również dostępu do tworzenia i/lub Zarządzanie grup akcji w celu automatycznego reagowania na opłaty za dodatkowe transakcje. Należy rozważyć udzielenie [Współautor monitorowania](../role-based-access-control/built-in-roles.md#monitoring-contributor) do grupy zasobów, która zawiera grupy akcji do użycia podczas przekroczeniu progów budżetu. Automatyzowanie określonych akcji wymaga dodatkowych ról dla określonych usług, które są używane, takich jak usługi Automation i Azure Functions.
- **Harmonogram koszt Eksport danych** — koszt zarządzania współautorzy potrzebują również dostępu do zarządzanie kontami magazynu, aby zaplanować eksportu do skopiowania danych na konto magazynu. Należy rozważyć udzielenie [Współautor konta magazynu](../role-based-access-control/built-in-roles.md#storage-account-contributor) do grupy zasobów, który zawiera magazyn kont, których danych kosztów są eksportowane.
- **Wyświetlanie zaleceń oszczędne** — czytelnicy Zarządzanie kosztami i kosztów zarządzania współautorzy mają dostęp do *widoku* koszt zalecenia domyślnie. Dostęp do działania w rekomendacji dotyczących kosztu wymaga jednak dostępu do poszczególnych zasobów. Należy rozważyć udzielenie [specyficzne dla usługi roli](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) chcącym zajmującym się zalecenia na podstawie kosztów.

## <a name="enterprise-agreement-scopes"></a>Zakresy umowy Enterprise Agreement

Konta rozliczeń Enterprise Agreement (EA), jest określana skrótem rejestracji, są wymienione poniżej zakresy:

- [**Konto rozliczeniowe** ](../billing/billing-view-all-accounts.md) — reprezentuje rejestracji umowy EA. Faktury są generowane w tym zakresie. Zakupy, które nie są na podstawie użycia, takich jak witryny Marketplace i zastrzeżenia, które są dostępne tylko w tym zakresie. Nie są one reprezentowane w działów lub konta rejestracji.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Dział** — jest to opcjonalne, grupowanie kont rejestracji.

    Typ zasobu: `Billing/billingAccounts/departments`

- **Konta rejestracji** — reprezentuje właściciela jednego konta. Nie obsługuje udzielanie dostępu do wielu osób.

    Typ zasobu: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Mimo że nadzoru zakresy są powiązane z jednym katalogu, nie są zakresy rozliczeniowym umowy EA. Konto rozliczeniowe EA może mieć subskrypcji w dowolnej liczbie katalogów usługi Azure AD.

Zakresy rozliczeń EA obsługują następujące role:

- **Administrator przedsiębiorstwa** — umożliwia zarządzanie rozliczeń ustawień konta i dostępem, można wyświetlić wszystkie koszty i może zarządzać konfiguracją kosztów. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji EA rozliczeń zakres jest taka sama jak [rolę RBAC platformy Azure Współautor zarządzania kosztami](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Użytkownik tylko do odczytu przedsiębiorstwa** — można wyświetlić ustawienia konta rozliczeniowego, dane kosztów i konfiguracji kosztów. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji EA rozliczeń zakres jest taka sama jak [rolę RBAC platformy Azure czytnika zarządzania kosztami](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrator działu** — można zarządzania ustawieniami działu, takich jak Centrum kosztu i można uzyskać dostęp, wyświetlić wszystkie koszty i zarządzanie konfiguracją kosztów. Na przykład, gdy jest dostępny budżet i eksportuje.  **DA Wyświetl opłaty** rozliczeń ustawienia konta musi być włączona dla działu administratorów i użytkowników tylko do odczytu, aby zobaczyć koszty. Jeśli **DA Wyświetl opłaty** jest wyłączona, dział użytkownicy nie widzą kosztów na dowolnym poziomie, nawet jeśli leżą one właściciela konta lub subskrypcji.
- **Dział użytkownika tylko do odczytu** — można wyświetlić ustawienia działu, danych kosztów i koszt konfiguracji. Na przykład, gdy jest dostępny budżet i eksportuje. Jeśli **DA Wyświetl opłaty** jest wyłączona, dział użytkownicy nie widzą kosztów na dowolnym poziomie, nawet jeśli leżą one właściciela konta lub subskrypcji.
- **Właściciel konta** — można zarządzać ustawieniami konta rejestracji (na przykład Centrum kosztów), wyświetlić wszystkie koszty oraz zarządzać konfiguracją koszt (na przykład budżetów i eksportuje) konta rejestracji. **AO Wyświetl opłaty** rozliczeń ustawienia konta musi być włączona dla właścicieli kont z odpowiednimi i ROLACH użytkowników, aby zobaczyć koszty.

EA — konta rozliczeniowego — użytkownicy nie mają bezpośredniego dostępu do faktury. Faktury są dostępne z zewnętrznego systemu hurtowe.

Subskrypcje platformy Azure są zagnieżdżone w ramach kont rejestracji. Karta Użytkownicy mają dostęp do danych rozwiązania cost dla subskrypcji i grup zasobów, które znajdują się w odpowiednich zakresach. Nie mają dostępu, aby wyświetlić lub zarządzać zasobami w witrynie Azure portal. Rozliczenia widoczne dla użytkowników kosztów, przechodząc do **Zarządzanie kosztami i rozliczenia** w portalu Azure listę usług. Następnie mogą filtrować kosztów dla określonej subskrypcji i grup zasobów, które są im potrzebne do raportowania.

Rozliczenia, użytkownicy nie mają dostępu do grup zarządzania, ponieważ nie należą one jawnie przy użyciu określonego konta rozliczeniowego. Dostęp musi otrzymać jawnie do grup zarządzania. Koszty zbiorcze z wszystkich zagnieżdżonych subskrypcji grup zarządzania. Jednak tylko obejmują one zakupy na podstawie użycia. Nie obejmują one zakupów, takich jak zastrzeżenia i innych ofert z portalu Marketplace. Aby wyświetlić te koszty, użyj konta rozliczeniowego umowy EA.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Zakresy poszczególnych umowy (płatność za rzeczywiste użycie)

Płatność za rzeczywiste użycie (PAYG) abonamentami oraz powiązanych typów, takie jak bezpłatnej/wersji próbnej i oferty tworzenia i testowania, nie mają jawnych rozliczeń zakres konta. Zamiast tego każda subskrypcja ma właściciel konta lub administrator konta, takie jak właściciel konta EA.

- [**Konto rozliczeniowe** ](../billing/billing-view-all-accounts.md) — reprezentuje właściciela jednego konta, na co najmniej jedną subskrypcję platformy Azure. Obecnie nie obsługuje, udzielanie dostępu do wielu osób lub dostępu do widoków zagregowane kosztów.

    Typ zasobu: Nie dotyczy

Administratorzy konta subskrypcji PAYG można przeglądać i zarządzać danych dotyczących rozliczeń, takie jak faktury i płatności, z [Centrum konta platformy Azure](https://account.azure.com/subscriptions). Jednak nie można wyświetlić danych rozwiązania cost lub zarządzać zasobami w witrynie Azure portal. Aby udzielić dostępu do administratora konta, należy użyć ról Cost Management wymienionych wcześniej.

W przeciwieństwie do Umowy EA Administratorzy kont subskrypcji PAYG można zobaczyć faktur w witrynie Azure portal. Należy pamiętać, że koszty zarządzania Czytelnik i współautor zarządzania kosztami role nie zapewniają dostęp do faktury. Aby uzyskać więcej informacji, zobacz [jak udzielić dostępu do faktury PAYG](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Zakresy umowy klienta

Konta rozliczeniowego umowy klienta firmy Microsoft mają następujące zakresy:

- **Konto rozliczeniowe** — reprezentuje umowy klienta w wielu usług i produktów firmy Microsoft. Konta rozliczeniowego umowy klienta nie są funkcjonalnie taka sama jak rejestracji umowy EA. Rejestracji umowy EA dokładniej są wyrównane do rozliczeń profilów.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profil rozliczeń** -określa subskrypcje, które są objęte faktury. Profile rozliczeń są funkcjonalności wielokrotność rejestracji umowy EA, ponieważ jest zakres, który faktury są generowane podczas. Podobnie zakupów, które nie są na podstawie użycia (na przykład z witryny Marketplace i zastrzeżenia) są dostępne tylko w tym zakresie. Nie są one uwzględnione w sekcjach faktury.

    Typ zasobu: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Faktury sekcji** — reprezentuje grupę subskrypcji w faktura lub profil rozliczeniowy. Sekcje faktury są podobne do działów — wiele osób może uzyskiwać dostęp do sekcji faktury.

    Typ zasobu: `Microsoft.Billing/billingAccounts/invoiceSections`

W przeciwieństwie do Umowy EA rozliczeń zakresów, umowy klienta z rozliczeniami kont _są_ powiązany z jednym katalogu, a nie może mieć subskrypcji między wieloma katalogami usługi Azure AD.

Zakresy rozliczeniowym umowy klienta obsługują następujące role:

- **Właściciel** — może zarządzać ustawieniami rozliczeń i dostępu, wyświetlić wszystkie koszty oraz zarządzać konfiguracją kosztów. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji niniejszej umowy klient rozliczeń zakres jest taka sama jak [rolę RBAC platformy Azure Współautor zarządzania kosztami](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Współautor** — może zarządzać ustawieniami rozliczeń, z wyjątkiem dostępu, wyświetlić wszystkie koszty oraz zarządzać konfiguracją kosztów. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji niniejszej umowy klient rozliczeń zakres jest taka sama jak [rolę RBAC platformy Azure Współautor zarządzania kosztami](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Czytnik** — można wyświetlić ustawienia rozliczeń, dane kosztów i konfiguracji kosztów. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji niniejszej umowy klient rozliczeń zakres jest taka sama jak [rolę RBAC platformy Azure czytnika zarządzania kosztami](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Menedżer faktur** — można wyświetlać i płaci faktur i może wyświetlanie kosztów danych i konfiguracji. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji niniejszej umowy klient rozliczeń zakres jest taka sama jak [rolę RBAC platformy Azure czytnika zarządzania kosztami](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Twórca subskrypcji platformy Azure** — można tworzyć subskrypcje platformy Azure, wyświetlanie kosztów oraz zarządzać konfiguracją kosztów. Na przykład, gdy jest dostępny budżet i eksportuje. W funkcji ten zakres rozliczeniowym umowy klienta jest taka sama jak rola właściciela konta rejestracji umowy EA.

Subskrypcje platformy Azure są zagnieżdżone w sekcjach faktury, takich jak jak są one w ramach kont rejestracji umowy EA. Karta Użytkownicy mają dostęp do danych rozwiązania cost dla subskrypcji i grup zasobów, które znajdują się w odpowiednich zakresach. Jednak nie mają dostępu, aby wyświetlić lub zarządzać zasobami w witrynie Azure portal. Rozliczenia widoczne dla użytkowników kosztów, przechodząc do **Zarządzanie kosztami i rozliczenia** w portalu Azure listę usług. Następnie można filtrować kosztów dla określonej subskrypcji i grup zasobów, które są im potrzebne do raportowania.

Rozliczenia, użytkownicy nie mają dostępu do grup zarządzania, ponieważ jawnie nie są one objęte konta rozliczeniowego. Jednak włączenie grup zarządzania w organizacji wszelką cenę subskrypcji są rzutowane do konta rozliczeniowego oraz do głównej grupy zarządzania, ponieważ ich są zarówno ograniczone do jednego katalogu. Grupy zarządzania są uwzględnione jedynie zakupów, które są na podstawie użycia. Zakupy, takich jak zastrzeżenia i innych ofert z portalu Marketplace nie są uwzględniane w grupach zarządzania. Tak rozliczania konta i katalog główny grupie zarządzania mogą zgłaszać różne sumy. Aby wyświetlić te koszty, użyj odpowiedniego profilu rozliczeń lub konta rozliczeniowego.

## <a name="cloud-solution-provider-csp-scopes"></a>Cloud Solution Provider (CSP) zakresów

Partnerzy programu cloud Solution Provider (CSP) nie są obecnie obsługiwane w Cost Management. Zamiast tego można użyć [Centrum partnerskiego](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Przełączanie między zakresów Cost Management

Wszystkie widoki zarządzania kosztami w witrynie Azure portal obejmują **zakres** skażone w lewym górnym widoku. Umożliwia szybką zmianę zakresu. Kliknij przycisk **zakres** skażone, aby otworzyć selektor zakresu. Pokazuje konta rozliczeniowego, głównej grupy zarządzania i wszystkich subskrypcji, które nie są zagnieżdżone w głównej grupy zarządzania. Aby wybrać zakres, kliknij tło, aby go zaznaczyć, a następnie kliknij przycisk **wybierz** u dołu. Do testowania odzyskiwania po awarii — w zagnieżdżonych zakresów, takich jak grupy zasobów w ramach subskrypcji, kliknij łącze nazwa zakresu. Aby wybrać zakresu nadrzędnego na dowolnym poziomie zagnieżdżony, kliknij przycisk **wybierz tę opcję, &lt;zakres&gt;**  u góry selektora zakresu.

## <a name="identify-the-resource-id-for-a-scope"></a>Zidentyfikuj identyfikator zasobu dla zakresu

Podczas pracy z interfejsów API usługi Cost Management, wiedząc, że zasięg jest krytyczny. Skorzystaj z poniższych informacji do tworzenia zakresu prawidłowego identyfikatora URI dla interfejsów API usługi Cost Management.

### <a name="billing-accounts"></a>Rozliczanie konta

1. Otwórz witrynę Azure portal, a następnie przejdź do **Zarządzanie kosztami i rozliczenia** na liście usług.
2. Wybierz **właściwości** w menu konta rozliczeniowego.
3. Skopiuj identyfikator konta rozliczeniowego
4. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profile rozliczeń

1. Otwórz witrynę Azure portal, a następnie przejdź do **Zarządzanie kosztami i rozliczenia** na liście usług.
2. Wybierz **profile rozliczeń** w menu konta rozliczeniowego.
3. Kliknij nazwę żądanego profilu rozliczeń.
4. Wybierz **właściwości** w menu profilu rozliczeń.
5. Skopiuj konta rozliczeniowego i rozliczeń identyfikatory profilu.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sekcje faktury

1. Otwórz witrynę Azure portal, a następnie przejdź do **Zarządzanie kosztami i rozliczenia** na liście usług.
2. Wybierz **faktury sekcje** w menu konta rozliczeniowego.
3. Kliknij nazwę sekcji żądaną faktury.
4. Wybierz **właściwości** w menu sekcji faktury.
5. Skopiuj konta rozliczeniowego i faktury sekcji identyfikatorów.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Działy EA

1. Otwórz witrynę Azure portal, a następnie przejdź do **Zarządzanie kosztami i rozliczenia** na liście usług.
2. Wybierz **działów** w menu konta rozliczeniowego.
3. Kliknij nazwę żądanej działu.
4. Wybierz **właściwości** w menu działu.
5. Skopiuj rozliczeń identyfikatorów kont i działu.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Konta rejestracji umowy EA

1. Otwórz witrynę Azure portal i przejdź do **Zarządzanie kosztami i rozliczenia** na liście usług.
2. Wybierz **kont rejestracji** w menu konta rozliczeniowego.
3. Kliknij nazwę konta żądaną rejestracji.
4. Wybierz **właściwości** w menu konta rejestracji.
5. Skopiuj konta rozliczeniowego i identyfikatory kont rejestracji.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupa zarządzania

1. Otwórz witrynę Azure portal i przejdź do **grup zarządzania** na liście usług.
2. Przejdź do żądanej grupie zarządzania.
3. Skopiuj identyfikator grupy zarządzania z tabeli.
4. Zakres jest: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subskrypcja

1. Otwórz witrynę Azure portal i przejdź do **subskrypcje** na liście usług.
2. Skopiuj identyfikator subskrypcji z tabeli.
3. Zakres jest: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupy zasobów

1. Otwórz witrynę Azure portal i przejdź do **grup zasobów** na liście usług.
2. Kliknij nazwę grupy żądanego zasobu.
3. Wybierz **właściwości** w menu grupy zasobów.
4. Skopiuj wartość pola Identyfikatora zasobu.
5. Zakres jest: `"/subscriptions/{id}/resourceGroups/{name}"`

Usługa Cost Management jest obecnie obsługiwany w [Azure Global](https://management.azure.com) i [Azure dla instytucji rządowych](https://management.usgovcloudapi.net). Aby uzyskać więcej informacji na temat platformy Azure Government, zobacz [punktów końcowych globalnej platformy Azure i interfejsu API dla instytucji rządowych](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).
