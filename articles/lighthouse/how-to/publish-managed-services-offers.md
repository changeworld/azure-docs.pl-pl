---
title: Publikowanie oferty usługi zarządzanej w portalu Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi zarządzanej, która dołącza klientów do zarządzania zasobami delegowanymi platformy Azure.
ms.date: 04/08/2020
ms.topic: conceptual
ms.openlocfilehash: 247f711188fa10de19cece27f164fdfa71612d1b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991913"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikowanie oferty usługi zarządzanej w portalu Azure Marketplace

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami usługi zarządzanej do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [aplikacji Utwórz nową ofertę usługi zarządzanej,](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) aby zarządzać zmigrowanymi ofertami.

W tym artykule dowiesz się, jak opublikować publiczną lub prywatną ofertę usługi zarządzanej w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) przy użyciu portalu cloud [partner.](https://cloudpartner.azure.com/) Klienci, którzy zakupią ofertę, mogą następnie dołączać subskrypcje i grupy zasobów do [zarządzania zasobami delegowanymi platformy Azure.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Aby tworzyć i publikować oferty, musisz mieć [prawidłowe konto w Centrum partnerskim.](../../marketplace/partner-center-portal/create-account.md) Jeśli nie masz jeszcze konta, [proces rejestracji](https://aka.ms/joinmarketplace) doprowadzi Cię przez etapy tworzenia konta w Centrum partnerów i rejestrowania się w programie Marketplace komercyjnych.

Zgodnie z [wymaganiami certyfikacji oferty managed service](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)musisz mieć [poziom kompetencji Silver lub Gold Cloud Platform](https://docs.microsoft.com/partner-center/learn-about-competencies) lub być [usługą Azure Expert MSP,](https://partner.microsoft.com/membership/azure-expert-msp) aby opublikować ofertę usługi zarządzanej.

Identyfikator sieci Partner Network (MPN) firmy Microsoft zostanie [automatycznie skojarzony](../../billing/billing-partner-admin-link-started.md) z publikowanych ofertami w celu śledzenia wpływu na zaangażowanie klientów.

> [!NOTE]
> Jeśli nie chcesz publikować oferty w portalu Azure Marketplace, możesz ręcznie dołączać klientów przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Dołączanie klienta do zarządzania zasobami delegowanymi platformy Azure](onboard-customer.md).

Publikowanie oferty usługi zarządzanej jest podobne do publikowania dowolnego innego typu oferty w portalu Azure Marketplace. Aby dowiedzieć się więcej o ogólnym procesie publikowania, zobacz [Przewodnik publikowania w portalu Azure Marketplace i AppSource](../../marketplace/marketplace-publishers-guide.md). Należy również zapoznać się z [zasadami certyfikacji rynku komercyjnego,](https://docs.microsoft.com/legal/marketplace/certification-policies)w szczególności sekcją [Usługi zarządzane.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Gdy klient doda ofertę, będzie mógł delegować jedną lub więcej subskrypcji lub grup zasobów, które następnie będą [dołączane do zarządzania zasobami delegowanymi platformy Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Każdy plan w ofercie usługi zarządzanej zawiera sekcję **Szczegóły manifestu,** w której definiujesz jednostki usługi Azure Active Directory (Azure AD) w dzierżawie, które będą miały dostęp do delegowanych grup zasobów i/lub subskrypcji dla klientów, którzy zakupią ten plan. Ważne jest, aby mieć świadomość, że każda grupa (lub użytkownik lub usługodawca), który zostanie uwzględniony, będzie miała takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z każdym klientem, musisz opublikować oddzielny [plan prywatny,](../../marketplace/private-offers.md) który jest dostępny wyłącznie dla każdego klienta.

## <a name="create-your-offer"></a>Stwórz swoją ofertę

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. Z lewego menu nawigacji wybierz **pozycję Nowa oferta**, a następnie wybierz pozycję Usługi **zarządzane**.
3. Zobaczysz sekcję **Edytor** dla oferty z czterema częściami do wypełnienia: **Ustawienia oferty,** **Plany,** **Marketplace**i **Wsparcie.** Czytaj dalej, aby uzyskać wskazówki dotyczące wypełniania tych sekcji.

### <a name="enter-offer-settings"></a>Wprowadź ustawienia oferty

W sekcji **Ustawienia oferty** podaj następujące informacje:

|Pole  |Opis  |
|---------|---------|
|**Identyfikator oferty**     | Unikatowy identyfikator oferty (w profilu wydawcy). Ten identyfikator może zawierać tylko małe litery alfanumeryczne, kreski i podkreślenia, maksymalnie 50 znaków. Pamiętaj, że identyfikator oferty może być widoczny dla klientów w miejscach takich jak adresy URL produktów i raporty rozliczeniowe. Po opublikowaniu oferty nie można zmienić tej wartości.        |
|**Identyfikator wydawcy**     | Identyfikator wydawcy, który będzie skojarzony z ofertą. Jeśli masz więcej niż jeden identyfikator wydawcy, możesz wybrać ten, którego chcesz użyć w tej ofercie.       |
|**Nazwa**     | Nazwa (do 50 znaków), które klienci zobaczą dla Twojej oferty w portalu Azure Marketplace i w witrynie Azure portal. Użyj rozpoznawalnej nazwy marki, którą zrozumieją klienci — jeśli promujesz tę ofertę za pośrednictwem własnej witryny sieci Web, pamiętaj, aby użyć tej samej nazwy tutaj.        |

Po zakończeniu wybierz pozycję **Zapisz**. Teraz możesz przejść do sekcji **Plany.**

### <a name="create-plans"></a>Tworzenie planów

Każda oferta musi mieć jeden lub więcej planów (czasami nazywanych jednostkami SKU). Możesz dodać wiele planów do obsługi różnych zestawów funkcji w różnych cenach lub dostosować określony plan dla ograniczonej grupy odbiorców określonych klientów. Klienci mogą wyświetlać plany, które są dla nich dostępne w ramach oferty nadrzędnej.

W sekcji Plany wybierz pozycję **Nowy plan**. Następnie wprowadź **identyfikator planu**. Ten identyfikator może zawierać tylko małe litery alfanumeryczne, kreski i podkreślenia, maksymalnie 50 znaków. Identyfikator planu może być widoczny dla klientów w miejscach takich jak adresy URL produktów i raporty rozliczeniowe. Po opublikowaniu oferty nie można zmienić tej wartości.

#### <a name="plan-details"></a>Szczegóły planu

Wykonaj następujące sekcje w sekcji **Szczegóły planu:**

|Pole  |Opis  |
|---------|---------|
|**Tytuł**     | Przyjazna nazwa planu wyświetlania. Maksymalna długość 50 znaków.        |
|**Podsumowanie**     | Zwięzły opis planu wyświetlania pod tytułem. Maksymalna długość 100 znaków.        |
|**Opis**     | Opis tekstu, który zawiera bardziej szczegółowe wyjaśnienie planu.         |
|**Model rozliczania**     | Istnieją 2 modele rozliczeń pokazano tutaj, ale należy wybrać **Bring własną licencję** dla ofert usług zarządzanych. Oznacza to, że użytkownik będzie rozliczał klientów bezpośrednio za koszty związane z tą ofertą, a firma Microsoft nie pobiera żadnych opłat.   |
|**Czy jest to plan prywatny?**     | Wskazuje, czy jednostka SKU jest prywatna czy publiczna. Wartość domyślna to **Nie** (publiczne). Jeśli opuścisz ten wybór, twój plan nie będzie ograniczony do konkretnych klientów (lub do określonej liczby klientów); po opublikowaniu planu publicznego nie można później zmienić go na prywatny. Aby udostępnić ten plan tylko określonym klientom, wybierz opcję **Tak**. Gdy to zrobisz, musisz zidentyfikować klientów, podając ich identyfikatory subskrypcji. Można je wprowadzić jeden po drugim (dla maksymalnie 10 subskrypcji) lub przesłać plik csv (dla maksymalnie 20 000 subskrypcji). Pamiętaj, aby dołączyć własne subskrypcje tutaj, dzięki czemu można przetestować i zweryfikować ofertę. Aby uzyskać więcej informacji, zobacz [Prywatne jednostki SKU i plany](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, użyj planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do niektórych klientów, a nawet do określonej liczby klientów (chociaż możesz całkowicie zaprzestać sprzedaży planu, jeśli to zrobisz). Dostęp [do delegowania](onboard-customer.md#remove-access-to-a-delegation) można usunąć po zaakceptowaniu oferty przez klienta tylko wtedy, gdy podczas publikowania oferty została uwzględniona **autoryzacja** z **definicją roli** ustawioną na [Rolę usuwania przypisania rejestracji usług zarządzanych.](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Możesz również skontaktować się z klientem i poprosić go o [usunięcie dostępu.](view-manage-service-providers.md#add-or-remove-service-provider-offers)

#### <a name="manifest-details"></a>Szczegóły manifestu

Uzupełnij sekcję **Szczegóły manifestu** dla swojego planu. Spowoduje to utworzenie manifestu z informacjami o autoryzacji do zarządzania zasobami klienta. Te informacje są wymagane w celu umożliwienia zarządzania zasobami delegowanymi platformy Azure.

> [!NOTE]
> Jak wspomniano powyżej, użytkownicy i role we wpisach **autoryzacji** będą miały zastosowanie do każdego klienta, który zakupi plan. Jeśli chcesz ograniczyć dostęp do określonego klienta, musisz opublikować prywatny plan do wyłącznego użytku.

Najpierw podaj **wersję** dla manifestu. Użyj formatu *n.n.n* (na przykład 1.2.5).

Następnie wprowadź identyfikator **dzierżawy**. Jest to identyfikator GUID skojarzony z identyfikatorem dzierżawy usługi Azure Active Directory w organizacji (tj. dzierżawą, w której będziesz pracować w celu zarządzania zasobami klientów). Jeśli nie masz tego pod ręką, możesz go znaleźć, najeżdżając kursorem na nazwę konta w prawym górnym rogu **witryny**Azure portal lub wybierając katalog Przełącznik .

Na koniec dodaj jeden lub więcej wpisów **autoryzacji** do planu. Autoryzacje definiują jednostki, które mogą uzyskiwać dostęp do zasobów i subskrypcji dla klientów, którzy kupują plan, i przypisują role, które przyznają określone poziomy dostępu.

> [!TIP]
> W większości przypadków należy przypisać uprawnienia do grupy użytkowników usługi Azure AD lub jednostki usługi, a nie do serii kont indywidualnych użytkowników. Dzięki temu można dodać lub usunąć dostęp dla poszczególnych użytkowników bez konieczności aktualizowania i ponownego publikowania planu po zmianie wymagań dostępu. Aby uzyskać dodatkowe zalecenia, zobacz [dzierżawy, role i użytkowników w scenariuszach latarni morskiej platformy Azure.](../concepts/tenants-users-roles.md)

Dla każdej **autoryzacji**należy podać następujące informacje. Następnie można wybrać **nową autoryzację** tyle razy, ile potrzeba, aby dodać więcej użytkowników i definicje ról.

- **Identyfikator obiektu usługi Azure AD:** identyfikator usługi Azure AD użytkownika, grupy użytkowników lub aplikacji, który otrzyma określone uprawnienia (zgodnie z opisem w definicji roli) do zasobów klientów.
- **Nazwa wyświetlania obiektu usługi Azure AD:** przyjazna nazwa ułatwiające klientowi zrozumienie celu tej autoryzacji. Klient zobaczy tę nazwę podczas delegowannia zasobów.
- **Definicja roli:** Wybierz jedną z dostępnych ról wbudowanych usługi Azure AD z listy. Ta rola określi uprawnienia, które użytkownik w polu **identyfikator obiektu usługi Azure AD** będzie miał w zasobach klientów. Aby uzyskać opisy tych ról, zobacz [Wbudowane role](../../role-based-access-control/built-in-roles.md) i [Obsługa ról dla zarządzania zasobami delegowanymi platformy Azure](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > W stosownych przypadkach nowe wbudowane role są dodawane do platformy Azure, staną się one dostępne w tym miejscu, chociaż może wystąpić pewne opóźnienie, zanim się pojawią.
- **Przypisywalne role:** Jest to wymagane tylko wtedy, gdy w **definicji roli** dla tej autoryzacji wybrano administratora dostępu użytkownika. Jeśli tak, należy dodać w tym miejscu jedną lub więcej ról, które można przypisać. Użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mógł przypisać te **przypisywalne role** do tożsamości [zarządzanych,](../../active-directory/managed-identities-azure-resources/overview.md)co jest wymagane do [wdrożenia zasad, które można naprawić.](deploy-policy-remediation.md) Należy zauważyć, że żadne inne uprawnienia zwykle skojarzone z rolą Administrator dostępu użytkownika nie będą miały zastosowania do tego użytkownika. Jeśli nie wybierzesz w tym miejscu jednej lub więcej ról, twoje przesłanie nie przejdzie certyfikacji. (Jeśli nie wybrano administratora dostępu użytkownika dla definicji roli tego użytkownika, to pole nie ma wpływu).

> [!TIP]
> Aby upewnić się, że w razie potrzeby można [usunąć dostęp do delegacji,](onboard-customer.md#remove-access-to-a-delegation) dołącz **autoryzację** z **definicją roli** ustawioną na Rolę [usuwania przydziału rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

Po zakończeniu tych informacji możesz wybrać **nowy plan** tyle razy, ile potrzeba, aby utworzyć dodatkowe plany. Po zakończeniu wybierz pozycję **Zapisz**, a następnie przejdź do sekcji **Marketplace.**

### <a name="provide-marketplace-text-and-images"></a>Dostarczanie tekstu i obrazów w portalu Marketplace

W sekcji **Marketplace** udostępniasz tekst i obrazy, które klienci będą widzieć w portalu Azure Marketplace i witrynie Azure Portal.

Wykonaj następujące pola w sekcji **Przegląd:**

|Pole  |Opis  |
|---------|---------|
|**Tytuł**     |  Tytuł oferty, często długa, formalna nazwa. Ten tytuł będzie wyświetlany w widocznym miejscu na rynku. Maksymalna długość 50 znaków. W większości przypadków powinno to być takie samo, jak **nazwa** wprowadzona w sekcji **Ustawienia oferty.**       |
|**Podsumowanie**     | Krótki cel lub funkcja Twojej oferty. Jest to zwykle wyświetlane pod tytułem. Maksymalna długość 100 znaków.        |
|**Długie podsumowanie**     | Dłuższe podsumowanie celu lub funkcji Twojej oferty. Maksymalna długość 256 znaków.        |
|**Opis**     | Więcej informacji o ofercie. To pole ma maksymalną długość 3000 znaków i obsługuje proste formatowanie HTML. W opisie należy podać słowa "usługa zarządzana" lub "usługi zarządzane".       |
|**Identyfikator marketingowy**     | Unikatowy identyfikator przyjazny dla adresów URL. Ten identyfikator może zawierać tylko małe litery alfanumeryczne znaki i myślniki. Będzie on używany w adresach URL Marketplace dla tej oferty. Jeśli na przykład identyfikator wydawcy to *contoso,* a identyfikator marketingowy to *sampleApp*, *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp*adres URL oferty w portalu Azure Marketplace będzie .        |
|**Identyfikatory subskrypcji w wersji zapoznawczej**     | Dodaj jeden do 100 identyfikatorów subskrypcji. Klienci skojarzony z tymi subskrypcjami będą mogli wyświetlić ofertę w portalu Azure Marketplace, zanim przejdzie ona do pracy. Firma Microsoft sugeruje uwzględnienie własnych subskrypcji w tym miejscu, aby można było wyświetlić podgląd oferty w portalu Azure Marketplace przed udostępnieniem jej klientom.  (Zespoły pomocy technicznej i inżynierów firmy Microsoft będą również mogły wyświetlać ofertę w tym okresie wersji zapoznawczej).   |
|**Przydatne linki**     | Adresy URL związane z ofertą, takie jak dokumentacja, informacje o wersji, często zadawane pytania itp.        |
|**Sugerowane kategorie (maksymalnie 5)**     | Jedna lub więcej kategorii (do pięciu), które mają zastosowanie do Twojej oferty. Te kategorie pomagają klientom odkrywać twoją ofertę w portalu Azure Marketplace i witrynie Azure Portal.        |

W sekcji **Artefakty marketingowe** możesz przesłać logo i inne zasoby, które mają być wyświetlane wraz z ofertą. Opcjonalnie możesz przesłać zrzuty ekranu lub linki do filmów, które pomogą klientom zrozumieć Twoją ofertę.

Wymagane są cztery rozmiary logo: **Mały (40x40),** **Średni (90x90),** **Duży (115x115)** i **Szeroki (255x115)**. Postępuj zgodnie z tymi wskazówkami dotyczącymi logo:

- Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
- Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe.
- Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.
- Logo powinno być proste i bez gradientów. Nie używaj w logo tła gradientowego.
- Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki.
- Upewnij się, że logo nie jest rozciągnięte.

Logo **Hero (815x290)** jest opcjonalne, ale zalecane. Jeśli dodasz logo bohatera, postępuj zgodnie z tymi wskazówkami:

- Nie umieszczaj żadnego tekstu w logo bohatera i pamiętaj, aby po prawej stronie logo pozostawić 415 pikseli pustej przestrzeni. Jest to wymagane, aby pozostawić miejsce dla elementów tekstowych, które zostaną osadzone programowo: nazwa wyświetlana wydawcy, tytuł planu, oferują długie podsumowanie.
- Tło logo bohatera może nie być czarne, białe lub przezroczyste. Upewnij się, że kolor tła nie jest zbyt jasny, ponieważ osadzony tekst będzie wyświetlany w kolorze białym.
- Po opublikowaniu oferty z ikoną bohatera nie można jej usunąć (chociaż w razie potrzeby możesz ją zaktualizować w innej wersji).

W sekcji **Zarządzanie potencjalnymi klientami** można wybrać system CRM, w którym będą przechowywane twoi potencjalni klienci. Należy zauważyć, że zgodnie z [zasadami certyfikacji usług zarządzanych](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)wymagane jest **miejsce docelowe potencjalnego klienta.**

Na koniec podaj **adres URL polityki prywatności** i Warunki **użytkowania** w sekcji **Prawna.** W tym miejscu można również określić, czy korzystać z [umowy standardowej](../../marketplace/standard-contract.md) dla tej oferty.

Przed przejściem do sekcji Pomoc **techniczna** należy zapisać zmiany.

### <a name="add-support-info"></a>Dodawanie informacji o pomocy technicznej

W sekcji **Pomoc techniczna** podaj nazwę, adres e-mail i numer telefonu dla kontaktu inżyniera i kontaktu z działem obsługi klienta. Musisz również podać adresy URL pomocy technicznej. Firma Microsoft może korzystać z tych informacji, gdy musimy skontaktować się z Użytkownikiem w sprawie problemów biznesowych i pomocy technicznej.

Po dodaniu tych informacji wybierz pozycję **Zapisz.**

## <a name="publish-your-offer"></a>Opublikuj swoją ofertę

Po zakończeniu wszystkich sekcji następnym krokiem jest opublikowanie oferty w portalu Azure Marketplace. Wybierz przycisk **Publikuj,** aby zainicjować proces tworzenia oferty na żywo. Aby uzyskać więcej informacji na temat tego procesu, zobacz [Publikowanie ofert portalu Azure Marketplace i AppSource.](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md)

W każdej chwili możesz [opublikować zaktualizowaną wersję swojej oferty.](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) Na przykład można dodać nową definicję roli do wcześniej opublikowanej oferty. Gdy to zrobisz, klienci, którzy już dodali ofertę zobaczą ikonę na stronie [**Dostawcy usług**](view-manage-service-providers.md) w witrynie Azure portal, która informuje ich, że dostępna jest aktualizacja. Każdy klient będzie mógł [przejrzeć zmiany](view-manage-service-providers.md#update-service-provider-offers) i zdecydować, czy chce zaktualizować do nowej wersji. 

## <a name="the-customer-onboarding-process"></a>Proces dołączania klienta

Po dodaniem oferty przez klienta będzie można [delegować jedną lub więcej określonych subskrypcji lub grup zasobów,](view-manage-service-providers.md#delegate-resources)które następnie będą dołączane do zarządzania zasobami delegowanymi platformy Azure. Jeśli klient zaakceptował ofertę, ale nie przekazał jeszcze żadnych zasobów, zobaczy notatkę w górnej części dostawcy **oferuje** sekcji [**dostawcy usługi**](view-manage-service-providers.md) strony strony w witrynie Azure portal.

> [!IMPORTANT]
> Delegowanie musi być wykonywane przez konto niebędące gościem w dzierżawie klienta, który ma [wbudowaną rolę Właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) dla subskrypcji jest dołączany (lub który zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie klienta może wybrać subskrypcję w portalu Azure, otworzyć **formant dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą Właściciel](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Gdy klient deleguje subskrypcję (lub jedną lub więcej grup zasobów w ramach subskrypcji), dostawca zasobów **Microsoft.ManagedServices** zostanie zarejestrowany dla tej subskrypcji, a użytkownicy w dzierżawie będą mogli uzyskać dostęp do zasobów delegowanych zgodnie z uprawnieniami w ofercie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rynku komercyjnym](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlanie klientów i zarządzanie nimi,](view-manage-customers.md) przechodząc do **witryny Moi klienci** w witrynie Azure portal.
