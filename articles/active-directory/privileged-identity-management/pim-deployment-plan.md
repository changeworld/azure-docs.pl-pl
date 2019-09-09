---
title: Wdróż Privileged Identity Management (PIM) — Azure Active Directory | Microsoft Docs
description: Opisuje sposób planowania wdrożenia programu Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ff46a693766ab12b53baba1ad8e4a56e174a076
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804492"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Wdróż Azure AD Privileged Identity Management (PIM)

W tym przewodniku krok po kroku opisano sposób planowania wdrożenia usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w organizacji.

> [!TIP]
> W tym dokumencie zobaczysz elementy oznaczone jako:
> 
> :heavy_check_mark: **Zalecane przez firmę Microsoft**
> 
> Są to ogólne zalecenia, które należy zaimplementować tylko w przypadku, gdy mają zastosowanie do konkretnych potrzeb przedsiębiorstwa.

## <a name="step-1-learn-about-pim"></a>Krok 1. Informacje o usłudze PIM

Azure AD Privileged Identity Management (PIM) ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usłudze Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. W świecie, w którym są przypisane i zapomniane tożsamości uprzywilejowane, PIM oferuje rozwiązania takie jak dostęp just in Time, żądania przepływów pracy zatwierdzania i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, odkrywać i zapobiegać złośliwym działaniom uprzywilejowanym role w czasie rzeczywistym. Wdrożenie usługi PIM w celu zarządzania rolami uprzywilejowanymi w całej organizacji znacznie zmniejsza ryzyko, a jednocześnie pozwala uzyskać cenne informacje o działaniach ról uprzywilejowanych.

### <a name="business-value-of-pim"></a>Wartość biznesowa PIM

**Zarządzanie ryzykiem** — Zabezpiecz swoją organizację, wymuszając zasadę [najniższych uprawnień dostępu](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) i dostęp just in Time. Minimalizując liczbę stałych przypisań użytkowników do ról uprzywilejowanych i wymuszanie zatwierdzeń i uwierzytelniania wieloskładnikowego dla podniesienia uprawnień, można znacznie zmniejszyć zagrożenia bezpieczeństwa związane z dostępem uprzywilejowanym w organizacji. Wymuszanie najniższych uprawnień i dostępu just in Time umożliwi również Wyświetlanie historii dostępu do ról uprzywilejowanych i Śledzenie problemów z zabezpieczeniami w miarę ich występowania.

**Zgodność z adresami i zarządzaniem** — wdrażanie usługi PIM powoduje utworzenie środowiska na potrzeby zarządzania tożsamościami. Podwyższenie poziomu uprawnień tożsamości uprzywilejowanych umożliwia usłudze PIM śledzenie działań uprzywilejowanego dostępu w organizacji. Będziesz również mieć możliwość wyświetlania i otrzymywania powiadomień o wszystkich przypisaniach trwałych i kwalifikujących się ról w organizacji. Za pomocą przeglądu dostępu można regularnie kontrolować i usuwać niepotrzebne tożsamości uprzywilejowane oraz upewnić się, że organizacja jest zgodna z najbardziej rygorystycznymi standardami dotyczącymi tożsamości, dostępu i zabezpieczeń.

**Zmniejsz koszty** — Zmniejsz koszty, eliminując nieefektywność, błąd ludzki i problemy z zabezpieczeniami, wdrażając program PIM poprawnie. Wynikiem jest spadek cybernetycznymi zbrodni związanych z tożsamościami uprzywilejowanymi, które są kosztowne i trudne do odzyskania. Ponadto program PIM pomoże firmie ograniczyć koszty związane z inspekcją dostępu do informacji, gdy chodzi o przestrzeganie przepisów i standardów.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Aby można było korzystać z usługi PIM, katalog musi mieć jedną z następujących licencji płatnych lub próbnych:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Aby uzyskać więcej informacji, zobacz [wymagania licencyjne do korzystania z usługi PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Najważniejsze terminologia PIM

| Termin lub pojęcie | Opis |
| --- | --- |
| kwalifikowanie się | Przypisanie roli, które wymaga od użytkownika wykonania jednej lub kilku akcji w celu użycia tej roli. Jeśli użytkownik został zakwalifikowany do roli, oznacza to, że może aktywować tę rolę, kiedy musi wykonać zadanie uprzywilejowane. Nie ma żadnej różnicy między dostępem udzielonym komuś za pomocą trwałego przypisania roli i przypisania kwalifikowania się do roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie potrzebują tego dostępu przez cały czas. |
| Aktywuj | Proces wykonywania jednej lub kilku akcji w celu użycia roli, do której użytkownik został zakwalifikowany. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających. |
| dostęp just-in-time (JIT) | Model, w którym użytkownicy uzyskują tymczasowe uprawnienia do wykonywania uprzywilejowanych zadań, dzięki czemu złośliwi lub nieautoryzowani użytkownicy nie mogą uzyskać dostępu po wygaśnięciu uprawnienia. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują. |
| zasada dostępu z najniższymi uprawnieniami | Zalecane rozwiązanie dotyczące zabezpieczeń, w którym każdy użytkownik ma minimalne uprawnienia wymagane do wykonania zadania, które mu przydzielono. Takie rozwiązanie pozwala zminimalizować liczbę administratorów globalnych i zamiast tego korzystać ze specyficznych ról administratorów dla konkretnych scenariuszy. |

Aby uzyskać więcej informacji, zobacz [terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Ogólne omówienie sposobu działania usługi PIM

1. Program PIM jest skonfigurowany tak, aby użytkownicy mieli uprawnienia do ról uprzywilejowanych.
1. Gdy uprawniony użytkownik musi korzystać z roli uprzywilejowanej, uaktywnia rolę w programie PIM.
1. W zależności od ustawień usługi PIM skonfigurowanych dla roli użytkownik musi wykonać pewne czynności (takie jak uwierzytelnianie wieloskładnikowe, uzyskanie zatwierdzenia lub określenie przyczyny).
1. Po pomyślnym aktywowaniu roli przez użytkownika otrzymasz rolę dla wstępnie skonfigurowanego okresu.
1. Administratorzy mogą wyświetlać historię wszystkich działań PIM w dzienniku inspekcji. Mogą również dodatkowo zabezpieczyć swoich dzierżawców i spełnić wymagania przy użyciu funkcji PIM, takich jak przeglądy i alerty dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Role, które mogą być zarządzane przez usługę PIM

**Role usługi Azure AD** — te role znajdują się w Azure Active Directory (na przykład administrator globalny, administrator programu Exchange i administrator zabezpieczeń). Więcej informacji na temat ról i ich funkcji można znaleźć w temacie [uprawnienia roli administrator w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać pomoc dotyczącą określania ról do przypisania administratorów, zobacz [najmniej uprzywilejowanych ról według zadania](../users-groups-roles/roles-delegate-by-task.md).

**Role zasobów platformy Azure** — te role są połączone z zasobem platformy Azure, grupą zasobów, subskrypcją lub grupą zarządzania. PIM zapewnia dostęp just in Time do obu wbudowanych ról, takich jak właściciel, administrator dostępu użytkowników i współautor, a także [role niestandardowe](../../role-based-access-control/custom-roles.md). Aby uzyskać więcej informacji na temat ról zasobów platformy Azure, zobacz [Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md).

Aby uzyskać więcej informacji, zobacz [role, którymi nie można zarządzać w usłudze PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Krok 2. Planowanie wdrożenia

Ta sekcja koncentruje się na tym, co należy zrobić przed wdrożeniem programu PIM w organizacji. Ważne jest, aby postępować zgodnie z instrukcjami i zrozumieć koncepcje przedstawione w tej sekcji, ponieważ przeprowadzą Cię przez proces tworzenia najlepszego planu dopasowanego do tożsamości uprzywilejowanych w organizacji.

### <a name="identify-your-stakeholders"></a>Zidentyfikuj swoje osoby zainteresowane

W poniższej sekcji znajdują się informacje ułatwiające zidentyfikowanie wszystkich zainteresowanych uczestników projektu i konieczność wylogowania się, przejrzenia lub poinformowanie użytkownika. Zawiera osobne tabele umożliwiające wdrażanie usług PIM dla ról usługi Azure AD i PIM dla ról zasobów platformy Azure. Dodaj uczestników projektu do poniższej tabeli, zgodnie z potrzebami organizacji.

- SO = Wyloguj się w tym projekcie
- R = przejrzyj ten projekt i podaj dane wejściowe
- I = poinformowany o tym projekcie

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Zainteresowanych Usługa PIM dla ról usługi Azure AD

| Name | Role | Action |
| --- | --- | --- |
| Nazwa i adres e-mail | **Architekt tożsamości lub Administrator globalny platformy Azure**<br/>Przedstawiciel zespołu ds. zarządzania tożsamościami odpowiedzialny za definiowanie sposobu wyrównywania tej zmiany z podstawową infrastrukturą zarządzania tożsamościami w organizacji. | SO/R/I |
| Nazwa i adres e-mail | **Menedżer linii/właściciela usługi**<br/>Przedstawiciel od właścicieli IT usługi lub grupy usług. Są one kluczowym sposobem podejmowania decyzji i ułatwienia wdrożenia PIM dla zespołu. | SO/R/I |
| Nazwa i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwa i adres e-mail | **Menedżer pomocy technicznej IT/pomoc techniczna**<br/>Przedstawiciel firmy z działu pomocy technicznej IT, który może dostarczyć dane wejściowe dotyczące możliwości obsługi tej zmiany w perspektywie pomocy technicznej. | R/I |
| Nazwa i wiadomość e-mail dla użytkowników pilotażowych | **Użytkownicy roli uprzywilejowanej**<br/>Grupa użytkowników, dla których zaimplementowano usługi Privileged Identity Management. Będą musieli wiedzieć, jak aktywować swoje role po zaimplementowaniu programu PIM. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Zainteresowanych Usługa PIM dla ról zasobów platformy Azure

| Name | Role | Action |
| --- | --- | --- |
| Nazwa i adres e-mail | **Właściciel subskrypcji/zasobu**<br/>Przedstawiciel z właścicieli IT dla każdej subskrypcji lub zasobu, dla którego chcesz wdrożyć usługę PIM | SO/R/I |
| Nazwa i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwa i adres e-mail | **Menedżer pomocy technicznej IT/pomoc techniczna**<br/>Przedstawiciel firmy z działu pomocy technicznej IT, który może dostarczyć dane wejściowe dotyczące możliwości obsługi tej zmiany w perspektywie pomocy technicznej. | R/I |
| Nazwa i wiadomość e-mail dla użytkowników pilotażowych | **Użytkownicy roli RBAC**<br/>Grupa użytkowników, dla których zaimplementowano usługi Privileged Identity Management. Będą musieli wiedzieć, jak aktywować swoje role po zaimplementowaniu programu PIM. | I |

### <a name="enable-pim"></a>Włącz usługę PIM

W ramach procesu planowania najpierw należy wyrazić zgodę na korzystanie z usługi PIM i włączyć ją, wykonując czynności opisane w [dokumencie PIM](pim-getting-started.md). Włączenie programu PIM zapewnia dostęp do niektórych funkcji, które są specjalnie zaprojektowane w celu ułatwienia wdrożenia.

Jeśli celem jest wdrożenie usług PIM dla zasobów platformy Azure, należy wykonać nasze [odnajdywanie zasobów platformy Azure w celu zarządzania nimi w dokumencie PIM](pim-resource-roles-discover-resources.md). Tylko właściciele każdego zasobu, grupy zasobów i subskrypcji będą mogli odnajdywać je w programie PIM. Jeśli jesteś administratorem globalnym próbującym wdrożyć usługę PIM dla zasobów platformy Azure, możesz podwyższyć [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , aby zapewnić sobie dostęp do wszystkich zasobów platformy Azure w katalogu do odnajdowania. Jednak przed zarządzaniem swoimi zasobami za pomocą usługi PIM zalecamy uzyskanie zgody od wszystkich właścicieli subskrypcji.

### <a name="enforce-principle-of-least-privilege"></a>Wymuś zasadę najniższych uprawnień

Ważne jest, aby upewnić się, że zasady najniższych uprawnień w Twojej organizacji zostały wymuszone dla usługi Azure AD i ról zasobów platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

W przypadku ról usługi Azure AD powszechne jest, aby organizacje przypisywały rolę administratora globalnego do znaczącej liczby administratorów, gdy większość administratorów potrzebuje tylko jednej lub dwóch określonych ról administratora. Przypisań ról uprzywilejowanych również można pozostawić niezarządzaną.

> [!NOTE]
> Wspólne pułapek w delegowaniu ról:
>
> - Administrator, który jest odpowiedzialny za wymianę, ma rolę administratora globalnego, mimo że potrzebuje tylko roli administratora programu Exchange do wykonywania codziennych zadań.
> - Rola administratora globalnego jest przypisywana do administratora pakietu Office, ponieważ administrator musi mieć role zabezpieczeń i administratorów programu SharePoint, a łatwiej jest tylko delegować jedną rolę.
> - Administratorowi przypisano rolę administratora zabezpieczeń, aby wykonać zadanie o długim czasie temu, ale nigdy nie zostało usunięte.

Wykonaj następujące kroki, aby wymusić zasadę najniższych uprawnień dla ról usługi Azure AD.

1. Zapoznaj się z szczegółowością ról, odczytując i opisując [dostępne role administratorów usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Ty i Twój zespół powinien również odwoływać się do [ról administratorów według tożsamości w usłudze Azure AD](../users-groups-roles/roles-delegate-by-task.md), która objaśnia najmniejszą rolę uprzywilejowaną dla konkretnych zadań.

1. Lista użytkowników, którzy mają role uprzywilejowane w organizacji. Możesz użyć [Kreatora PIM](pim-security-wizard.md#run-the-wizard) , aby przejść do strony podobnej do następującej.

    ![Okienko odnajdywanie ról uprzywilejowanych pokazujące, kto ma role uprzywilejowane](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Dla wszystkich administratorów globalnych w organizacji należy dowiedzieć się, dlaczego potrzebują roli. W oparciu o odczytywanie poprzedniej dokumentacji, jeśli zadanie osoby może być wykonywane przez jedną lub kilka szczegółowych ról administratora, należy usunąć je z roli administratora globalnego i przystąpić odpowiednio do Azure Active Directory (jako odwołanie: Obecnie firma Microsoft obejmuje około 10 administratorów z rolą administratora globalnego. Dowiedz się więcej o tym, [jak firma Microsoft używa programu PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. W przypadku wszystkich innych ról usługi Azure AD Przejrzyj listę przypisań, zidentyfikuj administratorów, którzy nie potrzebują już roli, i Usuń ich z przypisań.

Aby zautomatyzować kroki 3 i 4, można użyć funkcji przegląd dostępu w programie PIM. Wykonaj kroki opisane w sekcji [Rozpocznij przegląd dostępu dla ról usługi Azure AD w usłudze PIM](pim-how-to-start-security-review.md), można skonfigurować przegląd dostępu dla każdej roli usługi Azure AD, która ma co najmniej jednego członka.

![Tworzenie okienka przeglądu dostępu dla ról usługi Azure AD](./media/pim-deployment-plan/create-access-review.png)

Należy ustawić recenzentów na **elementy członkowskie (własne)** . Spowoduje to wysłanie wiadomości e-mail do wszystkich członków w roli w celu potwierdzenia, że potrzebują dostępu. Należy również włączyć opcję **Wymagaj przyczyny przy zatwierdzeniu** w ustawieniach zaawansowanych, aby użytkownicy mogli określać, dlaczego potrzebują roli. Na podstawie tych informacji można usunąć użytkowników z niepotrzebnych ról i delegować bardziej szczegółowe role administratorów w przypadku administratorów globalnych.

Przeglądy dostępu polegają na wiadomościach e-mail powiadamiających użytkowników o konieczności sprawdzenia dostępu do ról. Jeśli masz uprzywilejowane konta, które nie mają połączonych wiadomości e-mail, pamiętaj o wypełnieniu pola pomocniczy adres e-mail na tych kontach. Aby uzyskać więcej informacji, zobacz [proxyAddresses Attribute in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

W przypadku subskrypcji i zasobów platformy Azure można skonfigurować podobny proces przeglądu dostępu, aby przejrzeć role w każdej subskrypcji lub zasobie. Celem tego procesu jest minimalizowanie przypisań administratorów i dostępu użytkowników do poszczególnych subskrypcji lub zasobów oraz usuwanie niepotrzebnych przypisań. Jednak organizacje często delegują te zadania do właściciela każdej subskrypcji lub zasobu, ponieważ mają lepsze zrozumienie konkretnych ról (zwłaszcza ról niestandardowych).

Jeśli jesteś administratorem IT z rolą administratora globalnego próbującym wdrożyć usługę PIM dla zasobów platformy Azure w organizacji, możesz podwyższyć [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , aby uzyskać dostęp do każdej subskrypcji. Następnie można znaleźć wszystkich właścicieli subskrypcji i z nich korzystać, aby usunąć niepotrzebne przypisania i zminimalizować przypisanie roli właściciela.

Użytkownicy z rolą właściciela dla subskrypcji platformy Azure mogą również wykorzystywać [przeglądy dostępu dla zasobów platformy Azure](pim-resource-roles-start-access-review.md) , aby przeprowadzać inspekcję i usuwać niepotrzebne przypisania ról podobne do procesu opisanego wcześniej dla ról usługi Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Wybór przypisań ról, które mają być chronione przez usługę PIM

Po oczyszczeniu przypisań ról uprzywilejowanych w organizacji należy zdecydować, które role mają być chronione za pomocą programu PIM.

Jeśli rola jest chroniona przez usługę PIM, uprawnieni użytkownicy przypisani do niej muszą podnieść poziom uprawnień, aby korzystać z przywilejów przyznanych przez rolę. Proces podniesienia uprawnień może również obejmować uzyskanie zatwierdzenia, przeprowadzenie uwierzytelniania wieloskładnikowego i/lub dostarczenie przyczyny aktywacji. Usługa PIM może również śledzić podniesienia uprawnień za pomocą powiadomień i dzienników zdarzeń usług PIM i Azure AD Audit.

Wybór ról do ochrony za pomocą usługi PIM może być trudny i różny dla każdej organizacji. Ta sekcja zawiera nasze najlepsze rozwiązanie dla usług Azure AD i Azure Resource role.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

Ważne jest, aby określić priorytety ochrony ról usługi Azure AD, które mają największą liczbę uprawnień. W oparciu o wzorce użycia wśród wszystkich klientów PIM, 10 głównych ról usługi Azure AD zarządzanych przez program PIM są następujące:

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
> :heavy_check_mark: **Firma Microsoft zaleca** , aby zarządzać wszystkimi administratorami globalnymi i administratorami zabezpieczeń przy użyciu programu PIM jako pierwszy krok, ponieważ są one tymi, które mogą spowodować, że zostaną naruszone.

Ważne jest, aby wziąć pod uwagę, jakie dane i uprawnienia są najbardziej poufne dla Twojej organizacji. Przykładowo niektóre organizacje mogą chcieć chronić swoją rolę administratora Power BI lub rolę administratora zespołów przy użyciu programu PIM, ponieważ mają możliwość dostępu do danych i/lub zmiany podstawowych przepływów pracy.

Jeśli istnieją jakieś role z przypisanymi użytkownikami gościa, są one szczególnie narażone na ataki.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** , aby zarządzać wszystkimi rolami z użytkownikami-gośćmi przy użyciu usługi PIM, aby ograniczyć ryzyko związane z kontami użytkowników Gości.

Role czytnika, takie jak czytniki katalogów, czytnik centrum komunikatów i czytelnik zabezpieczeń, są czasami uważane za mniej ważne w porównaniu z innymi rolami, ponieważ nie mają uprawnień do zapisu. Jednak niektórzy klienci widzą również ochronę tych ról, ponieważ osoby atakujące, którzy uzyskali dostęp do tych kont, mogą odczytywać dane poufne, takie jak dane osobowe. Należy to wziąć pod uwagę podczas decydowania, czy role czytnika w organizacji muszą być zarządzane przy użyciu usługi PIM.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

Podczas wybierania przypisań ról, które mają być zarządzane za pomocą usługi PIM dla zasobów platformy Azure, należy najpierw zidentyfikować subskrypcje/zasoby, które są najważniejsze dla organizacji. Przykładami takich subskrypcji/zasobów są:

- Zasoby, które obsługują najbardziej poufne dane
- Zasoby, od których zależą podstawowe aplikacje dla klientów

Jeśli jesteś administratorem globalnym, który ma problemy z wybraniem najważniejszych subskrypcji/zasobów, należy skontaktować się z właścicielami subskrypcji w organizacji w celu zebrania listy zasobów zarządzanych przez każdą subskrypcję. Następnie należy skontaktować się z właścicielami subskrypcji w celu pogrupowania zasobów na podstawie poziomu ważności w przypadku naruszenia zabezpieczeń (niski, średni, wysoki). Należy określić priorytety zarządzania zasobami przy użyciu programu PIM na podstawie tego poziomu ważności.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** współpracę z właścicielami subskrypcji/zasobów krytycznych usług w celu skonfigurowania przepływu pracy w usłudze PIM dla wszystkich ról w poufnych subskrypcjach/zasobach.

Usługa PIM dla zasobów platformy Azure obsługuje powiązane z czasem konta usług. Konta usług należy traktować dokładnie tak samo, jak w przypadku traktowania zwykłego konta użytkownika.

W przypadku subskrypcji/zasobów niekrytycznych nie trzeba konfigurować usługi PIM dla wszystkich ról. Należy jednak nadal chronić rolę administratora dostępu właściciela i użytkownika przy użyciu programu PIM.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** Zarządzanie rolami właściciela i rolami administratora dostępu użytkowników wszystkich subskrypcji/zasobów przy użyciu usługi PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Zdecyduj, które przypisania ról powinny być trwałe lub kwalifikujące się

Po określeniu listy ról, które mają być zarządzane przez usługę PIM, należy zdecydować, którzy użytkownicy powinni uzyskać kwalifikującą się rolę i trwale aktywną rolę. Stałe aktywne role są normalnymi rolami przypisanymi za pomocą Azure Active Directory i zasobami platformy Azure, podczas gdy kwalifikujące się role można przypisywać tylko w usłudze PIM.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** , aby dla ról usługi Azure AD i ról zasobów platformy Azure nie było żadnych trwałych, a nie zalecanych [dwóch kont dostępu awaryjnego](../users-groups-roles/directory-emergency-access.md), które powinny mieć stały Administrator globalny. roli.

Mimo że firma Microsoft zaleca zero administratorów, czasami trudno jest uzyskać od tych organizacji. Poniżej przedstawiono kwestie, które należy wziąć pod uwagę podczas podejmowania tej decyzji:

- Częstotliwość podniesienia uprawnień — Jeśli użytkownik potrzebuje tylko jednego przypisania uprzywilejowanego, nie powinien mieć trwałego przypisania. Z drugiej strony, jeśli użytkownik potrzebuje roli do codziennego zadania, a korzystanie z usługi PIM znacznie zmniejszy swoją produktywność, można je rozważyć dla stałej roli.
- Przypadki specyficzne dla Twojej organizacji — jeśli osoba mająca kwalifikującą się rolę pochodzi z bardzo odległego zespołu lub dyrektora o wysokiej klasyfikacji do punktu, który komunikuje się i wymusza proces podniesienia uprawnień, może być traktowany jako rola trwała.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** skonfigurowanie cyklicznych przeglądów dostępu dla użytkowników z trwałymi przypisaniami ról (jeśli istnieją). Dowiedz się więcej o cyklicznym przeglądzie dostępu w ostatniej sekcji tego planu wdrożenia

### <a name="draft-your-pim-settings"></a>Wersje robocze ustawień programu PIM

Przed zaimplementowaniem rozwiązania PIM warto podwyższyć poziom ustawień usługi PIM dla każdej roli uprzywilejowanej używanej przez organizację. W tej sekcji przedstawiono kilka przykładów ustawień usługi PIM dla konkretnych ról (są one przeznaczone tylko dla celów informacyjnych i mogą być różne dla organizacji). Każde z tych ustawień zostało szczegółowo omówione w zaleceniach firmy Microsoft po tabelach.

#### <a name="pim-settings-for-azure-ad-roles"></a>Ustawienia usług PIM dla ról usługi Azure AD

| Role | Wymaganie usługi MFA | Powiadomienia | Bilet zdarzenia | Wymagaj zatwierdzenia | Osoba zatwierdzająca | Czas trwania aktywacji | Administrator trwały |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrator globalny | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni administratorzy globalni | 1 godzina | Konta dostępu awaryjnego |
| Administrator programu Exchange | :heavy_check_mark: | :heavy_check_mark: | y | y | Brak | 2 godziny | Brak |
| Administrator pomocy technicznej | y | y | :heavy_check_mark: | y | Brak | 8 godzin | Brak |

#### <a name="pim-settings-for-azure-resource-roles"></a>Ustawienia usług PIM dla ról zasobów platformy Azure

| Role | Wymaganie usługi MFA | Powiadomienia | Wymagaj zatwierdzenia | Osoba zatwierdzająca | Czas trwania aktywacji | Administrator aktywny | Aktywne wygaśnięcie | Kwalifikujące się wygaśnięcie |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Właściciel krytycznych subskrypcji | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni właściciele subskrypcji | 1 godzina | Brak | Nie dotyczy | 3 miesiące |
| Administrator dostępu użytkowników z mniej krytycznymi subskrypcjami | :heavy_check_mark: | :heavy_check_mark: | y | Brak | 1 godzina | Brak | Nie dotyczy | 3 miesiące |
| Współautor maszyny wirtualnej | y | :heavy_check_mark: | y | Brak | 3 godziny | Brak | Nie dotyczy | 6 miesięcy |

W poniższej tabeli opisano poszczególne ustawienia.

| Ustawienie | Opis |
| --- | --- |
| Role | Nazwa roli, dla której są definiowane ustawienia. |
| Wymaganie usługi MFA | Czy uprawniony użytkownik musi wykonać uwierzytelnianie wieloskładnikowe przed aktywowaniem roli.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** Wymuszanie uwierzytelniania wieloskładnikowego dla wszystkich ról administratora, szczególnie jeśli role mają użytkowników-Gości. |
| Powiadomienia | Jeśli wartość jest równa true, Administrator globalny, administrator ról uprzywilejowanych i administrator zabezpieczeń w organizacji otrzyma powiadomienie e-mail, gdy uprawniony użytkownik aktywuje rolę.<br/><br/>**Uwaga:** Niektóre organizacje nie mają adresu e-mail powiązanego z kontami administratorów, aby otrzymywać powiadomienia e-mail, należy ustawić alternatywny adres e-mail, aby administratorzy otrzymywali te wiadomości e-mail. |
| Bilet zdarzenia | Czy uprawniony użytkownik musi zarejestrować numer biletu zdarzenia podczas aktywowania ich roli. To ustawienie pomaga organizacji identyfikować każdą aktywację z wewnętrznym numerem zdarzenia w celu ograniczenia niepożądanej aktywacji.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** korzystanie z numerów biletów zdarzeń do powiązania PIM z systemem wewnętrznym. Jest to szczególnie przydatne w przypadku osób zatwierdzających, które wymagają kontekstu aktywacji. |
| Wymagaj zatwierdzenia | Czy uprawniony użytkownik musi uzyskać zatwierdzenie, aby aktywować rolę.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** , aby skonfigurować zatwierdzanie ról z największymi uprawnieniami. W oparciu o wzorce użycia wszystkich klientów PIM, administratorów globalnych, administratorów użytkowników, administratorów programu Exchange, administratora zabezpieczeń i administratora hasła są najczęściej używane role z konfiguracją zatwierdzania. |
| Osoba zatwierdzająca | Jeśli wymagane jest zatwierdzenie w celu aktywowania kwalifikującej się roli, należy podać osoby, które powinny zatwierdzić żądanie. Domyślnie program PIM ustawia osobę zatwierdzającą na wszystkich użytkowników, którzy są administratorami ról uprzywilejowanych, niezależnie od tego, czy są one stałe czy kwalifikujące się.<br/><br/>**Uwaga:** Jeśli użytkownik jest uprawniony do roli usługi Azure AD i osoby zatwierdzającej rolę, nie będzie w stanie zatwierdzić siebie.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** , aby wybrać osoby zatwierdzające, które są najbardziej znające konkretną rolę i jej częste użytkowników, a nie administratora globalnego. |
| Czas trwania aktywacji | Długość czasu aktywacji użytkownika w roli przed jego wygaśnięciem. |
| Administrator trwały | Lista użytkowników, którzy będą administratorami stałymi dla roli (nigdy nie trzeba aktywować).<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca** , aby nie mieć stałego administratora dla wszystkich ról, z wyjątkiem administratorów globalnych. Przeczytaj więcej na temat tego, kto powinien zostać uprawniony i kto powinien być trwale aktywny w tym planie. |
| Administrator aktywny | W przypadku zasobów platformy Azure Active administrator jest listą użytkowników, którzy nigdy nie będą musieli aktywować, aby korzystać z roli. Nie jest to nazywane stałymi administratorami, takimi jak role usługi Azure AD, ponieważ można ustawić czas wygaśnięcia dla momentu utraty tej roli przez użytkownika. |
| Aktywne wygaśnięcie | Przypisanie aktywnej roli dla ról zasobów platformy Azure wygasa po tym skonfigurowanym okresie. Możesz wybrać 15 dni, 1 miesiąc, 3 miesiące, 6-miesięczny, 1 rok lub trwale aktywny. |
| Kwalifikujące się wygaśnięcie | Odpowiednie przypisanie roli dla ról zasobów platformy Azure wygasa po tym skonfigurowanym okresie. Możesz wybrać 15 dni, 1 miesiąc, 3 miesiące, 6 miesięcy, 1 rok lub trwale kwalifikujące się. |

## <a name="step-3-implement-your-solution"></a>Krok 3. Implementowanie rozwiązania

Podstawą prawidłowego planowania jest podstawa, na której można pomyślnie wdrożyć aplikację z Azure Active Directory.  Zapewnia ona inteligentne zabezpieczenia i integrację, która upraszcza proces dołączania, skracając czas na pomyślne wdrożenia.  Ta kombinacja gwarantuje, że aplikacja jest zintegrowana z łatwością, jednocześnie ograniczając czas działania użytkowników końcowych.

### <a name="identify-test-users"></a>Identyfikowanie użytkowników testowych

Ta sekcja służy do identyfikowania zestawu użytkowników i grup użytkowników w celu sprawdzenia poprawności implementacji. Na podstawie ustawień wybranych w sekcji Planowanie Zidentyfikuj użytkowników, którzy mają zostać przetestować dla każdej roli.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** , aby właściciele usług każdej roli usługi Azure AD byli użytkownikami testowymi, dzięki czemu mogą zapoznać się z procesem i stać się wewnętrznym Rzecznikem.

W tej tabeli Zidentyfikuj użytkowników testowych, którzy weryfikują, że ustawienia dla każdej roli działają.

| Nazwa roli | Użytkownicy testowi |
| --- | --- |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy do testowania roli&gt; |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy do testowania roli&gt; |

### <a name="test-implementation"></a>Implementacja testowa

Teraz, po zidentyfikowaniu użytkowników testowych, użyj tego kroku, aby skonfigurować usługę PIM dla użytkowników testowych. Jeśli Twoja organizacja chce dołączyć przepływ pracy usługi PIM do własnej aplikacji wewnętrznej zamiast korzystania z interfejsu użytkownika programu PIM w ramach Azure Portal, wszystkie operacje w usłudze PIM są również obsługiwane za pośrednictwem [interfejsu API programu Graph](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Konfigurowanie usług PIM dla ról usługi Azure AD

1. [Skonfiguruj ustawienia roli usługi Azure AD](pim-how-to-change-default-settings.md) na podstawie planowanych czynności.

1. Przejdź do **ról usługi Azure AD**, kliknij pozycję **role**, a następnie wybierz właśnie skonfigurowaną rolę.

1. W przypadku grupy użytkowników testowych, jeśli są już stałymi uprawnieniami administratora, możesz je przykwalifikować, wyszukując je i konwertując z trwałego na kwalifikujące się, klikając trzy kropki w ich wierszu. Jeśli nie mają jeszcze przypisań ról, można [utworzyć nowe kwalifikujące się przypisanie](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Powtórz kroki od 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link pozwalający [aktywować rolę usługi Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Konfigurowanie usług PIM dla ról zasobów platformy Azure

1. [Skonfiguruj ustawienia roli zasobów platformy Azure](pim-resource-roles-configure-role-settings.md) dla roli w ramach subskrypcji lub zasobu, który chcesz przetestować.

1. Przejdź do **zasobów platformy Azure** dla tej subskrypcji, a następnie kliknij pozycję **role**, wybierz właśnie skonfigurowaną rolę.

1. W przypadku grupy użytkowników testowych, jeśli są one już aktywnym administratorem, możesz je kwalifikować, wyszukując je i [aktualizując przypisane role](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Jeśli nie mają jeszcze roli, możesz [przypisać nową rolę](pim-resource-roles-assign-roles.md#assign-a-role).

1. Powtórz kroki od 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link pozwalający [aktywować swoją rolę zasobów platformy Azure](pim-resource-roles-activate-your-roles.md).

Należy użyć tego etapu, aby sprawdzić, czy cała konfiguracja skonfigurowana dla ról działa prawidłowo. Skorzystaj z poniższej tabeli, aby udokumentować testy. Ten etap powinien również służyć do optymalizowania komunikacji z użytkownikami, których to dotyczy.

| Role | Oczekiwane zachowanie podczas aktywacji | Rzeczywiste wyniki |
| --- | --- | --- |
| Administrator globalny | (1) Wymagaj uwierzytelniania wieloskładnikowego<br/>(2) Wymagaj zatwierdzenia<br/>(3) osoba zatwierdzająca otrzymuje powiadomienie i może zatwierdzić<br/>(4) rola wygasa po upływie czasu wstępnego |  |
| Właściciel subskrypcji *X* | (1) Wymagaj uwierzytelniania wieloskładnikowego<br/>(2) kwalifikujące się przypisanie wygasa po upływie skonfigurowanego okresu |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Komunikuj się z usługą PIM na zainteresowane strony

Wdrożenie usługi PIM spowoduje wprowadzenie dodatkowych kroków dla użytkowników ról uprzywilejowanych. Chociaż program PIM znacznie zmniejsza problemy z zabezpieczeniami związanymi z tożsamościami uprzywilejowanymi, zmiana musi być efektywnie przekazywana przed wdrożeniem w całej dzierżawie. W zależności od liczby administratorów, którzy mają wpływ, organizacje często wybierają do tworzenia dokument wewnętrzny, wideo lub wiadomość e-mail o zmianie. Często zawarte w tych komunikacji obejmują:

- Co to jest PIM
- Co to jest korzyść dla organizacji
- Którego będzie dotyczyć
- Kiedy będzie można wdrożyć usługę PIM
- Jakie dodatkowe czynności będą wymagane, aby użytkownicy mogli aktywować swoją rolę
    - Należy wysłać linki do naszej dokumentacji:
    - [Aktywuj role usługi Azure AD](pim-how-to-activate-role.md)
    - [Aktywuj role zasobów platformy Azure](pim-resource-roles-activate-your-roles.md)
- Informacje kontaktowe lub pomoc techniczna dla wszystkich problemów związanych z usługą PIM

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** , aby skonfigurować czas pracy z zespołem pomocy technicznej/wsparcia technicznego w celu przechodzenia do nich za pomocą środowiska PIM (Jeśli organizacja ma wewnętrzny zespół pomocy technicznej IT). Podaj im odpowiednie dokumenty oraz informacje kontaktowe.

### <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Po zakończeniu testowania i pomyślnym przeniesieniu danych do środowiska usługi PIM należy powtórzyć wszystkie kroki w fazach testowania dla wszystkich użytkowników każdej roli zdefiniowanej w konfiguracji usługi PIM. W przypadku usług PIM dla ról usługi Azure AD organizacje często testują i wdrażają usługę PIM dla administratorów globalnych przed testowaniem i wdrażaniem usługi PIM dla innych ról. W tym czasie w przypadku zasobów platformy Azure zwykle testuje i rozwijają usługę PIM w ramach subskrypcji platformy Azure.

### <a name="in-the-case-a-rollback-is-needed"></a>W przypadku konieczności wycofania

Jeśli w środowisku produkcyjnym nie udało się prawidłowo wykonać działania programu PIM, następujące kroki wycofywania mogą pomóc w powrocie do znanego dobrego stanu przed skonfigurowaniem programu PIM:

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Kliknij pozycję **role usługi Azure AD** , a następnie kliknij pozycję **role**.
1. Dla każdej skonfigurowanej roli kliknij przycisk wielokropka ( **...** ) dla wszystkich użytkowników z uprawnionym przypisaniem.
1. Kliknij opcję **Utwórz trwale** , aby przypisać rolę jako trwałą.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Kliknij pozycję **zasoby platformy Azure** , a następnie kliknij subskrypcję lub zasób, które chcesz przywrócić.
1. Kliknij pozycję **role**.
1. Dla każdej skonfigurowanej roli kliknij przycisk wielokropka ( **...** ) dla wszystkich użytkowników z uprawnionym przypisaniem.
1. Kliknij opcję **Utwórz trwale** , aby przypisać rolę jako trwałą.

## <a name="step-4-next-steps-after-deploying-pim"></a>Krok 4. Następne kroki po wdrożeniu PIM

Pomyślnie wdrożono usługę PIM w środowisku produkcyjnym. jest to znaczący krok w zakresie zabezpieczania tożsamości uprzywilejowanych w organizacji. W przypadku wdrażania usługi PIM dostępne są dodatkowe funkcje usługi PIM, które powinny być używane na potrzeby zabezpieczeń i zgodności.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Używanie alertów PIM do zabezpieczenia uprzywilejowanego dostępu

Należy używać wbudowanych funkcji alertów PIM, aby lepiej chronić dzierżawę. Aby uzyskać więcej informacji, zobacz [alerty zabezpieczeń](pim-how-to-configure-security-alerts.md#security-alerts). Te alerty obejmują: Administratorzy nie używają uprzywilejowanych ról, role są przypisywane poza usługą PIM, role są aktywowane zbyt często i nie tylko. Aby w pełni chronić organizację, należy regularnie przechodzić przez listę alertów i rozwiązać problemy. Alerty można wyświetlać i usuwać w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Kliknij pozycję **role usługi Azure AD** , a następnie kliknij pozycję **alerty**.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** , aby od razu zająć się wszystkimi alertami oznaczonymi o wysokiej ważności. W przypadku alertów o średniej i niskiej ważności użytkownik powinien otrzymywać informacje i wprowadzić zmiany, jeśli sądzisz, że istnieje zagrożenie bezpieczeństwa.

Jeśli którekolwiek z określonych alertów nie są przydatne lub nie mają zastosowania w organizacji, zawsze możesz odrzucić ten alert na stronie alertów. Można zawsze przywrócić ten odrzucanie później na stronie ustawień usługi Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Skonfiguruj cykliczne przeglądy dostępu do regularnego przeprowadzania inspekcji tożsamości uprzywilejowanych w organizacji

Przeglądy dostępu są najlepszym sposobem na zaproszenie użytkowników przypisanych do ról uprzywilejowanych lub określonych recenzentów, czy każdy użytkownik musi mieć uprzywilejowaną tożsamość. Przeglądy dostępu są doskonałe, jeśli chcesz zmniejszyć obszar ataków i zachować zgodność. Aby uzyskać więcej informacji na temat rozpoczynania przeglądu dostępu, zobacz przeglądy [dostępu do ról usługi Azure AD](pim-how-to-start-security-review.md) oraz [przeglądy dostępu do ról zasobów platformy Azure](pim-resource-roles-start-access-review.md). W przypadku niektórych organizacji okresowe przeglądy dostępu są wymagane, aby zachować zgodność z przepisami i przepisami, a w przypadku innych, przegląd dostępu jest najlepszym sposobem wymuszania najniższych uprawnień w całej organizacji.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** skonfigurowanie kwartalnych przeglądów dostępu dla wszystkich ról usługi Azure AD i zasobów platformy Azure.

W większości przypadków, osoba dokonująca przeglądu dla ról usługi Azure AD jest właścicielem subskrypcji, w której znajduje się rola zasobów platformy Azure. Jednak często zdarza się, że firmy mają uprzywilejowane konta, które nie są połączone z żadnym z adresów e-mail poszczególnych osób. W takich przypadkach nikt nie odczytuje ani nie przegląda dostępu.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** dodanie pomocniczego adresu e-mail dla wszystkich kont z przypisaniami ról uprzywilejowanych, które nie są połączone z regularnie sprawdzonym adresem e-mail.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Maksymalnie zapoznaj się z dziennikiem inspekcji, aby zwiększyć bezpieczeństwo i zgodność

Dziennik inspekcji jest miejscem, w którym można zachować aktualność i być zgodne z przepisami. PIM obecnie przechowuje 30-dniową historię wszystkich historii organizacji w swoim dzienniku inspekcji, w tym:

- Aktywacja/dezaktywacja kwalifikujących się ról
- Działania przypisania roli wewnątrz i na zewnątrz programu PIM
- Zmiany w ustawieniach roli
- Działania żądania/zatwierdzania/odmowy dla aktywacji roli z konfiguracją zatwierdzania
- Aktualizowanie alertów

Możesz uzyskać dostęp do tych dzienników inspekcji, jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej. Aby uzyskać więcej informacji, zobacz [historię inspekcji dla ról usługi Azure AD](pim-how-to-use-audit-log.md) i [historii inspekcji dla ról zasobów platformy Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** , aby co najmniej jeden administrator odczytał wszystkie zdarzenia inspekcji co tydzień i regularnie eksportować zdarzenia inspekcji.

Jeśli chcesz automatycznie przechowywać zdarzenia inspekcji przez dłuższy czas, dziennik inspekcji programu PIM zostanie automatycznie zsynchronizowany z [dziennikami inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca** skonfigurowanie [monitorowania dzienników platformy Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) w celu archiwizowania zdarzeń inspekcji na koncie usługi Azure Storage w celu zapewnienia bezpieczeństwa i zgodności.
