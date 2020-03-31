---
title: Najważniejsze wskazówki dotyczące bezpiecznego dostępu administratora — usługa Azure AD | Dokumenty firmy Microsoft
description: Upewnij się, że dostęp administracyjny organizacji i konta administracyjne są bezpieczne. Dla architektów systemów i profesjonalistów IT, którzy konfigurują usługi Azure AD, Azure i Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266275"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD

Bezpieczeństwo większości lub wszystkich zasobów biznesowych w nowoczesnej organizacji zależy od integralności uprzywilejowanych kont, które administrują systemami IT i nimi zarządzają. Złośliwi aktorzy, w tym cyberprzestępcy, często atakują konta administratorów i inne elementy uprzywilejowanego dostępu, aby spróbować szybko uzyskać dostęp do poufnych danych i systemów za pomocą ataków kradzieży poświadczeń. W przypadku usług w chmurze zapobieganie i reagowanie to wspólne obowiązki dostawcy usług w chmurze i klienta. Aby uzyskać więcej informacji na temat najnowszych zagrożeń dla punktów końcowych i chmury, zobacz [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Ten artykuł może pomóc w opracowaniu planu działania w kierunku zamknięcia luk między bieżącymi planami a wytycznymi opisanymi tutaj.

> [!NOTE]
> Firma Microsoft dba o najwyższy poziom zaufania, przejrzystości, zgodności z normami i zgodności z przepisami. Dowiedz się więcej o tym, jak globalny zespół reagowania na incydenty firmy Microsoft łagodzi skutki ataków na usługi w chmurze oraz jak zabezpieczenia są wbudowane w produkty biznesowe firmy Microsoft i usługi w chmurze w [centrum zaufania firmy Microsoft — zabezpieczenia](https://www.microsoft.com/trustcenter/security) i cele zgodności firmy Microsoft — [Zgodność](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
W przypadku większości organizacji bezpieczeństwo zasobów biznesowych zależy od integralności uprzywilejowanych kont, które administrują systemami IT i nimi zarządzają. Cyberprzestępcy koncentrują się na uprzywilejowanym dostępie do systemów infrastruktury (takich jak usługa Active Directory i usługa Azure Active Directory), aby uzyskać dostęp do poufnych danych organizacji. 

Tradycyjne podejścia, które koncentrują się na zabezpieczeniu punktów wejścia i wyjścia sieci jako głównego obwodu zabezpieczeń, są mniej skuteczne ze względu na wzrost wykorzystania aplikacji SaaS i urządzeń osobistych w Internecie. Naturalnym sposobem zastąpienia obwodowych zabezpieczeń sieci w złożonych nowoczesnych przedsiębiorstwach jest uwierzytelnianie i kontrola autoryzacji w warstwie tożsamości organizacji.

Uprzywilejowane konta administracyjne skutecznie kontrolują ten nowy "obwód zabezpieczeń". Ochrona uprzywilejowanego dostępu jest bardzo ważne niezależnie od tego, czy środowisko jest usługami lokalnymi, chmurowymi czy hybrydowymi lokalnymi i hostowanymi w chmurze. Ochrona dostępu administracyjnego przed zdeterminowanymi przeciwnikami wymaga pełnego i przemyślanego podejścia do izolowania systemów organizacji od ryzyka. 

Zabezpieczenie uprzywilejowanego dostępu wymaga zmian

* Procesy, praktyki administracyjne i zarządzanie wiedzą
* Składniki techniczne, takie jak zabezpieczenia hosta, zabezpieczenia kont i zarządzanie tożsamościami

Ten dokument koncentruje się przede wszystkim na tworzeniu planu działania w celu zabezpieczenia tożsamości i dostępu, które są zarządzane lub zgłaszane w usłudze Azure AD, Microsoft Azure, Office 365 i innych usługach w chmurze. W przypadku organizacji, które mają lokalne konta administracyjne, zobacz wskazówki dotyczące lokalnego i hybrydowego dostępu uprzywilejowanego zarządzanego z usługi Active Directory w [zabezpieczaniu dostępu uprzywilejowanego](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Wskazówki zawarte w tym artykule odnosi się przede wszystkim do funkcji usługi Azure Active Directory, które są zawarte w planach usługi Azure Active Directory Premium P1 i P2. Usługa Azure Active Directory Premium P2 znajduje się w pakiecie EMS E5 i pakiecie Microsoft 365 E5. W tej wskazówki przyjęto założenie, że twoja organizacja ma już licencje usługi Azure AD Premium P2 zakupione dla użytkowników. Jeśli nie masz tych licencji, niektóre wskazówki mogą nie dotyczyć twojej organizacji. Ponadto w tym artykule termin administrator globalny (lub administrator globalny) jest synonimem "administrator firmy" lub "administrator dzierżawy".

## <a name="develop-a-roadmap"></a>Opracowanie planu działania 

Firma Microsoft zaleca opracowanie i wykonanie planu działania w celu zabezpieczenia uprzywilejowanego dostępu przed cyberprzestępcami. Zawsze możesz dostosować plan działania, aby dostosować go do istniejących możliwości i określonych wymagań w organizacji. Każdy etap planu działania powinien zwiększyć koszty i trudności przeciwników w atakowaniu uprzywilejowanego dostępu do zasobów lokalnych, chmurowych i hybrydowych. Firma Microsoft zaleca następujące cztery etapy planu działania: Ten zalecany plan planowania planuje najpierw najbardziej skuteczne i najszybsze implementacje, w oparciu o doświadczenia firmy Microsoft z implementacją incydentów i odpowiedzi związanych z cyberatakami. Terminy dla tego planu działania są przybliżone.

![Etapy planu działania z liniami czasowymi](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Etap 1 (24-48 godzin): Krytyczne przedmioty, które zalecamy od razu

* Etap 2 (2-4 tygodnie): Łagodzi najczęściej stosowane techniki ataku

* Etap 3 (1-3 miesiące): Budowanie widoczności i budowanie pełnej kontroli nad aktywnością administratorów

* Etap 4 (sześć miesięcy i dłużej): Kontynuuj budowanie zabezpieczeń, aby jeszcze bardziej wzmocnić platformę bezpieczeństwa

Ta struktura planu działania została zaprojektowana w celu zmaksymalizowania użycia technologii firmy Microsoft, które zostały już wdrożone. Można również skorzystać z kluczowych bieżących i nadchodzących technologii zabezpieczeń i zintegrować narzędzia zabezpieczeń od innych dostawców, które zostały już wdrożone lub rozważają wdrożenie. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Etap 1: Krytyczne przedmioty, które zalecamy od razu

![Etap 1 Przedmioty krytyczne do zrobienia w pierwszej kolejności](./media/directory-admin-roles-secure/stage-one.png)

Etap 1 planu działania koncentruje się na krytycznych zadaniach, które są szybkie i łatwe do wdrożenia. Zalecamy wykonanie tych kilku elementów od razu w ciągu pierwszych 24-48 godzin, aby zapewnić podstawowy poziom bezpiecznego uprzywilejowanego dostępu. Ten etap planu bezpiecznego dostępu uprzywilejowanego obejmuje następujące działania:

### <a name="general-preparation"></a>Przygotowanie ogólne

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Włączanie zarządzania tożsamościami uprzywilejowanymi usługi Azure AD

Jeśli nie włączyłeś jeszcze usługi Azure AD Privileged Identity Management (PIM), zrób to w dzierżawie produkcyjnej. Po włączeniu zarządzania tożsamościami uprzywilejowanymi otrzymasz wiadomości e-mail z powiadomieniami o zmianach ról dostępu uprzywilejowanego. Te powiadomienia zapewniają wczesne ostrzeganie, gdy dodatkowi użytkownicy są dodawane do ról o wysokim uprzywilejowanich w katalogu.

Usługa Azure AD Privileged Identity Management jest uwzględniona w usłudze Azure AD Premium P2 lub EMS E5. Te rozwiązania ułatwiają ochronę dostępu do aplikacji i zasobów w środowisku lokalnym i w chmurze. Jeśli nie masz jeszcze usługi Azure AD Premium P2 lub EMS E5 i chcesz ocenić więcej funkcji, o których mowa w tym planie działania, zarejestruj się w [bezpłatnej 90-dniowej wersji próbnej Enterprise Mobility + Security.](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial) Te próby licencjonowania można wypróbować usługę Azure AD Privileged Identity Management i usługę Azure AD Identity Protection w celu monitorowania aktywności przy użyciu zaawansowanego raportowania zabezpieczeń, inspekcji i alertów usługi Azure AD.

Po włączeniu zarządzania tożsamościami uprzywilejowanymi usługi Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy za pomocą konta, które jest globalnym administratorem dzierżawy produkcyjnej.

2. Aby wybrać dzierżawę, w której chcesz używać zarządzania tożsamościami uprzywilejowanymi, wybierz nazwę użytkownika w prawym górnym rogu witryny Azure portal.

3. W menu portalu platformy Azure wybierz **pozycję Wszystkie usługi** i przefiltruj listę **zarządzania tożsamościami uprzywilejowanymi usługą Azure AD**.

4. Otwórz zarządzanie tożsamościami uprzywilejowanymi z listy **Wszystkie usługi** i przypnij je do pulpitu nawigacyjnego.

Pierwsza osoba korzystająca z usługi Azure AD Privileged Identity Management w dzierżawie jest automatycznie przypisywana **administratorowi zabezpieczeń** i **uprzywilejowanym administratorem** ról w dzierżawie. Tylko administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami ról katalogu usługi Azure AD użytkowników. Ponadto po dodaniu usługi Azure AD uprzywilejowanego zarządzania tożsamościami, są wyświetlane kreator zabezpieczeń, który przeprowadzi Cię przez początkowe środowisko odnajdywania i przypisywania. W tej chwili można zamknąć kreatora bez wprowadzania dodatkowych zmian. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identyfikowanie i kategoryzowanie kont, które znajdują się w rolach o wysokim stopniu uprzywilejowania 

Po włączeniu zarządzania tożsamościami uprzywilejowanymi usługi Azure AD wyświetl użytkowników, którzy znajdują się w rolach katalogowych Administrator globalny, Administrator ról uprzywilejowanych, administrator usługi Exchange Online i administrator usługi SharePoint Online. Jeśli nie masz usługi Azure AD PIM w dzierżawie, można użyć [interfejsu API programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Zacznij od roli administratora globalnego, ponieważ ta rola jest ogólna: użytkownik, któremu przypisano tę rolę administratora, ma takie same uprawnienia we wszystkich usługach w chmurze, do których twoja organizacja została subskrybowana, niezależnie od tego, czy przypisano im tę rolę w usłudze Microsoft 365 centrum administracyjne, witryny Azure portal lub przy użyciu modułu usługi Azure AD dla programu Microsoft PowerShell. 

Usuń wszystkie konta, które nie są już potrzebne w tych rolach. Następnie kategoryzuj pozostałe konta przypisane do ról administratora:

* Indywidualnie przypisane do użytkowników administracyjnych i mogą być również używane do celów nieadyrnicznych (na przykład osobistych wiadomości e-mail)
* Indywidualnie przypisane użytkownikom administracyjnym i wyznaczone wyłącznie do celów administracyjnych
* Współużytkowane przez wielu użytkowników
* W przypadku scenariuszy awaryjnego dostępu do szkła łamania
* Dla skryptów automatycznych
* Dla użytkowników zewnętrznych

#### <a name="define-at-least-two-emergency-access-accounts"></a>Zdefiniuj co najmniej dwa konta dostępu awaryjnego 

Upewnij się, że nie dostaniesz się do sytuacji, w której mogą one przypadkowo zablokować administrowanie dzierżawy usługi Azure AD z powodu niemożności zalogowania się lub aktywowania konta istniejącego użytkownika jako administratora. Na przykład jeśli organizacja jest sfederowana do lokalnego dostawcy tożsamości, ten dostawca tożsamości może być niedostępny, więc użytkownicy nie mogą logować się lokalnie. Można złagodzić wpływ przypadkowego braku dostępu administracyjnego, przechowując co najmniej dwa konta dostępu awaryjnego w dzierżawie.

Konta dostępu awaryjnego pomagają organizacjom ograniczyć uprzywilejowany dostęp w istniejącym środowisku usługi Azure Active Directory. Konta te są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych w przypadku scenariuszy "break glass", w których nie można używać zwykłych rachunków administracyjnych. Organizacje muszą zapewnić cel kontrolowania i zmniejszania wykorzystania konta awaryjnego tylko do czasu, w którym jest to konieczne.

Oceń konta, które są przypisane lub kwalifikują się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w chmurze przy użyciu domeny *.onmicrosoft.com (przeznaczonej do dostępu awaryjnego "break glass"), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Włącz uwierzytelnianie wieloskładnikowe i zarejestruj wszystkie inne konta administratorów niesfederowanych z jednym użytkownikiem o wysokim stopniu uprawnień

Wymagaj uwierzytelniania wieloskładnikowego platformy Azure podczas logowania dla wszystkich indywidualnych użytkowników, którzy są trwale przypisani do jednej lub więcej ról administratora usługi Azure AD: administratora globalnego, administratora ról uprzywilejowanych, administratora usługi Exchange Online i programu SharePoint Administrator online. Skorzystaj z przewodnika, aby włączyć [uwierzytelnianie wieloskładnikowe (MFA) dla kont administratora](../authentication/howto-mfa-userstates.md) i upewnić się, że wszyscy ci użytkownicy zarejestrowali się w [https://aka.ms/mfasetup](https://aka.ms/mfasetup)pliku . Więcej informacji można znaleźć w kroku 2 i kroku 3 [przewodnika Ochrona dostępu do danych i usług w usłudze Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etap 2: Ograniczenie najczęściej używanych technik ataku

![Etap 2 Łagodzenie często używanych ataków](./media/directory-admin-roles-secure/stage-two.png)

Etap 2 planu koncentruje się na łagodzeniu najczęściej używanych technik ataku kradzieży poświadczeń i nadużyć i może być wdrożony w ciągu około 2-4 tygodni. Ten etap planu bezpiecznego dostępu uprzywilejowanego obejmuje następujące działania.

### <a name="general-preparation"></a>Przygotowanie ogólne

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Prowadzenie spisu usług, właścicieli i administratorów

Wraz ze wzrostem zasad "bring-your-own-device" (BYOD) i work-from-home oraz wzrostem łączności bezprzewodowej w firmach, bardzo ważne jest, aby monitorować, kto łączy się z twoją siecią. Skuteczny audyt zabezpieczeń często ujawnia urządzenia, aplikacje i programy uruchomione w sieci, które nie są obsługiwane przez firmę IT, a zatem potencjalnie nie są bezpieczne. Aby uzyskać więcej informacji, zobacz [omówienie zarządzania zabezpieczeniami i monitorowania platformy Azure](../../security/fundamentals/management-monitoring-overview.md). Upewnij się, że w procesie magazynowania uwzględnisz wszystkie następujące zadania. 

* Zidentyfikuj użytkowników, którzy mają role administracyjne i usługi, którymi mogą zarządzać.
* Użyj usługi Azure AD PIM, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do usługi Azure AD, w tym dodatkowe role poza tymi wymienionymi w etapie 1.
* Poza rolami zdefiniowanymi w usłudze Azure AD usługa Office 365 zawiera zestaw ról administratora, które można przypisać użytkownikom w organizacji. Każda rola administratora jest mapowana na typowe funkcje biznesowe i daje osobom w organizacji uprawnienia do wykonywania określonych zadań w [centrum administracyjnym usługi Microsoft 365](https://admin.microsoft.com). Użyj centrum administracyjnego usługi Microsoft 365, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do usługi Office 365, w tym za pośrednictwem ról niezarządzane w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Role administratora usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) i [najważniejsze wskazówki dotyczące zabezpieczeń dla usługi Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Wykonywanie spisu w innych usługach, na podstawie której opiera się organizacja, takich jak Azure, Intune lub Dynamics 365.
* Upewnij się, że konta administratora (konta, które są używane do celów administracyjnych, a nie tylko codzienne konta użytkowników) mają działające adresy e-mail dołączone do nich i zarejestrowały się dla usługi Azure MFA lub użyj usługi MFA w środowisku lokalnym.
* Poproś użytkowników o uzasadnienie biznesowe dostępu administracyjnego.
* Usuń dostęp administratora dla osób i usług, które go nie potrzebują.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identyfikowanie kont Microsoft na stanowiskach administracyjnych, które należy przełączyć na konta służbowe lub szkolne

Czasami początkowe administratorzy globalni dla organizacji ponownie użyć istniejących poświadczeń konta Microsoft, gdy zaczęli używać usługi Azure AD. Te konta Microsoft powinny zostać zastąpione przez indywidualne konta w chmurze lub zsynchronizowane. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zapewnianie oddzielnych kont użytkowników i przekazywania poczty dla globalnych kont administratorów 

Konta administratorów globalnych nie powinny mieć osobistych adresów e-mail, ponieważ osobiste konta e-mail są regularnie wygładzone przez cyberprzestępców. Aby ułatwić oddzielenie zagrożeń internetowych (ataki phishingowe, niezamierzone przeglądanie stron internetowych) od uprawnień administracyjnych, utwórz dedykowane konta dla każdego użytkownika z uprawnieniami administracyjnymi. 

Jeśli jeszcze tego nie zrobiono, utwórz oddzielne konta dla użytkowników do wykonywania globalnych zadań administracyjnych, aby upewnić się, że nie przypadkowo otwierają wiadomości e-mail ani nie uruchamiają programów skojarzonych z ich kontami administratora. Upewnij się, że te konta mają wiadomość e-mail przekazyconą do działającej skrzynki pocztowej.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Upewnij się, że hasła kont administracyjnych zostały ostatnio zmienione

Upewnij się, że wszyscy użytkownicy zalogowali się na swoje konta administracyjne i zmienili swoje hasła co najmniej raz w ciągu ostatnich 90 dni. Upewnij się również, że wszystkie współdzielone konta, w których wielu użytkowników wie, że hasło zostało ostatnio zmienione.

#### <a name="turn-on-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

Synchronizacja skrótów haseł to funkcja służąca do synchronizowania skrótów skrótów haseł użytkownika z lokalnego wystąpienia usługi Active Directory do wystąpienia usługi Azure AD opartej na chmurze. Nawet jeśli zdecydujesz się używać federacji z usługami federacyjnymi Active Directory (AD FS) lub innymi dostawcami tożsamości, opcjonalnie możesz skonfigurować synchronizację skrótów haseł jako kopię zapasową w przypadku awarii infrastruktury lokalnej, takiej jak serwery USŁUGI AD lub ADFS, lub tymczasowo niedostępne. Dzięki temu użytkownicy mogą logować się do usługi przy użyciu tego samego hasła, którego używają do logowania się do lokalnego wystąpienia usługi AD. Ponadto umożliwia ochronę tożsamości do wykrywania poświadczeń, które zostały naruszone, porównując te skróty haseł z hasłami, o których wiadomo, że zostały naruszone, jeśli użytkownik wykorzystał ten sam adres e-mail i hasło w innych usługach niepodłączonych do usługi Azure AD.  Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla użytkowników we wszystkich rolach uprzywilejowanych, a także dla użytkowników narażonych

Usługa Azure AD zaleca, aby wymagać uwierzytelniania wieloskładnikowego (MFA) dla wszystkich użytkowników, w tym administratorów i wszystkich innych użytkowników, którzy mieliby znaczący wpływ, jeśli ich konto zostało naruszone (na przykład urzędników finansowych). Zmniejsza to ryzyko ataku z powodu złamanego hasła.

Włącz:

* [Usługa MFA przy użyciu zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) dla wszystkich użytkowników w organizacji.

Jeśli używasz funkcji Windows Hello dla firm, wymóg usługi MFA można spełnić przy użyciu środowiska logowania windows hello. Aby uzyskać więcej informacji, zobacz [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurowanie ochrony tożsamości 

Usługa Azure AD Identity Protection to narzędzie do monitorowania i raportowania oparte na algorytmach, którego można użyć do wykrywania potencjalnych luk w zabezpieczeniach mających wpływ na tożsamość organizacji. Można skonfigurować automatyczne odpowiedzi na te wykryte podejrzane działania i podjąć odpowiednie działania, aby je rozwiązać. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection (Ochrona tożsamości w usłudze Azure Active Directory)](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Uzyskaj bezpieczny wynik usługi Office 365 (w przypadku korzystania z usługi Office 365)

Secure Score określa, z jakich usług usługi Office 365 korzystasz (takich jak OneDrive, SharePoint i Exchange), a następnie analizuje ustawienia i działania oraz porównuje je z linią bazową ustaloną przez firmę Microsoft. Otrzymasz wynik na podstawie tego, jak wyrównany jesteś z najlepszymi praktykami w zakresie zabezpieczeń. Każdy, kto ma uprawnienia administratora (administratora globalnego lub niestandardowego administratora) dla subskrypcji usługi [https://securescore.office.com](https://securescore.office.com/)Office 365 Business Premium lub Enterprise, może uzyskać dostęp do bezpiecznego wyniku w programie .

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń i zgodności usługi Office 365 (w przypadku korzystania z usługi Office 365)

[Plan zabezpieczeń i zgodności](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) przedstawia podejście do sposobu, w jaki klient usługi Office 365 powinien skonfigurować pakiet Office 365 i wykorzystać inne funkcje EMS. Następnie zapoznaj się z krokami 3-6 dotyczącymi [ochrony dostępu do danych i usług w usłudze Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) oraz przewodnikiem dotyczącym monitorowania zabezpieczeń i zgodności w [usłudze Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurowanie monitorowania aktywności usługi Office 365 (w przypadku korzystania z usługi Office 365)

Możesz monitorować, jak osoby w organizacji korzystają z usług Office 365, umożliwiając identyfikację użytkowników, którzy mają konto administracyjne i którzy mogą nie potrzebować dostępu do usługi Office 365 z powodu niezalogowania się do tych portali. Aby uzyskać więcej informacji, zobacz [Raporty aktywności w centrum administracyjnym usługi Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Ustanowienie właścicieli planów reagowania na incydenty/sytuacje awaryjne

Skuteczne reagowanie na incydenty jest złożonym przedsięwzięciem. W związku z tym ustanowienie pomyślnej zdolności reagowania na incydenty wymaga znacznego planowania i zasobów. Ważne jest, aby stale monitorować cyberataki i ustanowić procedury priorytetowego postępowania z incydentami. Skuteczne metody zbierania, analizowania i raportowania danych są niezbędne do budowania relacji i nawiązywania komunikacji z innymi grupami wewnętrznymi i właścicielami planów. Aby uzyskać więcej informacji, zobacz [Centrum odpowiedzi zabezpieczeń firmy Microsoft](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Bezpieczne konta administracyjne uprzywilejowane w środowisku lokalnym, jeśli nie zostały jeszcze wykonane

Jeśli dzierżawa usługi Azure Active Directory jest synchronizowana z lokalną usługą Active Directory, postępuj zgodnie ze wskazówkami zawartymi w [planie dostępu uprzywilejowanego zabezpieczeń:](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)etap 1. Obejmuje to tworzenie oddzielnych kont administratorów dla użytkowników, którzy muszą wykonywać lokalne zadania administracyjne, wdrażanie stacji roboczych dostępu uprzywilejowanego dla administratorów usługi Active Directory oraz tworzenie unikatowych haseł administratorów lokalnych dla stacji roboczych i Serwerów.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Uzupełnij spis subskrypcji

Użyj portalu enterprise i witryny Azure portal, aby zidentyfikować subskrypcje w organizacji, które hostują aplikacje produkcyjne.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Usuwanie kont Microsoft z ról administratora

Konta Microsoft z innych programów, takich jak Xbox, Live i Outlook, nie powinny być używane jako konta administratorów dla subskrypcji organizacji. Usuń stan administratora ze wszystkich kont Microsoft i zastąp kontami służbowymi usługi Azure Active Directory (na chris@contoso.comprzykład) lub kontami szkolnymi.

#### <a name="monitor-azure-activity"></a>Monitorowanie aktywności platformy Azure

Dziennik aktywności platformy Azure zawiera historię zdarzeń na poziomie subskrypcji na platformie Azure. Zawiera informacje o tym, kto utworzył, zaktualizował i usunął jakie zasoby i kiedy wystąpiły te zdarzenia. Aby uzyskać więcej informacji, zobacz [Inspekcja i odbieranie powiadomień o ważnych akcjach w ramach subskrypcji platformy Azure.](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do innych aplikacji w chmurze za pośrednictwem usługi Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurowanie zasad dostępu warunkowego

Przygotuj zasady dostępu warunkowego dla aplikacji lokalnych i hostowanych w chmurze. Jeśli masz użytkowników przyłączonych do miejsca pracy urządzeń, uzyskaj więcej informacji z [konfigurowania lokalnego dostępu warunkowego przy użyciu rejestracji urządzeń usługi Azure Active Directory](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Etap 3: Budowanie widoczności i przejęcie pełnej kontroli nad aktywnością administratorów

![Etap 3 przejmie kontrolę nad aktywnością administratora](./media/directory-admin-roles-secure/stage-three.png)

Etap 3 opiera się na czynnikach ograniczających zagrożenie z etapu 2 i ma zostać wdrożony w ciągu około 1-3 miesięcy. Ten etap planu bezpiecznego dostępu uprzywilejowanego obejmuje następujące składniki.

### <a name="general-preparation"></a>Przygotowanie ogólne

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Ukończ przegląd dostępu użytkowników w rolach administratora

Więcej użytkowników korporacyjnych uzyskuje uprzywilejowany dostęp za pośrednictwem usług w chmurze, co może prowadzić do rosnącej platformy niezarządzanej. Obejmuje to użytkowników, którzy stają się administratorami globalnymi dla usługi Office 365, administratorów subskrypcji platformy Azure i użytkowników, którzy mają dostęp administratorów do maszyn wirtualnych lub za pośrednictwem aplikacji SaaS. Zamiast tego organizacje powinny mieć wszystkich pracowników, zwłaszcza administratorów, obsługiwać codzienne transakcje biznesowe jako nieuprzywilejowanych użytkowników i tylko w razie potrzeby przyjmować prawa administratora. Ponieważ liczba użytkowników w rolach administratora może wzrosła od początkowego przyjęcia, pełne przeglądy dostępu, aby zidentyfikować i potwierdzić każdego użytkownika, który jest uprawniony do aktywacji uprawnień administratora. 

Wykonaj następujące czynności:

* Określ, którzy użytkownicy są administratorami usługi Azure AD, włącz dostęp administratora na żądanie, tylko w czasie i kontrolki zabezpieczeń oparte na rolach.
* Konwertuj użytkowników, którzy nie mają wyraźnego uzasadnienia dla uprzywilejowanego dostępu administratora do innej roli (jeśli nie ma kwalifikującej się roli, usuń ich).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Kontynuuj wdrażanie silniejszego uwierzytelniania dla wszystkich użytkowników 

Wymagaj, aby kadra kierownicza pakietu C, menedżerowie wysokiego szczebla, krytyczni pracownicy IT i zabezpieczeń oraz inni użytkownicy o wysokiej ekspozycji mieli nowoczesne, silne uwierzytelnianie, takie jak usługa Azure MFA lub Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Używanie dedykowanych stacji roboczych do administrowania usługą Azure AD

Osoby atakujące mogą próbować kierować reklamy na konta uprzywilejowane w celu uzyskania dostępu do danych i systemów organizacji, aby zakłócać integralność i autentyczność danych za pomocą złośliwego kodu, który zmienia logikę programu lub podsłuchuje administratora wprowadzającego poświadczenia. Stacje robocze z dostępem uprzywilejowanym (PAW, Privileged Access Workstation) zapewniają dedykowany system operacyjny do realizacji zadań poufnych, który jest zabezpieczony przed atakami internetowymi i wektorami zagrożenia. Oddzielenie poufnych zadań i kont od stacji roboczych i urządzeń wykorzystywanych na co dzień zapewnia bardzo wysoki poziom ochrony przed atakami mającymi na celu wyłudzenie informacji, lukami w zabezpieczeniach systemu operacyjnego i aplikacji oraz różnego rodzaju atakami związanymi z podszywaniem się pod użytkownika. Chroni także przed kradzieżą poświadczeń, na przykład w formie ataków wykorzystujących metodę rejestrowania naciskanych klawiszy oraz ataków typu Pass-the-Hash i Pass-The-Ticket. Wdrażając stacje robocze dostępu uprzywilejowanego, można zmniejszyć ryzyko, że administratorzy wejdą poświadczenia administratora, z wyjątkiem środowiska pulpitu, które zostało wzmocnione. Aby uzyskać więcej informacji, zobacz [Stacje robocze dostępu uprzywilejowanego](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Przegląd zaleceń Narodowego Instytutu Standardów i Technologii dotyczących postępowania z incydentami 

National Institute of Standards and Technology's (NIST) zawiera wytyczne dotyczące postępowania w przypadku incydentów, w szczególności do analizy danych związanych z incydentami i określenia odpowiedniej reakcji na każdy incydent. Aby uzyskać więcej informacji, zobacz [(NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementowanie uprzywilejowanego zarządzania tożsamościami (PIM) dla JIT do dodatkowych ról administracyjnych

W przypadku usługi Azure Active Directory użyj funkcji [zarządzania tożsamościami uprzywilejowanymi usługą Azure AD.](../privileged-identity-management/pim-configure.md) Ograniczona czasowo aktywacja ról uprzywilejowanych umożliwia:

* Aktywowanie uprawnień administratora w celu wykonania określonego zadania
* Wymuszanie usługi MFA podczas procesu aktywacji
* Używanie alertów do informowania administratorów o zmianach poza pasmem
* Umożliwianie użytkownikom zachowania pewnych uprawnień przez wstępnie skonfigurowany czas
* Zezwalaj administratorom zabezpieczeń na odnajdywanie wszystkich uprzywilejowanych tożsamości, wyświetlanie raportów inspekcji i tworzenie recenzji dostępu w celu zidentyfikowania każdego użytkownika, który kwalifikuje się do aktywowania uprawnień administratora

Jeśli korzystasz już z usługi Azure AD Privileged Identity Management, dostosuj przedziały czasowe dla uprawnień związanych z czasem w razie potrzeby (na przykład okna konserwacji).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Określanie narażenia na protokoły logowania oparte na hasłach (w przypadku korzystania z usługi Exchange Online)

W przeszłości protokoły zakładały, że kombinacje nazwy użytkownika i hasła były osadzone w urządzeniach, kontach e-mail, telefonach i tak dalej. Ale teraz z ryzykiem cyberataków w chmurze, zalecamy zidentyfikowanie każdego potencjalnego użytkownika, który, jeśli ich poświadczenia zostały naruszone, może być katastrofalne dla organizacji, i wykluczyć je z możliwości logowania się do poczty e-mail za pośrednictwem nazwy użytkownika / hasła, implementując silne wymagania uwierzytelniania i dostępu warunkowego. Uwierzytelnianie starsze można blokować [przy użyciu programu Dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Sprawdź szczegóły [dotyczące blokowania uwierzytelniania podstawowego](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) za pośrednictwem usługi Exchange online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Ukończ ocenę przeglądu ról dla ról usługi Office 365 (w przypadku korzystania z usługi Office 365)

Oceń, czy wszyscy użytkownicy administratorzy mają poprawne role (usuń i ponownie przypisz zgodnie z tą oceną).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Przejrzyj podejście do zarządzania zdarzeniami zabezpieczeń stosowane w usłudze Office 365 i porównaj je z własną organizacją

Ten raport można pobrać z programu [Zarządzanie zdarzeniami zabezpieczeń w usłudze Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Dalsze zabezpieczanie lokalnych uprzywilejowanych kont administracyjnych

Jeśli usługa Azure Active Directory jest połączona z lokalną usługą Active Directory, postępuj zgodnie ze wskazówkami [w planie dostępu uprzywilejowanego zabezpieczeń:](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)Etap 2. Obejmuje to wdrażanie stacje robocze dostępu uprzywilejowanego dla wszystkich administratorów, wymaganie usługi MFA, używanie wystarczającej liczby administratorów kontrolera domeny do konserwacji kontrolera domeny, obniżanie powierzchni ataku domen, wdrażanie usługi ATA w celu wykrywania ataków.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure

#### <a name="establish-integrated-monitoring"></a>Ustanowienie zintegrowanego monitorowania

[Usługa Azure Security Center](../../security-center/security-center-intro.md) zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inwentaryzacja kont uprzywilejowanych w obsługiwanych maszynach wirtualnych

W większości przypadków nie trzeba przyznawać użytkownikom nieograniczonych uprawnień do wszystkich subskrypcji platformy Azure lub zasobów. Za pomocą ról administratora usługi Azure AD można segregować obowiązki w organizacji i przyznać tylko ilość dostępu użytkownikom, którzy muszą wykonywać określone zadania. Na przykład użyj ról administratora usługi Azure AD, aby jeden administrator zarządzał tylko maszynami wirtualnymi w ramach subskrypcji, podczas gdy inny może zarządzać bazami danych SQL w ramach tej samej subskrypcji. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do kontroli dostępu opartej na rolach w witrynie Azure portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementowanie ról administratora usługi Azure AD dla usługi PiM

Zarządzanie tożsamościami uprzywilejowanymi za pomocą ról administratora usługi Azure AD umożliwia zarządzanie, kontrolowanie i monitorowanie dostępu do zasobów platformy Azure. Korzystanie z usługi PIM chroni uprzywilejowane konta przed cyberatakami, obniżając czas narażenia uprawnień i zwiększając wgląd w ich wykorzystanie za pomocą raportów i alertów. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem RBAC do zasobów platformy Azure za pomocą zarządzania tożsamościami uprzywilejowanymi](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Wysyłanie odpowiednich dzienników platformy Azure do systemów SIEM za pomocą funkcji azure 

Integracja z dziennikami platformy Azure umożliwia integrację nieprzetworzonych dzienników z zasobów platformy Azure z istniejącymi systemami zarządzania informacjami i zdarzeniami (SIEM) w organizacji. [Integracja z dziennikami platformy Azure](../../security/fundamentals/azure-log-integration-overview.md) zbiera zdarzenia systemu Windows z dzienników Podglądu zdarzeń systemu Windows i zasobów platformy Azure z dzienników aktywności platformy Azure, alertów Usługi Azure Security Center i dzienników diagnostyki platformy Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do innych aplikacji w chmurze za pośrednictwem usługi Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Wdrażanie inicjowania obsługi administracyjnej dla połączonych aplikacji

Usługa Azure AD umożliwia automatyzację tworzenia, konserwacji i usuwania tożsamości użytkowników w aplikacjach chmurze (SaaS), takich jak Dropbox, Salesforce, ServiceNow i tak dalej. Aby uzyskać więcej informacji, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integracja ochrony informacji

Usługa MCAS umożliwia badanie plików i ustawianie zasad na podstawie etykiet klasyfikacji usługi Azure Information Protection, co umożliwia lepszą widoczność i kontrolę nad danymi w chmurze. Skanowanie i klasyfikowanie plików w chmurze i stosowanie etykiet ochrony informacji platformy Azure. Aby uzyskać więcej informacji, zobacz [integracja usługi Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Skonfiguruj dostęp warunkowy na podstawie wrażliwości grupy, lokalizacji i aplikacji dla [aplikacji SaaS](https://azure.microsoft.com/overview/what-is-saas/) i aplikacji połączonych z usługą Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorowanie aktywności w połączonych aplikacjach w chmurze

Aby zapewnić ochronę dostępu użytkowników również w połączonych aplikacjach, zalecamy korzystanie z programu [Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) Zabezpiecza to dostęp przedsiębiorstwa do aplikacji w chmurze, oprócz zabezpieczania kont administratorów, umożliwiając:

* Rozszerzanie widoczności i kontroli na aplikacje w chmurze
* Tworzenie zasad dostępu, działań i udostępniania danych
* Automatyczne identyfikowanie ryzykownych działań, nietypowych zachowań i zagrożeń
* Zapobieganie wyciekom danych
* Minimalizowanie ryzyka i zautomatyzowane zapobieganie zagrożeniom i egzekwowanie zasad

Agent SIEM zabezpieczeń aplikacji w chmurze integruje usługę Cloud App Security z serwerem SIEM, aby umożliwić scentralizowane monitorowanie alertów i działań usługi Office 365. Działa na serwerze i pobiera alerty i działania z usługi Cloud App Security i przesyła je strumieniowo do serwera SIEM. Aby uzyskać więcej informacji, zobacz [integracja SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Etap 4: Kontynuuj budowanie obrony do bardziej proaktywnej postawy bezpieczeństwa

![Etap 4 przyjmuje proaktywną postawę bezpieczeństwa](./media/directory-admin-roles-secure/stage-four.png)

Etap 4 planu działania opiera się na widoczności z etapu 3 i ma zostać wdrożony w ciągu sześciu miesięcy i później. Ukończenie planu działania pomaga opracować silne zabezpieczenia uprzywilejowanego dostępu przed potencjalnymi atakami, które są obecnie znane i dostępne. Niestety zagrożenia bezpieczeństwa stale ewoluują i zmieniają się, dlatego zalecamy postrzeganie zabezpieczeń jako ciągłego procesu skoncentrowanego na podnoszeniu kosztów i zmniejszaniu wskaźnika sukcesu przeciwników ukierunkowanych na środowisko.

Zabezpieczenie uprzywilejowanego dostępu jest krytycznym pierwszym krokiem do ustanowienia zabezpieczeń dla zasobów biznesowych w nowoczesnej organizacji, ale nie jest jedyną częścią pełnego programu zabezpieczeń, który obejmowałby elementy, takie jak zasady, operacje, informacje zabezpieczenia, serwery, aplikacje, komputery, urządzenia, chmura szkieletowa i inne składniki zapewniają bieżące gwarancje bezpieczeństwa. 

Oprócz zarządzania kontami dostępu uprzywilejowanego zalecamy bieżące przeglądanie następujących czynności:

* Upewnij się, że administratorzy wykonują swoją codzienną działalność jako nieuprzywilejowani użytkownicy.
* Udzielaj uprzywilejowanego dostępu tylko wtedy, gdy jest to potrzebne, i usuń go później (just-in-time).
* Zachowaj i przejrzyj działania kontrolne związane z kontami uprzywilejowanymi.

Aby uzyskać więcej informacji na temat tworzenia kompletnego planu działania zabezpieczeń, zobacz [Zasoby architektury IT w chmurze firmy Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Aby uzyskać więcej informacji na temat angażowania usług firmy Microsoft w celu pomocy w dowolne z tych tematów, skontaktuj się z przedstawicielem firmy Microsoft lub zobacz [Tworzenie krytycznych cyberobrony w celu ochrony przedsiębiorstwa.](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)

Ten ostatni etap planu bezpiecznego dostępu uprzywilejowanego obejmuje następujące elementy.

### <a name="general-preparation"></a>Przygotowanie ogólne

#### <a name="review-admin-roles-in-azure-active-directory"></a>Przeglądanie ról administratora w usłudze Azure Active Directory 

Sprawdź, czy bieżące wbudowane role administratora usługi Azure AD są nadal aktualne i upewnij się, że użytkownicy są tylko w rolach i delegacjach, których potrzebują do odpowiednich uprawnień. Za pomocą usługi Azure AD można wyznaczyć oddzielnych administratorów do obsługi różnych funkcji. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Przeglądanie użytkowników, którzy mają administrowanie urządzeniami przyłączanym do usługi Azure AD

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować hybrydowe urządzenia przyłączone do usługi Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Przeglądanie członków [wbudowanych ról administratora usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Jeśli korzystasz z usługi Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Sprawdzanie poprawności planu reagowania na incydenty

Aby ulepszyć plan, firma Microsoft zaleca regularne sprawdzanie, czy plan działa zgodnie z oczekiwaniami:

* Przejdź przez istniejącą mapę drogową, aby zobaczyć, co zostało pominięte
* Na podstawie analizy pośmiertnej, zmiany istniejących lub zdefiniowania nowych najlepszych praktyk
* Upewnij się, że zaktualizowany plan reagowania na incydenty i najlepsze rozwiązania są rozpowszechniane w całej organizacji


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure 

Określ, czy musisz [przenieść własność subskrypcji platformy Azure na inne konto.](../../cost-management-billing/manage/billing-subscription-transfer.md)
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break glass": co robić w nagłych wypadkach

![Konta dostępu do szkła awaryjnego](./media/directory-admin-roles-secure/emergency.jpeg)

1. Powiadom kluczowych menedżerów i funkcjonariuszy ochrony o istotnych informacjach dotyczących incydentu.

2. Przejrzyj swój podręcznik ataku. 

3. Aby zalogować się do usługi Azure AD, przejdź do kombinacji nazwy użytkownika/hasła konta "break glass". 

4. Uzyskaj pomoc od firmy Microsoft, [otwierając żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Spójrz na [raporty logowania usługi Azure AD](../reports-monitoring/overview-reports.md). Może wystąpić opóźnienie między zdarzeniem występującym i po jego uwzględnieniu w raporcie.

6. W przypadku środowisk hybrydowych, jeśli federacyjne i serwer usług AD FS nie są dostępne, może być konieczne tymczasowe przełączenie się z uwierzytelniania federacyjnego na synchronizację hash haseł. Spowoduje to przywrócenie federacji domeny do uwierzytelniania zarządzanego, dopóki serwer usług AD FS nie stanie się dostępny.

7. Monitorowanie poczty e-mail dla kont uprzywilejowanych.

8. Upewnij się, że zapisujesz kopie zapasowe odpowiednich dzienników do potencjalnego dochodzenia kryminalistycznego i prawnego.

Aby uzyskać więcej informacji o tym, jak usługa Microsoft Office 365 obsługuje zdarzenia dotyczące zabezpieczeń, zobacz [Zarządzanie zdarzeniami zabezpieczeń w usłudze Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Często zadawane pytania: Często zadawane pytania dotyczące zabezpieczenia uprzywilejowanego dostępu  

**P.** Co zrobić, jeśli nie zaimplementowałem jeszcze żadnych składników bezpiecznego dostępu?

**Odpowiedź:** Zdefiniuj co najmniej dwa konto typu break-glass, przypisz uwierzytelnianie wieloskładnikowe do uprzywilejowanych kont administratora i oddziel konta użytkowników od kont administratorów globalnych.

**P.** Jaki jest problem, który należy rozwiązać w pierwszej kolejności po naruszeniu?

**Odpowiedź:** Upewnij się, że potrzebujesz najsilniejszego uwierzytelniania dla osób o wysokim stopniu narażenia.

**P.** Co się stanie, jeśli nasi uprzywilejowani administratorzy zostaną dezaktywowani?

**Odpowiedź:** Utwórz globalne konto administratora, które jest zawsze aktualne.

**P.** Co się stanie, jeśli pozostało tylko jedno administratora globalnego i nie można do niego dotrzeć? 

**Odpowiedź:** Użyj jednego ze swoich kont typu break-glass, aby uzyskać natychmiastowy uprzywilejowany dostęp.

**P.** Jak chronić administratorów w mojej organizacji?

**Odpowiedź:** Czy administratorzy zawsze wykonują swoją codzienną działalność jako standardowo "nieuprzywilejowanych" użytkowników.

**P.** Jakie są najlepsze rozwiązania dotyczące tworzenia kont administratorów w usłudze Azure AD?

**Odpowiedź:** Rezerwuj uprzywilejowany dostęp dla określonych zadań administratora.

**P.** Jakie narzędzia istnieją do ograniczania trwałego dostępu administratora?

**Odpowiedź:** Uprzywilejowane zarządzanie tożsamościami (PIM) i role administratora usługi Azure AD.

**P.** Jakie jest stanowisko firmy Microsoft dotyczące synchronizowania kont administratorów z usługą Azure AD?

**Odpowiedź:** Konta administratorów warstwy 0 (w tym konta, grupy i inne zasoby, które mają bezpośrednią lub pośrednią kontrolę administracyjną nad lasem, domenami lub kontrolerami domeny usługi AD i wszystkimi zasobami) są używane tylko dla lokalnych kont usługi AD i zazwyczaj nie są synchronizowane dla usługi Azure AD dla chmury.

**P.** W jaki sposób administratorzy nie przypisują losowego dostępu administratora w portalu?

**Odpowiedź:** Używaj kont nieuprzywilejowanych dla wszystkich użytkowników i większości administratorów. Zacznij od opracowania śladu organizacji, aby określić, które kilka kont administratora powinno być uprzywilejowanych. Monitoruj nowo utworzonych użytkowników administracyjnych.

## <a name="next-steps"></a>Następne kroki

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) — funkcje zabezpieczeń produktów i usług w chmurze firmy Microsoft

* [Microsoft Trust Center - Zgodność](https://www.microsoft.com/trustcenter/compliance/complianceofferings) — kompleksowy zestaw ofert zgodności firmy Microsoft dla usług w chmurze

* [Wskazówki dotyczące przeprowadzania oceny ryzyka](https://www.microsoft.com/trustcenter/guidance/risk-assessment) — zarządzanie wymaganiami dotyczącymi zabezpieczeń i zgodności usług w chmurze firmy Microsoft

### <a name="other-microsoft-online-services"></a>Inne usługi online firmy Microsoft

* [Usługa Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) — usługa Intune zapewnia zarządzanie urządzeniami przenośnymi, zarządzanie aplikacjami mobilnymi i zarządzanie komputerami z chmury.

* [Zabezpieczenia systemu Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) — Dynamics 365 to rozwiązanie chmurowe firmy Microsoft, które ujednolica funkcje zarządzania relacjami z klientami (CRM) i planowania zasobów przedsiębiorstwa (ERP).
