---
title: Najlepsze rozwiązania dotyczące zabezpieczania dostępu administracyjnego w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Upewnij się, że w organizacji administrator i dostępu do konta z uprawnieniami administracyjnymi są bezpieczne. Dla architektów systemów i specjalistów IT, którzy Konfigurowanie usługi Azure AD Azure i usługi Online firmy Microsoft.
services: active-directory
keywords: ''
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 45a0cf104df6d29515ed1a94c78c8cc1e42a2223
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpieczanie uprzywilejowanego dostępu w przypadku wdrożeń hybrydowymi i w chmurze w usłudze Azure AD

Zabezpieczenia większość lub wszystkie zasoby biznesowych w organizacji nowoczesnych zależy od integralności uprzywilejowane konta, które Administruj i Zarządzaj systemów informatycznych. Złośliwych osób często tym osobom atakującym przez docelowego kont administratora oraz inne elementy uprzywilejowanego dostępu, aby szybko uzyskać dostęp do poufnych danych i systemami za pomocą ataków kradzieży poświadczeń. W chmurze usługi, zapobiegania i odpowiedzi są wspólnego obowiązków dostawcy usług w chmurze i klienta. Aby uzyskać więcej informacji o najnowszych zagrożeniach punktów końcowych i w chmurze, zobacz [raportów analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/sir/default.aspx). Ten artykuł ułatwia opracowania planu kierunku zamykanie przerw między bieżącym planów i wskazówek opisanych tutaj.

> [!NOTE] 
> Firma Microsoft dokłada starań, aby najwyższy poziom zaufania, przezroczystość zgodność standardów i zgodności z przepisami. Więcej informacji na temat jak zespół firmy Microsoft w odpowiedzi na zdarzenia globalne Skoryguj wyniki ataków na usługi w chmurze i jak zabezpieczeń wbudowane produktów firmy Microsoft i usługi w chmurze w [Microsoft Trust Center — zabezpieczeń](https://www.microsoft.com/en-us/trustcenter/security)i celów zgodności firmy Microsoft w [Microsoft Trust Center — zgodność](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
W przypadku większości organizacji bezpieczeństwa zasobów biznesowych zależy od integralności uprzywilejowane konta, które Administruj i Zarządzaj systemów informatycznych. Osoby atakujące przez skupić się na uprzywilejowany dostęp do systemów infrastruktury (na przykład usługi Active Directory i Azure Active Directory) do uzyskania dostępu do poufnych danych w organizacji. 

Tradycyjne metody skupić się na zabezpieczanie punktów wejścia i wyjścia sieci jako obwodowej głównej zabezpieczeń są mniej skuteczne z powodu wzrost użycia aplikacji SaaS i osobistych urządzeń w Internecie. Fizyczne zastąpienie dla sieci obwodowej zabezpieczeń w przedsiębiorstwie nowoczesnych złożonych jest kontrolki uwierzytelniania i autoryzacji w warstwie tożsamości organizacji. 

Uprzywilejowane konta z uprawnieniami administracyjnymi skutecznie mają kontrolę nad tego nowego "zabezpieczeń obwodu." Warto chronić uprzywilejowanego dostępu, niezależnie od tego, czy środowisko jest lokalnie, chmury lub hybrydowe lokalnej i usług hostowanych w chmurze. Ochrona dostępu administracyjnego na podstawie atakujący wymaga podjęcia odpowiednich pełne i przemyślane podejścia do izolowania systemów organizacji przed zagrożeniami. 

Zabezpieczanie uprzywilejowanego dostępu wymaga wprowadzenia zmian
* Procesy, praktyk administracyjnych i zarządzania wiedzą
* Techniczne składniki, takie jak host zabezpieczenia, zabezpieczenia konta i zarządzania tożsamościami

Ten dokument koncentruje się przede wszystkim na tworzenie planu do zabezpieczania tożsamości i dostępu, która zarządza lub w usłudze Azure AD, Microsoft Azure, Office 365 i innych usług chmury. W przypadku organizacji, które mają lokalnego konta z uprawnieniami administracyjnymi, zobacz wskazówki dotyczące lokalne i hybrydowe uprzywilejowany dostęp zarządzać za pomocą usługi Active Directory w [zabezpieczanie uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Wskazówki zawarte w tym artykule odnosi się głównie do funkcji usługi Azure Active Directory, które znajdują się w planie usługi Azure Active Directory Premium P1 i P2. Azure Active Directory Premium P2 znajduje się w pakiet EMS E5 i pakiet E5 365 firmy Microsoft. W tych wskazówkach przyjęto założenie, że Twoja organizacja już ma licencji usługi Azure AD Premium P2 zakupionych dla użytkowników. Jeśli nie masz tych licencji, niektóre wskazówki nie mogą być stosowane dla Twojej organizacji. Ponadto w tym artykule termin administratora globalnego (lub administrator globalny) jest synonimem "firmy administrator" ani "administratora dzierżawy".

## <a name="develop-a-roadmap"></a>Opracuj plan 

Firma Microsoft zaleca opracowanie i wykonaj plan do zabezpieczania uprzywilejowanego dostępu przed atakami ataków. Zawsze można dostosować mapy, aby zmieścił się w istniejących możliwości, a określone wymagania w organizacji. Każdego etapu przewodnik powinna podnieść koszty i trudności dla atakujący na ataki uprzywilejowanego dostępu lokalnego, chmur i zasoby hybrydowego. Firma Microsoft zaleca cztery następujące etapy plan: to zalecane harmonogramy plan najszybszy implementacji i najbardziej efektywne, na podstawie doświadczeń firmy Microsoft z implementacją ataku przez zdarzenia i odpowiedzi. Przybliżony są osi czasu dla tego planu.

![Etapy plan liniami czasu](./media/admin-roles-best-practices/roadmap-timeline.png)

* Etap 1 (24 48 godziny): elementów krytycznych, które firma Microsoft zaleca wykonaj natychmiast

* Etap 2 (2 – 4 tygodnie): ograniczenia techniki ataku najczęściej używanych

* Etap 3 (1-3 miesiące): tworzenie widoczność i kompilacji pełną kontrolę nad aktywność administratora

* Etap 4 (sześciu miesięcy i nowszych): kontynuować tworzenie zabezpieczenia w celu dalszego ograniczenia funkcjonalności platformy zabezpieczeń

Ta struktura plan zaprojektowano w celu optymalnego wykorzystania technologii firmy Microsoft, które mogą zostały już wdrożone. Można również korzystać z technologii kluczy zabezpieczeń obecne i przyszłe i integrowanie narzędzi zabezpieczeń od innych dostawców, które zostały już wdrożone, lub rozważane jest wdrożenie. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Etap 1: Elementów krytycznych, które firma Microsoft zaleca wykonaj natychmiast

![Etap 1](./media/admin-roles-best-practices/stage-one.png)

Etap 1 przewodnik koncentruje się na krytyczny zadania, które są szybka i łatwa do wdrożenia. Zaleca się, czy te elementy kilka od razu w pierwszym 24-48 godzin zapewnienie podstawowy poziom bezpiecznego uprzywilejowanego dostępu. Ten etap plan zabezpieczone uprzywilejowanego dostępu zawiera następujące akcje:

### <a name="general-preparation"></a>Przygotowanie

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Włączanie usługi Azure AD Privileged Identity Management

Jeśli Azure AD Privileged Identity Management (PIM) nie ma już włączony, zrób to w dzierżawie produkcji. Po włączeniu Privileged Identity Management, otrzymasz powiadomienie e-mail wiadomości do uprzywilejowanego dostępu zmiany roli. Te powiadomienia stanowić wczesne ostrzeżenie po dodaniu dodatkowych użytkowników do ról uprawnieniach w katalogu.

Azure AD Privileged Identity Management znajduje się w usłudze Azure AD Premium P2 lub EMS E5. Te rozwiązania pomóc w ochronie dostępu do aplikacji i zasobów, między środowiskiem lokalnym i w chmurze. Jeśli nie jest jeszcze P2 Azure AD Premium lub pakietu EMS E5 i chcesz ocenić więcej funkcji, do którego odwołuje się ten plan, należy zarejestrować się w celu [Enterprise Mobility + Security wolne 90-dniowa wersja próbna](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Korzystanie z tych prób licencji Azure AD Privileged Identity Management i Azure AD Identity Protection, aby monitorować aktywność przy użyciu usługi Azure AD zaawansowane raportowanie zabezpieczeń, inspekcji i alerty.

Po wykonaniu włączono Azure AD Privileged Identity Management:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) przy użyciu konta, które jest administratorem globalnym dzierżawy produkcji.

2. Aby wybrać dzierżawy, w której chcesz użyć Privileged Identity Management, wybierz nazwę użytkownika w prawym górnym rogu portalu Azure.

3. Wybierz **wszystkie usługi** i filtrować listy dla **Azure AD Privileged Identity Management**.

4. Otwórz Privileged Identity Management z **wszystkie usługi** listę i przypiąć go do pulpitu nawigacyjnego.

Pierwszą osobą do użycia w dzierżawie usługi Azure AD Privileged Identity Management jest automatycznie przypisywana **administrator zabezpieczeń** i **administrator ról uprzywilejowanych** ról w dzierżawie. Tylko administrator ról uprzywilejowanych można zarządzać przypisań ról katalogu usługi Azure AD użytkowników. Ponadto po dodaniu usługi Azure AD Privileged Identity Management, są wyświetlane Kreator zabezpieczeń, który przeprowadzi Cię przez środowisko początkowego odnajdywania i przypisania. Można zakończyć działanie kreatora bez wprowadzania dodatkowych zmian w tej chwili. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identyfikowanie i kategoryzowanie konta, które są wysoce uprzywilejowane ról 

Po włączeniu usługi Azure AD Privileged Identity Management, Wyświetl użytkowników, którzy są w administratora globalnego katalogu ról, administrator ról uprzywilejowanych, administrator usługi Exchange Online i administratora usługi SharePoint Online. Jeśli nie masz usługi Azure AD PIM w dzierżawie, możesz użyć [interfejs API środowiska PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Rozpocznij z rolą administratora globalnego, jak ta rola jest ogólny: użytkownika, który jest przypisany do tej roli Administrator ma te same uprawnienia we wszystkich usługach chmury, dla których zostały zasubskrybowane przez organizację, niezależnie od tego, czy są przypisane tej roli w portalu usługi Office 365 , Azure, portalu lub przy użyciu modułu Azure AD PowerShell firmy Microsoft. 

Usuń dowolne konta, które nie są już potrzebne w tych ról, a skategoryzować pozostałe konta, które są przypisane do ról administratora:

* Indywidualnie przypisane do użytkowników administracyjnych i może również służyć do celów innych niż administracyjne (na przykład osobiste wiadomości e-mail)
* Indywidualnie przypisane do użytkowników administracyjnych i przeznaczone tylko do celów administracyjnych
* Współużytkowany przez wielu użytkowników
* Dla scenariuszy dostępu awaryjnego szkła podziału
* Dla zautomatyzowanych skryptów
* Dla użytkowników zewnętrznych

#### <a name="define-at-least-two-emergency-access-accounts"></a>Zdefiniuj przynajmniej dwóch kont dostęp awaryjny 

Upewnij się, że użytkownik nie może korzystać w sytuacji, gdy ich może być przypadkowo zablokowana poza administracji dzierżawy usługi Azure AD z powodu niemożności zalogowanie się lub uaktywnić konto istniejące danego użytkownika z uprawnieniami administratora. Na przykład jeśli organizacja jest Sfederowane do dostawcy tożsamości lokalnych, tego dostawcy tożsamości można niedostępny, użytkownicy nie można zarejestrować w lokalnym. Można zmniejszyć skuteczność przypadkowemu braku dostępu administracyjnego przez przechowywanie dwóch lub więcej kont dostęp awaryjny w dzierżawie.

Dostęp awaryjny kont pomagające organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory. Te konta są wysoce uprzywilejowane i nie są przypisane do konkretnych osób. Dostęp awaryjny konta są ograniczone do awaryjnego dla scenariuszy "awaryjne, gdy nie można użyć normalnego konta z uprawnieniami administracyjnymi. Organizacje muszą zapewnić w celu kontrolowania i ograniczania się tylko do tego czasu, dla których konieczne jest użycie kont awaryjnych. 

Ocena konta, które są przypisane lub kwalifikuje się do roli administratora globalnego. Jeśli nie tylko w chmurze kont przy użyciu narzędzia *. onmicrosoft.com domeny (przeznaczonych do dostęp awaryjny "awaryjne"), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostęp awaryjny konta z uprawnieniami administracyjnymi w usłudze Azure AD](active-directory-admin-manage-emergency-access-accounts.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Włącz uwierzytelnianie wieloskładnikowe i Zarejestruj wszystkie inne konta administratora niefederacyjnych pojedynczego użytkownika uprawnieniach 

Azure Multi-Factor Authentication (MFA) jest wymagany co logowania dla wszystkich poszczególnych użytkowników, którzy są trwale przypisana do co najmniej jednej z ról administratora usługi Azure AD: administrator globalny, administrator ról uprzywilejowanych, administrator usługi Exchange Online i SharePoint Administrator w trybie online. Aby włączyć za pomocą przewodnika [Multi-Factor Authentication (MFA) dla kont administratorów](authentication/howto-mfa-userstates.md) i upewnij się, że zarejestrowano tych użytkowników, na [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Więcej informacji można znaleźć w kroki 2 i 3 przewodnika [ochrony dostępu do danych i usług w usłudze Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etapu 2: Techniki najczęściej używanych ataku ograniczyć

![Etap 2](./media/admin-roles-best-practices/stage-two.png)

Etap 2 przewodnik koncentruje się na zmniejszenia technik najczęściej używanych ataku kradzieży poświadczeń i nadużycia i jest przeznaczony do implementacji w około 2 – 4 tygodnie. Ten etap plan zabezpieczone uprzywilejowanego dostępu zawiera następujące czynności.

### <a name="general-preparation"></a>Przygotowanie

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Przeprowadzenie spisu, usług, właściciele i Administratorzy

Ze wzrostem bring-your własne urządzenie (BYOD) i pracy z głównej, zasad i rozwój łączności bezprzewodowej w firmach jest krytyczny, monitorować kto nawiązuje połączenie z siecią. Audyt efektywnym elementem systemu zabezpieczeń często ujawnia urządzeń, aplikacji i programów w sieci, które nie są obsługiwane przez IT i w związku z tym potencjalnie nie bezpieczny. Aby uzyskać więcej informacji, zobacz [zabezpieczeń platformy Azure, zarządzanie i monitorowanie omówienie](../security/security-management-and-monitoring-overview.md). Upewnij się, Uwzględnij wszystkie z następujących czynności w procesie spisu. 

* Określ użytkowników, którzy mają ról administracyjnych i usługi, w którym można zarządzać.
* Użyj usługi Azure AD PIM, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do usługi Azure AD, w tym dodatkowe role poza tymi, które się na liście etap 1.
* Poza role zdefiniowane w usłudze Azure AD Office 365 zawiera zestaw ról administratora, które można przypisać do użytkowników w organizacji. Każda rola administratora mapuje do typowych funkcji biznesowych i zapewnia osób w organizacji uprawnienia do wykonywania określonych zadań w Centrum administracyjnym usługi Office 365. Użyj Centrum administracyjnego usługi Office, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do usługi Office 365, w tym za pomocą ról, które nie są zarządzane w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) i [najlepsze rozwiązania dotyczące usługi Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Wykonaj spisu w innych usług, które organizacji zależy, takich jak Azure, Intune lub Dynamics 365.
* Upewnij się, że kont administratorów (konta, które są używane do celów administracji, nie tylko konta codziennych użytkowników) mają adresy e-mail dołączony do ich pracy i zarejestrowany dla usługi Azure MFA lub użyj MFA lokalnymi.
* Poproś użytkowników o ich biznesowego wyjaśnienia dostępu administracyjnego.
* Dla tych osób i usług, które nie jest on potrzebny, Usuń dostępu z uprawnieniami administratora.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Określenie konta Microsoft w ról administracyjnych, które muszą zostać przełączone do konta służbowego. 

Czasami początkowej Administratorzy globalni dla organizacji, ponownie użyć swoich istniejących poświadczeń konta Microsoft, po ich rozpoczął się przy użyciu usługi Azure AD. Te konta Microsoft, powinien zostać zastąpiony indywidualnych kont oparte na chmurze, jak i zsynchronizowane. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Upewnij się, oddzielnych kont użytkowników i przekazywanie dla konta administratora globalnego wiadomości 

Osobiste konta e-mail są regularnie phished przez osoby atakujące przez osobistych adresów e-mail, nie powinna mieć konta administratora globalnego. Aby oddzielić ryzyka internet (wyłudzania informacji, przeglądanie sieci web przypadkowe) z uprawnieniami administracyjnymi, Utwórz dedykowane konta dla wszystkich użytkowników z uprawnieniami administracyjnymi. 

Jeśli nie zostało to jeszcze zrobione, Utwórz oddzielne konta dla użytkowników do wykonywania zadań administratora globalnego, aby upewnić się, nie mogą przypadkowo otworzyć wiadomości e-mail lub uruchamianie programów skojarzonych z konta administratora. Upewnij się, że te konta mają poczty e-mail przekazywane do pracy skrzynki pocztowej.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Upewnij się, że hasła konta z uprawnieniami administracyjnymi była ostatnio zmieniana.

Upewnij się, że wszyscy użytkownicy zalogowaniem się do swojego konta z uprawnieniami administracyjnymi i zmienić swoje hasła co najmniej raz w ciągu ostatnich 90 dni. Upewnij się również, że miały który kont udostępnionych w wielu użytkowników, którzy znać hasło zostało niedawno zmienione haseł.

#### <a name="turn-on-password-hash-synchronization"></a>Włączanie synchronizacji skrótu hasła

Synchronizacja skrótów haseł to funkcja używane do synchronizowania skrótów skrótów haseł użytkowników z lokalnego wystąpienia usługi Active Directory na platformie Azure opartej na chmurze wystąpienia usługi AD. Nawet jeśli zdecydujesz się używać federacyjnego z usługi Active Directory Federation Services (AD FS) lub innych dostawców tożsamości, możesz opcjonalnie skonfigurować synchronizacji skrótu hasła jako kopia zapasowa w przypadku infrastruktury lokalnej, takich jak usługi AD lub serwerów usług ADFS nie powieść lub staje się tymczasowo niedostępna. Dzięki temu użytkownicy mogą logować się do usługi przy użyciu tego samego hasła, używanego do logowania się w ich lokalnym wystąpieniem usługi AD. Ponadto umożliwia ochronę tożsamości do wykrywania przejęcie poświadczeń przez porównanie tych skrótów haseł z hasłami znane naruszenia, jeśli użytkownik ma wykorzystać ich tego samego adresu e-mail i hasło na inne usługi nie jest połączony z usługą Azure AD.  Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacji Azure AD Connect](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Wymagane uwierzytelnianie wieloskładnikowe (MFA) dla użytkowników w wszystkich ról uprzywilejowanych, a także narażonych użytkowników

Usługi Azure AD zaleca wymusić uwierzytelnianie wieloskładnikowe (MFA) dla wszystkich użytkowników, w tym administratorów i innych użytkowników, którzy będą mieć znaczący wpływ naruszenie swojego konta (na przykład urzędników finansowych). Pozwala to ograniczyć ryzyko ataku z powodu którego bezpieczeństwo zostało naruszone hasła.

Włącz funkcję:

* [Uwierzytelnianie wieloskładnikowe dla kont narażenia wysokiej](authentication/multi-factor-authentication-security-best-practices.md) takich jak konta dla biuro w organizacji 
* [Uwierzytelnianie wieloskładnikowe dla każdego konta administratora skojarzonego z indywidualnego użytkownika](authentication/howto-mfa-userstates.md) dla innych połączonych aplikacji SaaS 
* Uwierzytelnianie wieloskładnikowe dla wszystkich administratorów dla aplikacji SaaS firmy Microsoft, w tym administratorów w rolach zarządzane w programie Exchange Online i portalu usługi Office

Jeśli używasz usługi Windows Hello dla firm, wymaganie uwierzytelniania Wieloskładnikowego można spełnić przy użyciu konta usługi Windows Hello w środowisku. Aby uzyskać więcej informacji, zobacz [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurowanie usługi Identity Protection 

Azure AD Identity Protection jest oparte na algorytmu monitorowania i raportowania narzędzie, które umożliwia wykrywanie potencjalnych luk w zabezpieczeniach wpływających na tożsamości organizacji. Skonfiguruj automatyczne odpowiedzi na te wykryte podejrzane działania i podejmij odpowiednią akcję, aby je rozwiązać. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Uzyskaj wynik Secure Office 365 (Jeśli przy użyciu usługi Office 365)

Zabezpiecz rysunki wynik się, jakie usługi Office 365 używasz (np. OneDrive, SharePoint i Exchange), a następnie sprawdza ustawienia i działań i porównuje je do linii bazowej ustanowionych przez firmę Microsoft. Zostanie wyświetlony wynik, w oparciu o sposób wyrównany są z najlepszymi rozwiązaniami zabezpieczeń. Każdy, kto ma uprawnienia administratora (Administrator globalny lub roli administratora niestandardowych), dla subskrypcji usługi Office 365 Business Premium lub Enterprise mogą uzyskiwać dostęp do bezpiecznego wynik w [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Przejrzyj wskazówki dotyczące zabezpieczeń i zgodności usługi Office 365 (Jeśli przy użyciu usługi Office 365)

[Plan zabezpieczeń i zgodności](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) przedstawiono podejście jak usługi Office 365 należy skonfigurować usługi Office 365 i korzystać z innych funkcji pakietu EMS. Następnie, przejrzyj kroki 3 – 6 sposobu [ochrony dostępu do danych i usług w usłudze Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) i w podręczniku jak [monitorowanie zabezpieczeń i zgodności w usłudze Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Skonfiguruj Monitorowanie działania usługi Office 365 (Jeśli przy użyciu usługi Office 365)

Można monitorować osób w danej organizacji korzystania z usługi Office 365, dzięki któremu można zidentyfikować użytkowników, którzy mają konta administratora i kto może nie muszą usługi Office 365 dostępu z powodu podpisu nie do tych portali. Aby uzyskać więcej informacji, zobacz [raporty działanie w Centrum administracyjnym usługi Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Ustanowić właścicieli plan odpowiedzi na zdarzenia/awaryjnego

Efektywne wykonanie odpowiedzi na zdarzenia jest złożone przedsiębiorstwa. W związku z tym ustanowienie możliwości pomyślnej odpowiedzi na zdarzenia wymaga znacznej planowania i zasobów. Istotne jest ciągłe monitorowanie przez atakami i ustanowić procedury obsługi zdarzeń stawiając na pierwszym miejscu. Skuteczne metody zbierania, analizy i raportowania danych są istotne do tworzenia relacji i dla nawiązać komunikacji z innymi grupami wewnętrzny i zaplanować właścicieli. Aby uzyskać więcej informacji, zobacz [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Bezpieczną lokalną uprzywilejowane konta z uprawnieniami administracyjnymi, jeśli nie zostało już przeprowadzone

Jeśli dzierżawy usługi Azure Active Directory jest synchronizowane z lokalnej usługi Active Directory, postępuj zgodnie ze wskazówkami w [zabezpieczeń uprzywilejowanego dostępu plan](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): etap 1. Obejmuje to tworzenie oddzielnych administratora konta dla użytkowników, którzy potrzebują do przeprowadzania zadań administracyjnych lokalnie, wdrażanie uprzywilejowanego dostępu stacje robocze administratorów usługi Active Directory i tworzenie hasła administratora lokalnego unikatowy dla stacji roboczych i serwery.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do platformy Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Zakończenie spis subskrypcji

Umożliwia zidentyfikowanie subskrypcje w organizacji, które udostępniać aplikacji produkcyjnych w witrynie Enterprise portal i portalu Azure. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Usuwanie konta Microsoft z ról administratora

Konta Microsoft z innych programów, takich jak Xbox Live i Outlook powinny być używane nie jako konta administratora dla subskrypcji organizacji. Usuń stanu administracji wszystkie konta Microsoft i Zastąp z usługą Active Directory (na przykład chris@contoso.com) konta służbowego.

#### <a name="monitor-azure-activity"></a>Monitorowanie aktywności platformy Azure

Dziennik aktywności platformy Azure zawiera historię zdarzenia na poziomie subskrypcji platformy Azure. Zapewnia informacje o który tworzona, aktualizacji i usunąć zasobów oraz gdy te zdarzenia wystąpiły. Aby uzyskać więcej informacji, zobacz [inspekcji i otrzymywanie powiadomień o ważnych akcje w ramach subskrypcji platformy Azure](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do innych aplikacji w chmurze za pomocą usługi Azure AD 

#### <a name="configure-conditional-access-policies"></a>Konfigurowanie zasad dostępu warunkowego

Przygotowanie zasad dostępu warunkowego dla lokalnego i aplikacji hostowanych w chmurze. Jeśli użytkownicy urządzeń dołączonych do miejsca pracy, uzyskać więcej informacji o [Konfigurowanie lokalnego dostępu warunkowego przy użyciu rejestracji urządzeń usługi Azure Active Directory](active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Etap 3: Tworzenie widoczność i w pełni kontrolować aktywność administratora

![Etap 3](./media/admin-roles-best-practices/stage-three.png)

Etap 3 oparty na czynniki od etapu 2 i jest przeznaczony do implementacji w około 1 – 3 miesiące. Ten etap plan zabezpieczone uprzywilejowanego dostępu zawiera następujące składniki.

### <a name="general-preparation"></a>Przygotowanie

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Zakończenie przeglądu dostęp użytkowników do ról administratora

Więcej firmy użytkownicy są uzyskanie uprzywilejowanego dostępu za pośrednictwem usługi w chmurze, które może prowadzić do zwiększania platformy niezarządzane. Dotyczy to również użytkowników, staje się Administratorzy globalni dla usługi Office 365, Administratorzy subskrypcji platformy Azure i użytkowników, którzy mają dostęp administratora do maszyn wirtualnych lub za pośrednictwem aplikacji SaaS. Jednak organizacje powinny mieć wszystkich pracowników, w szczególności administratorzy obsługi codziennych operacji nieuprawnionym użytkownikom i tylko podejmij uprawnień administratora, zgodnie z potrzebami. Ponieważ liczbę użytkowników, ról administratora może przekroczył od początkowego przyjęcia, pełny dostęp przegląda do identyfikowania i upewnij się, każdy użytkownik, który jest uprawniona do aktywowania uprawnień administratora. 

Wykonaj następujące czynności:

* Określ użytkowników, którzy są usługi Azure AD Administratorzy, włącz na żądanie dostępu z uprawnieniami administratora w czasie i środki zabezpieczające oparte na rolach.
* Konwertuj użytkowników, którzy mają nieuzasadnione wyczyść admin uprzywilejowany dostęp do innej roli (Jeśli nie kwalifikuje się roli, należy je usunąć).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Kontynuować wdrażanie silniejsze uwierzytelnianie dla wszystkich użytkowników 

Wymagaj przedstawicieli kadry kierowniczej pakiet C, menedżerów ogólny krytycznych IT i personelu zabezpieczeń i innych użytkowników wysokiej widoczne powinny być nowoczesnych, silne uwierzytelnianie, takich jak uwierzytelnianie wieloskładnikowe Azure lub usługi Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Użyj dedykowanych stacje robocze dla administracji dla usługi Azure AD

Osoby atakujące mogą próbować target uprzywilejowanych kont w celu uzyskania dostępu do danych organizacji i systemów, ich może zakłócać integralności i autentyczności danych przez złośliwy kod, który zmienia logice programu lub snoops wprowadzenie poświadczeń administratora. Uprzywilejowany dostęp do stacji roboczych (łapy) podaj dedykowanego systemu operacyjnego dla poufnych zadań, które jest chronione przed Internet ataków i zagrożeń wektory. Oddzielanie tych ważnych zadań i konta z dziennego korzystania ze stacji roboczej i urządzeń zapewnia bardzo silną ochronę przed wyłudzania, aplikacji i luk w zabezpieczeniach systemu operacyjnego, ataków na różnych personifikacji i ataki kradzieży poświadczeń, takie jak naciśnięcie klawisza Rejestrowanie i Pass--Hash, Pass--Ticket. Wdrażając stacje robocze uprzywilejowanego dostępu, można zmniejszyć ryzyko, że administratorzy wprowadź poświadczenia administratora, z wyjątkiem na środowisko pulpitu, który ma zostać wzmocnione zabezpieczenia. Aby uzyskać więcej informacji, zobacz [uprzywilejowanego dostępu stacje robocze](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Przejrzyj National Institute of Standards i technologii zalecenia dotyczące obsługi zdarzeń 

National Institute of Standards i technologii (NIST) zawiera wskazówki dotyczące obsługi zdarzenia, szczególnie w przypadku analizowania danych związanych z zdarzenia i określania właściwą odpowiedź do każdego zdarzenia. Aby uzyskać więcej informacji, zobacz [(NIST) komputera zdarzenie obsługi Przewodnik po zabezpieczeniach (SP 800 61, wersji 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Dodatkowe role administracyjne implementacji zarządzania tożsamości uprzywilejowanych (PIM) dla JIT

Azure Active Directory, można użyć [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) możliwości. Czas aktywacji ról uprzywilejowanych polega na umożliwieniu:

* Aktywuj uprawnień administratora do wykonywania określonych zadań
* Wymusić uwierzytelnianie MFA w procesie aktywacji
* Użyj alertów do informuje administratorów o zmianach poza pasmem
* Użytkownicy mogą zachować określone uprawnienia dla wstępnie skonfigurowanego czasu
* Zezwalaj administratorom zabezpieczeń wykrywanie wszystkich tożsamości uprzywilejowanych, Wyświetl raporty dotyczące inspekcji i tworzenie przeglądami dostępu do identyfikowania każdy użytkownik, który jest uprawniona do aktywowania uprawnień administratora

Jeśli już używasz usługi Azure AD Privileged Identity Management, Dostosuj przedziały czasu uprawnienia powiązane z czasu, w razie potrzeby (na przykład okna obsługi).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Określić narażenia na podstawie hasła logowania protokołów (Jeśli przy użyciu usługi Exchange Online)

W przeszłości protokołów założono, że kombinacji nazwy użytkownika i hasła zostały osadzone w urządzeń, konta e-mail, telefony i tak dalej. Ale teraz z ryzyko ataków-atakom w chmurze, zaleca się zidentyfikowanie każdy potencjalnych użytkownik, który, jeśli ich poświadczenia zostały naruszone, może być krytycznego do organizacji i wykluczyć je z będą mogli logować się do poczty e-mail za pomocą nazwy użytkownika / hasło zaimplementowanie silnego uwierzytelniania wymogi i dostępu warunkowego. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Ukończenie oceny Przegląd ról dla ról usługi Office 365 (Jeśli przy użyciu usługi Office 365)

Sprawdzenia, czy wszyscy użytkownicy Administratorzy są poprawne ról (usunięcia i ponownego przypisania zgodnie z tym oceny).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Przejrzyj służą zarządzania zdarzeniami zabezpieczeń w usłudze Office 365 i porównaj z organizacji

Możesz pobrać ten raport z [Zarządzanie zdarzeniami zabezpieczeń w usłudze Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Zabezpieczanie lokalnej uprzywilejowane konta z uprawnieniami administracyjnymi w dalszym ciągu

Jeśli w usłudze Azure Active Directory jest połączony z lokalnej usługi Active Directory, postępuj zgodnie ze wskazówkami w [zabezpieczeń uprzywilejowanego dostępu plan](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): etap 2. W tym wdrażanie uprzywilejowanego dostępu do stacji roboczych dla wszystkich administratorów, wymagania uwierzytelniania Wieloskładnikowego, przy użyciu tylko tyle administratora obsługi kontrolera domeny, co zmniejsza możliwości zaatakowania domen, wdrożenie usługi ATA do wykrywania ataków.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do platformy Azure

#### <a name="establish-integrated-monitoring"></a>Ustanowienia zintegrowanego monitorowania

[Centrum zabezpieczeń Azure](../security-center/security-center-intro.md) zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogą w przeciwnym razie niezauważone, a także współpracuje z szerokim ekosystemem zabezpieczeń rozwiązania.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Spis uprzywilejowanych kontach w hostowanej maszyny wirtualne

W większości przypadków nie należy udzielić nieograniczonych uprawnień użytkowników do subskrypcji platformy Azure lub zasobów. Role administratora usługi Azure AD umożliwia rozdzielenie obowiązków w organizacji i udzielić tylko takiego dostępu użytkowników, którzy potrzebują do wykonania określonego zadania. Na przykład użyć ról administratora usługi Azure AD pozwala jeden administrator zarządzać tylko maszyny wirtualne w ramach subskrypcji, gdy inny można zarządzać baz danych w ramach tej samej subskrypcji. Aby uzyskać więcej informacji, zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure](../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Wdrożenie usługi PIM dla ról administratora usługi Azure AD

Umożliwia zarządzanie tożsamościami uprzywilejowanymi z ról administratora usługi Azure AD zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure. Przy użyciu usługi PIM chroni kont uprzywilejowanych przed atakami przez co zmniejsza czas ekspozycji uprawnień i zwiększając wydajność sieci wgląd w ich za pośrednictwem raporty i alerty. Aby uzyskać więcej informacji, zobacz [Zarządzanie RBAC dostęp do zasobów platformy Azure z Privileged Identity Management](../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Umożliwia wysłanie odpowiednich integracji dzienników Azure dzienniki platformy Azure z systemów SIEM 

Integracja z dzienników Azure pozwala na integrowanie nowych dzienników z zasobów platformy Azure do systemów zarządzania zdarzenia (SIEM) i istniejące informacje o zabezpieczeniach w organizacji. [Integracja z dzienników Azure](../security/security-azure-log-integration-overview.md) gromadzi zdarzenia systemu Windows z dzienniki Podglądu zdarzeń systemu Windows i zasobów platformy Azure z dzienników aktywności platformy Azure, alerty Centrum zabezpieczeń Azure i dzienników diagnostycznych platformy Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do innych aplikacji w chmurze za pomocą usługi Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementuje Inicjowanie obsługi użytkowników dla połączonych aplikacji

Usługi Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takich jak usługi ServiceNow Dropbox, Salesforce i tak dalej. Aby uzyskać więcej informacji, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS przy użyciu usługi Azure AD](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrować ochronę informacji

MCAS służy do badania plików i ustawić zasady dotyczące usługi Azure Information Protection etykiet klasyfikacji, włączanie lepszy wgląd i kontrolę nad danymi w chmurze. Skanowanie i klasyfikowania plików w chmurze i zastosowania usługi Azure information protection etykiety. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Konfigurowanie dostępu warunkowego na podstawie grupy, lokalizacji i czułości aplikacji dla [aplikacji SaaS](https://azure.microsoft.com/overview/what-is-saas/) i aplikacje połączone usługi Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorowanie aktywności w aplikacji połączonych w chmurze

Zapewnienie dostępu użytkowników jest chroniona w połączonych aplikacjach również firma Microsoft zaleca, można wykorzystać [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). To ogranicza dostęp przedsiębiorstwa do aplikacji, oprócz Zabezpieczanie kont administratorów, umożliwiając w chmurze:

* Rozszerzanie wgląd i większą kontrolę do aplikacji w chmurze
* Tworzenie zasad dostępu, działań i udostępnianie danych
* Automatycznie wykryj ryzykowne działań, nietypowe zachowania i zagrożeniami
* Zapobiega wyciekowi danych
* Zminimalizować ryzyko i automatyczne zagrożeń związanych z zapobieganiem i wymuszanie zasad

Agenta Cloud App Security SIEM Cloud App Security jest zintegrowany z serwera SIEM umożliwia scentralizowane monitorowanie alertów usługi Office 365 i działania. Uruchamiany na serwerze i ściąga alertów i działań z Cloud App Security i strumieni ich do serwera SIEM. Aby uzyskać więcej informacji, zobacz [integracji SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Etap 4: Kontynuować tworzenie zabezpieczenia do bardziej aktywnego stan zabezpieczeń


![Etap 4](./media/admin-roles-best-practices/stage-four.png)

Etap 4 przewodnik jest oparty na widoczność od etapu 3 i jest przeznaczony do implementacji sześciu miesięcy i późniejsze. Kończenie pracy pomaga plan, które tworzysz strong uprzywilejowany zabezpieczenia dostępu z potencjalnych ataków, które są obecnie znane i dostępne dzisiaj. Niestety zagrożenia bezpieczeństwa stale ewoluują i shift, dlatego zalecamy, aby wyświetlić zabezpieczeń jako ciągły proces koncentruje się na zwiększenie kosztów i zmniejsza Częstotliwość powodzeń atakujący przeznaczonych dla środowiska.

Zabezpieczanie uprzywilejowanego dostępu jest krytyczne pierwszy krok w celu ustanawiania gwarancji bezpieczeństwa zasobów biznesowych w organizacji nowoczesnych, ale nie jest tylko część programu pełną zabezpieczeń obejmujące elementów, takich jak zasady, operacje, informacje zabezpieczenia, serwerów aplikacji, komputerów, urządzeń, sieci szkieletowej chmury i inne składniki zapewnienie gwarancji trwającą zabezpieczeń. 

Oprócz zarządzania konta uprzywilejowanego dostępu, zaleca się, że Przejrzyj następujący kod w sposób ciągły:

* Upewnij się, że administratorzy robią ich codziennych jako nieuprawnionym użytkownikom.
* Tylko udzielić dostępu uprzywilejowanego w razie potrzeby i usunąć go później (just-in-time).
* Zatrzymuje i przejrzeć audytu odnoszących się do kont uprzywilejowanych.

Aby uzyskać więcej informacji na tworzeniu planu pełną zabezpieczeń, zobacz [firmy Microsoft w chmurze zasobów informatycznych architektura](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Aby uzyskać więcej informacji na angażowaniu usług firmy Microsoft z dowolnego z tych tematów, skontaktuj się z przedstawicielem firmy Microsoft, lub zobacz [kompilacji zabezpieczenia krytyczne przez ochrony przedsiębiorstwa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Ten końcowy etap trwającej plan zabezpieczone uprzywilejowanego dostępu zawiera następujące składniki.

### <a name="general-preparation"></a>Przygotowanie

#### <a name="review-admin-roles-in-azure-active-directory"></a>Przejrzyj role administratora w usłudze Azure Active Directory 

Ustal, czy bieżący wbudowane usługi Azure AD ról administratora są nadal aktualne i upewnij się, że użytkownicy są tylko w przypadku ról i delegowania, które są im niezbędne do odpowiednich uprawnień. Z usługą Azure AD możesz określić osobne administratorom różne funkcje. Aby uzyskać więcej informacji, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Urządzeniach przyłączonych do przeglądu użytkowników, którzy mają administrowanie usługą Azure AD

Aby uzyskać więcej informacji, zobacz [jak do skonfigurowania hybrydowego przyłączonych do usługi Azure Active Directory urządzeń](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Przejrzyj elementy członkowskie [wbudowanych ról administratora usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Jeśli używasz usługi Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Sprawdź poprawność plan odpowiedzi na zdarzenia

Aby ulepszyć planu, firma Microsoft zaleca się, że regularnie Zweryfikuj czy planu działa zgodnie z oczekiwaniami:

* Przejdź do istniejącej mapy drogowej w taki sposób, aby sprawdzić, co zostało pominięte
* Oparte na analizie postmortem, popraw istniejące lub zdefiniuj nowe najlepsze rozwiązania
* Upewnij się, że Twojego planu zaktualizowane odpowiedzi na zdarzenia i najlepsze rozwiązania są rozpowszechniane w całej organizacji


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do platformy Azure 

Określ, czy konieczne [przeniesienia własności subskrypcji platformy Azure na inne konto](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Awaryjne": co zrobić w razie zagrożenia

![W nagłych przypadkach](./media/admin-roles-best-practices/emergency.jpeg)

1. Powiadom klucza menedżerów i funkcjonariusze stosowne informacje dotyczące incydentu.

2. Zapoznaj się z podręcznika dotyczącego ataku. 

3. Dostęp "awaryjne" kombinacja nazwy użytkownika i hasła konta do logowania do usługi Azure AD. 

4. Uzyskiwanie pomocy firmy Microsoft przez [otwarcia żądania pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md).

5. Przyjrzyj się [usługi Azure AD logowania raporty](active-directory-reporting-azure-portal.md). Może występować opóźnienie między zdarzeń występujących i znajduje się w raporcie.

6. Dla środowisk hybrydowych, jeśli federacyjnych i usługi AD FS serwer jest niedostępny, konieczne może być tymczasowo zmienić uwierzytelniania federacyjnego synchronizacji skrótów haseł. Spowoduje to przywrócenie Federacja domen do zarządzanego uwierzytelniania do momentu udostępnienia serwera usług AD FS.

7. Monitorowanie wiadomości e-mail do kont uprzywilejowanych.

8. Upewnij się, że zapisywania kopii zapasowych dzienników istotne dla potencjalnych dochodzenia śledczej i prawnych.

Aby uzyskać więcej informacji na temat obsługi zdarzeń zabezpieczeń systemu Microsoft Office 365, zobacz [Zarządzanie zdarzeniami zabezpieczeń w usłudze Microsoft Office 365](http://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Często zadawane pytania: Często zadawane pytania otrzymane dotyczące zabezpieczania uprzywilejowanego dostępu  


**Pytanie:** co zrobić, jeśli I wszystkie składniki bezpieczny dostęp nie zostało jeszcze zaimplementowane?

**Odpowiedź:** zdefiniować co najmniej dwa szkła podziału konta, przypisz MFA do kont uprzywilejowanych administratorów i oddzielnych kont użytkowników z konta administratora globalnego.


**Pytanie:** po naruszenia, co to jest problem top, który należy najpierw zająć?

**Odpowiedź:** Pamiętaj, że jest wymaganie uwierzytelniania najwyższy dla dużej udostępniane osobom.


**Pytanie:** co się stanie, jeśli zostały dezaktywowane naszych uprzywilejowanych administratorów?

**Odpowiedź:** Utwórz konto administratora globalnego, który jest zawsze są aktualne.


**Pytanie:** co się stanie, jeśli istnieje tylko jeden administrator globalny po lewej i nie można połączyć? 

**Odpowiedź:** użyj jednej z konta awaryjne podziału na uzyskanie uprzywilejowanego dostępu bezpośredniego.


**Pytanie:** jak chronić Administratorzy mojej organizacji?

**Odpowiedź:** mają Administratorzy zawsze wykonuj ich codziennych jako "nieuprzywilejowanego" użytkowników standardowych.
 

**Pytanie:** jakie są najlepsze rozwiązania dotyczące tworzenia kont administratora w usłudze Azure AD?

**Odpowiedź:** rezerwy uprzywilejowany dostęp dla określonego administratora zadań.


**Pytanie:** jakie narzędzia istnieje dzięki temu administrator trwały dostęp?

**Odpowiedź:** Privileged Identity Management (PIM) i Azure AD ról administratora.


**Pytanie:** co to jest stanowisko firmy Microsoft, na synchronizowanie kont administratora do usługi Azure AD?

**Odpowiedź:** warstwy 0 kont administratorów (w tym kont, grupy i innych zasobów, które mają bezpośrednich lub pośrednich kontrolę administracyjną nad lesie usługi Active Directory, domeny i kontrolery domeny i wszystkie zasoby) są używane tylko dla lokalnych kont usługi AD i są Zazwyczaj nie są zsynchronizowane dla usługi Azure AD dla chmury. 


**Pytanie:** jak możemy uniemożliwić Administratorzy przypisywanie dostępu administracyjnego losowe w portalu?

**Odpowiedź:** Użyj konta nieuprzywilejowanego dla wszystkich użytkowników i administratorów większości. Uruchom tworzenie rozmiaru organizacji, aby określić, które kilka kont administratorów powinna uprzywilejowany. Monitor i dla nowo utworzonego użytkowników administracyjnych.


## <a name="next-steps"></a>Kolejne kroki

* [Microsoft Center Trust zabezpieczeń produktu](https://www.microsoft.com/en-us/trustcenter/security) — funkcje zabezpieczeń firmy Microsoft w chmurze, produktów i usług

* [Microsoft Trust Center — zgodność](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) — kompleksowy zestaw zgodności oferty usług w chmurze firmy Microsoft

* [Instrukcje dotyczące przeprowadzenie oceny ryzyka](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment) — Zarządzanie wymagania dotyczące zabezpieczeń i zgodności dla usług w chmurze firmy Microsoft

### <a name="other-ms-online-services"></a>Inne usługi Online MS 

* [Microsoft Intune zabezpieczeń](https://www.microsoft.com/en-us/trustcenter/security/intune-security) — usługa Intune umożliwia zarządzanie urządzeniami przenośnymi, zarządzanie aplikacjami mobilnymi i możliwości zarządzania Komputerami z chmury.

* [Zabezpieczenia programu Microsoft Dynamics 365](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) — Dynamics 365 jest rozwiązanie oparte na chmurze firmy Microsoft, które łączy zarządzanie relacjami z klientami (CRM) i (ERP) możliwości planowania zasobów przedsiębiorstwa.

 
