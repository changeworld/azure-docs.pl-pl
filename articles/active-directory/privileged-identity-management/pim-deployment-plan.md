---
title: Wdrażanie Privileged Identity Management (PIM) — usługa Azure Active Directory | Dokumentacja firmy Microsoft
description: Opisuje sposób planowania wdrożenia usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1755d627473b0ae47bbc4bc74a3f0d2210e5372b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578206"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Wdrażanie usługi Azure AD Privileged Identity Management (PIM)

Ten przewodnik krok po kroku w tym artykule opisano sposób planowania wdrożenia programu Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w Twojej organizacji.

> [!TIP]
> W tym dokumencie pojawią się elementy oznaczone jako:
> 
> :heavy_check_mark: **Firma Microsoft zaleca**
> 
> Są to ogólne zalecenia i należy zaimplementować tylko jeśli mają one zastosowanie do Twoich potrzeb określonego przedsiębiorstwa.

## <a name="step-1-learn-about-pim"></a>Krok 1. Dowiedz się więcej o usłudze PIM

Usługa Azure AD Privileged Identity Management (PIM) pomaga w zarządzaniu uprzywilejowanych ról administracyjnych w usłudze Azure AD i innych Microsoft Online Services zasobów platformy Azure. W świecie, w których przypisany uprzywilejowanych tożsamości oraz zapomniane PIM udostępnia rozwiązań, takich jak dostęp just in time, przepływy pracy zatwierdzania żądania i w pełni zintegrowane dostępu, przeglądy, dzięki czemu można zidentyfikować odkryć i zapobiec złośliwej aktywności uprzywilejowanego role w czasie rzeczywistym. Wdrażanie usługi PIM do zarządzania usługi ról uprzywilejowanych w całej organizacji znacznie zmniejszy ryzyka podczas dzięki czemu są ujawniane cenne informacje o działaniach usługi ról uprzywilejowanych.

### <a name="business-value-of-pim"></a>Wartości biznesowej technologii usługi PIM

**Zarządzanie ryzykiem** -zabezpieczyć Twoją organizację, wymuszając zasady [najniższych uprawnień dostępu](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) i dostęp just in time. Minimalizując liczbę stałych przypisań użytkowników do ról uprzywilejowanych i wymuszenie zatwierdzenia i MFA o podniesienie uprawnień, można znacznie ograniczyć zagrożenia bezpieczeństwa związane z dostępem uprzywilejowanym w Twojej organizacji. Stosowanie najniższych uprawnień i just-in-time dostępu będzie również umożliwiać wyświetlanie historii dostępu do ról uprzywilejowanych i śledzenie problemów z zabezpieczeniami po ich wprowadzeniu.

**Adres zgodności i nadzór nad** — wdrażanie usługi PIM tworzy środowisko na potrzeby zarządzania tożsamościami w toku. Podniesienie poziomu just-in-time tożsamości uprzywilejowanych umożliwia PIM do śledzenia działań uprzywilejowanego dostępu w Twojej organizacji. Również będzie mogła wyświetlać i otrzymywać powiadomienia dla wszystkich przypisań ról trwałe i kwalifikujące się wewnątrz organizacji. Za pomocą przeglądu dostępu należy regularnie inspekcji i usunąć niepotrzebne uprzywilejowanych tożsamości i upewnij się, że Twoja organizacja jest zgodne z najbardziej rygorystyczne standardy tożsamości, dostęp i zabezpieczenia.

**Obniżenie kosztów** — obniżenie kosztów przez wyeliminowanie nieefektywności, ludzkiego błędu i problemy z zabezpieczeniami, wdrażając PIM poprawnie. Wynikiem jest zmniejszenie crimes cybernetycznymi skojarzone z uprzywilejowanych tożsamości, które są kosztowne i trudne do odzyskania z. PIM pomoże również zmniejszenie kosztów związanych z inspekcją uzyskiwanie dostępu do informacji, jeśli chodzi o zgodności z przepisami i standardami organizacji.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Aby korzystać z usługi PIM, katalogiem musi mieć jedną z następujących płatnych lub licencje próbne:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Aby uzyskać więcej informacji, zobacz [licencji wymagania dotyczące korzystania z usługi PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Terminologia klucz usługi PIM

| Termin lub pojęcie | Opis |
| --- | --- |
| kwalifikowanie się | Przypisanie roli, które wymaga od użytkownika wykonania jednej lub kilku akcji w celu użycia tej roli. Jeśli użytkownik został zakwalifikowany do roli, oznacza to, że może aktywować tę rolę, kiedy musi wykonać zadanie uprzywilejowane. Nie ma żadnej różnicy między dostępem udzielonym komuś za pomocą trwałego przypisania roli i przypisania kwalifikowania się do roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie potrzebują tego dostępu przez cały czas. |
| aktywuj | Proces wykonywania jednej lub kilku akcji w celu użycia roli, do której użytkownik został zakwalifikowany. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających. |
| dostęp just-in-time (JIT) | Model, w którym użytkownicy uzyskują tymczasowe uprawnienia do wykonywania uprzywilejowanych zadań, dzięki czemu złośliwi lub nieautoryzowani użytkownicy nie mogą uzyskać dostępu po wygaśnięciu uprawnienia. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują. |
| zasada dostępu z najniższymi uprawnieniami | Zalecane rozwiązanie dotyczące zabezpieczeń, w którym każdy użytkownik ma minimalne uprawnienia wymagane do wykonania zadania, które mu przydzielono. Takie rozwiązanie pozwala zminimalizować liczbę administratorów globalnych i zamiast tego korzystać ze specyficznych ról administratorów dla konkretnych scenariuszy. |

Aby uzyskać więcej informacji, zobacz [terminologii](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Ogólne omówienie sposobu działania usługi PIM

1. Usługa PIM jest skonfigurowany tak, aby użytkownicy byli rejestrowani ról uprzywilejowanych.
1. Gdy uprawniony użytkownik musi używać ich roli uprzywilejowanej, ich aktywować dla roli w usłudze PIM.
1. W zależności od ustawień usługi PIM skonfigurowany dla roli użytkownika, należy wykonać pewne czynności (np. wykonywania uwierzytelniania wieloskładnikowego, zgody lub określając przyczynę.)
1. Po użytkownik pomyślnie aktywuje swoją rolę, otrzymają one rolę wstępnie skonfigurowanego okresu.
1. Administratorzy mogą wyświetlać historię wszystkich działań PIM w dzienniku inspekcji. Mogą również zabezpieczanie dzierżawcom i spełniać wymogów zgodności przy użyciu funkcji usługi PIM, takich jak przeglądów i alerty.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Role, które mogą być zarządzane przez usługę PIM

**Role usługi Azure AD** — te role są w usłudze Azure Active Directory (na przykład Administrator globalny, Administrator programu Exchange i Administrator zabezpieczeń). Możesz dowiedzieć się więcej o rolach i ich funkcji w [uprawnienia roli administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać pomoc dotyczącą określania role można przypisać administratorów, zobacz [najmniej ról uprzywilejowanych przez zadanie](../users-groups-roles/roles-delegate-by-task.md).

**Role zasobów platformy Azure** — te role są połączone z zasobów platformy Azure, grupę zasobów, subskrypcji lub grupy zarządzania. PIM zapewnia dostęp just in time do obu wbudowanych ról, takich jak właściciel, Administrator dostępu użytkowników i współautorów, a także [ról niestandardowych](../../role-based-access-control/custom-roles.md). Aby uzyskać więcej informacji na temat ról zasobów platformy Azure, zobacz [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md).

Aby uzyskać więcej informacji, zobacz [role nie może zarządzać w usłudze PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Krok 2. Planowanie wdrożenia

Ta sekcja koncentruje się na co należy zrobić przed wdrożeniem usługi PIM w Twojej organizacji. Istotne jest postępuj zgodnie z instrukcjami i zrozumienie pojęcia w tej sekcji, zgodnie z ich pomoże utworzyć plan najlepsze przeznaczonym dla uprzywilejowanych tożsamości w organizacji.

### <a name="identify-your-stakeholders"></a>Określ swoje zainteresowane strony

Poniższa sekcja pomoże Ci Określ wszystkie zainteresowane strony, które są zaangażowane w projekcie i konieczne wylogowanie, przejrzyj i bądź na bieżąco. Obejmuje ona oddzielnych tabelach dla wdrażania PIM dla ról usługi Azure AD i usługa PIM dla ról zasobów platformy Azure. W poniższej tabeli jako odpowiednie dla Twojej organizacji, należy dodać zainteresowanych stron.

- DLATEGO = logowanie off w ramach tego projektu
- R = przegląd tego projektu, a następnie wprowadzić dane wejściowe
- Czy mogę = poinformowaniu tego projektu

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Uczestnicy projektu: Role AD PIM dla systemu Azure

| Name (Nazwa) | Rola | Akcja |
| --- | --- | --- |
| Nazwę i adres e-mail | **Architekt systemów tożsamości lub administratora globalnego usługi Azure**<br/>Przedstawiciel zespołu zarządzającego tożsamości odpowiedzialnym za określające, jak ta zmiana jest powiązana z infrastrukturę zarządzania tożsamościami podstawowe w Twojej organizacji. | SO/R/I |
| Nazwę i adres e-mail | **Właściciel usługi / wiersz Menedżera**<br/>Przedstawiciel od właścicieli IT usługi lub grupy usług. Są one klucza w podejmowaniu decyzji i ułatwia wdrażanie usługi PIM dla swojego zespołu. | SO/R/I |
| Nazwę i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu zabezpieczeń, który można wyrejestrować, planu spełnia wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwę i adres e-mail | **IT Menedżera wsparcia / pomocy technicznej**<br/>Przedstawiciel z pomocy technicznej IT w organizacji, która może udostępniać dane wejściowe na możliwość tej zmiany z punktu widzenia działu pomocy technicznej. | R/I |
| Nazwę i adres e-mail dla użytkowników pilotażowych | **Użytkownicy o roli uprzywilejowanej**<br/>Grupa użytkowników, dla których usługa privileged identity management jest zaimplementowana. Należy się dowiedzieć, jak do aktywacji ich ról, po zaimplementowaniu usługi PIM. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Uczestnicy projektu: Role zasobów usługi PIM dla systemu Azure

| Name (Nazwa) | Rola | Akcja |
| --- | --- | --- |
| Nazwę i adres e-mail | **Subskrypcja / właściciela zasobu**<br/>Przedstawiciel od właścicieli IT każdej subskrypcji lub zasobu, który chcesz wdrożyć usługi PIM dla | SO/R/I |
| Nazwę i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu zabezpieczeń, który można wyrejestrować, planu spełnia wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwę i adres e-mail | **IT Menedżera wsparcia / pomocy technicznej**<br/>Przedstawiciel z pomocy technicznej IT w organizacji, która może udostępniać dane wejściowe na możliwość tej zmiany z punktu widzenia działu pomocy technicznej. | R/I |
| Nazwę i adres e-mail dla użytkowników pilotażowych | **Użytkownicy o roli RBAC**<br/>Grupa użytkowników, dla których usługa privileged identity management jest zaimplementowana. Należy się dowiedzieć, jak do aktywacji ich ról, po zaimplementowaniu usługi PIM. | I |

### <a name="enable-pim"></a>Włącz usługę PIM

W ramach procesu planowania, należy najpierw wyrazić zgodę na i włącz usługę PIM, postępując zgodnie z naszym [rozpocząć korzystanie z usługi PIM dokumentu](pim-getting-started.md). Włączanie usługi PIM daje dostęp do niektórych funkcji, które zostały zaprojektowane specjalnie pomagające w danym wdrożeniu.

W przypadku celu wdrażania PIM dla zasobów platformy Azure, należy wykonać nasze [odnajdź zasoby platformy Azure do zarządzania w dokumencie usługi PIM](pim-resource-roles-discover-resources.md). Tylko właściciele każdego zasobu, grupy zasobów i subskrypcji będą mogli odnajdywać wewnątrz usługi PIM. Jeśli jesteś administratorem globalnym próby wdrażania PIM dla zasobów platformy Azure, możesz [podniesienie uprawnień dostępu do wszystkich subskrypcji platformy Azure do zarządzania](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) udzielić sobie dostępu do wszystkich zasobów platformy Azure w katalogu do odnajdywania. Jednak zaleca się pobrać zatwierdzenia z każdej usługi właściciele subskrypcji przed zarządzanie zasobami za pomocą usługi PIM.

### <a name="enforce-principle-of-least-privilege"></a>Wymusić zasadę najmniejszych uprawnień

Jest ważne upewnić się, możesz wymusić zasadę najmniejszych uprawnień w Twojej organizacji, obie usługi Azure AD i role zasobów platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

Role usługi Azure AD jest typowe dla organizacji przypisać rolę administratora globalnego do znacznej liczby administratorów, gdy większość administratorów należy tylko jeden lub dwa określonego administratora ról. Ról uprzywilejowanych, który przypisania następuje pozostać niezarządzany.

> [!NOTE]
> Typowych pułapek w delegacja roli:
>
> - Administrator zarządzający Exchange otrzymuje rolę administratora globalnego, mimo że potrzebują tylko roli Administrator programu Exchange, aby wykonywać swoje codzienne zadania.
> - Rolę administratora globalnego jest przydzielone administrator pakietu Office, ponieważ administrator musi role administratorów zabezpieczeń i programu SharePoint i jest łatwiejsze do delegowania tylko jedną rolę.
> - Administrator przypisano rolę administratora zabezpieczeń w celu wykonania zadania dawno temu, ale nigdy nie został usunięty.

Wykonaj następujące kroki, aby wymusić zasadę najmniejszych uprawnień dla poszczególnych ról usługi Azure AD.

1. Zrozumieć poziom szczegółowości role, odczytując i interpretując kolejne [dostępne role administratora usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Ty i Twój zespół powinien także odwoływać się do [ról administratora przez zadanie tożsamości w usłudze Azure AD](../users-groups-roles/roles-delegate-by-task.md), co wyjaśnia najniższych uprawnieniach roli do wykonywania określonych zadań.

1. Lista, która ma uprzywilejowane role w swojej organizacji. Możesz użyć [kreatora PIM](pim-security-wizard.md#run-the-wizard) można uzyskać dostęp do strony podobnie do poniższego.

    ![Wykryj role uprzywilejowane](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Dla wszystkich administratorów globalnych w Twojej organizacji Dowiedz się, dlaczego muszą roli. Oparte na odczytywania dokumentacja poprzednich, jeśli zadanie osoby mogą być wykonywane przez co najmniej jedną rolę administratora szczegółowe, należy usunąć je z rolą administratora globalnego i upewnić przydziałów odpowiednio w usłudze Azure Active Directory (jako odniesienia: Obecnie firma Microsoft ma tylko około 10 administratorów z roli administratora globalnego. Dowiedz się więcej o [jak firma Microsoft korzysta z usługi PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Dla innych ról usługi Azure AD Przejrzyj listę przypisań zidentyfikować administratorów, którzy nie są już potrzebne roli i usunąć je z ich przypisania.

Aby zautomatyzować kroki 3 i 4, możesz użyć funkcji przeglądu dostępu w usłudze PIM. Czynności opisane w [Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w usłudze PIM](pim-how-to-start-security-review.md), możesz skonfigurować przeglądu dostępu dla każdej roli usługi Azure AD, która ma co najmniej jednego członka.

![Tworzenie przeglądu dostępu](./media/pim-deployment-plan/create-access-review.png)

Należy ustawić recenzentów **członkowie (własny)**. Spowoduje to wysłanie wiadomość e-mail do wszystkich elementów członkowskich w roli można pobrać w celu sprawdzenia, czy potrzebują dostępu. Należy również włączyć **wymagana Przyczyna przy zatwierdzaniu** w zaawansowanych ustawieniach, dzięki czemu użytkownicy mogą podać Dlaczego muszą roli. Na podstawie tych informacji, można usunąć użytkowników z zbędne role i delegowanie bardziej szczegółowego ról administratora w przypadku administratorów globalnych.

Przeglądy dostępu zależy od wiadomości e-mail, aby powiadomić użytkowników, aby zapoznać się z ich dostęp do tych ról. Jeśli ma uprzywilejowane konta, które nie zostały połączone w wiadomości e-mail, pamiętaj wypełnić pola pomocniczego adresu e-mail dla tych kont. Aby uzyskać więcej informacji, zobacz [proxyAddresses atrybutu w usłudze Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

W przypadku subskrypcji platformy Azure i zasobów można skonfigurować podobny proces przeglądu dostępu, aby zapoznać się z rolami w każdej subskrypcji lub zasobu. Celem tego procesu jest zminimalizowanie przypisania właściciela i Administrator dostępu użytkowników, dołączone do każdej subskrypcji lub zasobu również, aby usunąć niepotrzebne przydziały. Jednak organizacje często delegować takie zadania do właściciela w każdej subskrypcji lub zasobu, ponieważ mają one lepsze zrozumienie określonych ról (szczególnie niestandardowe role).

Jeśli jesteś administratorem IT z rolą administratora globalnego próby wdrożenia usługi PIM dla zasobów platformy Azure w Twojej organizacji, możesz [podniesienie uprawnień dostępu do wszystkich subskrypcji platformy Azure do zarządzania](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) uzyskać dostęp do każdej subskrypcji. Następnie można znaleźć każdego właściciela subskrypcji i pracować z nimi, aby usunąć niepotrzebne przydziały i zminimalizować przypisanie roli właściciela.

Użytkownicy o roli właściciel subskrypcji platformy Azure może również korzystać z [przeglądów dla zasobów platformy Azure dostępu](pim-resource-roles-start-access-review.md) do inspekcji i usuwanie przypisań ról niepotrzebne podobny do procesu opisanego wcześniej dla ról usługi Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Zdecyduj, które przypisania ról powinny być chronione przez usługę PIM

Po wyczyszczeniu się przypisaniami ról uprzywilejowanych w Twojej organizacji, należy zdecydować, które role mają być chronione przez usługę PIM.

Jeśli roli jest chroniony przez usługę PIM, uprawnionych użytkowników przypisanych do niego należy podnieść poziom do korzystania z uprawnień przyznanych przez rolę. Proces podniesienia uprawnień mogą również obejmować uzyskiwania zatwierdzenia, uwierzytelniania wieloskładnikowego i/lub podania przyczyny, dlaczego aktywowania. PIM można także śledzić wybierającym za pośrednictwem powiadomienia i dzienniki zdarzeń inspekcji PIM i usługi Azure AD.

Wybieranie, które role mają być chronione przez usługę PIM może być trudne i będzie różna dla każdej organizacji. Ta sekcja zawiera opis naszych najlepszych rozwiązań dla usługi Azure AD i role zasobów platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

Należy określić priorytety ochrony ról usługi Azure AD, które mają najwięcej uprawnień. 10 najważniejszych ról usługi Azure AD, zarządzane przez usługę PIM na podstawie wzorców użycia wśród wszystkich klientów usługi PIM, są:

1. Administrator globalny
1. Administrator zabezpieczeń
1. Administrator użytkownika
1. Administrator programu Exchange
1. Administrator programu SharePoint
1. Administrator usługi Intune
1. Czytelnik zabezpieczeń
1. Administrator usługi
1. Administrator rozliczeń
1. Administrator programu Skype dla firm

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** Zarządzanie wszyscy administratorzy globalni i Administratorzy zabezpieczeń za pomocą usługi PIM jako pierwszego kroku są tymi, które mogą wykonywać najbardziej szkód w przypadku naruszenia zabezpieczeń.

Należy wziąć pod uwagę, jakie dane i uprawnienia są najbardziej poufnych dla Twojej organizacji. Na przykład niektóre organizacje mogą zdecydować chronić ich rolę administratora Power BI lub ich roli administratora zespoły mają możliwość dostęp do danych i/lub zmienić podstawowych przepływów pracy przy użyciu usługi PIM.

W przypadku wszelkich ról użytkowników-gości przypisaną są szczególnie podatne na ataki.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** Zarządzanie wszystkich ról przy użyciu użytkowników-gości za pomocą usługi PIM pozwala zredukować ryzyko skojarzone z kontami użytkowników z gościa ze złamanymi zabezpieczeniami.

Role odczytywanie, np. czytnik katalogu, czytnika Centrum wiadomości i Czytelnik zabezpieczeń są czasami uznane za mniej ważne, ponieważ nie mają uprawnień do zapisu w porównaniu do innych ról. Jednak Zaobserwowaliśmy niektórzy klienci także chronić tych ról, ponieważ osoby atakujące, które uzyskały dostęp do tych kont mogą mieć możliwość odczytu poufnych danych, takie jak dane osobowe (PII). Użytkownik powinien wziąć pod uwagę przy podejmowaniu decyzji, czy role odczytywanie w Twojej organizacji muszą być zarządzane za pomocą usługi PIM.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

Podejmując decyzję o przypisania ról, które mają być zarządzane za pomocą usługi PIM dla zasobów platformy Azure, należy najpierw zidentyfikować subskrypcje/zasoby, które są najbardziej istotne dla Twojej organizacji. Przykłady takich zasobów/subskrypcji:

- Zasoby, które hostują najbardziej poufnych danych
- Zasoby, których podstawowych aplikacji przeznaczonych dla klientów są zależne od

Jeśli jesteś administratorem globalnym, masz problemy z podejmowania decyzji, które subskrypcje/zasoby są dla Ciebie najważniejsze, możesz powinni skontaktować się ze do właścicieli subskrypcji w Twojej organizacji, aby zebrać listę zasobów zarządzanych w ramach każdej subskrypcji. Następnie powinien współpracować z właścicielami subskrypcji do grupowania zasobów na podstawie poziomu ważności w przypadku, gdy naruszenia ich zabezpieczeń (niskiej, średniej, wysokiej). Należy nadać priorytet zarządzające zasobami za pomocą usługi PIM w oparciu o ten poziom ważności.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** pracujesz z właścicielami subskrypcji lub zasobu krytycznych usług, aby skonfigurować przepływ pracy usługi PIM, dla wszystkich ról wewnątrz poufnych subskrypcji/zasobów.

Usługa PIM dla zasobów platformy Azure obsługuje konta usług związanych z czasem. Należy traktować kont usług dokładnie tak samo jak będą traktować to zwykłe konto użytkownika.

Dla subskrypcji/zasobów, które nie są tak ważne nie będzie już konieczne skonfigurować usługi PIM dla wszystkich ról. Należy jednak nadal chronić właściciela i Administrator dostępu użytkowników ról za pomocą usługi PIM.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** zarządzania ról właściciela i Administrator dostępu użytkowników wszystkich subskrypcji/zasobów za pomocą usługi PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Zdecyduj, przypisania ról, które powinny być stałe lub kwalifikujących się

Po użytkownik zdecydował się na liście ról, które mają być zarządzane przez usługę PIM, możesz zdecydować, użytkowników, którzy pobieraj kwalifikowania się do roli i roli trwale aktywne. Trwale aktywne role są normalne role przypisane przy użyciu usługi Azure Active Directory i zasobów platformy Azure, gdy kwalifikujące się role można przypisywać tylko w usłudze PIM.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** ma zero trwale aktywne przypisania ról usługi Azure AD i role zasobów platformy Azure innych niż zalecane [dwóch kont dostępu awaryjnego break szkła](../users-groups-roles/directory-emergency-access.md), które powinny mieć stałe rolę administratora globalnego.

Mimo że firma Microsoft zaleca zero administratora stałego, czasami trudno jest dla organizacji, można to osiągnąć natychmiast. Poniżej przedstawiono kwestie, które należy uwzględnić podczas podejmowania decyzji tego:

- Częstotliwość podniesienia uprawnień — Jeśli użytkownik potrzebuje tylko raz, uprzywilejowanych przypisania ich nie powinny mieć trwałe przypisanie. Z drugiej strony Jeśli użytkownik musi mieć rolę dla ich codziennych zadań za pomocą usługi PIM może znacznie ograniczyć ich wydajności, mogą zostać uwzględnione dla trwałego roli.
- Przypadków specyficzne dla danej organizacji — Jeśli osoba podane są kwalifikowania się do roli z dużej odległości zespołu lub rangą wykonawczego punkt komunikacji oraz wymuszanie proces podniesienia uprawnień jest trudne, mogą zostać uwzględnione dla trwałego roli.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** można skonfigurować cykliczny dostępu przeglądy dla użytkowników za pomocą przypisań ról stałych (powinna być dowolne). Dowiedz się więcej o cykliczne przeglądu dostępu w końcowej części tego planu wdrażania

### <a name="draft-your-pim-settings"></a>Projekt ustawień usługi PIM

Przed zaimplementowaniem rozwiązania PIM jest dobrym rozwiązaniem do ustawień usługi PIM dla każdej roli uprzywilejowanej, której używa Twoja organizacja stanu wersji roboczej. Ta sekcja zawiera przykłady ustawień usługi PIM dla ról określonego (są tylko dla odwołania i mogą być różne dla swojej organizacji). Każde z tych ustawień jest szczegółowo wyjaśniony wraz z zaleceniami firmy Microsoft po instalacji tabel.

#### <a name="pim-settings-for-azure-ad-roles"></a>Ustawień usługi PIM dla ról usługi Azure AD

| Rola | Wymaganie usługi MFA | Powiadomienie | Bilet zdarzenia | Wymagaj zatwierdzenia | Osoba zatwierdzająca | Czas trwania aktywacji | Administratora trwałego |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrator globalny | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Innych administratorów globalnych | 1 godzina | Kont dostępu awaryjnego |
| Administrator programu Exchange | :heavy_check_mark: | :heavy_check_mark: | : x:. | : x:. | Brak | 2 Hour | Brak |
| Administrator pomocy technicznej | : x:. | : x:. | :heavy_check_mark: | : x:. | Brak | 8 Hour | Brak |

#### <a name="pim-settings-for-azure-resource-roles"></a>Ustawień usługi PIM dla ról zasobów platformy Azure

| Rola | Wymaganie usługi MFA | Powiadomienie | Wymagaj zatwierdzenia | Osoba zatwierdzająca | Czas trwania aktywacji | Administrator usługi Active | Aktywne wygaśnięcia | Kwalifikujące się wygaśnięcia |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Właściciel subskrypcji krytyczne | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Innych właścicieli subskrypcji | 1 godzina | Brak | Nie dotyczy | 3-miesięczna |
| Administrator dostępu użytkowników z mniej ważnych subskrypcji | :heavy_check_mark: | :heavy_check_mark: | : x:. | Brak | 1 godzina | Brak | Nie dotyczy | 3-miesięczna |
| Współautor maszyny wirtualnej | : x:. | :heavy_check_mark: | : x:. | Brak | 3 godziny | Brak | Nie dotyczy | 6-miesięczna |

Poniższa tabela zawiera opis ustawienia.

| Ustawienie | Opis |
| --- | --- |
| Rola | Nazwa roli którą definiujesz ustawienia. |
| Wymaganie usługi MFA | Czy uprawniony użytkownik musi wykonać uwierzytelnianie wieloskładnikowe przed aktywowaniem roli.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** wymuszanie uwierzytelniania Wieloskładnikowego dla wszystkich ról administratora, zwłaszcza, jeśli są użytkowników-gości. |
| Powiadomienie | Jeśli ustawiona na wartość true, Administrator globalny, Administrator ról uprzywilejowanych, a Administrator zabezpieczeń w organizacji otrzyma wiadomość e-mail z powiadomieniem po uprawniony użytkownik aktywuje roli.<br/><br/>**Uwaga:** W niektórych organizacjach nie mają adres e-mail powiązany z ich konta administratora, aby otrzymywać te powiadomienia e-mail, należy go ustawić alternatywny adres e-mail, dzięki czemu administratorzy będą otrzymywać te wiadomości e-mail. |
| Bilet zdarzenia | Czy uprawniony użytkownik musi zarejestrować numeru biletu zdarzenia podczas aktywacji ich ról. To ustawienie pozwala organizacji identyfikowanie poszczególnych aktywacji z użyciem wewnętrzny numer zdarzenia eliminowanie niechciane aktywacji.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** wykorzystując liczb biletów zdarzeń, aby powiązać PIM przy użyciu Twój system wewnętrzny. Jest to szczególnie przydatne dla osób zatwierdzających potrzebujących kontekstu aktywacji. |
| Wymagaj zatwierdzenia | Czy uprawniony użytkownik musi uzyskać zatwierdzenia do aktywowania roli.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** można skonfigurować zatwierdzenia dla ról z najbardziej uprawnień. Na podstawie wzorców użycia wszystkich klientów usługi PIM, administratora globalnego, administratora użytkowników, Administrator programu Exchange, administratora zabezpieczeń i Administrator haseł są najbardziej typowe role o zatwierdzenie instalacji. |
| Osoba zatwierdzająca | Jeśli zatwierdzenie jest wymagane do aktywowania kwalifikujących się ról, wyświetlić listę osób, które należy zatwierdzać żądania. Domyślnie usługi PIM ustawia osoby zatwierdzającej do wszystkich użytkowników, którzy są administratorami ról uprzywilejowanych, czy są one stałe lub kwalifikujące się.<br/><br/>**Uwaga:** Jeśli użytkownik należy zarówno kwalifikuje się do roli usługi Azure AD i osoba zatwierdzająca roli, nie będą mogły zatwierdzać samodzielnie.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** wybranie osób zatwierdzających do osób, które są najbardziej odpowiednią wiedzę na temat konkretnej roli i jej użytkownikach, częste, a nie administratora globalnego. |
| Czas trwania aktywacji | Długość czasu, przez który użytkownik zostanie aktywowany w roli zostanie wygaśnięcia. |
| Administratora trwałego | Listę użytkowników, którzy będą administrator trwały dla roli (nigdy nie musi aktywować).<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** ma zero administratora stałego dla wszystkich ról, z wyjątkiem administratorów globalnych. Przeczytaj więcej o nim w kto powinny kwalifikujących się oraz którzy użytkownicy powinni być trwale aktywne część tego planu. |
| Administrator usługi Active | Dla zasobów platformy Azure administratora active znajduje się lista użytkowników, którzy nigdy nie będą musiały Aktywuj, aby korzystać z roli. To nie nazywa się administrator trwały, takich jak role usługi Azure AD, ponieważ można ustawić czas wygaśnięcia po użytkownik utraci tej roli. |
| Aktywne wygaśnięcia | Aktywne przypisania ról dla ról zasobów platformy Azure wygasają po tym okresie. Możesz wybrać spośród 15 dni, 1 miesiąc, 3-miesięczna 6 miesięcy, 1 roku lub trwale aktywne. |
| Kwalifikujące się wygaśnięcia | Kwalifikujące się przypisanie roli dla ról zasobów platformy Azure wygasają po tym okresie. Możesz wybrać spośród 15 dni, 1 miesiąc, 3-miesięczna 6 miesięcy, 1 roku lub trwale zakwalifikowane. |

## <a name="step-3-implement-your-solution"></a>Krok 3. Implementowania rozwiązania

Podstawą odpowiednie planowanie to podstawa, na którym można wdrożyć aplikację pomyślnym z usługą Azure Active Directory.  Zapewnia ona usługę intelligent security i integracji, która upraszcza dołączania, jednocześnie skracając czas dla pomyślnego wdrożenia.  Ta kombinacja gwarantuje, że aplikacja jest zintegrowana z usługą łatwość, jednocześnie zmniejszając czas przestoju dla użytkowników końcowych.

### <a name="identify-test-users"></a>Identyfikowanie użytkowników testowych

Ta sekcja umożliwia identyfikację zbioru użytkowników i grup użytkowników do sprawdzania poprawności wdrożenia. Na podstawie ustawień, które zostały wybrane w sekcji dotyczącej planowania zidentyfikować użytkowników, które chcesz przetestować dla każdej roli.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** wprowadzeniu właściciele usługi poszczególnych ról usługi Azure AD jako użytkowników testowych, dzięki czemu mogą zapoznać się z procesem i stają się advocator wewnętrznego dla roll out.

W tej tabeli należy zidentyfikować użytkowników testowych, które sprawdzi, czy działa ustawienia dla każdej roli.

| Nazwa roli | Testowanie użytkowników |
| --- | --- |
| &lt;Nazwa roli&gt; | &lt;Użytkowników na potrzeby testowania roli&gt; |
| &lt;Nazwa roli&gt; | &lt;Użytkowników na potrzeby testowania roli&gt; |

### <a name="test-implementation"></a>Wykonanie testu

Teraz, gdy zidentyfikowano użytkowników testowych, umożliwia skonfigurowanie usługi PIM dla użytkowników testów w tym kroku. Jeśli Twoja organizacja chce uwzględnienie przepływu pracy usługi PIM w wewnętrznych aplikacji zamiast przy użyciu interfejsu użytkownika w usłudze PIM w witrynie Azure portal, wszystkie operacje w usłudze PIM są również obsługiwane za pośrednictwem naszego [interfejs API programu graph](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Skonfiguruj usługę PIM dla ról usługi Azure AD

1. [Konfigurowanie ustawień roli usługi Azure AD](pim-how-to-change-default-settings.md) oparte na co to są planowane.

1. Przejdź do **ról usługi Azure AD**, kliknij przycisk **role**, a następnie wybierz rolę, został skonfigurowany.

1. Dla grupy użytkowników testowych jeśli są one już administrator trwały, możesz zwiększyć ich uprawnionych, wyszukując je i konwersji ich z trwałe kwalifikujące się, klikając trzy kropki znajdujące się w jego wierszu. Jeśli nie ma przypisań ról, ale możesz [wprowadzić nowe kwalifikującego się przypisania](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Powtórz kroki 1 – 3 dla wszystkich ról, które mają zostać przetestowane.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link do sposobu [aktywować swoją rolę usługi Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Skonfiguruj usługę PIM dla ról zasobów platformy Azure

1. [Konfigurowanie ustawień roli usługi Azure resource](pim-resource-roles-configure-role-settings.md) dla roli w subskrypcji lub zasobu, który ma zostać przetestowana.

1. Przejdź do **zasobów platformy Azure** tej subskrypcji, a następnie kliknij przycisk **role**, wybierz rolę, został skonfigurowany.

1. Dla grupy użytkowników testowych, jeśli są już aktywne administratora, możesz zwiększyć ich uprawnionych, wyszukując je i [aktualizacji przypisania roli](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Jeśli nie ma roli, ale możesz [przypisanie nowej roli](pim-resource-roles-assign-roles.md#assign-a-role).

1. Powtórz kroki 1 – 3 dla wszystkich ról, które mają zostać przetestowane.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link do sposobu [aktywować swoją rolę usługi Azure resource](pim-resource-roles-activate-your-roles.md).

Aby sprawdzić, czy wszystkie konfiguracje skonfigurowane pod kątem role działają poprawnie, należy użyć tego etapu. Skorzystaj z poniższej tabeli, aby udokumentować testów. Należy również użyć tego etapu do optymalizacji komunikacji z narażeni użytkownicy.

| Rola | Oczekiwane zachowanie podczas aktywacji | Rzeczywiste wyniki |
| --- | --- | --- |
| Administrator globalny | (1) wymagane uwierzytelnianie MFA<br/>(2) wymagają zatwierdzenia<br/>(3), osoba zatwierdzająca otrzyma powiadomienie i zatwierdzić<br/>(4) roli wygasa po upływie wstępnie ustawiony czas |  |
| Właściciel subskrypcji *X* | (1) wymagane uwierzytelnianie MFA<br/>(2) kwalifikującego się przypisania wygasa po upływie skonfigurowanego okresu. |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Nawiązanie PIM dotyczy uczestników projektu

Wdrażanie usługi PIM wprowadzi dodatkowe kroki dla użytkowników ról uprzywilejowanych. Mimo że PIM, znacznie ogranicza problemy dotyczące zabezpieczeń skojarzonych z uprzywilejowanych tożsamości, zmiana musi skutecznie przekazywanych przed wdrożeniem w całej dzierżawie. W zależności od liczby administratorów objęte wpływem organizacje często zdecydować się na tworzenie wewnętrzny dokument, wideo lub wiadomość e-mail o zmianie. Często objęte otrzymywania tych wiadomości obejmują:

- Co to jest PIM
- Co to jest korzyść dla organizacji
- Kto będzie miała wpływ
- Gdy zostanie PIM udostępniona
- Jakie dodatkowe kroki będą wymagane dla użytkowników aktywować swoją rolę
    - Należy wysyłanie linków do dokumentacji:
    - [Aktywuj role usługi Azure AD](pim-how-to-activate-role.md)
    - [Aktywuj role zasobów platformy Azure](pim-resource-roles-activate-your-roles.md)
- Informacje kontaktowe lub link do pomocy technicznej dla problemy za pomocą usługi PIM

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** można skonfigurować razem z zespołem pomocy technicznej w celu zaprezentowania przepływu pracy usługi PIM (Jeśli Twoja organizacja ma wewnętrznej pomocy technicznej IT zespołu). Udostępniaj im odpowiednie dokumentacje, a także Twoich informacji kontaktowych.

### <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Gdy testy są kompletne i się powiedzie, przenoszenie PIM do środowiska produkcyjnego, powtarzając kroki opisane w fazach testowania dla wszystkich użytkowników w każdej roli, które zostały zdefiniowane w konfiguracji usługi PIM. Usługa PIM dla ról usługi Azure AD organizacje często testu i wprowadzane przed testowanie i wdrażanie usługi PIM dla innych ról usługi PIM dla administratorów globalnych. W międzyczasie dla zasobów platformy Azure, organizacji zwykle testu i wdrażanie usługi PIM jedną subskrypcję platformy Azure w danym momencie.

### <a name="in-the-case-a-rollback-is-needed"></a>W przypadku wycofywania jest wymagana

Gdy usługi PIM nie może działać zgodnie z oczekiwaniami w środowisku produkcyjnym, poniższe kroki wycofywania mogą pomóc powrócić do znanego, dobrego stanu przed skonfigurowaniem usługi PIM:

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **usługi Azure AD Privileged Identity Management**.
1. Kliknij przycisk **ról usługi Azure AD** a następnie kliknij przycisk **role**.
1. Dla każdej roli, który został skonfigurowany, kliknij przycisk wielokropka (**...** ) dla wszystkich użytkowników z kwalifikującego się przypisania.
1. Kliknij przycisk **były trwałe** opcję, aby były trwałe przypisania roli.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **usługi Azure AD Privileged Identity Management**.
1. Kliknij przycisk **zasobów platformy Azure** a następnie kliknij subskrypcję lub zasób ma zostać przywrócony.
1. Kliknij przycisk **role**.
1. Dla każdej roli, który został skonfigurowany, kliknij przycisk wielokropka (**...** ) dla wszystkich użytkowników z kwalifikującego się przypisania.
1. Kliknij przycisk **były trwałe** opcję, aby były trwałe przypisania roli.

## <a name="step-4-next-steps-after-deploying-pim"></a>Krok 4. Następne kroki po wdrożeniu usługi PIM

Pomyślne wdrożenie usługi PIM w środowisku produkcyjnym jest istotny krok do przodu w zakresie zabezpieczania organizacji użytkownika uprzywilejowanych tożsamości. Przy użyciu wdrażania PIM zawiera dodatkowe funkcje usługi PIM, które należy używać zabezpieczeń i zgodności.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Używanie usługi PIM alertów ochrona uprzywilejowanego dostępu

W usłudze PIM alerty wbudowane lepsze zabezpieczenie należy wykorzystać w swojej dzierżawy. Aby uzyskać więcej informacji, zobacz [alerty zabezpieczeń](pim-how-to-configure-security-alerts.md#security-alerts). Te alerty zawierają: Administratorzy nie są przy użyciu ról uprzywilejowanych, role są przypisywane poza usługą PIM, role są aktywowane zbyt często i nie tylko. Do w pełni chronić swoją organizację, należy regularnie przechodzą przez listy alertów i rozwiąż problemy. Można wyświetlać i naprawić alertów w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **usługi Azure AD Privileged Identity Management**.
1. Kliknij przycisk **ról usługi Azure AD** a następnie kliknij przycisk **alerty**.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** przeciwdziałania wszystkie alerty oznaczone o wysokiej ważności natychmiast. Dla alerty o średnim i niskim znaczeniu należy na bieżąco i wprowadzić zmiany, jeśli uważasz, że istnieje zagrożenie bezpieczeństwa.

Jeśli jakiekolwiek konkretne alerty nie są przydatne, lub nie ma zastosowania do Twojej organizacji, należy zawsze odrzucić alert na stronie alertów. Zawsze możesz przywrócić tej zwolnienia później na stronie ustawień usługi Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Konfigurowanie cyklicznego przeglądów dostępu w celu regularnie przeprowadzaj inspekcje uprzywilejowanych tożsamości organizacji

Przeglądy dostępu są najlepszym sposobem uzyskania użytkownicy sami nie aktualizowali przypisane do ról uprzywilejowanych lub konkretne osoby dokonujące przeglądu czy każdy użytkownik musi uprzywilejowanych tożsamości. Przeglądy dostępu są znakomite, jeśli chcesz zmniejszyć obszar narażony na ataki i nadal zgodne. Aby uzyskać więcej informacji na temat uruchamiania przeglądu dostępu, zobacz [przeglądów dostępu dla ról usługi Azure AD](pim-how-to-start-security-review.md) i [przeglądów dostępu dla ról zasobów platformy Azure](pim-resource-roles-start-access-review.md). W niektórych organizacjach wykonywanie przeglądu dostępu okresowe jest wymagany do pozostają zgodne z prawem podczas dla innych osób, przeglądu dostępu to najlepszy sposób, aby wymusić podmiot zabezpieczeń najniższych uprawnień w całej organizacji.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** Konfigurowanie kwartalnie przeglądów dostępu dla usługi Azure AD i role zasobów platformy Azure.

W większości przypadków recenzenta do ról usługi Azure AD jest samych użytkowników, a osoba dokonująca przeglądu dla ról zasobów platformy Azure jest właścicielem subskrypcji, która zawiera rolę. Jednak często jest przypadek, w którym firm ma uprzywilejowany kont, które nie są połączone za pomocą adresu e-mail przez poszczególne osoby. W takich przypadkach nikt odczytuje i przeglądów dostępu.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** Dodaj adres pomocniczego adresu e-mail dla wszystkich kont z przypisaniami ról uprzywilejowanych, które nie są połączone z adresów e-mail regularnie sprawdzane

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Najlepiej wykorzystać dziennika inspekcji w celu zwiększenia bezpieczeństwa i zgodności

Dziennik inspekcji jest miejscem, w którym najnowsze informacje i zachować zgodność z przepisami. PIM przechowuje obecnie 30-dniową historię historii wszystkich w organizacji, w tym jej dzienników inspekcji:

- Aktywacja/dezaktywacja kwalifikujące się role
- Działania przypisania roli wewnątrz lub na zewnątrz usługi PIM
- Zmiany w ustawieniach roli
- Żądania zatwierdzenia/skierowany działania aktywacji roli za pomocą ustawienia akceptacji
- Aktualizowanie alertów

Dostęp do tych dzienników inspekcji, jeśli jesteś administratorem globalnym lub administratorem ról uprzywilejowanych. Aby uzyskać więcej informacji, zobacz [inspekcji historii uaktywnień ról usługi Azure AD](pim-how-to-use-audit-log.md) i [inspekcji historii dla ról zasobów platformy Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** posiadania co najmniej jednego administratora, zapoznaj się z artykułem wszystkie zdarzenia w cyklu tygodniowym inspekcji i eksportowanie zdarzeń inspekcji co miesiąc.

Jeśli chcesz automatycznie przechowywania zdarzeń inspekcji przez dłuższy czas dziennika inspekcji w usłudze PIM jest automatycznie synchronizowane w [dzienniki inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** Ci o skonfigurowaniu [monitorowanie dzienników platformy Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) do archiwizacji zdarzeń inspekcji na koncie magazynu platformy Azure na potrzeby zabezpieczeń i zgodności.
