---
title: Wdrażanie zarządzania tożsamościami uprzywilejowanymi (PIM) — usługa Azure AD | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób planowania wdrażania usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026000"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Wdrażanie zarządzania tożsamościami uprzywilejowanymi usługi Azure AD (PIM)

W tym przewodniku krok po kroku opisano sposób planowania wdrażania usługi Zarządzania tożsamościami uprzywilejowanymi (PIM) w organizacji usługi Azure Active Directory (Azure AD).

> [!TIP]
> W całym tym artykule zobaczysz elementy oznaczone jako:
> 
> :heavy_check_mark: Microsoft **poleca**
> 
> Są to ogólne zalecenia i należy je implementować tylko wtedy, gdy mają one zastosowanie do konkretnych potrzeb przedsiębiorstwa.

## <a name="learn-about-privileged-identity-management"></a>Dowiedz się więcej o zarządzaniu tożsamościami uprzywilejowanymi

Usługa Azure AD Privileged Identity Management ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usługach Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. W świecie, w którym uprzywilejowane tożsamości są przypisywane i zapomniane, uprzywilejowane zarządzanie tożsamościami zapewnia rozwiązania, takie jak dostęp just-in-time, przepływy pracy zatwierdzania żądań i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, wykrywać i zapobiegać złośliwym uprzywilejowanych ról w czasie rzeczywistym. Wdrażanie zarządzania tożsamościami uprzywilejowanymi w celu zarządzania uprzywilejowanymi rolami w całej organizacji znacznie zmniejszy ryzyko, jednocześnie przedstawiając cenne informacje na temat działań ról uprzywilejowanych.

### <a name="business-value-of-privileged-identity-management"></a>Wartość biznesowa zarządzania tożsamościami uprzywilejowanymi

**Zarządzanie ryzykiem** — zabezpiecz organizację, wymuszając zasadę [dostępu z uprawnieniami i dostępu](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) just-in-time. Minimalizując liczbę stałych przypisań użytkowników do ról uprzywilejowanych i wymuszając zatwierdzenia i uwierzytelnianie wieloskładnikowe dla podniesienia uprawnień, można znacznie zmniejszyć ryzyko związane z bezpieczeństwem związanym z dostępem uprzywilejowanym w organizacji. Wymuszanie najmniejszych uprawnień i dostępu just-in-time umożliwia również wyświetlanie historii dostępu do ról uprzywilejowanych i śledzenie problemów z zabezpieczeniami w miarę ich zdarzających się.

**Zgodność adresów i zasady nadzoru** — wdrażanie zarządzania tożsamościami uprzywilejowanymi tworzy środowisko do zarządzania tożsamościami w toku. Just-in-time podniesienie uprawnień tożsamości uprzywilejowanych umożliwia zarządzanie tożsamościami uprzywilejowanymi, aby śledzić działania uprzywilejowanego dostępu w organizacji. Będzie można również wyświetlać i otrzymywać powiadomienia o wszystkich przypisaniach stałych i kwalifikujących się ról w organizacji. Dzięki przeglądowi dostępu można regularnie przeprowadzać inspekcje i usuwać niepotrzebne tożsamości uprzywilejowane oraz upewnić się, że organizacja jest zgodna z najbardziej rygorystycznymi standardami tożsamości, dostępu i zabezpieczeń.

**Zmniejsz koszty** — zmniejsz koszty, eliminując nieefektywność, błędy ludzkie i problemy z zabezpieczeniami, prawidłowo wdrażając zarządzanie tożsamościami uprzywilejowanymi. Rezultatem netto jest ograniczenie przestępstw cybernetycznych związanych z uprzywilejowanymi tożsamościami, które są kosztowne i trudne do odzyskania. Uprzywilejowane zarządzanie tożsamościami pomoże również organizacji zmniejszyć koszty związane z inspekcją informacji o dostępie, jeśli chodzi o zgodność z przepisami i standardami.

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?](pim-configure.md).

### <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Aby korzystać z zarządzania tożsamościami uprzywilejowanymi, katalog musi mieć jedną z następujących płatnych lub próbnych licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne dotyczące korzystania z zarządzania tożsamościami uprzywilejowanymi](subscription-requirements.md).

### <a name="key-terminology"></a>Kluczowa terminologia

| Termin lub pojęcie | Opis |
| --- | --- |
| kwalifikowanie się | Przypisanie roli, które wymaga od użytkownika wykonania jednej lub kilku akcji w celu użycia tej roli. Jeśli użytkownik został zakwalifikowany do roli, oznacza to, że może aktywować tę rolę, kiedy musi wykonać zadanie uprzywilejowane. Nie ma żadnej różnicy między dostępem udzielonym komuś za pomocą trwałego przypisania roli i przypisania kwalifikowania się do roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie potrzebują tego dostępu przez cały czas. |
| aktywuj | Proces wykonywania jednej lub kilku akcji w celu użycia roli, do której użytkownik został zakwalifikowany. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających. |
| dostęp just-in-time (JIT) | Model, w którym użytkownicy uzyskują tymczasowe uprawnienia do wykonywania uprzywilejowanych zadań, dzięki czemu złośliwi lub nieautoryzowani użytkownicy nie mogą uzyskać dostępu po wygaśnięciu uprawnienia. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują. |
| zasada dostępu z najniższymi uprawnieniami | Zalecane rozwiązanie dotyczące zabezpieczeń, w którym każdy użytkownik ma minimalne uprawnienia wymagane do wykonania zadania, które mu przydzielono. Takie rozwiązanie pozwala zminimalizować liczbę administratorów globalnych i zamiast tego korzystać ze specyficznych ról administratorów dla konkretnych scenariuszy. |

Aby uzyskać więcej informacji, zobacz [Terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Omówienie wysokiego poziomu działania zarządzania tożsamościami uprzywilejowanymi

1. Zarządzanie tożsamościami uprzywilejowanymi jest skonfigurowane tak, aby użytkownicy kwalifikowali się do ról uprzywilejowanych.
1. Gdy uprawniony użytkownik musi użyć swojej uprzywilejowanej roli, aktywuje rolę w zarządzaniu tożsamościami uprzywilejowanymi.
1. W zależności od ustawień zarządzania tożsamościami uprzywilejowanymi skonfigurowanych dla roli użytkownik musi wykonać określone kroki (takie jak wykonywanie uwierzytelniania wieloskładnikowego, uzyskiwanie zatwierdzenia lub określanie przyczyny).
1. Gdy użytkownik pomyślnie aktywuje swoją rolę, otrzymają rolę dla wstępnie skonfigurowanego okresu.
1. Administratorzy mogą wyświetlać historię wszystkich działań związanych z zarządzaniem tożsamościami uprzywilejowanymi w dzienniku inspekcji. Mogą również dodatkowo zabezpieczyć swoje organizacje usługi Azure AD i spełniać zgodność przy użyciu funkcji zarządzania tożsamościami uprzywilejowanymi, takich jak przeglądy dostępu i alerty.

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Role, którymi może zarządzać zarządzanie tożsamością uprzywilejowaną

**Role usługi Azure AD** — wszystkie te role są w usłudze Azure Active Directory (takich jak administrator globalny, administrator programu Exchange i administrator zabezpieczeń). Więcej informacji na temat ról i ich funkcji można przeczytać w obszarze [Uprawnienia roli administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać pomoc dotyczącą określania ról, które mają być przypisywane administratorom, zobacz [najmniej uprzywilejowane role według zadania](../users-groups-roles/roles-delegate-by-task.md).

**Role zasobów platformy Azure** — te role są połączone z zasobem platformy Azure, grupą zasobów, subskrypcją lub grupą zarządzania. Zarządzanie tożsamościami uprzywilejowanymi zapewnia dostęp tylko w czasie zarówno do wbudowanych ról, takich jak właściciel, administrator dostępu do użytkownika i współautor, jak i [do ról niestandardowych.](../../role-based-access-control/custom-roles.md) Aby uzyskać więcej informacji na temat ról zasobów platformy Azure, zobacz [kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md).

Aby uzyskać więcej informacji, zobacz [Role, którymi nie można zarządzać w zarządzania tożsamościami uprzywilejowanymi](pim-roles.md).

## <a name="plan-your-deployment"></a>Planowanie wdrożenia

W tej sekcji skupiono się na tym, co należy zrobić przed wdrożeniem zarządzania tożsamościami uprzywilejowanymi w organizacji. Ważne jest, aby postępować zgodnie z instrukcjami i zrozumieć pojęcia w tej sekcji, ponieważ poprowadzą Cię do tworzenia najlepszego planu dostosowanego do uprzywilejowanych tożsamości organizacji.

### <a name="identify-your-stakeholders"></a>Zidentyfikuj interesariuszy

Poniższa sekcja pomaga zidentyfikować wszystkie zainteresowane strony, które są zaangażowane w projekt i muszą się podpisać, przejrzeć lub być na bieżąco. Zawiera oddzielne tabele do wdrażania zarządzania tożsamościami uprzywilejowanymi dla ról usługi Azure AD i zarządzania tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure. Dodaj interesariuszy do poniższej tabeli, stosownie do potrzeb organizacji.

- SO = Wyloguj się w tym projekcie
- R = Przejrzyj ten projekt i podaj dane wejściowe
- I = Informacja o tym projekcie

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Interesariusze: Zarządzanie tożsamościami uprzywilejowanymi dla ról usługi Azure AD

| Nazwa | Rola | Akcja |
| --- | --- | --- |
| Imię i nazwisko oraz adres e-mail | **Architekt tożsamości lub administrator globalny platformy Azure**<br/>Przedstawiciel zespołu zarządzania tożsamościami odpowiedzialny za zdefiniowanie sposobu dostosowania tej zmiany do podstawowej infrastruktury zarządzania tożsamościami w organizacji. | SO/R/I |
| Imię i nazwisko oraz adres e-mail | **Właściciel serwisu / Menedżer linii**<br/>Przedstawiciel właścicieli IT usługi lub grupy usług. Są one kluczowe w podejmowaniu decyzji i pomaga wdrożyć uprzywilejowane zarządzanie tożsamościami dla swojego zespołu. | SO/R/I |
| Imię i nazwisko oraz adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu zabezpieczeń, który może się podpisać, że plan spełnia wymagania dotyczące zabezpieczeń organizacji. | So/R |
| Imię i nazwisko oraz adres e-mail | **Kierownik pomocy technicznej / Helpdesk**<br/>Przedstawiciel organizacji wsparcia IT, który może dostarczyć informacji na temat wsparcia tej zmiany z punktu widzenia helpdesku. | R/I |
| Nazwa i adres e-mail dla użytkowników pilotażowych | **Użytkownicy ról uprzywilejowanych**<br/>Grupa użytkowników, dla których implementowane jest zarządzanie tożsamościami uprzywilejowanymi. Będą musieli wiedzieć, jak aktywować swoje role po zaimplementowanie zarządzania tożsamościami uprzywilejowanymi. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Interesariusze: Zarządzanie tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure

| Nazwa | Rola | Akcja |
| --- | --- | --- |
| Imię i nazwisko oraz adres e-mail | **Subskrypcja / Właściciel zasobu**<br/>Przedstawiciel właścicieli IT każdej subskrypcji lub zasobu, który ma wdrożyć zarządzanie tożsamościami uprzywilejowanymi dla | SO/R/I |
| Imię i nazwisko oraz adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu zabezpieczeń, który może się podpisać, że plan spełnia wymagania dotyczące zabezpieczeń organizacji. | So/R |
| Imię i nazwisko oraz adres e-mail | **Kierownik pomocy technicznej / Helpdesk**<br/>Przedstawiciel organizacji wsparcia IT, który może dostarczyć informacji na temat wsparcia tej zmiany z punktu widzenia helpdesku. | R/I |
| Nazwa i adres e-mail dla użytkowników pilotażowych | **Użytkownicy roli RBAC**<br/>Grupa użytkowników, dla których implementowane jest zarządzanie tożsamościami uprzywilejowanymi. Będą musieli wiedzieć, jak aktywować swoje role po zaimplementowanie zarządzania tożsamościami uprzywilejowanymi. | I |

### <a name="enable-privileged-identity-management"></a>Włączanie zarządzania tożsamościami uprzywilejowanymi

W ramach procesu planowania należy najpierw wyrazić zgodę na zarządzanie tożsamościami uprzywilejowanymi i włączyć je, wykonując nasz [artykuł o korzystaniu z zarządzania tożsamościami uprzywilejowanymi.](pim-getting-started.md) Włączenie zarządzania tożsamościami uprzywilejowanymi zapewnia dostęp do niektórych funkcji, które są specjalnie zaprojektowane, aby ułatwić wdrożenie.

Jeśli twoim celem jest wdrożenie zarządzania tożsamościami uprzywilejowanymi dla zasobów platformy Azure, należy postępować zgodnie z naszymi zasobami platformy Azure, aby zarządzać nimi w artykule [Zarządzanie tożsamościami uprzywilejowanymi.](pim-resource-roles-discover-resources.md) Tylko właściciele subskrypcji i grup zarządzania można odnajdyć i dołączać tych zasobów do zarządzania tożsamościami uprzywilejowanymi. Po jego dołączaniu funkcja pim jest dostępna dla właścicieli na wszystkich poziomach, w tym grupy zarządzania, subskrypcji, grupy zasobów i zasobu. Jeśli jesteś administratorem globalnym, który próbuje wdrożyć zarządzanie tożsamościami uprzywilejowanymi dla zasobów platformy Azure, możesz [podnieść poziom dostępu, aby zarządzać wszystkimi subskrypcjami platformy Azure,](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) aby zapewnić sobie dostęp do wszystkich zasobów platformy Azure w katalogu do odnajdywania. Zalecamy jednak uzyskanie zgody od każdego z właścicieli subskrypcji przed zarządzaniem ich zasobami za pomocą uprzywilejowanego zarządzania tożsamościami.

### <a name="enforce-principle-of-least-privilege"></a>Wymuszanie zasady najmniejszych uprawnień

Ważne jest, aby upewnić się, że wymuszone zasady najmniejszych uprawnień w organizacji dla ról zasobów usługi Azure AD i platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

W przypadku ról usługi Azure AD często organizacje przypisują rolę administratora globalnego do znacznej liczby administratorów, gdy większość administratorów potrzebuje tylko jednej lub dwóch określonych ról administratora. Przypisania ról uprzywilejowanych również wydają się być pozostawione niezarządzane.

> [!NOTE]
> Typowe pułapki w delegowaniu ról:
>
> - Administrator odpowiedzialny za program Exchange otrzymuje rolę administratora globalnego, nawet jeśli do wykonywania codziennych zadań potrzebna jest tylko rola administratora programu Exchange.
> - Rola Administratora globalnego jest przypisana do administratora pakietu Office, ponieważ administrator potrzebuje zarówno ról administratora programu Security, jak i programu SharePoint i łatwiej jest po prostu delegować jedną rolę.
> - Administratorowi przypisano rolę Administratora zabezpieczeń do wykonania zadania dawno temu, ale nigdy nie został usunięty.

Wykonaj następujące kroki, aby wymusić zasadę najmniejszych uprawnień dla ról usługi Azure AD.

1. Poznaj szczegółowość ról, czytając i rozumiejąc [dostępne role administratora usługi Azure AD.](../users-groups-roles/directory-assign-admin-roles.md#available-roles) Ty i Twój zespół należy również odwołać się do [ról administratora według zadania tożsamości w usłudze Azure AD](../users-groups-roles/roles-delegate-by-task.md), który wyjaśnia najmniej uprzywilejowanych roli dla określonych zadań.

1. Wyświetl, kto ma uprzywilejowane role w organizacji. [Kreatora zarządzania tożsamościami uprzywilejowanymi](pim-security-wizard.md#run-the-wizard) można użyć, aby przejść do strony podobnej do poniższej.

    ![Odnajduj okienko role uprzywilejowane pokazujące, kto ma role uprzywilejowane](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Dla wszystkich administratorów globalnych w organizacji dowiedz się, dlaczego potrzebują tej roli. Na podstawie odczytu poprzedniej dokumentacji, jeśli zadanie danej osoby może być wykonywane przez jedną lub więcej szczegółowych ról administratora, należy usunąć je z roli administratora globalnego i odpowiednio wykonać przydziały w usłudze Azure Active Directory (jako odwołanie: Firma Microsoft ma obecnie tylko około 10 administratorów z rolą administratora globalnego. Dowiedz się więcej [o tym, jak firma Microsoft korzysta z zarządzania tożsamościami uprzywilejowanymi](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. W przypadku wszystkich innych ról usługi Azure AD przejrzyj listę przypisań, zidentyfikuj administratorów, którzy nie potrzebują już tej roli, i usuń je ze swoich przypisań.

Aby zautomatyzować dwa ostatnie kroki, można użyć przeglądów dostępu w zarządzania tożsamościami uprzywilejowanymi. Wykonując kroki [w rozpocząć przegląd dostępu dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi,](pim-how-to-start-security-review.md)można skonfigurować przegląd dostępu dla każdej roli usługi Azure AD, która ma jeden lub więcej członków.

![Tworzenie okienka przeglądu dostępu dla ról usługi Azure AD](./media/pim-deployment-plan/create-access-review.png)

Należy ustawić recenzentów **do Członków (self)**. Spowoduje to wysłanie wiadomości e-mail do wszystkich członków w roli, aby uzyskać je, aby potwierdzić, czy potrzebują dostępu. Należy również włączyć **Wymagaj przyczyny zatwierdzania** w ustawieniach zaawansowanych, aby użytkownicy mogli podać, dlaczego potrzebują roli. Na podstawie tych informacji będzie można usunąć użytkowników z niepotrzebnych ról i delegować bardziej szczegółowe role administratora w przypadku administratorów globalnych.

Przeglądy dostępu opierają się na wiadomościach e-mail, aby powiadomić użytkowników o ich dostępie do ról. Jeśli masz konta uprzywilejowane, które nie mają połączonych wiadomości e-mail, pamiętaj, aby wypełnić dodatkowe pole wiadomości e-mail na tych kontach. Aby uzyskać więcej informacji, zobacz [atrybut proxyAddresses w usłudze Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

W przypadku subskrypcji i zasobów platformy Azure można skonfigurować podobny proces przeglądu programu Access, aby przejrzeć role w każdej subskrypcji lub zasobie. Celem tego procesu jest zminimalizowanie przypisania administratora dostępu właściciela i użytkownika dołączone do każdej subskrypcji lub zasobu, a także usunięcie niepotrzebnych przypisań. Jednak organizacje często delegują takie zadania właścicielowi każdej subskrypcji lub zasobu, ponieważ mają lepsze zrozumienie określonych ról (zwłaszcza ról niestandardowych).

Jeśli jesteś administratorem IT z rolą Administrator globalny, próbując wdrożyć zarządzanie tożsamościami uprzywilejowanymi dla zasobów platformy Azure w organizacji, możesz [podnieść poziom dostępu, aby zarządzać wszystkimi subskrypcjami platformy Azure,](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) aby uzyskać dostęp do każdej subskrypcji. Następnie można znaleźć każdego właściciela subskrypcji i pracować z nimi, aby usunąć niepotrzebne przypisania i zminimalizować przypisanie roli właściciela.

Użytkownicy z rolą Właściciela dla subskrypcji platformy Azure mogą również korzystać z [przeglądów dostępu dla zasobów platformy Azure](pim-resource-roles-start-access-review.md) do inspekcji i usuwania niepotrzebnych przypisań ról podobnych do procesu opisanego wcześniej dla ról usługi Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Określanie, które przypisania ról powinny być chronione przez zarządzanie tożsamościami uprzywilejowanymi

Po oczyszczeniu uprzywilejowanych przypisań ról w organizacji należy zdecydować, które role mają być chronione za pomocą zarządzania tożsamościami uprzywilejowanymi.

Jeśli rola jest chroniona przez zarządzanie tożsamościami uprzywilejowanymi, uprawnieni użytkownicy przypisani do niej muszą podnieść poziom, aby korzystać z uprawnień przyznanych przez tę rolę. Proces podniesienia uprawnień może również obejmować uzyskanie zatwierdzenia, wykonywanie uwierzytelniania wieloskładnikowego i/lub podanie przyczyny aktywacji. Zarządzanie tożsamościami uprzywilejowanymi można również śledzić elewacje za pośrednictwem powiadomień i dzienników zdarzeń zarządzania tożsamościami uprzywilejowanymi i usługi Azure AD.

Wybór ról do ochrony za pomocą uprzywilejowanego zarządzania tożsamościami może być trudny i będzie inny dla każdej organizacji. Ta sekcja zawiera nasze porady dotyczące najlepszych rozwiązań dla ról zasobów usługi Azure AD i platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

Ważne jest, aby nadać priorytet ochronie ról usługi Azure AD, które mają największą liczbę uprawnień. Na podstawie wzorców użycia wśród wszystkich klientów usługi Zarządzania tożsamościami uprzywilejowanymi 10 najlepszych ról usługi Azure AD zarządzanych przez zarządzanie tożsamościami uprzywilejowanymi to:

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
> :heavy_check_mark: Firma Microsoft zaleca zarządzanie **wszystkimi** administratorami globalnymi i administratorami zabezpieczeń przy użyciu uprzywilejowanego zarządzania tożsamościami jako pierwszy krok, ponieważ są to te, które mogą wyrządzić największą szkodę w przypadku naruszenia zabezpieczeń.

Należy wziąć pod uwagę, jakie dane i uprawnienia są najbardziej wrażliwe dla twojej organizacji. Na przykład niektóre organizacje mogą chcieć chronić swoją rolę administratora usługi Power BI lub rolę administratora zespołów przy użyciu uprzywilejowanego zarządzania tożsamościami, ponieważ mają możliwość dostępu do danych i/lub zmiany podstawowych przepływów pracy.

Jeśli istnieją jakieś role z przypisanymi użytkownikami-gośćmi, są one szczególnie narażone na ataki.

> [!TIP]
> :heavy_check_mark: Firma Microsoft zaleca zarządzanie wszystkimi rolami z **użytkownikami-gośćmi** korzystającymi z zarządzania tożsamościami uprzywilejowanymi w celu zmniejszenia ryzyka związanego z kontami użytkowników-gościa, których bezpieczeństwo zostało naruszone.

Role programu Reader, takie jak czytnik katalogów, czytnik centrum wiadomości i czytnik zabezpieczeń, są czasami uważane za mniej ważne w porównaniu do innych ról, ponieważ nie mają uprawnień do zapisu. Jednak widzieliśmy, że niektórzy klienci również chronią te role, ponieważ osoby atakujące, które uzyskały dostęp do tych kont, mogą być w stanie odczytać poufne dane, takie jak dane osobowe. Należy wziąć to pod uwagę przy podejmowaniu decyzji, czy role czytelnika w organizacji muszą być zarządzane przy użyciu zarządzania tożsamościami uprzywilejowanymi.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

Przy podejmowaniu decyzji, które przypisania ról powinny być zarządzane przy użyciu zarządzania tożsamościami uprzywilejowanymi dla zasobów platformy Azure, należy najpierw zidentyfikować subskrypcje/zasoby, które są najważniejsze dla twojej organizacji. Przykładami takich subskrypcji/zasobów są:

- Zasoby, które hostuje najbardziej poufne dane
- Zasoby, od których zależą podstawowe aplikacje skierowane do klienta

Jeśli jesteś administratorem globalnym, który ma problemy z podjęciem decyzji, które subskrypcje/zasoby są najważniejsze, powinieneś skontaktować się z właścicielami subskrypcji w organizacji, aby zebrać listę zasobów zarządzanych przez każdą subskrypcję. Następnie należy pracować z właścicielami subskrypcji, aby pogrupować zasoby na podstawie poziomu ważności w przypadku, gdy są one zagrożone (niski, średni, wysoki). Należy nadać priorytet zarządzaniu zasobami za pomocą uprzywilejowanego zarządzania tożsamościami na podstawie tego poziomu ważności.

> [!TIP]
> :heavy_check_mark: Firma Microsoft zaleca współpracę z **właścicielami** subskrypcji/zasobów usług krytycznych w celu skonfigurowania przepływu pracy zarządzania tożsamościami uprzywilejowanymi dla wszystkich ról w poufnych subskrypcjach/zasobach.

Zarządzanie tożsamościami uprzywilejowanymi dla zasobów platformy Azure obsługuje konta usług powiązanych czasowo. Konta usług należy traktować dokładnie tak samo, jak traktować zwykłe konto użytkownika.

W przypadku subskrypcji/zasobów, które nie są tak krytyczne, nie trzeba skonfigurować zarządzanie tożsamością uprzywilejowaną dla wszystkich ról. Należy jednak nadal chronić role administratora dostępu właściciela i użytkownika za pomocą uprzywilejowanego zarządzania tożsamościami.

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca** zarządzanie rolami właściciela i administratora dostępu użytkowników wszystkich subskrypcji/zasobów przy użyciu zarządzania tożsamościami uprzywilejowanymi.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Zdecyduj, które przypisania ról powinny być stałe lub kwalifikowalne

Po podjęciu decyzji lista ról, które mają być zarządzane przez zarządzanie tożsamością uprzywilejowaną, należy zdecydować, którzy użytkownicy powinni uzyskać rolę kwalifikowalącą się w porównaniu do roli stale aktywnej. Stale aktywne role to normalne role przypisane za pośrednictwem usługi Azure Active Directory i zasobów platformy Azure, podczas gdy kwalifikujące się role można przypisać tylko w dziale Zarządzanie tożsamościami uprzywilejowanymi.

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca, aby** mieć zero stałych przypisań dla ról usługi Azure AD i ról zasobów platformy Azure innych niż zalecane dwa konta [dostępu awaryjnego typu break-glass,](../users-groups-roles/directory-emergency-access.md)które powinny mieć stałą rolę administratora globalnego.

Mimo że zalecamy zerowy stały administrator, czasami organizacjom trudno jest to od razu osiągnąć. Oto rzeczy, które należy wziąć pod uwagę przy podejmowaniu tej decyzji:

- Częstotliwość podniesienia — jeśli użytkownik potrzebuje tylko przypisania uprzywilejowanego raz, nie powinien mieć stałego przypisania. Z drugiej strony, jeśli użytkownik potrzebuje roli dla ich codziennej pracy i przy użyciu uprzywilejowanego zarządzania tożsamościami znacznie zmniejszyć ich produktywność, mogą być brane pod uwagę dla stałej roli.
- Sprawy specyficzne dla organizacji — jeśli osoba, która otrzymuje kwalifikującą się rolę, pochodzi z bardzo odległego zespołu lub wysokiej rangi kadry kierowniczej do tego stopnia, że komunikowanie się i egzekwowanie procesu elewacji jest trudne, można je rozważyć dla stałej roli.

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca konfigurowanie** cyklicznych przeglądów dostępu dla użytkowników ze stałymi przypisaniami ról (jeśli masz jakieś). Dowiedz się więcej o przeglądzie dostępu cyklicznego w ostatniej sekcji tego planu wdrażania

### <a name="draft-your-privileged-identity-management-settings"></a>Opracowywanie ustawień zarządzania tożsamościami uprzywilejowanymi

Przed zaimplementowanie rozwiązania do zarządzania tożsamościami uprzywilejowanymi dobrym rozwiązaniem jest opracowanie ustawień zarządzania tożsamościami uprzywilejowanymi dla każdej uprzywilejowanej roli używanej przez organizację. W tej sekcji znajdują się przykłady ustawień zarządzania tożsamościami uprzywilejowanymi dla określonych ról (są one tylko w celach informacyjnych i mogą być różne w organizacji). Każde z tych ustawień jest szczegółowo wyjaśnione za pomocą zaleceń firmy Microsoft po tabelach.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Ustawienia zarządzania tożsamościami uprzywilejowanymi dla ról usługi Azure AD

| Rola | Wymaganie usługi MFA | Powiadomienie | Bilet na incydent | Wymagaj zatwierdzenia | Approver | Czas trwania aktywacji | Stały administrator |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrator globalny | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni administratorzy globalni | 1 godzina | Konta dostępu awaryjnego |
| Exchange Administrator | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Brak | 2 godz. | Brak |
| Administrator działu pomocy technicznej | :x: | :x: | :heavy_check_mark: | :x: | Brak | 8 godz. | Brak |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Ustawienia zarządzania tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure

| Rola | Wymaganie usługi MFA | Powiadomienie | Wymagaj zatwierdzenia | Approver | Czas trwania aktywacji | Aktywny administrator | Aktywne wygasanie | Kwalifikujący się okres ważności |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Właściciel subskrypcji krytycznych | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni właściciele abonamentu | 1 godzina | Brak | Nie dotyczy | 3 miesiące |
| Administrator dostępu użytkownika mniej krytycznych subskrypcji | :heavy_check_mark: | :heavy_check_mark: | :x: | Brak | 1 godzina | Brak | Nie dotyczy | 3 miesiące |
| Współautor maszyny wirtualnej | :x: | :heavy_check_mark: | :x: | Brak | 3 godz. | Brak | Nie dotyczy | 6 miesięcy |

W poniższej tabeli opisano każde z ustawień.

| Ustawienie | Opis |
| --- | --- |
| Rola | Nazwa roli, dla której definiujesz ustawienia. |
| Wymaganie usługi MFA | Czy uprawniony użytkownik musi wykonać uwierzytelnianie wieloskładnikowe przed aktywowaniem roli.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca wymuszanie** funkcji mfa dla wszystkich ról administratora, zwłaszcza jeśli role mają użytkowników-gości. |
| Powiadomienie | Jeśli ustawiona wartość true, administrator globalny, administrator ról uprzywilejowanych i administrator zabezpieczeń w organizacji otrzymają powiadomienie e-mail, gdy kwalifikujący się użytkownik aktywuje tę rolę.<br/><br/>**Uwaga:** Niektóre organizacje nie mają adresu e-mail powiązanego z kontami administratorów, aby otrzymywać te powiadomienia e-mail, należy ustawić alternatywny adres e-mail, aby administratorzy otrzymywali te wiadomości e-mail. |
| Bilet na incydent | Czy uprawniony użytkownik musi zarejestrować numer biletu zdarzenia podczas aktywowania swojej roli. To ustawienie pomaga organizacji zidentyfikować każdą aktywację przy pomocy wewnętrznego numeru zdarzenia w celu ograniczenia niechcianych aktywacji.<br/><br/> :heavy_check_mark: Firma **Microsoft zaleca** korzystanie z numerów biletów na incydenty w celu powiązania zarządzania tożsamościami uprzywilejowanymi z systemem wewnętrznym. Jest to szczególnie przydatne dla osób zatwierdzających, które potrzebują kontekstu do aktywacji. |
| Wymagaj zatwierdzenia | Czy uprawniony użytkownik musi uzyskać zatwierdzenie, aby aktywować rolę.<br/><br/> :heavy_check_mark: Firma **Microsoft zaleca** skonfigurowanie zatwierdzania ról z największą liczesz uprawnieniami. Na podstawie wzorców użycia wszystkich klientów zarządzania tożsamościami uprzywilejowanymi, administrator globalny, administrator użytkownika, administrator programu Exchange, administrator zabezpieczeń i administrator haseł są najczęstszymi rolami z konfiguracją zatwierdzania. |
| Approver | Jeśli wymagana jest zgoda na aktywowanie kwalifikującej się roli, wyekw. Domyślnie zarządzanie tożsamościami uprzywilejowanymi ustawia osobę zatwierdzającą na wszystkich użytkowników, którzy są administratorem ról uprzywilejowanych, niezależnie od tego, czy są oni stali, czy uprawnieni.<br/><br/>**Uwaga:** Jeśli użytkownik kwalifikuje się zarówno do roli usługi Azure AD, jak i osoby zatwierdzającej rolę, nie będzie mógł sami zatwierdzić.<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca wybranie** osób zatwierdzających jako tych, którzy mają największą wiedzę na temat konkretnej roli i jej częstych użytkowników, a nie administratora globalnego. |
| Czas trwania aktywacji | Czas, przez jaki użytkownik zostanie aktywowany w roli, zanim wygaśnie. |
| Stały administrator | Lista użytkowników, którzy będą stałym administratorem roli (nigdy nie trzeba aktywować).<br/><br/> :heavy_check_mark: **Firma Microsoft zaleca, aby** mieć zerowy stały administrator dla wszystkich ról z wyjątkiem administratorów globalnych. Przeczytaj więcej na ten temat, kto powinien się kwalifikować i kto powinien być stale aktywny w tej sekcji tego planu. |
| Aktywny administrator | W przypadku zasobów platformy Azure aktywny administrator jest listą użytkowników, którzy nigdy nie będą musieli aktywować, aby użyć tej roli. Nie jest to określane jako stały administrator, jak w rolach usługi Azure AD, ponieważ można ustawić czas wygaśnięcia, gdy użytkownik straci tę rolę. |
| Aktywne wygasanie | Aktywne przypisanie roli dla ról zasobów platformy Azure wygasa po tym skonfigurowanym okresie czasu. Możesz wybrać jeden z 15 dni, 1 miesiąc, 3 miesiące, 6 miesięcy, 1 rok lub na stałe aktywny. |
| Kwalifikujący się okres ważności | Przypisanie kwalifikującego się zadania dla ról zasobów platformy Azure wygasa po tym skonfigurowanym okresie. Możesz wybrać jeden z 15 dni, 1 miesiąc, 3 miesiące, 6 miesięcy, 1 rok lub trwale kwalifikujących się. |

## <a name="implement-your-solution"></a>Zaimplementuj swoje rozwiązanie

Podstawą prawidłowego planowania jest podstawa, na której można pomyślnie wdrożyć aplikację za pomocą usługi Azure Active Directory.  Zapewnia inteligentne zabezpieczenia i integrację, która upraszcza dołączanie przy jednoczesnym skróceniu czasu pomyślnego wdrożenia.  Ta kombinacja zapewnia, że aplikacja jest zintegrowana z łatwością, jednocześnie ograniczając czas przestoju dla użytkowników końcowych.

### <a name="identify-test-users"></a>Identyfikowanie użytkowników testowych

Ta sekcja służy do identyfikowania zestawu użytkowników i lub grup użytkowników, aby sprawdzić poprawność implementacji. Na podstawie ustawień wybranych w sekcji planowania zidentyfikuj użytkowników, których chcesz przetestować dla każdej roli.

> [!TIP]
> :heavy_check_mark: **Firma Microsoft zaleca,** aby właściciele usług każdej roli usługi Azure AD byli użytkownikami testowymi, aby mogli zapoznać się z procesem i stać się wewnętrznym orędownikiem wdrożenia.

W tej tabeli zidentyfikuj użytkowników testowych, którzy sprawdzą, czy ustawienia dla każdej roli działają.

| Nazwa roli | Użytkownicy testowi |
| --- | --- |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy, aby przetestować rolę&gt; |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy, aby przetestować rolę&gt; |

### <a name="test-implementation"></a>Implementacja testu

Teraz, gdy zostały zidentyfikowane użytkowników testowych, użyj tego kroku, aby skonfigurować zarządzanie tożsamościami uprzywilejowanymi dla użytkowników testowych. Jeśli organizacja chce włączyć przepływ pracy zarządzania tożsamościami uprzywilejowanymi do własnej aplikacji wewnętrznej zamiast korzystać z uprzywilejowanego zarządzania tożsamościami w witrynie Azure portal, wszystkie operacje w zarządzania tożsamościami uprzywilejowanymi są również obsługiwane za pośrednictwem naszego [interfejsu API wykresu.](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurowanie zarządzania tożsamościami uprzywilejowanymi dla ról usługi Azure AD

1. [Skonfiguruj ustawienia roli usługi Azure AD](pim-how-to-change-default-settings.md) na podstawie zaplanowanych.

1. Przejdź do **ról usługi Azure AD**, kliknij pozycję **Role**, a następnie wybierz właśnie skonfigurowane.

1. W przypadku grupy użytkowników testowych, jeśli są już stałym administratorem, możesz je zakwalifikować, wyszukując ich i konwertując je ze stałego na kwalifikujący się, klikając trzy kropki w wierszu. Jeśli nie mają jeszcze przypisania roli, można [dokonać nowego kwalifikującego się zadania.](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)

1. Powtórz kroki 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im łącze dotyczące [sposobu aktywowania roli usługi Azure AD.](pim-how-to-activate-role.md)

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Konfigurowanie zarządzania tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure

1. [Skonfiguruj ustawienia roli zasobu platformy Azure](pim-resource-roles-configure-role-settings.md) dla roli wewnątrz subskrypcji lub zasobu, który chcesz przetestować.

1. Przejdź do **zasobów platformy Azure** dla tej subskrypcji i kliknij pozycję **Role**, wybierz właśnie skonfigurowane rolę.

1. Dla grupy użytkowników testowych, jeśli są już aktywnym administratorem, można je zakwalifikować, wyszukując je i [zaktualizować ich przypisanie roli](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Jeśli nie mają jeszcze tej roli, można [przypisać nową rolę](pim-resource-roles-assign-roles.md#assign-a-role).

1. Powtórz kroki 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im łącze dotyczące [sposobu aktywowania roli zasobów platformy Azure.](pim-resource-roles-activate-your-roles.md)

Na tym etapie należy sprawdzić, czy cała konfiguracja skonfigurowana dla ról działa poprawnie. Poniższa tabela służy do dokumentowania testów. Należy również użyć tego etapu, aby zoptymalizować komunikację z zainteresowanych użytkowników.

| Rola | Oczekiwane zachowanie podczas aktywacji | Rzeczywiste wyniki |
| --- | --- | --- |
| Administrator globalny | (1) Wymagaj pomocy makrofinansowej<br/>(2) Wymagać zatwierdzenia<br/>(3) Osoba zatwierdzająca otrzymuje powiadomienie i może zatwierdzić<br/>(4) Rola wygasa po upływie ustawionego czasu |  |
| Właściciel subskrypcji *X* | (1) Wymagaj pomocy makrofinansowej<br/>(2) kwalifikujące się przypisanie wygasa po skonfigurowanym okresie |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Komunikowanie zarządzania tożsamościami uprzywilejowanymi do zainteresowanych stron, których dotyczy problem

Wdrażanie zarządzania tożsamościami uprzywilejowanymi wprowadzi dodatkowe kroki dla użytkowników ról uprzywilejowanych. Chociaż zarządzanie tożsamością uprzywilejowaną znacznie zmniejsza problemy z zabezpieczeniami związane z tożsamościami uprzywilejowanymi, zmiana musi być skutecznie komunikowana przed wdrożeniem w całej dzierżawie. W zależności od liczby administratorów, na które wpłynęło to, organizacje często decydują się na utworzenie dokumentu wewnętrznego, filmu lub wiadomości e-mail na temat zmiany. Często zawarte w tych komunikatach obejmują:

- Co to jest PIM
- Jakie są korzyści dla organizacji
- Kogo to dotyczy
- Kiedy pim zostanie wdrożony
- Jakie dodatkowe kroki będą wymagane, aby użytkownicy aktywowali swoją rolę
    - Należy wysłać linki do naszej dokumentacji:
    - [Aktywowanie ról usługi Azure AD](pim-how-to-activate-role.md)
    - [Aktywowanie ról zasobów platformy Azure](pim-resource-roles-activate-your-roles.md)
- Informacje kontaktowe lub łącze do działu pomocy technicznej w przypadku jakichkolwiek problemów związanych z usługią PIM

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca** skonfigurowanie czasu pracy w pomocy technicznej/zespołowi pomocy technicznej w celu przejścia przez przepływ pracy Zarządzanie tożsamościami uprzywilejowanymi (jeśli organizacja ma wewnętrzny zespół pomocy technicznej IT). Dostarczenie im odpowiednich dokumentów oraz informacji kontaktowych.

### <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Po zakończeniu testowania i pomyślnym, przenieś zarządzanie tożsamościami uprzywilejowanymi do produkcji, powtarzając wszystkie kroki w fazach testowania dla wszystkich użytkowników każdej roli zdefiniowanej w konfiguracji zarządzania tożsamościami uprzywilejowanymi. W przypadku ról zarządzania tożsamościami uprzywilejowanymi dla usług Azure AD organizacje często testują i wdrażają zarządzanie tożsamościami uprzywilejowanymi dla administratorów globalnych przed przetestowaniem i wdrożeniem zarządzania tożsamościami uprzywilejowanymi dla innych ról. Tymczasem dla zasobów platformy Azure organizacje zwykle testują i wdrażają uprzywilejowane zarządzanie tożsamościami po jednej subskrypcji platformy Azure naraz.

### <a name="in-the-case-a-rollback-is-needed"></a>W przypadku, gdy konieczne jest wycofanie

Jeśli zarządzanie tożsamością uprzywilejowaną nie może działać zgodnie z potrzebami w środowisku produkcyjnym, następujące kroki wycofywania mogą pomóc przywrócić do znanego dobrego stanu przed skonfigurowaniem zarządzania tożsamościami uprzywilejowanymi:

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.
1. Kliknij pozycję **Role usługi Azure AD,** a następnie kliknij pozycję **Role**.
1. Dla każdej skonfigurowanej roli kliknij wielokropek (**...**) dla wszystkich użytkowników z kwalifikującym się przypisaniem.
1. Kliknij opcję **Pozycję Na stałe,** aby przydział roli stał się trwały.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.
1. Kliknij **pozycję Zasoby platformy Azure,** a następnie kliknij subskrypcję lub zasób, który chcesz wycofać.
1. Kliknij pozycję **Role**.
1. Dla każdej skonfigurowanej roli kliknij wielokropek (**...**) dla wszystkich użytkowników z kwalifikującym się przypisaniem.
1. Kliknij opcję **Pozycję Na stałe,** aby przydział roli stał się trwały.

## <a name="next-steps-after-deploying"></a>Kolejne kroki po wdrożeniu

Pomyślne wdrożenie uprzywilejowanego zarządzania tożsamościami w produkcji jest znaczącym krokiem naprzód pod względem zabezpieczania uprzywilejowanych tożsamości organizacji. Wraz z wdrożeniem zarządzania tożsamościami uprzywilejowanymi są dostępne dodatkowe funkcje zarządzania tożsamościami uprzywilejowanymi, których należy używać ze względów bezpieczeństwa i zgodności.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Ochrona uprzywilejowanego dostępu za pomocą alertów zarządzania tożsamościami uprzywilejowanych

Należy korzystać z wbudowanej funkcji alertów uprzywilejowanych zarządzania tożsamościami, aby lepiej chronić dzierżawę. Aby uzyskać więcej informacji, zobacz [alerty zabezpieczeń](pim-how-to-configure-security-alerts.md#security-alerts). Alerty te obejmują: administratorzy nie używają ról uprzywilejowanych, role są przypisywane poza zarządzaniem tożsamościami uprzywilejowanymi, role są aktywowane zbyt często i więcej. Aby w pełni chronić organizację, należy regularnie przeglądać listę alertów i rozwiązywać problemy. Alerty można wyświetlać i naprawiać w następujący sposób:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.
1. Kliknij pozycję **Role usługi Azure AD,** a następnie kliknij pozycję **Alerty**.

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca natychmiastowe zajęcie** się wszystkimi alertami oznaczonymi wysoką ważnością. W przypadku alertów o średniej i niskiej ważności należy być na bieżąco i wprowadzać zmiany, jeśli uważasz, że istnieje zagrożenie bezpieczeństwa.

Jeśli którykolwiek z określonych alertów nie są przydatne lub nie ma zastosowania do organizacji, zawsze można odrzucić alert na stronie alertów. Zawsze można przywrócić to zwolnienie później na stronie ustawień usługi Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Konfigurowanie przeglądów dostępu cyklicznego w celu regularnego inspekcji uprzywilejowanych tożsamości organizacji

Przeglądy dostępu to najlepszy sposób na pytanie użytkowników przypisanych z uprzywilejowanymi rolami lub określonych recenzentów, czy każdy użytkownik potrzebuje tożsamości uprzywilejowanej. Recenzje dostępu są świetne, jeśli chcesz zmniejszyć powierzchnię ataku i zachować zgodność. Aby uzyskać więcej informacji na temat uruchamiania przeglądu dostępu, zobacz [Przeglądy dostępu ról usługi Azure AD i](pim-how-to-start-security-review.md) dostęp do ról zasobów platformy [Azure.](pim-resource-roles-start-access-review.md) W przypadku niektórych organizacji przeprowadzanie okresowego przeglądu dostępu jest wymagane, aby zachować zgodność z przepisami i regulacjami, podczas gdy w przypadku innych, przegląd dostępu jest najlepszym sposobem wymuszania głównego zobowiązanego o najmniejszych uprawnieniach w całej organizacji.

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca** skonfigurowanie kwartalnych przeglądów dostępu dla wszystkich ról zasobów usługi Azure AD i platformy Azure.

W większości przypadków recenzent ról usługi Azure AD jest samych użytkowników, podczas gdy recenzent ról zasobów platformy Azure jest właścicielem subskrypcji, w której znajduje się rola. Jednak często ma to miejsce w przypadku, gdy firmy mają uprzywilejowane konta, które nie są powiązane z adresem e-mail konkretnej osoby. W takich przypadkach nikt nie czyta i nie przegląda dostępu.

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca** dodanie dodatkowego adresu e-mail dla wszystkich kont z przypisaniami ról uprzywilejowanych, które nie są połączone z regularnie sprawdzanym adresem e-mail

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Wykorzystaj w pełni dziennik inspekcji, aby poprawić bezpieczeństwo i zgodność

Dziennik inspekcji to miejsce, w którym można być na bieżąco i być zgodnym z przepisami. Zarządzanie tożsamościami uprzywilejowanymi przechowuje obecnie 30-dniową historię całej historii organizacji w dzienniku inspekcji, w tym:

- Aktywacja/dezaktywacja kwalifikujących się ról
- Działania związane z przypisywaniem ról wewnątrz i na zewnątrz zarządzania tożsamościami uprzywilejowanymi
- Zmiany w ustawieniach roli
- Żądanie/zatwierdzanie/odmawianie działań dotyczących aktywacji roli przy konfigurowaniu zatwierdzania
- Aktualizacja do alertów

Dostęp do tych dzienników inspekcji można uzyskać, jeśli jesteś administratorem globalnym lub administratorem ról uprzywilejowanych. Aby uzyskać więcej informacji, zobacz [historię inspekcji ról usługi Azure AD](pim-how-to-use-audit-log.md) i historii inspekcji ról zasobów platformy [Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca,** aby co najmniej jeden administrator co tydzień przeglądał wszystkie zdarzenia inspekcji i co miesiąc eksportować zdarzenia inspekcji.

Jeśli chcesz automatycznie przechowywać zdarzenia inspekcji przez dłuższy okres czasu, dziennik inspekcji usługi Uprzywilejowane zarządzanie tożsamościami jest automatycznie synchronizowany z [dziennikami inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: Firma **Microsoft zaleca** skonfigurowanie [monitorowania dzienników platformy Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) w celu archiwizowania zdarzeń inspekcji na koncie magazynu platformy Azure w celu zapewnienia bezpieczeństwa i zgodności.
