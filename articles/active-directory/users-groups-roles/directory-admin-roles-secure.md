---
title: Najlepsze rozwiązania dotyczące bezpiecznego dostępu administratora — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Upewnij się, że Twoja organizacja dostępu i administratora konta z uprawnieniami administracyjnymi są bezpieczne. Dla architektów systemów i specjalistów IT, którzy konfigurują usługi Azure AD platformy Azure i Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 03/18/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e61207ea23b9b9560668b58b33e3f32f5f70ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083944"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD

Bezpieczeństwo większości lub wszystkich zasobów biznesowych w nowoczesnej organizacji, zależy od integralności uprzywilejowanych kont służących do administrowania i zarządzania systemami IT. Uczestników złośliwych działań, w tym osoby przeprowadzające ataki cybernetyczne często docelowych kont administratorów i inne elementy uprzywilejowanego dostępu, aby szybko uzyskać dostęp do poufnych danych i systemy przy użyciu przed atakami kradzieży poświadczeń. Chmury usługi, zapobiegania i odpowiedzi są wspólnego obowiązków dostawcy usług w chmurze i klienta. Aby uzyskać więcej informacji o najnowszych zagrożeniach do punktów końcowych i w chmurze, zobacz [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report). Ten artykuł może pomóc Ci opracować plan kierunku zamykanie przerw między bieżącym planów i wytyczne opisane w tym miejscu.

> [!NOTE]
> Firma Microsoft dba do zapewnienia najwyższych poziomów zaufania, przejrzystości, zgodność ze standardami i zgodności z przepisami. Dowiedz się więcej o jak zespół reagowania na zdarzenia globalne Microsoft zmniejsza skutki ataków na usługi w chmurze i jak bezpieczeństwo jest wbudowane w produktów firmy Microsoft i usługi w chmurze w [Microsoft Trust Center — bezpieczeństwo](https://www.microsoft.com/trustcenter/security)i elementy docelowe zgodności firmy Microsoft w [Microsoft Trust Center - zgodności](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
W przypadku większości organizacji bezpieczeństwa zasobów biznesowych zależy od integralności uprzywilejowanych kont służących do administrowania i zarządzania systemami IT. Osoby przeprowadzające ataki Cybernetyczne koncentrują się na uprzywilejowany dostęp do systemów infrastruktury (na przykład usługi Active Directory i Azure Active Directory) do uzyskania dostępu do danych poufnych w organizacji. 

Tradycyjne podejścia, koncentrujących się na zabezpieczaniu punkty wejścia i wyjścia sieci jako głównych zabezpieczeń obwodowych są mniej skuteczne, ze względu na wzrost użycia aplikacji SaaS i urządzeń osobistych w Internecie. Naturalnym sposobem zastąpienia obwodowych zabezpieczeń sieci w złożonych nowoczesnych przedsiębiorstwach jest uwierzytelnianie i kontrola autoryzacji w warstwie tożsamości organizacji.

Uprzywilejowane konta administracyjne są faktycznymi kontrolę nad tym nowych "zabezpieczeń obwodowych." Jest to znaczenie ma ochrona uprzywilejowanego dostępu, niezależnie od tego, czy środowisko jest w środowisku lokalnym, chmurze lub hybrydowe środowiska lokalnego i usług hostowanych w chmurze. Ochrona dostępu administracyjnego przed ujawnionymi wymaga podjęcia kompleksowego, przemyślanego podejścia do izolowania systemów organizacji przed zagrożeniami. 

Zabezpieczanie uprzywilejowanego dostępu wymaga wprowadzenia zmian

* Procesy, praktyk administracyjnych i zarządzania wiedzą
* Składnikach technicznych, takich jak mechanizmów obronnych hosta, ochrony kont i zarządzania tożsamościami

W tym dokumencie skupiono się przede wszystkim na tworzenie planu do zabezpieczania tożsamości i dostępu, które są zarządzane lub w usłudze Azure AD, Microsoft Azure, Office 365 i innych usług cloud services. W przypadku organizacji, które mają lokalnego konta z uprawnieniami administracyjnymi, zobacz wskazówki dotyczące lokalne i hybrydowe uprzywilejowany dostęp, zarządzać za pomocą usługi Active Directory w [zabezpieczanie uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Wskazówki zawarte w tym artykule odwołuje się przede wszystkim do funkcji usługi Azure Active Directory, które są uwzględnione w planach usługi Azure Active Directory — wersja Premium P1 i P2. Azure Active Directory Premium P2 jest dołączona do pakietu EMS E5 i pakietu Microsoft 365 E5. We wskazówkach tych założono, że Twoja organizacja ma już licencji usługi Azure AD Premium P2 zakupionych dla użytkowników. Jeśli nie masz te licencje, niektóre wytyczne nie mogą być stosowane dla Twojej organizacji. Ponadto w tym artykule określenie administratora globalnego (lub administrator globalny) jest synonimem "administrator firmy" lub "administrator dzierżawy."

## <a name="develop-a-roadmap"></a>Tworzenie planu 

Firma Microsoft zaleca tworzenie, a następnie postępuj zgodnie z planem w celu zabezpieczenia uprzywilejowanego dostępu przed atakami cybernetycznymi. Twój plan, aby pomieścić do istniejących możliwości i szczególnych wymagań organizacji zawsze można dostosować. Każdy etap planu powinna podnieść koszty i trudności przeciwników na ataki uprzywilejowany dostęp do usługi lokalne, chmurę oraz zasoby hybrydowego. Firma Microsoft zaleca cztery następujące etapy plan: To zalecane, harmonogramy plan najskuteczniejsze i najbardziej efektywnym najpierw na podstawie doświadczeń firmy Microsoft przy użyciu ataku cybernetycznego implementację zdarzenia i odpowiedzi. Osie czasu dla tego planu są przybliżone.

![Etapy plan z liniami czasu](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Etap 1 (24-48 godzin). Elementów krytycznych, które firma Microsoft zaleca wykonaj natychmiast

* Etap 2 (2 – 4 tygodnie). Eliminowanie na najczęściej używane techniki ataku

* Etap 3 (1 – 3 miesiące). Budowanie widoczności i tworzenia pełnej kontroli działań administratora

* Etap 4 (sześć miesięcy i nowszych): Kontynuacja tworzenia mechanizmów obronnych w celu dalszego ograniczenia funkcjonalności platformy zabezpieczeń

Ta struktura plan zaprojektowano w celu optymalnego wykorzystania technologii firmy Microsoft, które może masz już wdrożone. Można również korzystać z zabezpieczeń klucza bieżących i przyszłych technologii i integrowanie narzędzi zabezpieczeń od innych dostawców, który został już wdrożony lub rozważane jest wdrożenie. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Etap 1: Elementów krytycznych, które firma Microsoft zaleca wykonaj natychmiast

![Etap 1 elementów krytycznych w celu najpierw](./media/directory-admin-roles-secure/stage-one.png)

Etap 1 planu koncentruje się na krytyczne zadania, które są szybkie i łatwe do wdrożenia. Zaleca się, czy te elementy kilku razu w pierwszym 24-48 godzin zapewnienie podstawowy poziom bezpiecznego dostępu uprzywilejowanego. Ten etap planu zabezpieczone dostępu uprzywilejowanego obejmuje następujące czynności:

### <a name="general-preparation"></a>Przygotowanie

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Włączanie usługi Azure AD Privileged Identity Management

Jeśli usługi Azure AD Privileged Identity Management (PIM) nie została już włączona, zrób to w ramach dzierżawy w środowisku produkcyjnym. Po włączeniu Privileged Identity Management otrzymasz powiadomienie e-mail wiadomości do uprzywilejowanego dostępu zmiany roli. Te powiadomienia stanowić wczesne ostrzeżenie, po dodaniu dodatkowych użytkowników do wysoce uprzywilejowanych ról w katalogu.

Usługa Azure AD Privileged Identity Management znajduje się w usłudze Azure AD Premium P2 lub EMS E5. Te rozwiązania pomóc w ochronie dostępu do aplikacji i zasobów w środowisku lokalnym i w chmurze. Jeśli nie masz już P2 usługi Azure AD Premium lub EMS E5 ale do oceny, więcej funkcji, do którego odwołuje się ten przewodnik, należy zasubskrybować [Enterprise Mobility + Security bezpłatna 90-dniowa wersja próbna](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Do wypróbowania usługi Azure AD Privileged Identity Management i Azure AD Identity Protection, aby monitorować działania przy użyciu usługi Azure AD, zaawansowane raportowanie zabezpieczeń, inspekcjom oraz alertom, należy użyć wersji próbnych tych licencji.

Po zakończeniu włączono usługi Azure AD Privileged Identity Management:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla dzierżawy produkcyjnej.

2. Aby wybrać dzierżawy, w której chcesz używać Privileged Identity Management, wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure Portal.

3. Wybierz **wszystkich usług** i filtrować listy dla **usługi Azure AD Privileged Identity Management**.

4. Otwórz Privileged Identity Management z **wszystkich usług** listy i przypiąć go do pulpitu nawigacyjnego.

Pierwszą osobą, która do użycia usługi Azure AD Privileged Identity Management w dzierżawie jest automatycznie przypisywany **administrator zabezpieczeń** i **administratorem ról uprzywilejowanych** ról w ramach dzierżawy. Tylko administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami ról katalogu usługi Azure AD użytkowników. Ponadto po dodaniu usługi Azure AD Privileged Identity Management, są wyświetlane Kreator zabezpieczeń, który przeprowadzi Cię przez środowisko początkowego odnajdywania i przypisywania. Można zamknąć kreatora bez wprowadzania dodatkowych zmian w tej chwili. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identyfikowanie i klasyfikowanie kont, które znajdują się w wysoce uprzywilejowanych ról 

Po włączeniu usługi Azure AD Privileged Identity Management, należy wyświetlić użytkowników, którzy znajdują się w role katalogu, administratorem globalnym, administratorem ról uprzywilejowanych, administrator usługi Exchange Online i administrator usługi SharePoint Online. Jeśli nie masz usługi Azure AD PIM w Twojej dzierżawie, możesz użyć [interfejs API środowiska PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Uruchomić z rolą administratora globalnego, ponieważ ta rola jest ogólny: użytkownik, który jest przypisany do tej roli Administrator ma te same uprawnienia dla wszystkich usług w chmurze dla których zostały zasubskrybowane przez organizację, niezależnie od tego, czy są już przypisane tej roli w usłudze Microsoft 365 Centrum administracyjne usługi Azure portal, lub przy użyciu modułu Azure AD dla programu Microsoft PowerShell. 

Usuń wszelkie konta, które nie są już potrzebne w ramach tych ról. Następnie skategoryzować pozostałe konta, które są przypisane do ról administratora:

* Indywidualnie przypisane do użytkowników administracyjnych i może również służyć do celów innych niż administracyjne (na przykład osobistego adresu e-mail)
* Indywidualnie przypisane do użytkowników administracyjnych i przeznaczone wyłącznie do celów administracyjnych
* Współużytkowane przez wielu użytkowników
* Dla scenariuszy dostępu awaryjnego break szkła
* W przypadku zautomatyzowanych skryptów
* Dla użytkowników zewnętrznych

#### <a name="define-at-least-two-emergency-access-accounts"></a>Zdefiniuj co najmniej dwóch kont dostępu awaryjnego 

Upewnij się, że nie uzyskasz w sytuacji, gdzie one można zostanie przypadkowo zablokowana administrowanie dzierżawą usługi Azure AD z powodu braku możliwości, zaloguj się lub aktywować istniejące poszczególne konta z uprawnieniami administratora. Na przykład jeśli organizacja jest sfederowana do środowiska lokalnego dostawcy tożsamości, to tego dostawcy tożsamości może być niedostępny, dzięki czemu użytkownicy nie zalogować się w środowisku lokalnym. Za łagodzenia skutków przypadkowego braku dostępu administracyjnego przez zapisanie co najmniej dwóch kont dostępu awaryjnego w dzierżawie.

Kont dostępu awaryjnego pomagają organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory. Te konta są wysoce uprzywilejowane i nie są przypisane do konkretnych osób. Kont dostępu awaryjnego są ograniczone do awaryjnego w scenariuszach "break szkła", gdy nie można użyć normalnego konta z uprawnieniami administracyjnymi. Organizacje muszą zapewnić w celu kontrolowania i ograniczania użycia konta dostępu awaryjnego tylko tego czasu, dla których konieczne jest.

Ocena kont, które są przypisane lub kwalifikuje się do roli administratora globalnego. Jeśli nie występują tylko w chmurze kont przy użyciu narzędzia *. onmicrosoft.com domain (przeznaczonych do dostępu awaryjnego "break szkła"), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi z dostępem awaryjnym w usłudze Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Włącz uwierzytelnianie wieloskładnikowe i Zarejestruj wszystkie inne konta administratora inne niż federacyjne pojedynczego użytkownika wysoce uprzywilejowanych

Wymaga usługi Azure Multi-Factor Authentication (MFA), podczas logowania dla wszystkich poszczególnych użytkowników, którzy są trwale przypisana do co najmniej jednej z ról administratora usługi Azure AD: Administratorem globalnym, administratorem ról uprzywilejowanych, administrator usługi Exchange Online i administratorem usługi SharePoint Online. Użyj przewodnika, aby włączyć [Multi-Factor Authentication (MFA) dla kont administratorów](../authentication/howto-mfa-userstates.md) i upewnij się, że wszystkich tych użytkowników, którzy zarejestrowali na [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Więcej informacji można znaleźć w kroku 2 i krok 3 przewodnika [ochrona dostępu do danych i usług w usłudze Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etap 2: Eliminowanie na najczęściej używane techniki ataku

![Etap 2 Mitigate często używane ataków](./media/directory-admin-roles-secure/stage-two.png)

Etap 2 koncentruje się plan na łagodzenia często najczęściej używane techniki ataku z kradzieżą poświadczeń i nadużyciami i może być implementowany w około 2 – 4 tygodnie. Ten etap planu zabezpieczone dostępu uprzywilejowanego obejmuje następujące czynności.

### <a name="general-preparation"></a>Przygotowanie

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Przeprowadź spis usług, właściciele i Administratorzy

Wraz ze wzrostem w bring-your własne urządzenie (BYOD) i Praca z domu zasad i wzrost łączności bezprzewodowej w firmach ważne jest monitorowanie łączącego się z siecią. Audyt efektywnym elementem systemu zabezpieczeń często ujawnia urządzeń, aplikacji i programów działających w sieci, które nie są obsługiwane przez IT i w związku z tym potencjalnie nie bezpieczne. Aby uzyskać więcej informacji, zobacz [zabezpieczeń platformy Azure, zarządzania i monitorowania — omówienie](../../security/security-management-and-monitoring-overview.md). Upewnij się, Uwzględnij wszystkie z następujących czynności w procesie spisu. 

* Zidentyfikuj użytkowników, którzy mają ról administracyjnych i usług, w którym można zarządzać.
* Użyj usługi Azure AD PIM, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administracyjny do usługi Azure AD, w tym dodatkowe role poza tymi, które się na liście etap 1.
* Poza rolami zdefiniowane w usłudze Azure AD Office 365 zawiera zestaw ról administratora, które można przypisać do użytkowników w Twojej organizacji. Każda rola administratora mapowany typowe funkcje biznesowe i zapewnia osobom w organizacji uprawnień do wykonywania określonych zadań [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com). Aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administracyjny do usługi Office 365, w tym za pomocą ról, które nie są zarządzane w usłudze Azure AD za pomocą Centrum administracyjnego usługi Microsoft 365. Aby uzyskać więcej informacji, zobacz [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) i [najlepsze rozwiązania dla usługi Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Wykonaj spis w innych usługach, które Twoja organizacja zależy, takich jak Azure, Intune i Dynamics 365.
* Upewnij się, że konta administratora (konta, które są używane do celów administracji, nie tylko kont codziennych użytkowników) pracy adresy e-mail do nich dołączone i zarejestrowany dla usługi Azure MFA lub użyć lokalnego uwierzytelniania Wieloskładnikowego.
* Poproś użytkowników o uzasadnienie biznesowe ich dostęp administracyjny.
* Usuń prawa dostępu administratora dla tych użytkowników indywidualnych i usług, które nie są potrzebne.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Zidentyfikować konta Microsoft w rolach administracyjnych, które muszą zostać przełączone do konta służbowego.

Czasami początkowej Administratorzy globalni dla organizacji ponowne użycie istniejących poświadczeń konta Microsoft, gdy zaczęli korzystać z funkcji usługi Azure AD. Te konta Microsoft, powinny zostać zastąpione przez indywidualnych kont oparte na chmurze, jak i zsynchronizowane. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Upewnij się, oddzielnych kont użytkowników i przekazywania dla konta administratora globalnego poczty 

Konta administratora globalnego nie powinna mieć osobistych adresów e-mail, ponieważ osobiste konta e-mail są regularnie phished ataków cybernetycznych. Aby oddzielić ryzyka internetowe (wyłudzanie informacji, przeglądanie Internetu niezamierzone) z uprawnieniami administracyjnymi, należy utworzyć dedykowane konta dla każdego użytkownika z uprawnieniami administracyjnymi. 

Jeśli jeszcze tego nie zrobiono, Utwórz oddzielne konta dla użytkowników do wykonywania zadań administratora globalnego, aby upewnić się, nie nieodwracalnie otworzyć wiadomości e-mail lub uruchom programy powiązane przy użyciu konta administratora. Upewnij się, że te konta mają swój adres e-mail, przekazywane do skrzynki pocztowej pracy.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Upewnij się, że niedawno zmieniono hasła konta z uprawnieniami administracyjnymi

Upewnij się, że wszyscy użytkownicy zalogowali się do swoich kont administratora i zmienić swoje hasła co najmniej raz w ciągu ostatnich 90 dni. Ponadto upewnij się, że który jakiekolwiek udostępnionego konta, w których wielu użytkowników znać hasło miał haseł zostało niedawno zmienione.

#### <a name="turn-on-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

Synchronizacja skrótów haseł jest funkcją, używane do synchronizowania skrótów skrótów haseł użytkowników z lokalnym wystąpieniem usługi Active Directory na platformie Azure opartych na chmurze wystąpienia usługi AD. Nawet jeśli użytkownik zdecyduje się za pomocą usług federacyjnych Active Directory Federation Services (AD FS) lub innych dostawców tożsamości, możesz opcjonalnie skonfigurować synchronizacji skrótów haseł do przechowywania kopii zapasowych w przypadku takiej infrastruktury, takich jak usługi AD lub serwerów usług AD FS się nie powieść lub staje się tymczasowo niedostępne. Umożliwia to użytkownikom na logowanie się do usługi przy użyciu tego samego hasła, używanego do logowania się na swoje lokalne wystąpienie usługi AD. Umożliwia także, Identity Protection wykryć przejęcie poświadczeń przez porównanie tych skrótów haseł z hasłami, znane złamać, jeśli użytkownik wykorzystała ich tego samego adresu e-mail i hasło w innych usługach, które nie są podłączone do usługi Azure AD.  Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla użytkowników pełniących uprzywilejowane wszystkie role, a także ujawnionych użytkowników

Usługa Azure AD zaleca Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla wszystkich użytkowników, w tym administratorzy i wszyscy użytkownicy, którzy będą mieć znaczący wpływ, jeśli swojego konta zostało naruszone bezpieczeństwo (na przykład, dyrektorów finansowych). Zmniejsza to ryzyko ataku z powodu hasła ze złamanymi zabezpieczeniami.

Włącz funkcję:

* [Uwierzytelnianie wieloskładnikowe za pomocą zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) dla wszystkich użytkowników w Twojej organizacji.

Jeśli używasz Windows Hello dla firm, wymaganie uwierzytelniania Wieloskładnikowego można spełnić przy użyciu usługi Windows Hello sign w środowisku. Aby uzyskać więcej informacji, zobacz [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurowanie usługi Identity Protection 

Usługa Azure AD Identity Protection to algorytm monitorowania i raportowania narzędzie, które można użyć do wykrywania potencjalnych luk w zabezpieczeniach wpływających na tożsamości w organizacji. Skonfigurowanie automatycznych odpowiedzi na te wykrytych podejrzanych działań i podejmij odpowiednią akcję, aby je rozwiązać. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Uzyskaj ocenę Secure Office 365 (w przypadku korzystania z usługi Office 365)

Zabezpiecz dane wynik się, jakie usługi Office 365, używasz (np. OneDrive, SharePoint i Exchange), a następnie sprawdza ustawienia i działań i porównuje je do linii bazowej ustanowione przez firmę Microsoft. Zostanie wyświetlony wynik, w oparciu o sposób wyrównany są najlepsze praktyki w zakresie zabezpieczeń. Każdy, kto ma uprawnienia administratora (Administrator globalny lub rolę administrator niestandardowych) dla subskrypcji usługi Office 365 Business Premium lub Enterprise mogą uzyskiwać dostęp do bezpiecznego wynik w [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Przejrzyj wskazówki dotyczące zabezpieczeń i zgodności usługi Office 365 (w przypadku korzystania z usługi Office 365)

[Planowanie zabezpieczeń i zgodności](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) opisano podejście do jak konfigurowania usługi Office 365 oraz korzystać z innych możliwości pakietu EMS klientów usługi Office 365. Następnie przejrzyj kroki 3 – 6, jak [ochrona dostępu do danych i usług w usłudze Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) przewodnik dotyczący sposobu i [monitorowanie zabezpieczeń i zgodności w usłudze Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurowanie, monitorowanie aktywności usługi Office 365 (w przypadku korzystania z usługi Office 365)

Można monitorować, osoby w Twojej organizacji korzystania z usługi Office 365, dzięki któremu można zidentyfikować użytkowników, którzy mają konto administracyjne i który mogą nie potrzebować usługi Office 365 dostępu z powodu nie logowania się do tych portali. Aby uzyskać więcej informacji, zobacz [raporty o działaniach w Centrum administracyjnym usługi Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Ustanowić właścicieli plan odpowiedzi na zdarzenia/awaryjnego

Efektywne wykonywanie reagowania na zdarzenia jest złożone przedsiębiorstwa. W związku z tym ustanowienie możliwość pomyślnej odpowiedzi na zdarzenia wymaga znacznej planowania i zasobów. Istotne jest, stale monitorować ataków cybernetycznych i ustanowić procedury priorytetyzowanie obsługi zdarzeń. Skuteczne metody zbierania, analizowanie i raportowanie danych są istotne do tworzenia relacji i nawiązania komunikacji z innych grup, wewnętrzny i zaplanować właścicieli. Aby uzyskać więcej informacji, zobacz [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Bezpieczną lokalną uprzywilejowanego konta z uprawnieniami administracyjnymi, jeśli nie zostało zrobione

Jeśli Twoja dzierżawa usługi Azure Active Directory jest zsynchronizowany z usługą Active Directory w środowisku lokalnym, postępuj zgodnie ze wskazówkami w [zabezpieczenia uprzywilejowanego dostępu plan](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Etap 1. Obejmuje to utworzenie oddzielnych administratora konta dla użytkowników, którzy muszą przeprowadzić zadania administracyjne w środowisku lokalnym, wdrażanie stacji roboczych z dostępem uprzywilejowanym dla administratorów usługi Active Directory i tworzenie unikatowe hasła administratora lokalnego dla stacji roboczych i serwery.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do platformy Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Wykonaj spis subskrypcji

Użyj witryny Enterprise portal i portalu Azure, aby zidentyfikować subskrypcje w Twojej organizacji, które hostują aplikacje produkcyjne.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Usuń konta Microsoft z rolami administratora

Konta Microsoft z innych programów, takich jak Xbox Live i Outlook powinna nie służyć jako konta administratora dla subskrypcji w organizacji. Usuń statusu administratora z wszystkie konta Microsoft, a następnie zastąpić za pomocą usługi Azure Active Directory (na przykład chris@contoso.com) kont służbowych.

#### <a name="monitor-azure-activity"></a>Monitorowanie aktywności platformy Azure

Dziennik aktywności platformy Azure zawiera historię zdarzeń na poziomie subskrypcji na platformie Azure. Oferuje informacje o który utworzone, aktualizować i usuwać zasobów i po wystąpieniu zdarzenia. Aby uzyskać więcej informacji, zobacz [Inspekcja i otrzymywanie powiadomień dotyczących ważnych działań związanych z subskrypcją platformy Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do innych aplikacji w chmurze za pomocą usługi Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurowanie zasad dostępu warunkowego

Przygotuj zasady dostępu warunkowego dla lokalnych i hostowanych w chmurze. W przypadku urządzeń dołączonych w miejscu pracy użytkowników więcej informacji zawierają artykuły [Konfigurowanie lokalnego dostępu warunkowego przy użyciu usługi Azure Active Directory urządzenia rejestracji](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Etap 3: Budowanie widoczności i przejęciu pełnej kontroli działań administratora

![Etap 3 przejęcie kontroli działań administratora](./media/directory-admin-roles-secure/stage-three.png)

Etap 3 opiera się na środkach z etap 2 i jest przeznaczony do implementacji w około 1 – 3 miesięcy. Ten etap planu zabezpieczone dostępu uprzywilejowanego obejmuje następujące składniki.

### <a name="general-preparation"></a>Przygotowanie

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Kończenie przeglądu dostępu użytkowników w rolach administratora

Użytkownicy firmowi bardziej są uzyskanie uprzywilejowanego dostępu za pośrednictwem usług w chmurze, co może prowadzić do niezarządzanego platformę, która zwiększa. W tym użytkowników, staje się Administratorzy globalni usługi Office 365, Administratorzy subskrypcji platformy Azure i użytkowników, którzy mają dostęp administratora do maszyn wirtualnych lub za pośrednictwem aplikacji SaaS. Zamiast tego organizacje powinny mieć wszystkich pracowników, szczególnie Administratorzy obsługi codziennych operacji nieuprawnionym użytkownikom, a tylko przyjmą uprawnień administratora, zgodnie z potrzebami. Ponieważ liczba użytkowników w rolach administratora może wzrosła od momentu początkowego wdrożenia, pełny dostęp przeglądów do identyfikowania i upewnij się, każdy użytkownik, kto jest uprawniony do aktywowania uprawnień administratora. 

Wykonaj następujące czynności:

* Określ użytkowników, którzy są Administratorzy usługi Azure AD, włącz na żądanie, dostępu just in time administratora i kontroli opartej na rolach zabezpieczeń.
* Konwertuj użytkowników, którzy mają nieuzasadnione wyczyść administratora uprzywilejowany dostęp do innej roli (Jeśli nie kwalifikowania się do roli, należy je usunąć).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Kontynuuj wdrażanie silniejszemu uwierzytelnianiu dla wszystkich użytkowników 

Wymagaj C-suite przedstawiciele kadry kierowniczej, menedżerów wysokiego poziomu, krytyczne IT i bezpieczeństwa, personel i inni użytkownicy narażone powinny być nowoczesny, silne uwierzytelnianie, takich jak Azure MFA lub Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Dedykowane stacje robocze na użytek administracji dla usługi Azure AD

Osoby atakujące mogą próbować identyfikacji uprzywilejowanych kont do uzyskania dostępu do danych i systemy w organizacji, dzięki czemu może zakłócać integralności i autentyczności danych przez złośliwy kod, który zmienia logiki programu lub snoops administratora, wprowadzając poświadczenia. Stacji roboczych z dostępem (Paw) zapewniają dedykowany system operacyjny do realizacji zadań poufnych, które jest chronione przed atakami internetowymi i wektorami zagrożenia. Oddzielenie poufnych zadań i kont od codziennie korzystanie z stacje robocze oraz urządzeń bardzo silną ochronę przed wyłudzanie informacji, aplikacji i luk w zabezpieczeniach systemu operacyjnego, różnych atakami personifikacji i ataków kradzieży poświadczeń, takich jak naciśnięcie klawisza Rejestrowanie i ataków typu Pass--Hash, Pass--Ticket. Wdrażając z stacje robocze dostępu uprzywilejowanego, można zmniejszyć ryzyko, że administratorzy wprowadź poświadczenia administratora, z wyjątkiem w środowisku komputerowym, który został wzmocnione. Aby uzyskać więcej informacji, zobacz [stacji roboczych z dostępem uprzywilejowanym](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Przeglądanie zaleceń NIST, National Institute of Standards i technologii obsługi zdarzeń 

NIST, National Institute of Standards i technologii (NIST) zawiera wytyczne do obsługi zdarzenia, szczególnie w przypadku analizowania danych dotyczących zdarzenia i określania właściwą odpowiedź do każdego zdarzenia. Aby uzyskać więcej informacji, zobacz [(NIST) komputera zdarzenie obsługi Przewodnik po zabezpieczeniach (SP 800-61, poprawka 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementowanie Privileged Identity Management (PIM) dla JIT na dodatkowe role administracyjne

W przypadku usługi Azure Active Directory, użyj [usługi Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) możliwości. Czas aktywacji ról uprzywilejowanych działa, ponieważ umożliwia:

* Aktywuj uprawnieniami administratora w celu wykonania określonego zadania
* Wymuszanie uwierzytelniania Wieloskładnikowego w procesie aktywacji
* Użyj alertów do informuje administratorów o zmianach wprowadzonych poza pasmem
* Umożliwianie użytkownikom przechowywanie określone uprawnienia, wstępnie skonfigurowanym czasie
* Zezwalaj na użytkowników jako administratorów zabezpieczeń do odnajdywania wszystkich uprzywilejowanych tożsamości, Wyświetl raporty z audytów i tworzenia przeglądów dostępu, aby zidentyfikować każdego użytkownika, kto jest uprawniony do aktywowania uprawnień administratora

Jeśli już używasz usługi Azure AD Privileged Identity Management, należy dostosować harmonogramy czasowo uprawnień, zgodnie z potrzebami (na przykład okna obsługi).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Określić narażenia na protokołów opartych na hasło logowania (w przypadku korzystania z usługi Exchange Online)

W przeszłości protokołów przyjęto, że kombinacji nazwy użytkownika/hasła zostały osadzone w urządzeniach, konta e-mail, telefonach i tak dalej. Ale teraz ze sobą ryzyko ataków cybernetycznych, w chmurze, zaleca się zidentyfikowanie wszystkich potencjalnych użytkowników, którzy, jeśli ich poświadczenia zostały naruszone, może być krytyczny do organizacji i je wykluczyć mogli zalogować się do swojej poczty e-mail za pomocą nazwy użytkownika / hasło, implementując wymagania silnego uwierzytelniania i dostępu warunkowego. Możesz zablokować [starsze uwierzytelnianie przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Sprawdź szczegóły w [sposobu blokowania uwierzytelnianie podstawowe](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) za pośrednictwem Exchnage w trybie online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Ukończenie oceny Przegląd ról dla ról usługi Office 365 (w przypadku korzystania z usługi Office 365)

Oceń, czy wszyscy użytkownicy Administratorzy są poprawne role (Usuń i ponownie przypisać zgodnie z tej oceny).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Przejrzyj podejścia zarządzania zdarzeniami zabezpieczeń, które są używane w usłudze Office 365 i porównaj z Twojej organizacji

Możesz pobrać raport z [Zarządzanie zdarzeniami zabezpieczeń w usłudze Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Zabezpieczanie uprzywilejowanego lokalne konta z uprawnieniami administracyjnymi w dalszym ciągu

Jeśli usługi Azure Active Directory jest podłączony do usługi Active Directory w środowisku lokalnym, postępuj zgodnie ze wskazówkami w [zabezpieczenia uprzywilejowanego dostępu plan](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Etap 2. Obejmuje to wdrażanie stacji roboczych z dostępem dla wszystkich administratorów, wymaganie uwierzytelniania Wieloskładnikowego, przy użyciu tylko tyle administratora obsługi kontrolera domeny, zmniejszyć obszar narażony na domenach, wdrożenie usługi ATA do wykrywania ataków.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do platformy Azure

#### <a name="establish-integrated-monitoring"></a>Ustanowić zintegrowane monitorowanie

[Usługi Azure Security Center](../../security-center/security-center-intro.md) zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogą w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem zabezpieczeń rozwiązania.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Spis uprzywilejowanych kontach w ramach hostowanych maszyn wirtualnych

W większości przypadków nie trzeba nadać użytkownikom nieograniczone uprawnienia do subskrypcji platformy Azure lub zasobów. Role administratora usługi Azure AD umożliwia segregowanie zadań w ramach organizacji i udzielać uprawnień dostępu do użytkowników, którzy potrzebują do wykonywania określonych zadań. Na przykład użyć ról administratora usługi Azure AD, aby umożliwić jednego administratora zarządzać tylko dla maszyn wirtualnych w ramach subskrypcji, gdy inny można zarządzać baz danych SQL w ramach tej samej subskrypcji. Aby uzyskać więcej informacji, zobacz [wprowadzenie opartej na rolach kontrola dostępu w witrynie Azure portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Wdrożenie usługi PIM dla ról administratora w usłudze Azure AD

Za pomocą Privileged identity Management role administratora usługi Azure AD do zarządzania, kontrolę i monitorowanie dostępu do zasobów platformy Azure. Za pomocą usługi PIM chroni uprzywilejowanych kont przed cyberatakami zmniejszyć czas narażenia uprawnień i zwiększyć wgląd w ich używanie przez raporty i alerty. Aby uzyskać więcej informacji, zobacz [Zarządzanie RBAC dostęp do zasobów platformy Azure Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Użyj integracji dzienników platformy Azure, aby wysłać odpowiednie dzienników platformy Azure w systemach SIEM 

Integracja dzienników platformy Azure umożliwia integrowanie nieprzetworzonych dzienników z zasobów platformy Azure z istniejących informacji o zabezpieczeniach i systemami Event Management (SIEM) w organizacji. [Integracja dzienników platformy Azure](../../security/security-azure-log-integration-overview.md) zbiera dane zdarzeń Windows dzienniki Podglądu zdarzeń Windows i zasobów platformy Azure z dzienników aktywności platformy Azure, usługa Azure Security Center alerty i dzienniki diagnostyczne platformy Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do innych aplikacji w chmurze za pomocą usługi Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementowanie aprowizacja użytkowników dla połączonych aplikacji

Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takie jak Dropbox, Salesforce, ServiceNow i tak dalej. Aby uzyskać więcej informacji, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS z usługą Azure AD](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Zintegrować information protection

MCAS umożliwia badanie plików i ustawianie zasad na podstawie etykiet klasyfikacji usługi Azure Information Protection, umożliwiając zapewnia lepszy wgląd i kontrolę nad swoimi danymi w chmurze. Skanowanie i klasyfikowania plików w chmurze oraz stosowanie etykiet programu usługi Azure information protection. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Skonfiguruj dostęp warunkowy bazujący na grupie, lokalizacji i ważności aplikacji dla [aplikacji SaaS](https://azure.microsoft.com/overview/what-is-saas/) i aplikacje połączone usługi Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitoruj aktywność w aplikacjach połączonych w chmurze

W celu zapewnienia dostępu użytkowników jest chroniona w połączonych aplikacjach także, firma Microsoft zaleca, że możesz korzystać z [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Dostęp do przedsiębiorstwa z chmurą aplikacje, oprócz zabezpieczanie konta administratora, umożliwiając w ten sposób:

* Rozszerzanie widoczności i kontroli do aplikacji w chmurze
* Tworzenie zasad dostępu, działań i udostępnianie danych
* Automatycznie wykryj ryzykowne działania, nietypowe zachowania i zagrożeniami
* Zapobiega wyciekowi danych
* Zminimalizować ryzyko i zapobieganie automatycznych zagrożeń oraz wymuszanie stosowania zasad

Agent SIEM zabezpieczeń aplikacji w chmurze usługa Cloud App Security jest zintegrowany z serwerem SIEM, aby korzystać ze scentralizowanego monitorowania alertów usługi Office 365 i działań. Uruchamiany na serwerze i pobiera alerty i działania usługi Cloud App Security i przesyła je strumieniowo do serwera SIEM. Aby uzyskać więcej informacji, zobacz [integracja rozwiązania SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Etap 4: Kontynuacja tworzenia mechanizmów obronnych w celu bardziej proaktywnego działania

![Etap 4 przyjmuje poziom zabezpieczeń aktywnego](./media/directory-admin-roles-secure/stage-four.png)

Etap 4 planu opiera się na widoczność z 3 etapów i jest przeznaczony do zaimplementowania w sześć miesięcy i nie tylko. Kończenie pomaga plan, które tworzysz strong uprzywilejowanego dostępu zabezpieczenia z wypadek potencjalnych ataków, które są obecnie znane i dostępne już dzisiaj. Niestety zagrożenia bezpieczeństwa stale rozwijać i zmieniać, zatem zalecamy spojrzenie na zabezpieczenia jako ciągły proces skupiony na podwyższaniu kosztu i zmniejszaniu skuteczności przeciwników zagrażających Twojemu środowisku.

Zabezpieczanie uprzywilejowanego dostępu jest pierwszym ważnym krokiem do ustanowienia gwarancji bezpieczeństwa dla zasobów biznesowych w nowoczesnej organizacji, ale nie jest to jedyna część pełnego programu bezpieczeństwa zawierających elementy, takie jak zasady, operacje, informacje zabezpieczeń, serwerów, aplikacji, komputerów, urządzeń, sieci szkieletowej chmury i inne składniki zapewniają gwarancji bezpieczeństwa. 

Oprócz zarządzania konta uprzywilejowanego dostępu, zaleca się, że zostały spełnione następujące czynności w sposób ciągły:

* Upewnij się, że administratorzy robią ich codziennych jako nieuprawnionym użytkownikom.
* Tylko udzielić dostępu uprzywilejowanego w razie i usunąć go później (just-in-time).
* Zatrzymuje i przejrzyj działania inspekcji odnoszących się do kont uprzywilejowanych.

Aby uzyskać więcej informacji dotyczących tworzenia pełnego planu zabezpieczeń, zobacz [IT architektury zasobów w chmurze firmy Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Aby uzyskać więcej informacji dotyczących angażowania usług firmy Microsoft na potrzeby z dowolnym z tych tematów, skontaktuj się z przedstawicielem handlowym firmy Microsoft, lub zobacz [twórz krytyczne cybernetycznymi mechanizmów obronnych w celu Chroń swoje przedsiębiorstwo](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Ten końcowy etap trwającą planu zabezpieczone uprzywilejowanego dostępu obejmuje następujące składniki.

### <a name="general-preparation"></a>Przygotowanie

#### <a name="review-admin-roles-in-azure-active-directory"></a>Przegląd ról administratora w usłudze Azure Active Directory 

Ustal, czy role administratora bieżącego wbudowanej usługi Azure AD są nadal aktualne i upewnij się, że użytkownicy znajdują się tylko w przypadku ról i delegacji, które są im niezbędne do odpowiedniego uprawnienia. Za pomocą usługi Azure AD można wyznaczyć oddzielny administratorom różne funkcje. Aby uzyskać więcej informacji, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Urządzenia przyłączone do użytkowników przeglądu, którzy mają administracji usługi Azure AD

Aby uzyskać więcej informacji, zobacz [w jaki sposób skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Przejrzyj elementy członkowskie [wbudowane role administratora usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Jeśli używasz usługi Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Sprawdź poprawność planu reagowania na zdarzenia

Aby ulepszyć planu, firma Microsoft zaleca regularnie zweryfikować że Twój plan działa zgodnie z oczekiwaniami:

* Przejdź do istniejącej mapy drogowej w taki sposób, aby sprawdzić, co zostało pominięte
* Oparte na analizie postmortem, popraw istniejące lub zdefiniować nowe najlepszych rozwiązań
* Upewnij się, że Twojego planu reagowania na zdarzenia zaktualizowane i najlepsze rozwiązania są rozpowszechniane w całej organizacji


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki w przypadku organizacji, zarządzanie dostępem do platformy Azure 

Określ, czy wymagane do [przenieść własność subskrypcji platformy Azure do innego konta](../../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break szkła": co zrobić w nagłych

![Konta awaryjne break szkła dostępu](./media/directory-admin-roles-secure/emergency.jpeg)

1. Powiadom klucza menedżerów i specjaliści ds. bezpieczeństwa odpowiednie informacje dotyczące incydentu.

2. Zapoznaj się z podręcznikiem ataku. 

3. Dostęp do Twojej nazwy użytkownika/hasła kombinacji "break szkła" konta do logowania do usługi Azure AD. 

4. Uzyskaj pomoc od firmy Microsoft przez [otwarcia żądania pomocy technicznej platformy Azure](../../azure-supportability/how-to-create-azure-support-request.md).

5. Przyjrzyj się [raportów logowania usługi Azure AD](../reports-monitoring/overview-reports.md). Może to być opóźnienie między zdarzeń mających miejsce i znajduje się w raporcie.

6. Dla środowisk hybrydowych, jeśli federacyjnej i usługi AD FS, serwer jest niedostępny, konieczne może być tymczasowo Przełącz z federacyjnego uwierzytelniania do użycia synchronizacji skrótów haseł. Przywraca Federacji domeny wróć na uwierzytelnianie zarządzane, dopóki serwer usług AD FS stanie się dostępny.

7. Monitorowanie wiadomości e-mail do kont uprzywilejowanych.

8. Upewnij się, że zapisywania kopii zapasowych dzienników istotnych dla potencjalnych analizy śledczej i prawne.

Aby uzyskać więcej informacji na temat obsługi zdarzenia związane z bezpieczeństwem w Microsoft Office 365, zobacz [Zarządzanie zdarzeniami zabezpieczeń w usłudze Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>CZĘSTO ZADAWANE PYTANIA: Często zadawane pytania, które firma Microsoft otrzyma dotyczące zabezpieczania uprzywilejowanego dostępu  

**Pyt.:** Co należy zrobić, jeśli I nie została jeszcze zaimplementowana wszystkie składniki bezpieczny dostęp?

**Odpowiedź:** Zdefiniuj co najmniej dwóch break szkła konta, przypisz MFA do kont uprzywilejowanych administratorów i oddzielnych kont użytkowników z konta administratora globalnego.

**Pyt.:** Po naruszeniu zabezpieczeń co to jest najważniejsze problem, który trzeba najpierw usunąć?

**Odpowiedź:** Upewnij się, że jest wymaganie najsilniejszych uwierzytelniania dla osób, narażone.

**Pyt.:** Co się stanie, jeśli zostały dezaktywowane naszych uprzywilejowanych administratorów?

**Odpowiedź:** Tworzenie konta administratora globalnego, który jest zawsze są aktualne.

**Pyt.:** Co się stanie, jeśli istnieje tylko jeden globalnego administratora po lewej stronie i nie można nawiązać połączenia? 

**Odpowiedź:** Aby uzyskać natychmiastowy dostęp uprzywilejowany, użyj jednego z kont break szkła.

**Pyt.:** Jak chronić Administratorzy mojej organizacji?

**Odpowiedź:** Istnieją Administratorzy, które zawsze wykonuj ich codziennych jako standardowych użytkowników "nieuprzywilejowanego".

**Pyt.:** Jakie są najlepsze rozwiązania dotyczące tworzenia kont administratora w usłudze Azure AD?

**Odpowiedź:** Rezerwa uprzywilejowany dostęp do zadań administracyjnych określonych.

**Pyt.:** Jakich narzędzi istnieje w celi zmniejszenia dostępu administratora trwałego?

**Odpowiedź:** Privileged Identity Management (PIM) i Azure AD ról administratora.

**Pyt.:** Co to jest pozycji firmy Microsoft, na synchronizowanie kont administratorów do usługi Azure AD?

**Odpowiedź:** Warstwa 0 kont administratorów (w tym kont, grupy i inne zasoby, które mają bezpośrednich lub pośrednich kontrolę administracyjną nad lasem usługi AD, domeny lub kontrolerów domeny i wszystkimi zasobami) są wykorzystywane tylko w przypadku lokalnego konta usługi AD i są zazwyczaj nie synchronizowane dla usługi Azure AD w chmurze.

**Pyt.:** Jak możemy zapewnić Administratorzy przypisywaniu dostępu losowego administratora w portalu?

**Odpowiedź:** Użyj konta bez uprawnień dla wszystkich użytkowników i administratorów większość. Rozpocznij od tworzenia zasięgowi organizacji umożliwia określenie, które kilka kont administratorów powinien uprzywilejowanego. I Monitoruj użytkowników administracyjnych, nowo utworzony.

## <a name="next-steps"></a>Kolejne kroki

* [Microsoft Trust Center zabezpieczeń produktu](https://www.microsoft.com/trustcenter/security) — funkcje zabezpieczeń firmy Microsoft w chmurze produktów i usług

* [Microsoft Trust Center - zgodności](https://www.microsoft.com/trustcenter/compliance/complianceofferings) — wszechstronny zestaw ofert zgodności dla usług w chmurze firmy Microsoft

* [Wskazówki dotyczące sposobu przeprowadzania oceny ryzyka](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -zarządzania wymaganiami bezpieczeństwa i zgodności dla usług w chmurze firmy Microsoft

### <a name="other-microsoft-online-services"></a>Inne Microsoft Online Services

* [Usługa Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) — usługa Intune umożliwia zarządzanie urządzeniami przenośnymi, zarządzanie aplikacjami mobilnymi i możliwości zarządzania Komputerami z chmury.

* [Microsoft Dynamics 365 zabezpieczeń](https://www.microsoft.com/trustcenter/security/dynamics365-security) — Dynamics 365 to rozwiązanie oparte na chmurze firmy Microsoft, które łączy zarządzanie relacjami z klientami (CRM) i możliwości (ERP) do planowania zasobów przedsiębiorstwa.
