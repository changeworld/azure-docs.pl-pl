---
title: Zrozumienie zakresów Azure Cost Management i korzystanie z nich
description: Ten artykuł pomaga zrozumieć zakresy zarządzania rozliczeniami i zasobami dostępne na platformie Azure oraz jak korzystać z zakresów w Cost Management i interfejsy API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 58bd1d3e3fb27344706b23866a68c7e1363e7ec2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990140"
---
# <a name="understand-and-work-with-scopes"></a>Opis zakresów i praca z nimi

Ten artykuł pomaga zrozumieć zakresy zarządzania rozliczeniami i zasobami dostępne na platformie Azure oraz jak korzystać z zakresów w Cost Management i interfejsy API.

## <a name="scopes"></a>Zakresy

_Zakres_ to węzeł w hierarchii zasobów platformy Azure, w którym użytkownicy usługi Azure AD uzyskują dostęp do usług i zarządzają nimi. Większość zasobów platformy Azure jest tworzonych i wdrażanych w grupach zasobów, które są częścią subskrypcji. Firma Microsoft oferuje również dwie hierarchie powyżej subskrypcji platformy Azure, które mają wyspecjalizowane role do zarządzania danymi rozliczeń:
- Dane dotyczące rozliczeń, takie jak płatności i faktury
- Usługi w chmurze, takie jak zarządzanie kosztami i zasadami

Zakresy są miejscem, w którym są zarządzane dane dotyczące rozliczeń, mają role specyficzne dla płatności, wyświetlają faktury i przeprowadzają ogólne zarządzanie kontami. Role rozliczeń i kont są zarządzane oddzielnie od tych używanych do zarządzania zasobami, które korzystają z funkcji [RBAC platformy Azure](../../role-based-access-control/overview.md). Aby wyraźnie odróżnić intencje oddzielnych zakresów, w tym różnice kontroli dostępu, są one określane odpowiednio jako _zakresy rozliczeń_ i _zakresy RBAC_.

## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management używa zakresów

Cost Management działa we wszystkich zakresach powyżej zasobów, aby umożliwić organizacjom zarządzanie kosztami na poziomie, na którym mają dostęp, niezależnie od tego, czy jest to całe konto rozliczania czy jedna grupa zasobów. Chociaż zakresy rozliczeniowe różnią się w zależności od umowy firmy Microsoft (typu subskrypcji), zakresy RBAC nie.

## <a name="azure-rbac-scopes"></a>Zakresy RBAC platformy Azure

Platforma Azure obsługuje trzy zakresy zarządzania zasobami. Każdy zakres obsługuje zarządzanie dostępem i nadzorem, w tym między innymi zarządzanie kosztami.

- [**Grupy zarządzania**](../../governance/management-groups/overview.md) — kontenery hierarchiczne, do ośmiu poziomów, do organizowania subskrypcji platformy Azure.

    Typ zasobu: [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Subskrypcje** — podstawowe kontenery dla zasobów platformy Azure.

    Typ zasobu: [Microsoft. resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupy zasobów**](../../azure-resource-manager/management/overview.md#resource-groups) — logiczne grupowanie powiązanych zasobów dla rozwiązania platformy Azure, które współużytkują ten sam cykl życia. Na przykład zasoby, które są wdrażane i usuwane razem.

    Typ zasobu: [Microsoft. resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Grupy zarządzania umożliwiają organizowanie subskrypcji w hierarchię. Można na przykład utworzyć logiczną hierarchię organizacyjną przy użyciu grup zarządzania. Następnie nadaj zespołom subskrypcje dla obciążeń produkcyjnych i deweloperskich i testowych. Następnie utwórz grupy zasobów w ramach subskrypcji, aby zarządzać każdym podsystemem lub składnikiem.

Tworzenie hierarchii organizacyjnej pozwala na zbiorczą zgodność kosztów i zasad. Następnie każdy lider może wyświetlać i analizować bieżące koszty. A następnie mogą tworzyć budżety, aby ograniczyć niewłaściwe wzorce wydatków i zoptymalizować koszty z zaleceniami klasyfikatora na najniższym poziomie.

Przyznanie dostępu do wyświetlania kosztów i opcjonalnego zarządzania konfiguracją kosztów, takich jak budżety i eksporty, odbywa się na zakresach ładu przy użyciu funkcji RBAC platformy Azure. Funkcja RBAC systemu Azure umożliwia użytkownikom i grupom usługi Azure AD dostęp do wykonywania wstępnie zdefiniowanego zestawu akcji zdefiniowanych w roli w określonym zakresie i poniżej. Na przykład rola przypisana do zakresu grupy zarządzania również przyznaje te same uprawnienia do zagnieżdżonych subskrypcji i grup zasobów.

Cost Management obsługuje następujące wbudowane role dla każdego z następujących zakresów:

- [**Owner**](../../role-based-access-control/built-in-roles.md#owner) — umożliwia wyświetlanie kosztów i zarządzanie wszystkimi elementami, w tym konfiguracją kosztów.
- [**Współautor**](../../role-based-access-control/built-in-roles.md#contributor) — umożliwia wyświetlanie kosztów i zarządzanie wszystkimi, w tym konfiguracjami kosztów, ale z wyłączeniem kontroli dostępu.
- [**Reader**](../../role-based-access-control/built-in-roles.md#reader) — może wyświetlać wszystko, w tym dane dotyczące kosztów i konfigurację, ale nie może wprowadzać żadnych zmian.
- [**Współautor Cost Management**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) — umożliwia wyświetlanie kosztów, zarządzanie konfiguracją kosztów i wyświetlanie zaleceń.
- [**Cost Management Reader**](../../role-based-access-control/built-in-roles.md#cost-management-reader) — może wyświetlać dane dotyczące kosztów, konfigurację kosztów i wyświetlać zalecenia.

Współautor Cost Management jest zalecaną rolą najniższych uprawnień. Dzięki temu użytkownicy mogą tworzyć budżety i eksporty oraz zarządzać nimi w celu skuteczniejszego monitorowania i raportowania kosztów. Współautorzy Cost Management mogą również wymagać dodatkowych ról do obsługi kompleksowych scenariuszy zarządzania kosztami. Poniżej przedstawiono przykładowe scenariusze:

- **Postępuj w przypadku przekroczenia budżetów** — Cost Management Współautorzy potrzebują także dostępu do tworzenia grup akcji i/lub zarządzania nimi w celu automatycznego reagowania na nadwyżkowe. Rozważ przyznanie [współautora monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) do grupy zasobów zawierającej grupę akcji do użycia w przypadku przekroczenia progów budżetu. Automatyzacja określonych akcji wymaga dodatkowych ról dla określonych usług, takich jak Automatyzacja i Azure Functions.
- **Planowanie eksportu danych kosztów** — współautorzy Cost Management muszą także mieć dostęp do zarządzania kontami magazynu, aby zaplanować eksport w celu skopiowania danych na konto magazynu. Rozważ przydzielenie [współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor) do grupy zasobów zawierającej konto magazynu, w którym eksportowane są dane kosztów.
- **Wyświetlanie zaleceń dotyczących oszczędności kosztów** — Cost Management czytelnicy i współautorzy Cost Management mają dostęp do *wyświetlania* zaleceń dotyczących kosztów. Jednak dostęp do działania zgodnie z zaleceniami dotyczącymi kosztów wymaga dostępu do poszczególnych zasobów. Rozważ udzielenie [roli specyficznej dla usługi](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions) , jeśli chcesz korzystać z rekomendacji opartej na kosztach.

## <a name="enterprise-agreement-scopes"></a>Zakresy Umowa Enterprise

Konta rozliczeń Umowa Enterprise (EA), nazywane również rejestracjami, mają następujące zakresy:

- [**Konto rozliczeniowe**](../manage/view-all-accounts.md) — reprezentuje rejestrację w ramach umowy EA. Faktury są generowane w tym zakresie. Zakupy, które nie są oparte na użyciu, takie jak Marketplace i rezerwacje, są dostępne tylko w tym zakresie. Nie są one reprezentowane przez działy ani konta rejestracji.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Dział** — opcjonalne grupowanie kont rejestracji.

    Typ zasobu: `Billing/billingAccounts/departments`

- **Konto rejestracji** — reprezentuje właściciela pojedynczego konta. Nie obsługuje udzielania dostępu do wielu osobom.

    Typ zasobu: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Chociaż zakresy ładu są powiązane z pojedynczym katalogiem, zakresy rozliczeniowe EA nie są. Konto rozliczeniowe EA może mieć subskrypcje w dowolnej liczbie katalogów usługi Azure AD.

Zakresy rozliczeń EA obsługują następujące role:

- **Administrator przedsiębiorstwa** — może zarządzać ustawieniami konta rozliczeń i dostępem, może wyświetlać wszystkie koszty i zarządzać konfiguracją kosztów. Na przykład budżety i eksporty. W funkcji zakres rozliczeniowy EA jest taki sam jak [Cost Management współautor roli RBAC platformy Azure](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Użytkownik tylko do odczytu przedsiębiorstwa** — może wyświetlać ustawienia konta rozliczeniowego, dane kosztów i konfigurację kosztów. Na przykład budżety i eksporty. W funkcji zakres rozliczeń EA jest taki sam jak dla [roli RBAC Cost Management czytnika Azure](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrator działu** — może zarządzać ustawieniami działu, takimi jak centrum kosztów, oraz uzyskiwać dostęp do wszystkich kosztów i zarządzać konfiguracją kosztów. Na przykład budżety i eksporty.  Aby można było wyświetlać koszty, dla administratorów działu i użytkowników tylko do odczytu musi być włączone ustawienie konto rozliczeniowe **opłat w widoku da** . Jeśli **opłaty za widok da** nie są wyłączone, użytkownicy działu nie mogą zobaczyć kosztów na żadnym poziomie, nawet jeśli są właścicielami konta lub subskrypcji.
- **Użytkownik tylko do odczytu działu** — może wyświetlać ustawienia działu, dane kosztów i konfigurację kosztów. Na przykład budżety i eksporty. Jeśli **opłaty za widok da** nie są wyłączone, użytkownicy działu nie mogą zobaczyć kosztów na żadnym poziomie, nawet jeśli są właścicielami konta lub subskrypcji.
- **Właściciel konta** — umożliwia zarządzanie ustawieniami konta rejestracji (na przykład centrum kosztów), wyświetlanie wszystkich kosztów i zarządzanie konfiguracją kosztów (takich jak budżety i eksporty) dla konta rejestracji. Aby można było wyświetlać koszty, dla właścicieli kont i użytkowników RBAC należy włączyć ustawienie **Ao Wyświetl opłaty** .

Użytkownicy konta rozliczania EA nie mają bezpośredniego dostępu do faktur. Faktury są dostępne w zewnętrznym systemie licencjonowania zbiorowego.

Subskrypcje platformy Azure są zagnieżdżone w obszarze konta rejestracji. Użytkownicy rozliczeń mają dostęp do danych kosztów dla subskrypcji i grup zasobów, które znajdują się w odpowiednich zakresach. Nie mają dostępu do wyświetlania zasobów w Azure Portal ani zarządzania nimi. Użytkownicy rozliczeń mogą wyświetlać koszty, przechodząc do **Cost Management i rozliczeń** na Azure Portal liście usług. Następnie mogą filtrować koszty do określonych subskrypcji i grup zasobów, których potrzebują do raportowania.

Użytkownicy rozliczeń nie mają dostępu do grup zarządzania, ponieważ nie są one bezpośrednio objęte określonym kontem rozliczeniowym. Dostęp do grup zarządzania musi być przyznany jawnie. Grupy zarządzania są kosztami zbiorczymi ze wszystkich zagnieżdżonych subskrypcji. Jednak zawierają one tylko zakupy oparte na użyciu. Nie obejmują one zakupów, takich jak rezerwacje i oferty w portalu Marketplace innych firm. Aby wyświetlić te koszty, użyj konta rozliczania EA.

## <a name="individual-agreement-scopes"></a>Zakresy umów indywidualnych

Subskrypcje platformy Azure utworzone na podstawie indywidualnych ofert, takich jak płatność zgodnie z rzeczywistym użyciem i powiązane typy, takie jak bezpłatna wersja próbna i oferty deweloperskie/testowe, nie mają jawnego zakresu konta rozliczeniowego. Zamiast tego każda subskrypcja ma właściciela konta lub administratora konta, takiego jak właściciel konta EA.

- [**Konto rozliczeniowe**](../manage/view-all-accounts.md) — reprezentuje właściciela pojedynczego konta dla co najmniej jednej subskrypcji platformy Azure. Obecnie nie obsługuje udzielania dostępu do wielu osób ani dostępu do widoków zagregowanych kosztów.

    Typ zasobu: nie dotyczy

Indywidualni Administratorzy konta subskrypcji platformy Azure mogą wyświetlać dane dotyczące rozliczeń, takie jak faktury i płatności, a także zarządzać nimi, w [centrum konta platformy Azure](https://account.azure.com/subscriptions). Nie mogą jednak wyświetlać danych kosztów ani zarządzać zasobami w Azure Portal. Aby udzielić dostępu do administratora konta, użyj wcześniej wymienionych ról Cost Management.

W przeciwieństwie do umowy EA Administratorzy indywidualnych kont subskrypcji platformy Azure mogą zobaczyć swoje faktury w Azure Portal. Należy pamiętać, że role Cost Management Reader i Cost Management współautor nie zapewniają dostępu do faktur. Aby uzyskać więcej informacji, zobacz [jak udzielić dostępu do faktur](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Zakresy umów klienta firmy Microsoft

Konta rozliczeń umowy klienta firmy Microsoft mają następujące zakresy:

- **Konto rozliczeniowe** — reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. Konta rozliczeń umowy klienta nie działają tak samo jak rejestracje EA. Rejestracje EA są bardziej ściśle wyrównane do profilów rozliczeń.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profil rozliczeń** — definiuje subskrypcje dołączone do faktury. Profile rozliczeń to funkcjonalny odpowiednik rejestracji w ramach umowy EA, ponieważ jest to zakres, w którym są generowane faktury. Podobnie zakupy, które nie są oparte na użyciu (takie jak witryna Marketplace i rezerwacje), są dostępne tylko w tym zakresie. Nie znajdują się w sekcji faktur.

    Typ zasobu: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Sekcja faktury** — reprezentuje grupę subskrypcji w profilu faktury lub rozliczeń. Sekcje faktury są podobne do działów — wiele osób może mieć dostęp do sekcji faktury.

    Typ zasobu: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Klient** — reprezentuje grupę subskrypcji powiązanych z konkretnym klientem, który jest dołączony do umowy klienta firmy Microsoft przez partnera. Ten zakres jest specyficzny dla dostawcy CSP.

W przeciwieństwie do zakresów rozliczeniowych EA, konta rozliczeń umowy klienta _są_ powiązane z pojedynczym katalogiem i nie mogą zawierać subskrypcji wielu katalogów usługi Azure AD.

Zakresy rozliczeń umowy klienta nie mają zastosowania do partnerów. Role i uprawnienia partnerów są udokumentowane w obszarze [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

Zakresy rozliczeń umowy klienta obsługują następujące role:

- **Owner** — umożliwia zarządzanie ustawieniami rozliczeń i dostępem, wyświetlanie wszystkich kosztów i zarządzanie konfiguracją kosztów. Na przykład budżety i eksporty. W funkcji zakres rozliczeniowy umowy klienta jest taki sam jak [Cost Management współautor roli RBAC platformy Azure](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Współautor** — umożliwia zarządzanie ustawieniami rozliczeń z wyjątkiem dostępu, wyświetlanie wszystkich kosztów i zarządzanie konfiguracją kosztów. Na przykład budżety i eksporty. W funkcji zakres rozliczeniowy umowy klienta jest taki sam jak [Cost Management współautor roli RBAC platformy Azure](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Reader** — można wyświetlać ustawienia rozliczeń, dane kosztów i konfigurację kosztów. Na przykład budżety i eksporty. W funkcji, ten zakres rozliczeń umowy klienta jest taki sam, jak w przypadku [roli RBAC Cost Management czytnika platformy Azure](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Menedżer faktur** — może wyświetlać i płacić faktury oraz wyświetlać dane i konfigurację kosztów. Na przykład budżety i eksporty. W funkcji, ten zakres rozliczeń umowy klienta jest taki sam, jak w przypadku [roli RBAC Cost Management czytnika platformy Azure](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Kreator subskrypcji platformy Azure** — umożliwia tworzenie subskrypcji platformy Azure, wyświetlanie kosztów i zarządzanie konfiguracją kosztów. Na przykład budżety i eksporty. W funkcji zakres rozliczeniowy umowy klienta jest taki sam jak w przypadku roli właściciela konta rejestracji EA.

Subskrypcje platformy Azure są zagnieżdżone w sekcji faktury, podobnie jak w przypadku kont rejestracji EA. Użytkownicy rozliczeń mają dostęp do danych kosztów dla subskrypcji i grup zasobów, które znajdują się w odpowiednich zakresach. Nie mają jednak dostępu do wyświetlania zasobów i zarządzania nimi w Azure Portal. Użytkownicy rozliczeń mogą wyświetlać koszty, przechodząc do **Cost Management i rozliczeń** na Azure Portal liście usług. Następnie odfiltruj koszty do określonych subskrypcji i grup zasobów, których potrzebują do raportowania.

Użytkownicy rozliczeń nie mają dostępu do grup zarządzania, ponieważ nie są one jawnie objęte kontem rozliczania. Jednak po włączeniu grup zarządzania dla organizacji wszystkie koszty subskrypcji są zestawiane na konto rozliczeniowe i do głównej grupy zarządzania, ponieważ są one ograniczone do jednego katalogu. Grupy zarządzania obejmują tylko zakupy, które są oparte na użyciu. Zakupy, takie jak rezerwacje i oferty w portalu Marketplace innych firm nie są uwzględniane w grupach zarządzania. W związku z tym konto rozliczeń i główna Grupa zarządzania mogą raportować różne sumy. Aby wyświetlić te koszty, użyj konta rozliczeń lub odpowiedniego profilu rozliczania.

## <a name="aws-scopes"></a>Zakresy AWS

Po zakończeniu integracji AWS zapoznaj się z tematem Konfigurowanie [i Konfigurowanie integracji AWS](aws-integration-set-up-configure.md). Dostępne są następujące zakresy:

- **Konto rozliczeń zewnętrznych** — reprezentuje umowę klienta z dostawcą innej firmy. Jest to podobne do konta rozliczeń EA.

    Typ zasobu: `Microsoft.CostManagement/externalBillingAccounts`

- **Subskrypcja zewnętrzna** — reprezentuje konto operacyjne klienta z niezależnym dostawcą. Jest to podobne do subskrypcji platformy Azure.

    Typ zasobu: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Zakresy dostawcy rozwiązań w chmurze (CSP)

Następujące zakresy są obsługiwane dla dostawców usług kryptograficznych dla klientów z umową klienta firmy Microsoft:

- **Konto rozliczeniowe** — reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. Konta rozliczeń umowy klienta nie działają tak samo jak rejestracje EA. Rejestracje EA są bardziej ściśle wyrównane do profilów rozliczeń.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profil rozliczeń** — definiuje subskrypcje dołączone do faktury. Profile rozliczeń to funkcjonalny odpowiednik rejestracji w ramach umowy EA, ponieważ jest to zakres, w którym są generowane faktury. Podobnie zakupy, które nie są oparte na użyciu (takie jak witryna Marketplace i rezerwacje), są dostępne tylko w tym zakresie.

    Typ zasobu: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Klient** — reprezentuje grupę subskrypcji powiązanych z konkretnym klientem, który jest dołączony do umowy klienta firmy Microsoft przez partnera.

Tylko użytkownicy z rolami *administrator globalny* i *administrator mogą* zarządzać kosztami kont rozliczeń, profilów rozliczeń i klientów bezpośrednio w dzierżawie platformy Azure partnera. Aby uzyskać więcej informacji na temat ról Centrum partnerskiego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

Azure Cost Management obsługuje tylko klientów partnerskich dostawcy usług kryptograficznych, jeśli klienci mają umowę klienta firmy Microsoft. W przypadku klientów z dostawcami usług kryptograficznych, którzy nie znajdują się jeszcze w umowie klienta firmy Microsoft, zobacz [Centrum partnerskie](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Przełączanie między zakresami w Cost Management

Wszystkie widoki Cost Management w Azure Portal obejmują wybór **zakresu** pill w lewym górnym rogu widoku. Użyj go, aby szybko zmienić zakres. Kliknij **zakres** pill, aby otworzyć selektor zakresu. Są w nim wyświetlane konta rozliczeniowe, główna Grupa zarządzania i wszystkie subskrypcje, które nie są zagnieżdżone w głównej grupie zarządzania. Aby wybrać zakres, kliknij tło, aby je zaznaczyć, a następnie kliknij przycisk **Wybierz** w dolnej części. Aby przejść do szczegółów zakresów zagnieżdżonych, takich jak grupy zasobów w subskrypcji, kliknij link Nazwa zakresu. Aby wybrać zakres nadrzędny na dowolnym poziomie zagnieżdżonym, kliknij pozycję **zaznacz ten &lt;zakres&gt;** w górnej części selektora zakresu.

## <a name="identify-the-resource-id-for-a-scope"></a>Identyfikowanie identyfikatora zasobu dla zakresu

Podczas pracy z interfejsami API Cost Management, wiedząc, że zakres jest krytyczny. Poniższe informacje służą do tworzenia prawidłowego identyfikatora URI zakresu dla Cost Management interfejsów API.

### <a name="billing-accounts"></a>Konta rozliczeniowe

1. Otwórz Azure Portal a następnie przejdź do **Cost Management i rozliczeń** na liście usług.
2. Wybierz pozycję **Właściwości** w menu konto rozliczeń.
3. Skopiuj identyfikator konta rozliczeniowego.
4. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profile rozliczeniowe

1. Otwórz Azure Portal a następnie przejdź do **Cost Management i rozliczeń** na liście usług.
2. Wybierz pozycję **Profile rozliczeń** w menu konto rozliczeń.
3. Kliknij nazwę żądanego profilu rozliczania.
4. Wybierz pozycję **Właściwości** w menu Profil rozliczeń.
5. Skopiuj konto rozliczeniowe i identyfikatory profilów rozliczeń.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sekcje faktury

1. Otwórz Azure Portal a następnie przejdź do **Cost Management i rozliczeń** na liście usług.
2. Wybierz **sekcje faktury** w menu konto rozliczeń.
3. Kliknij nazwę żądanej sekcji faktury.
4. Wybierz pozycję **Właściwości** w menu sekcja faktury.
5. Skopiuj identyfikatory konta rozliczenia i sekcji faktury.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Działy EA

1. Otwórz Azure Portal a następnie przejdź do **Cost Management i rozliczeń** na liście usług.
2. Wybierz pozycję **działy** w menu Konto rozliczenia.
3. Kliknij nazwę żądanego działu.
4. Wybierz pozycję **Właściwości** w menu dział.
5. Skopiuj konto rozliczeniowe i identyfikatory działów.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Konto rejestracji EA

1. Otwórz Azure Portal i przejdź do **Cost Management i rozliczeń** na liście usług.
2. W menu konto rozliczeniowe wybierz pozycję **konta rejestracji** .
3. Kliknij nazwę żądanego konta rejestracji.
4. Wybierz pozycję **Właściwości** w menu konto rejestracji.
5. Skopiuj konto rozliczeniowe i identyfikator konta rejestracji.
6. Zakres jest: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupa zarządzania

1. Otwórz Azure Portal i przejdź do **grup zarządzania** na liście usług.
2. Przejdź do żądanej grupy zarządzania.
3. Skopiuj identyfikator grupy zarządzania z tabeli.
4. Zakres jest: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subskrypcja

1. Otwórz Azure Portal i przejdź do obszaru **subskrypcje** na liście usług.
2. Skopiuj identyfikator subskrypcji z tabeli.
3. Zakres jest: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupy zasobów

1. Otwórz Azure Portal i przejdź do **grup zasobów** na liście usług.
2. Kliknij nazwę żądanej grupy zasobów.
3. Wybierz pozycję **Właściwości** w menu Grupa zasobów.
4. Skopiuj wartość pola Identyfikator zasobu.
5. Zakres jest: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management jest obecnie obsługiwana w globalnych i [Azure Government](https://management.usgovcloudapi.net)na [platformie Azure](https://management.azure.com) . Aby uzyskać więcej informacji na temat Azure Government, zobacz [punkty końcowe interfejsu API globalnego i administracji publicznej platformy Azure](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Następne kroki

- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).
