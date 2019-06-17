---
title: Planowanie usługi Azure Active Directory pojedynczego logowania jednokrotnego wdrożenia
description: Przewodnik, aby ułatwić planowanie, wdrażanie i Zarządzanie rejestracją Jednokrotną w Twojej organizacji.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd8cebbd7c60715bc90412d9f53458edfee6c56d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108197"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planowanie wdrożenia pojedynczego logowania jednokrotnego

Logowanie jednokrotne (SSO) oznacza, że dostęp do wszystkich aplikacji i zasobów użytkownik musi zalogować się tylko wtedy, gdy za pomocą jednego konta użytkownika. Przy użyciu logowania jednokrotnego użytkownicy mają dostęp do wszystkich niezbędnych aplikacji bez konieczności uwierzytelnienia po raz drugi.

## <a name="benefits-of-sso"></a>Korzyści wynikające z rejestracji Jednokrotnej

Logowanie jednokrotne (SSO) dodaje zabezpieczenia i wygodę, gdy użytkownicy zalogować się do aplikacji w usłudze Azure Active Directory (Azure AD). 

Wiele organizacji zależą od oprogramowania jako aplikacji usługi (SaaS), takich jak Office 365, Box i Salesforce, na produktywność użytkownika końcowego. W przeszłości indywidualnie tworzenia i aktualizowania kont użytkowników w każdej aplikacji SaaS i użytkowników potrzebnych do zapamiętania hasło dla każdego musieli pracowników wsparcia informatycznego.

W portalu Azure Marketplace ma ponad 3000 aplikacji za pomocą wstępnie zintegrowanych połączenia logowania jednokrotnego, co ułatwia ich integracji w dzierżawie.

## <a name="licensing"></a>Licencjonowanie

- **Licencjonowanie usługi Azure AD** — logowanie Jednokrotne do wstępnie zintegrowanych aplikacji SaaS jest bezpłatna. Jednak liczbę obiektów w katalogu i funkcje, które chcesz wdrożyć mogą być potrzebne dodatkowe licencje. Aby uzyskać pełną listę wymagań dotyczących licencji, zobacz [usługi Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licencjonowanie aplikacji** — należy odpowiednie licencje dla aplikacji SaaS, stosownie do potrzeb biznesowych. Praca z właścicielem aplikacji do określenia, czy użytkowników przypisanych do aplikacji mają odpowiednie licencje dla ich ról w ramach aplikacji. Jeśli usługa Azure AD zarządza automatyczną aprowizację na podstawie ról, role przypisane w usłudze Azure AD muszą być dopasowane liczbę licencji należące do aplikacji. Niewłaściwy liczbę licencji, których właścicielem w aplikacji może prowadzić do błędów podczas inicjowania obsługi administracyjnej/aktualizowania użytkownika.

## <a name="plan-your-sso-team"></a>Planowanie zespołowi logowania jednokrotnego

- **Współpraca z zainteresowanymi stronami prawo** — w przypadku technologii projektów kończyć się niepowodzeniem, zazwyczaj jest ono spowodowane niezgodnymi oczekiwania na wpływ, wyniki i odpowiedzialności. Aby uniknąć tych problemów [upewnij się, że jesteś interesujące prawo zainteresowane strony](https://aka.ms/deploymentplans) i czy zainteresowane strony zrozumienie ich ról.
- **Planowanie komunikacji** — komunikacja jest bardzo istotny dla sukcesu wszelkie nowe usługi. Proaktywnie komunikują się dotyczący użytkowników, jak zmieni się ich środowisko pracy, gdy ulegnie zmianie i sposób uzyskiwania pomocy technicznej w przypadku wystąpienia problemów. Zapoznaj się z opcjami dla [jak użytkownicy końcowi będą miały dostęp ich rejestracji Jednokrotnej aplikacjami obsługującymi](end-user-experiences.md)i tworzyć jego korespondencji, aby dopasować wybór. 

## <a name="plan-your-sso-protocol"></a>Planowanie protokołu usługi logowania jednokrotnego

Implementacja logowanie Jednokrotne oparte na protokołach Federacji zwiększa bezpieczeństwo, niezawodność, i środowisk dla użytkowników końcowych i jest prostsza do zaimplementowania. Wiele aplikacji są wstępnie zintegrowane w usłudze Azure AD za pomocą [krok po kroku zawiera informacje na temat dostępnych](../saas-apps/tutorial-list.md). Można je znaleźć na naszej [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Szczegółowe informacje na temat każdej metody logowania jednokrotnego, można znaleźć w artykule [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](what-is-single-sign-on.md).

Istnieją dwa podstawowe sposoby, w których umożliwia użytkownikom logowanie jednokrotne do aplikacji:

- **Za pomocą federacyjnego logowania jednokrotnego** usługi Azure AD uwierzytelnia użytkownika do aplikacji przy użyciu swojego konta usługi Azure AD. Ta metoda jest obsługiwana w przypadku aplikacji protokoły, takie jak SAML 2.0, WS-Federation i OpenID Connect, pomocy technicznej i najszerszym tryb logowania jednokrotnego. Zalecamy używanie Federacyjna usługa rejestracji Jednokrotnej z usługą Azure AD, gdy aplikacja ją obsługuje, zamiast na podstawie hasła logowania jednokrotnego i usług AD FS.

- **Za pomocą opartego na hasłach logowania jednokrotnego** użytkownicy logują się do aplikacji przy użyciu nazwy użytkownika i hasła po raz pierwszy, mogą uzyskać do niego dostęp. Po pierwsze logowanie jednokrotne usługi Azure AD zawiera nazwę użytkownika i hasło do aplikacji. Oparte na hasłach logowanie jednokrotne umożliwia bezpieczną aplikację przechowywanie i powtarzanie haseł przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Ta opcja wykorzystuje istniejący proces logowania w aplikacji umożliwia administratorowi Zarządzanie hasłami i nie wymaga, aby użytkownik znał hasło.

### <a name="considerations-for-federation-based-sso"></a>Uwagi dotyczące logowania jednokrotnego opartego na Federacji

- **Za pomocą protokołu OpenID Connect i OAuth** — w przypadku aplikacji łączysz się obsługuje, Włącz usługi logowania jednokrotnego do tej aplikacji przy użyciu metody OIDC/OAuth 2.0. Ta metoda wymaga mniej konfiguracji i umożliwia bogatszych środowisko użytkownika. Aby uzyskać więcej informacji, zobacz [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), i [przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Konfiguracje punktu końcowego dla rejestracji Jednokrotnej opartej na SAML** — Jeśli używasz protokołu SAML, deweloperzy należy określone informacje przed skonfigurowaniem aplikacji. Aby uzyskać więcej informacji, zobacz [skonfigurować podstawowe opcje SAML](configure-single-sign-on-portal.md).
- **Certyfikat zarządzania dla rejestracji Jednokrotnej opartej na SAML** — po włączeniu federacyjnej usługi logowania jednokrotnego dla aplikacji usługi Azure AD tworzy certyfikat, który jest domyślnie ważne przez trzy lata. Można dostosować daty wygaśnięcia dla tego certyfikatu, jeśli to konieczne. Upewnij się, że procesy odnawiania certyfikatów przed ich wygaśnięciem. Aby dowiedzieć się więcej, zobacz [usługi Azure AD zarządzanie certyfikatami](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Zagadnienia dotyczące SSO oparte na hasłach

Używanie programu Azure AD na podstawie hasła logowania jednokrotnego wymaga wdrażania rozszerzenia przeglądarki, która bezpiecznie pobierze poświadczenia i wypełnianie formularzy logowania. Zdefiniowanie zasad, aby wdrożyć rozszerzenie na dużą skalę z [obsługiwanych przeglądarek](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Opcje obejmują:

- [Zasady grupy dla programu Internet Explorer ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Program System Center Configuration Manager (SCCM) dla programu Internet Explorer ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Użytkownik, oparte na pobieranie i konfiguracji dla programu Chrome, Firefox, Microsoft Edge lub programu Internet Explorer ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Aby dowiedzieć się więcej, zobacz [sposób konfigurowania pojedynczego hasła logowania](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Przechwytywanie metadanych formularzy logowania dla aplikacji, które nie znajdują się w galerii

Firma Microsoft obsługuje przechwytywania metadanych w aplikacji sieci web dla hasła vaulting (Przechwytywanie pola nazwy użytkownika i hasło). Przejdź do adresu URL logowania podczas procesu konfigurowania aplikacji do przechwytywania metadanych formularzy. Poproś właściciela aplikacji jako adres URL logowania dokładne. Te informacje są używane w procesie logowania jednokrotnego, mapowanie poświadczeń usługi Azure AD do aplikacji podczas logowania jednokrotnego.

Aby dowiedzieć się więcej, zobacz [co to jest dostęp do aplikacji i logowanie Jednokrotne z usługą Azure AD? — opartego na hasłach logowania jednokrotnego](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Oznaczenia tych metadanych w formularzach konieczne ponowne przechwycenie

Gdy aplikacje zmieniają się ich układ HTML, konieczne może być ponownie przechwycić metadane, aby dopasować zmiany. Typowe objawy, które wskazują, że układ HTML zmieniło obejmują:

- Użytkownicy zgłoszenie, klikając przycisk w aplikacji "zablokowania" na stronie logowania
- Użytkownicy raportów, że nazwa użytkownika lub hasło nie jest wypełnione

#### <a name="shared-accounts"></a>Udostępnione konta

Z perspektywy logowania w aplikacji za pomocą konta udostępnionego nie różni się od aplikacji z galerii, korzystającą z hasłami logowania jednokrotnego dla poszczególnych użytkowników. Jednakże istnieją dodatkowe kroki wymagane podczas planowania i konfigurowania aplikacji przeznaczony do użycia udostępnionych kont:

1. Praca z aplikacji użytkownikom biznesowym Udokumentowanie następujących:
   1. Zbiór użytkowników w organizacji, którzy będą korzystać z aplikacji
   1. Istniejący zestaw poświadczeń w aplikacji skojarzonej z zestawem użytkowników 
1. Dla każdej kombinacji zestaw użytkowników i poświadczeń należy utworzyć grupę zabezpieczeń w chmurze lub lokalnie zgodnie z wymaganiami.
1. Resetowanie poświadczeń udostępnionych. Gdy aplikacja jest wdrażana w usłudze Azure AD, osób nie ma potrzeby hasło konta współużytkowanego. Ponieważ usługa Azure AD będą przechowywane hasła, należy wziąć pod uwagę ustawienie, aby była bardzo długie i złożone. 
1. Jeśli aplikacja obsługuje tę funkcję, należy skonfigurować automatyczne Przerzucanie haseł. W ten sposób nawet administratora, który wykonał początkowej konfiguracji będzie znać hasło konta współużytkowanego. 

## <a name="plan-your-authentication-method"></a>Planowanie metody uwierzytelniania

Wybranie poprawnej metody uwierzytelniania jest niezwykle istotne pierwszej decyzji w procesie konfigurowania rozwiązania tożsamości hybrydowej usługi Azure AD. Implementuje metodę uwierzytelniania, która jest konfigurowana przy użyciu usługi Azure AD Connect, która również Inicjuje obsługę użytkowników w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącej infrastruktury, złożoności i kosztów wykonania wybranych. Czynniki te różnią się dla każdej organizacji i mogą ulec zmianie wraz z upływem czasu. Wybierz ten, który najlepiej pasuje do określonego scenariusza. Aby uzyskać więcej informacji, zobacz [wybierz metodę uwierzytelniania odpowiednie dla Twojego rozwiązania tożsamości hybrydowej usługi Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Planowanie bezpieczeństwa i zarządzanie 

Tożsamość jest nowy pivot głównej dla zabezpieczeń uwagi i inwestycji, ponieważ strefy sieci stają się coraz bardziej porowaty a w mniejszym stopniu obowiązywać z masowego urządzenia BYOD i aplikacji w chmurze. 

### <a name="plan-access-reviews"></a>Planowanie przeglądów dostępu

[Przeglądów dostępu](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Należy zaplanować Przegląd dostępu użytkowników w regularnych odstępach czasu, aby upewnić się, że odpowiednie osoby mieć przedłużony dostęp.

Kluczowe tematy, aby zaplanować podczas konfigurowania przeglądów dostępu, należą:

1. Identyfikowanie cykl dla przeglądów dostępu, który spełnia Twoje potrzeby biznesowe. Może to być jak często tydzień, co miesiąc, rocznie lub ćwiczenia na żądanie.

1. Utwórz grupy, które reprezentują recenzentów raporty dostępu do aplikacji. Należy upewnić się, że uczestników projektu najbardziej zapoznać się z aplikacji i jej użytkowników docelowych i przypadki użycia uczestników z przeglądów dostępu

1. Kończenie przeglądu dostępu zawiera zdjęciu uprawnienia dostępu do aplikacji dla użytkowników, którzy już nie muszą mieć dostęp. Planowanie obsługi potencjalne żądania pomocy technicznej z zablokowani użytkownicy. Usunięty użytkownik pozostanie usunięte w usłudze Azure AD przez 30 dni, w tym czasie pliki mogą zostać przywrócone przez administratora w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Przy użyciu portalu Azure Active Directory, Administrator globalny może jawnie trwałe usunięcie ostatnio usuniętego użytkownika przed osiągnięciem tego okresu.

### <a name="plan-auditing"></a>Planowanie inspekcji

Usługa Azure AD zapewnia [raporty zawierające szczegółowe informacje techniczne i biznesowe](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Aktualizacje zabezpieczeń, jak i raporty aktywności są dostępne. Raporty dotyczące zabezpieczeń pokazują dotyczący użytkowników oflagowanych z ryzykiem i ryzykownych logowań. Raporty aktywności ułatwiają zrozumienie zachowania użytkowników w Twojej organizacji, szczegółowych informacji na temat aktywności logowania oraz udostępniając dziennika inspekcji wszystkich danych logowania. Raporty można użyć do zarządzania ryzykiem, zwiększającym produktywność i monitorowania zgodności.

| Typ raportu | Przegląd dostępu | Raporty dotyczące zabezpieczeń | W raporcie logowania |
|-------------|---------------|------------------|----------------|
| Służy do przeglądania | Uprawnienia aplikacji i użycia. | Mogą mieć złamane zabezpieczenia konta | Kto ma dostęp do aplikacji |
| Potencjalnych akcji | Przeprowadź inspekcję dostępu do; Odwołaj uprawnienia | Odwołaj dostęp; Wymuszanie zabezpieczeń resetowania | Odwołaj dostęp |

Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni i udostępnia dane za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API dla Ciebie pobrać do systemów analizy.

### <a name="consider-using-microsoft-cloud-application-security"></a>Należy wziąć pod uwagę przy użyciu programu Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) to rozwiązanie chmury dostępu zabezpieczeń brokera (CASB). Go zapewnia wgląd w swoje aplikacje w chmurze i usługi, zapewnia zaawansowanych możliwości analitycznych, aby zidentyfikować i zwalczania cyberzagrożeniami i umożliwia kontrolowanie sposobu przesyłane dane.

Wdrażanie MCAS pozwala na:

- Użyj rozwiązania Cloud Discovery w celu zamapowania i zidentyfikowania środowiska chmury oraz aplikacji w chmurze, które Twoja organizacja używa.
- Oficjalne akceptowanie lub anulować oficjalnie aplikacji w chmurze
- Używanie łączników łatwych do wdrożenia aplikacji, które korzystają z interfejsów API dostawców w celu zapewnienia wglądu i nadzoru aplikacji łączących się z
- Użyj kontroli dostępu warunkowego aplikacji ochrony, aby uzyskać wgląd w czasie rzeczywistym oraz kontrolę dostępu i działań w ramach aplikacji w chmurze
- Pomaga w ciągłej kontroli przez ustawienie i stałe dostrajanie zasad.

Kontrola sesji Microsoft Cloud Application Security (MCAS) jest dostępna dla dowolnej przeglądarki, na dowolnej platformie głównych w dowolnym systemie operacyjnym. Aplikacje mobilne i aplikacje komputerowe można być zablokowane lub dozwolone. Dzięki natywnej integracji z usługą Azure AD, wszelkie aplikacje, które są skonfigurowane przy użyciu protokołu SAML lub Open ID Connect aplikacji za pomocą logowania jednokrotnego w usłudze Azure AD mogą być obsługiwane, w tym [kilka sekcji polecane aplikacje](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Aby uzyskać informacje o MCAS, zobacz [omówienie Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS to usługa oparta na użytkowniku subskrypcji. Możesz przejrzeć szczegóły licencjonowania w [arkusz danych dotyczący licencjonowania MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Korzystanie z dostępu warunkowego

Przy użyciu dostępu warunkowego możesz zautomatyzować decyzji dotyczących kontroli dostępu na podstawie kryteriów dla aplikacji w chmurze.

Po zakończeniu pierwszego czynnika, wymuszane są zasady dostępu warunkowego. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwszy defense wiersza w scenariuszach typu "odmowa usługi" (DoS) ataki, takie jak, ale można użyć sygnałów z tych zdarzeń w celu określenia dostępu do. Na przykład poziom ryzyka logowania może służyć lokalizacji żądania, i tak dalej. Aby uzyskać więcej informacji na temat dostępu warunkowego, zobacz [Przegląd](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) i [planu wdrożenia](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Usługa rejestracji Jednokrotnej techniczne wymagania dotyczące usługi Azure

W poniższej sekcji przedstawiono wymagania aby skonfigurować aplikację określonych w tym niezbędne środowisk, punkty końcowe, Mapowanie oświadczeń wymaganych atrybutów, certyfikaty i protokoły używane. Należy to informacje, aby skonfigurować logowanie Jednokrotne w [portalu usługi Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Szczegóły mechanizmu uwierzytelniania

Dla wszystkich wstępnie zintegrowanych aplikacji SaaS firma Microsoft udostępnia zapoznać się z samouczkiem i nie potrzebujesz tych informacji. Jeśli aplikacja nie znajduje się w rynku aplikacji / Galeria, konieczne może być zebrać następujące elementy danych:

- **Bieżący dostawca tożsamości używane przez aplikację dla rejestracji Jednokrotnej, jeśli ma to zastosowanie** — na przykład: Usługi AD FS i serwera PingFederate, Okta
- **Protokoły obsługiwane przez aplikację docelową** — na przykład, SAML 2.0, OpenID Connect, OAuth, w przypadku uwierzytelniania opartego na formularzach WS-Fed WS-Trust
- **Protokół konfigurowany za pomocą usługi Azure AD** — na przykład, SAML 2.0 lub 1.1, OpenID Connect, OAuth, oparte na formularzach, WS-Fed

### <a name="attribute-requirements"></a>Wymagania dotyczące atrybutów

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowania atrybutów między obiektami użytkownika usługi Azure AD i obiektów użytkowników każdej aplikacji SaaS. Niektóre aplikacje, zarządzać inne typy obiektów, takich jak grupy. Planowanie mapowania atrybutów użytkownika z usługi Azure AD do aplikacji i [Dostosowywanie mapowań atrybutów domyślne](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) zgodnie z Twojej firmy wymaga.

### <a name="certificate-requirements"></a>Wymagania dotyczące certyfikatów

Aktualne muszą być certyfikat dla aplikacji lub istnieje ryzyko, użytkownicy nie będą mogli uzyskiwać dostęp do aplikacji. Większość certyfikatów aplikacji SaaS są dobre 36 miesięcy. Możesz zmienić ten czas certyfikat w bloku aplikacji. Upewnij się, że dokumentów po upływie i znać sposób zarządzania Twojej odnowienia certyfikatu. 

Istnieją dwa sposoby zarządzania certyfikatami. 

- **Automatyczne przerzucanie** — firma Microsoft obsługuje [Przerzucanie klucza podpisywania w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Gdy jest to preferowana metoda zarządzania certyfikatami, nie wszystkie niezależnego dostawcy oprogramowania obsługuje ten scenariusz.

- **Ręczna aktualizacja** — każda aplikacja ma własny certyfikat, która wygaśnie po oparte na jak jest zdefiniowany. Przed wygaśnięciem certyfikatu aplikacji, Utwórz nowy certyfikat i wysyłać je do niezależnego dostawcy oprogramowania. Te informacje mogą być ściągane z metadanych federacji. [Dowiedz się więcej w metadanych federacji w tym miejscu.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementowanie logowania jednokrotnego

Do planowania i wdrażania rozwiązania w Twojej organizacji, należy użyć następujących faz:

### <a name="user-configuration-for-sso"></a>Konfiguracja użytkownika na potrzeby logowania jednokrotnego

- **Identyfikowanie użytkowników testowych**

   Skontaktuj się do właściciela aplikacji i poproś go, aby utworzyć co najmniej trzy użytkowników w aplikacji. Upewnij się, informacje, które będzie używane jako podstawowy identyfikator jest wypełniony poprawnie i dopasowuje atrybut, który jest dostępny w usłudze Azure AD. W większości przypadków to będzie zmapowana do "NameID" w przypadku aplikacji opartej na SAML. Dla tokenów JWT jest "preferred_username."
   
   Utwórz użytkownika w usłudze Azure AD albo ręcznie, jako użytkownik oparte na chmurze lub zsynchronizować użytkowników z lokalnej przy użyciu aparatu synchronizacji usługi Azure AD Connect. Upewnij się, że informacje o zgodne oświadczenia są wysyłane do aplikacji.

- **Konfigurowanie logowania jednokrotnego**

   Z [listy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), Znajdź i Otwórz samouczek logowania jednokrotnego dla aplikacji, a następnie wykonaj kroki tego samouczka na pomyślnie skonfigurować aplikację SaaS.

   Jeśli nie możesz znaleźć aplikacji, zobacz [dokumentacji aplikacji niestandardowych](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). To przeprowadzi Cię o tym, jak dodać aplikację, która nie znajduje się w galerii usługi Azure AD.

   Opcjonalnie można użyć oświadczeń wystawionych w tokenie SAML dla aplikacji przedsiębiorstwa przy użyciu [firmy Microsoft, dokumentacja ze wskazówkami dotyczącymi](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Upewnij się, że to mapuje oczekują uzyskania odpowiedzi SAML dla aplikacji. Jeśli wystąpią problemy podczas konfiguracji, należy użyć nasze wskazówki w [jak debugować logowanie Jednokrotne integracji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Dołączanie do aplikacji niestandardowej jest funkcją licencji usługi Azure AD Premium P1 lub P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Podaj komunikacji zmiany logowania jednokrotnego dla użytkowników końcowych

Implementowanie własnego planu komunikacji. Upewnij się, że jest umożliwienie użytkownikowi końcowemu wiedzieć, że zmiana pochodzi, gdy jest już dostępna, co musisz zrobić i jak uzyskać pomoc.

### <a name="verify-end-user-scenarios-for-sso"></a>Sprawdzić, czy scenariuszy użytkownika końcowego logowania jednokrotnego

Można użyć następujących przypadków testowych do przeprowadzania testów na należących do firmy i urządzeń osobistych w celu zapewnienia konfiguracji logowania jednokrotnego działają zgodnie z oczekiwaniami. Scenariusze poniżej założono, że użytkownik jest przejście do adresu URL aplikacji i przechodzenia przez przepływ uwierzytelniania inicjowane przez dostawcę usług (przepływu autoryzacji zainicjowanego przez dostawcę usług).

| Scenariusz | Oczekiwany wynik przepływ uwierzytelniania zainicjowanego przez dostawcę usług przez użytkownika |
|----------|---------------------------------------------------|
| Zaloguj się do aplikacji przy użyciu programu Internet Explorer znajduje się w sieci corpnet. | Zintegrowane Windows Authentication (Zintegrowane) występuje w przypadku nie dodatkowe monity. |
| Zaloguj się do aplikacji przy użyciu programu Internet Explorer znajduje się poza siecią firmową za pomocą nowego próba logowania. | Oparte na formularzach monit serwera usług AD FS. Logowanie użytkownika przebiegnie pomyślnie, a przeglądarka wyświetla monit dotyczący uwierzytelniania Wieloskładnikowego. |
| Zaloguj się do aplikacji za pomocą programu Internet Explorer znajduje się poza siecią firmową za pomocą bieżącej sesji i nigdy nie wykonał usługi MFA. | Użytkownik nie otrzyma monit o podanie współczynnik pierwszy. Użytkownik otrzymuje monit dla usługi MFA. |
| Zaloguj się do aplikacji za pomocą programu Internet Explorer znajduje się poza siecią firmową za pomocą bieżącej sesji i przeprowadził już usługę MFA w ramach tej sesji. | Użytkownik nie otrzyma monit o podanie współczynnik pierwszy. Użytkownik nie otrzyma usługi MFA. SSO użytkownika do aplikacji. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox/Safari znajduje się poza siecią firmową za pomocą bieżącej sesji i przeprowadził już usługę MFA w ramach tej sesji. | Użytkownik nie otrzyma monit o podanie współczynnik pierwszy. Użytkownik nie otrzyma usługi MFA. Użytkownik firmy logowania jednokrotnego do aplikacji. |
| Logowanie do aplikacji za pomocą przeglądarki Chrome/Firefox/Safari znajduje się poza siecią firmową za pomocą nowego próba logowania. | Oparte na formularzach monit serwera usług AD FS. Logowanie użytkownika przebiegnie pomyślnie, a przeglądarka wyświetla monit dotyczący uwierzytelniania Wieloskładnikowego. |
| Zaloguj się do aplikacji w przeglądarce Chrome/Firefox znajduje się w sieci firmowej przy użyciu bieżącej sesji. | Użytkownik nie otrzyma monit o podanie współczynnik pierwszy. Użytkownik nie otrzyma usługi MFA. Użytkownik firmy logowania jednokrotnego do aplikacji. |
| Zaloguj się do aplikacji przy użyciu aplikacji mobilnej aplikacji za pomocą nowego próba logowania. | Oparte na formularzach monit serwera usług AD FS. Logowanie użytkownika przebiegnie pomyślnie, a klient biblioteki ADAL wyświetla monit dotyczący uwierzytelniania Wieloskładnikowego. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania. | Oparte na formularzach monit serwera usług AD FS. Użytkownik nie może się zalogować się przy użyciu współczynnika pierwszy. |
| Autoryzowany użytkownik próbuje zalogować się, ale wprowadzi nieprawidłowe hasło. | Użytkownik przechodzi do adresu URL aplikacji i otrzyma błąd nieprawidłowo podawali nazwę użytkownika/hasło. |
| Autoryzowany użytkownik kliknie link w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika przycisk na adres URL i jest zalogowali się do aplikacji bez monitowania dodatkowe. |
| Autoryzowany użytkownik kliknie link w wiadomości e-mail i nie jest jeszcze uwierzytelniony. | Użytkownik klika przycisk na adres URL i jest Monituj o uwierzytelnianie przy użyciu współczynnika pierwszy. |
| Uprawnienia logowania użytkownika do aplikacji za pomocą aplikacji mobilnej aplikacji (zainicjowanego przez dostawcę usług) przy użyciu nowego próba logowania. | Oparte na formularzach monit serwera usług AD FS. Logowanie użytkownika przebiegnie pomyślnie, a klient biblioteki ADAL wyświetla monit dotyczący uwierzytelniania Wieloskładnikowego. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania (zainicjowanego przez dostawcę usług). | Oparte na formularzach monit serwera usług AD FS. Użytkownik nie może się zalogować się przy użyciu współczynnika pierwszy. |
| Autoryzowany użytkownik próbuje zalogować się, ale wprowadzi nieprawidłowe hasło.| Użytkownik przechodzi do adresu URL aplikacji i otrzyma błąd nieprawidłowo podawali nazwę użytkownika/hasło. |
| Autoryzowany użytkownik wylogowuje i loguje się do niej ponownie. | Jeśli adres URL wylogowania jest skonfigurowana, użytkownik jest zalogowany z wszystkich usług i monit uwierzytelnienia. |
| Autoryzowany użytkownik wylogowuje i loguje się do niej ponownie. | Nie skonfigurowano adresu URL wylogowania, użytkownik zostanie automatycznie zalogowany w przy użyciu istniejącego tokenu z istniejącą sesję przeglądarki usługi Azure AD. |
| Autoryzowany użytkownik kliknie link w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika przycisk na adres URL i jest zalogowali się do aplikacji bez monitowania dodatkowe. |
| Autoryzowany użytkownik kliknie link w wiadomości e-mail i nie jest jeszcze uwierzytelniony. | Użytkownik klika przycisk na adres URL i jest Monituj o uwierzytelnianie przy użyciu współczynnika pierwszy. |

## <a name="manage-sso"></a>Zarządzanie logowania jednokrotnego

W tej sekcji opisano wymagania i zalecenia dotyczące zarządzania pomyślnie logowania jednokrotnego.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

Zawsze należy używać roli z najmniejszą liczbą uprawnień do wykonania zadania wymagane w ramach usługi Azure Active Directory. Firma Microsoft zaleca [Przejrzyj różne role, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) i wybrać właściwy rozwiązać potrzeb dla każdej osoby w tej aplikacji. Niektóre role może być konieczne może być stosowane tymczasowo i usunięte po zakończeniu wdrożenia.

| Osoby| Role | Rolę na platformie Azure AD (jeśli jest to wymagane) |
|--------|-------|-----------------------------|
| Pomoc techniczna administratora | Pomoc techniczną warstwy 1 | Brak |
| Administrator tożsamości | Konfigurowanie i debugowanie, gdy problemy dotyczące wpływu na usługi Azure AD | Administrator globalny |
| Administrator aplikacji | Poświadczenie użytkownika w aplikacji, konfiguracji użytkownicy z uprawnieniami | Brak |
| Administratorzy infrastruktury | Certyfikat przerzucania właściciela | Administrator globalny |
| Firm właściciela/biorący udział w projekcie | Poświadczenie użytkownika w aplikacji, konfiguracji użytkownicy z uprawnieniami | Brak |

Firma Microsoft zaleca używanie [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM), aby zarządzać poszczególnych ról w celu zapewnienia dodatkowych inspekcji, kontroli i dostępu Przejrzyj plik pod kątem użytkownicy z uprawnieniami do katalogu.

### <a name="sso-certificate-lifecycle-management"></a>Zarządzanie cyklem życia certyfikatu logowania jednokrotnego

Jest ważne zidentyfikować odpowiednich ról i wiadomości e-mail listy dystrybucyjnej odpowiedzialnych za zarządzanie cyklem życia certyfikatu podpisywania między usługą Azure AD i aplikacji, które jest konfigurowane za pomocą logowania jednokrotnego. Oto niektóre kluczowe role, które firma Microsoft zaleca się umieszczenie w miejscu:

- Właściciela na potrzeby aktualizacji właściwości użytkownika w aplikacji
- Właściciel dyżurów dla pomocy technicznej w zakresie usuwania awarii aplikacji
- Listy dystrybucyjnej e-mail ściśle monitorowane powiadomienia o zmianach związanych z certyfikatami

Maksymalny okres istnienia certyfikatu jest trzy lata. Firma Microsoft zaleca, ustanawiania proces w sposób będzie obsługiwać zmiany certyfikatu między usługą Azure AD i aplikacji. Może to pomóc zapobiec lub zminimalizować awarii z powodu wygaśnięcia ważności certyfikatu lub wymusić Przerzucanie certyfikatów.

### <a name="rollback-process"></a>Procesu wycofywania

Po zakończeniu testowania, w oparciu o przypadki testowe, nadszedł czas na przenoszenie do środowiska produkcyjnego z aplikacją. Przenoszenie do środowiska produkcyjnego oznacza, że należy zaimplementować konfiguracje planowane i przetestowane w ramach dzierżawy w środowisku produkcyjnym i wprowadzane do użytkowników. Jest ważne, aby zaplanować, co należy zrobić w przypadku, gdy wdrożenie nie jest akceptowana zgodnie z planem. Jeśli Konfiguracja logowania jednokrotnego nie powiedzie się podczas wdrażania, należy zrozumieć, jak rozwiązać ewentualnej awarii i ograniczyć wpływ na użytkowników.

Dostępność metod uwierzytelniania w aplikacji określi najlepszych strategii. Zawsze upewnij się, że szczegółowej dokumentacji dla właścicieli aplikację w dokładnie, jak wrócić do pierwotnego stanu logowania w konfiguracji w przypadku, gdy wdrożenie jest uruchamiane na problemy.

- **Jeśli aplikacja obsługuje wielu dostawców tożsamości**dla przykładu protokołu LDAP i usług AD FS i polecenie Ping, nie usuwaj istniejącej konfiguracji logowania jednokrotnego w miarę wprowadzania aktualizacji. Zamiast tego należy je wyłączyć podczas migracji w przypadku, gdy trzeba zmienić go ponownie później. 

- **Jeśli aplikacja nie obsługuje wielu dostawców tożsamości** , ale umożliwia użytkownikom zalogowanie się przy użyciu uwierzytelniania opartego na formularzach (nazwy użytkownika/hasła), upewnij się, że użytkownicy mogą wrócić do tego podejścia w przypadku, gdy nowe wdrożenie konfiguracji logowania jednokrotnego nie powiedzie się.

### <a name="access-management"></a>Zarządzanie dostępem

Firma Microsoft zaleca wybranie skalowanych podejście związane z zarządzaniem dostępem do zasobów. Typowe metody obejmują wykorzystanie lokalnych grup synchronizacji za pomocą usługi Azure AD Connect, [tworzenia grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkownika](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), lub tworzenia [grup samoobsługi](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) w usłudze Azure AD zarządzane przez właściciela zasobu.

### <a name="monitor-security"></a>Monitorowanie zabezpieczeń

Zalecamy skonfigurowanie regularnych cyklach, w którym można przejrzeć różne aspekty zabezpieczenia aplikacji SaaS i wykonywać żadnych czynności zaradczych, które są wymagane.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniższe linki zawierają scenariusze dotyczące rozwiązywania problemów. Można utworzyć określonego przewodnika personelowi pomocy technicznej, która zawiera te scenariusze i kroki, aby to naprawić.

#### <a name="consent-issues"></a>Problemy z zgody

- [Zgoda nieoczekiwany błąd](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Błąd zgody użytkownika](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemy dotyczące logowania

- [Problemy z logowaniem w portalu niestandardowym](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemy podczas logowania się z poziomu panelu dostępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Błąd na stronie logowania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problem z logowaniem do aplikacji firmy Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemy z logowania jednokrotnego dla aplikacji w galerii aplikacji Azure

- [Problem z hasłami logowania jednokrotnego dla aplikacji na liście w galerii aplikacji Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problem z federacyjnego logowania jednokrotnego dla aplikacji w galerii aplikacji Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemy z logowania jednokrotnego aplikacji niewymienionych w galerii aplikacji Azure

- [Problem z hasłami logowania jednokrotnego dla aplikacji, które nie są wyświetlane w galerii aplikacji Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problem z federacyjnego logowania jednokrotnego dla aplikacji, które nie są wyświetlane w galerii aplikacji Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Kolejne kroki

[Debugowanie rejestracji jednokrotnej opartej na SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapowanie oświadczeń dla aplikacji za pomocą programu PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Dostosowywanie oświadczeń wystawionych w tokenie języka SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protokół SAML logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protokół SAML wylogowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Usługa Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (dla użytkowników zewnętrznych, takich jak partnerami i dostawcami)

[Dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Dostęp z użyciem logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Samouczek aplikacji w rejestracji Jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
