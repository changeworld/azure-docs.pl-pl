---
title: Planowanie wdrożenia logowania jednokrotnego usługi Azure Active Directory
description: Przewodnik ułatwiające planowanie, wdrażanie i zarządzanie sytą w organizacji.
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
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512831"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planowanie wdrożenia logowania jednokrotnego

Logowanie jednokrotne (Logowanie jednokrotne) oznacza dostęp do wszystkich aplikacji i zasobów, które użytkownik potrzebuje, logując się tylko raz przy użyciu jednego konta użytkownika. Dzięki aplikacji SSO użytkownicy mogą uzyskiwać dostęp do wszystkich potrzebnych aplikacji bez konieczności uwierzytelniania po raz drugi.

## <a name="benefits-of-sso"></a>Korzyści z YSO

Logowanie jednokrotne (Logowanie jednokrotne) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji w usłudze Azure Active Directory (Azure AD). 

Wiele organizacji polega na aplikacjach typu oprogramowanie jako usługa (SaaS), takich jak Office 365, Box i Salesforce, aby zwiększyć produktywność użytkowników końcowych. W przeszłości pracownicy IT musieli indywidualnie tworzyć i aktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli zapamiętywać hasło dla każdej z nich.

Portal Azure Marketplace ma ponad 3000 aplikacji z wstępnie zintegrowanymi połączeniami samosojowymi, co ułatwia ich integrację z dzierżawą.

## <a name="licensing"></a>Licencjonowanie

- **Licencjonowanie usługi Azure AD** — samouczeniesz typu SSO dla wstępnie zintegrowanych aplikacji SaaS jest bezpłatne. Jednak liczba obiektów w katalogu i funkcje, które chcesz wdrożyć, mogą wymagać dodatkowych licencji. Aby uzyskać pełną listę wymagań dotyczących licencji, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licencjonowanie aplikacji** — do twoich aplikacji SaaS potrzebne są odpowiednie licencje, aby spełnić twoje potrzeby biznesowe. Praca z właścicielem aplikacji, aby ustalić, czy użytkownicy przypisani do aplikacji mają odpowiednie licencje dla swoich ról w aplikacji. Jeśli usługa Azure AD zarządza automatyczną inicjowania obsługi administracyjnej na podstawie ról, role przypisane w usłudze Azure AD musi być zgodna z liczbą licencji posiadanych w aplikacji. Niewłaściwa liczba licencji posiadanych w aplikacji może prowadzić do błędów podczas inicjowania obsługi administracyjnej/aktualizacji użytkownika.

## <a name="plan-your-sso-team"></a>Zaplanuj swój zespół SSO

- **Zaangażuj odpowiednie zainteresowane strony** — gdy projekty technologiczne nie powiodą się, zazwyczaj wynika to z niedopasowanych oczekiwań co do wpływu, wyników i odpowiedzialności. Aby uniknąć tych pułapek, [upewnij się, że angażujesz odpowiednie zainteresowane strony](https://aka.ms/deploymentplans) i że interesariusze rozumieją ich role.
- **Planowanie komunikacji** — komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Proaktywnie komunikuj się z użytkownikami o tym, jak zmieni się ich środowisko, kiedy to się zmieni i jak uzyskać wsparcie, jeśli wystąpią problemy. Przejrzyj [opcje, w jaki sposób użytkownicy końcowi będą uzyskiwać dostęp do aplikacji obsługujących protokół SSO](end-user-experiences.md)i stwórz komunikację, aby dopasować ją do wybranej opcji. 

## <a name="plan-your-sso-protocol"></a>Planowanie protokołu SSO

Implementacja SSO oparta na protokołach federacyjnej poprawia bezpieczeństwo, niezawodność i środowisko użytkownika końcowego i jest łatwiejsza do zaimplementowania. Wiele aplikacji jest wstępnie zintegrowanych z usługą Azure AD z [dostępnymi przewodnikami krok po kroku.](../saas-apps/tutorial-list.md) Można je znaleźć w naszym portalu [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) Szczegółowe informacje na temat każdej metody logowania jednokrotnego można znaleźć w artykule [Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](what-is-single-sign-on.md).

Istnieją dwa podstawowe sposoby umożliwiające użytkownikom logowanie się do aplikacji w trybie jednokrotnym:

- **Z sfederowanym logiem jednokrotnym** Usługa Azure AD uwierzytelnia użytkownika do aplikacji przy użyciu konta usługi Azure AD. Ta metoda jest obsługiwana dla aplikacji, które obsługują protokoły, takie jak SAML 2.0, Federacja WS lub OpenID Connect, i jest najbogatszym trybem logowania jednokrotnego. Firma Microsoft zaleca używanie federacyjnego loga logować z usługą Azure AD, gdy aplikacja obsługuje go, zamiast sso oparte na hasłach i ADFS.

- W przypadku logowania **jednokrotnego** użytkowników opartych na hasłach, którzy logują się do aplikacji przy użyciu nazwy użytkownika i hasła za pierwszym razem, uzyskują do niej dostęp. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji. Logowanie jednookrotne oparte na hasłach umożliwia bezpieczne przechowywanie haseł aplikacji i powtarzanie za pomocą rozszerzenia przeglądarki internetowej lub aplikacji mobilnej. Ta opcja wykorzystuje istniejący proces logowania dostarczony przez aplikację, umożliwia administratorowi zarządzanie hasłami i nie wymaga od użytkownika znać hasła.

### <a name="considerations-for-federation-based-sso"></a>Zagadnienia dotyczące sytego sytego typu sycowego opartego na federacji

- **Za pomocą OpenID Connect i OAuth** — jeśli aplikacja, z którą łączysz, obsługuje ją, użyj metody OIDC/OAuth 2.0, aby włączyć swo-so do tej aplikacji. Ta metoda wymaga mniej konfiguracji i umożliwia bogatsze środowisko użytkownika. Aby uzyskać więcej informacji, zobacz [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)i [Przewodnik dla deweloperów usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Konfiguracje punktu końcowego dla logowania sytego oparte na SAML** — jeśli używasz SAML, deweloperzy będą potrzebować określonych informacji przed skonfigurowaniem aplikacji. Aby uzyskać więcej informacji, zobacz [Edytowanie podstawowej konfiguracji SAML](configure-single-sign-on-non-gallery-applications.md).
- **Zarządzanie certyfikatami dla samol oparte na SSO** — po włączeniu federacyjnego sytuowanego sytuowanego dla aplikacji usługa Azure AD tworzy certyfikat, który jest domyślnie ważny przez trzy lata. W razie potrzeby można dostosować datę wygaśnięcia tego certyfikatu. Upewnij się, że masz procesy w celu odnowienia certyfikatów przed ich wygaśnięciem. Aby dowiedzieć się więcej, zobacz [Zarządzanie certyfikatami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Zagadnienia dotyczące loga loga bez hasła

Korzystanie z usługi Azure AD dla logowania samouczłowego oparte na hasłach wymaga wdrożenia rozszerzenia przeglądarki, która bezpiecznie pobrać poświadczenia i wypełnić formularze logowania. Zdefiniuj mechanizm wdrażania rozszerzenia na dużą skalę za pomocą [obsługiwanych przeglądarek](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Dostępne są następujące opcje:

- [Zasady grupy dla programu Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Menedżer konfiguracji dla programu Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Pobieranie i konfiguracja oparta na użytkownikach dla Chrome, Firefox, Microsoft Edge lub IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Aby dowiedzieć się więcej, zobacz [Jak skonfigurować logowanie jednokrotne hasła .](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Przechwytywanie metadanych formularzy logowania dla aplikacji, których nie ma w galerii

Firma Microsoft obsługuje przechwytywanie metadanych w aplikacji sieci web do przechowywania haseł (przechwytywanie pól nazwy użytkownika i hasła). Przejdź do adresu URL logowania podczas konfigurowania aplikacji do przechwytywania metadanych formularzy. Zapytaj właściciela aplikacji o dokładny adres URL logowania. Te informacje są używane podczas procesu logowania, mapowanie poświadczeń usługi Azure AD do aplikacji podczas logowania.

Aby dowiedzieć się więcej, zobacz [Co to jest dostęp do aplikacji i loga logować przy logowaniu samoso w usłudze Azure AD? — logowaniu loga bez hasła.](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Wskazania, że metadane w formularzach muszą zostać odzyskane

Gdy aplikacje zmienić swój układ HTML, może być konieczne ponowne odzyskanie metadanych, aby dostosować się do zmian. Typowe objawy wskazujące, że układ HTML ma się zmienić obejmują:

- Użytkownicy zgłaszający, że kliknięcie aplikacji zostaje "zablokowane" na stronie logowania
- Użytkownicy zgłaszający, że nazwa użytkownika lub hasło nie są wypełnione

#### <a name="shared-accounts"></a>Konta współdzielone

Z punktu widzenia logowania aplikacje z kontami udostępnionymi nie różnią się od aplikacji galerii, która używa logowania logowania bez logowania do indywidualnych użytkowników. Jednak podczas planowania i konfigurowania aplikacji przeznaczonej do używania kont współdzielonych wymagane są pewne dodatkowe kroki:

1. Praca z użytkownikami biznesowymi aplikacji w celu udokumentowania następujących dokumentów:
   1. Zestaw użytkowników w organizacji, którzy będą korzystać z aplikacji
   1. Istniejący zestaw poświadczeń w aplikacji skojarzonej z zestawem użytkowników 
1. Dla każdej kombinacji zestawu użytkowników i poświadczeń utwórz grupę zabezpieczeń w chmurze lub lokalnie na podstawie wymagań.
1. Resetowanie poświadczeń udostępnionych. Po wdrożeniu aplikacji w usłudze Azure AD osoby nie potrzebują hasła konta udostępnionego. Ponieważ usługa Azure AD będzie przechowywać hasło, należy rozważyć ustawienie go jako bardzo długi i złożony. 
1. Skonfiguruj automatyczne narzucanie hasła, jeśli aplikacja je obsługuje. W ten sposób nawet administrator, który wykonał wstępną konfigurację, nie zna hasła do konta udostępnionego. 

## <a name="plan-your-authentication-method"></a>Planowanie metody uwierzytelniania

Wybranie poprawnej metody uwierzytelniania jest kluczową pierwszą decyzją podczas konfigurowania rozwiązania tożsamości hybrydowej usługi Azure AD. Zaimplementuj metodę uwierzytelniania skonfigurowana przy użyciu usługi Azure AD Connect, która również apowie na użytkowników w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt wdrożenia wyboru. Te czynniki są różne dla każdej organizacji i mogą się zmieniać w czasie. Należy wybrać ten, który najbardziej pasuje do konkretnego scenariusza. Aby uzyskać więcej informacji, zobacz [Wybieranie właściwej metody uwierzytelniania dla rozwiązania do tożsamości hybrydowej usługi Azure Active Directory.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)

## <a name="plan-your-security-and-governance"></a>Planowanie bezpieczeństwa i zarządzania 

Tożsamość jest nowym podstawowym punktem zwrotnym dla uwagi bezpieczeństwa i inwestycji, ponieważ obwody sieci stają się coraz bardziej porowate i mniej skuteczne wraz z eksplozją urządzeń BYOD i aplikacji w chmurze. 

### <a name="plan-access-reviews"></a>Recenzje dostępu planu

[Przeglądy programu Access](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) umożliwiają organizacjom efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisania ról. Należy planować regularne przeglądanie dostępu użytkowników, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.

Oto niektóre z kluczowych tematów, które należy zaplanować podczas konfigurowania przeglądów dostępu:

1. Identyfikowanie rytmu dla opinii o dostępie, który odpowiada Twoim potrzebom biznesowym. Może to być tak częste, jak raz w tygodniu, co miesiąc, rocznie, lub jako ćwiczenie na żądanie.

1. Tworzenie grup, które reprezentują recenzentów raportów dostępu do aplikacji. Musisz upewnić się, że interesariusze najbardziej zaznajomieni z aplikacją i jej docelowymi użytkownikami oraz przypadkami użycia są uczestnikami twoich recenzji dostępu

1. Ukończenie przeglądu dostępu obejmuje odebranie uprawnień dostępu do aplikacji użytkownikom, którzy nie potrzebują już dostępu. Planowanie obsługi potencjalnych żądań pomocy technicznej od użytkowników, którym odmówiono. Usunięty użytkownik pozostanie usunięty w usłudze Azure AD przez 30 dni, w którym czas może zostać przywrócony przez administratora, jeśli to konieczne. Po 30 dniach użytkownik jest trwale usuwany. Korzystając z portalu usługi Azure Active Directory, administrator globalny może jawnie trwale usunąć niedawno usuniętego użytkownika przed osiągnięciem tego okresu.

### <a name="plan-auditing"></a>Inspekcja planu

Usługa Azure AD udostępnia [raporty zawierające informacje techniczne i biznesowe.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) 

Dostępne są zarówno raporty zabezpieczeń, jak i aktywności. Raporty zabezpieczeń pokazują użytkowników oznaczonych pod kątem ryzyka i ryzykownych logowań. Raportów można używać do zarządzania ryzykiem, zwiększania produktywności i monitorowania zgodności.

| Typ raportu | Przegląd dostępu | Raporty dotyczące zabezpieczeń | Raport logowania |
|-------------|---------------|------------------|----------------|
| Służy do przeglądania | Uprawnienia aplikacji i użycie. | Potencjalnie przejęte konta | Kto uzyskuje dostęp do aplikacji |
| Potencjalne działania | Inspekcja dostępu; odwoływanie uprawnień | Odwołaj dostęp; wymuszanie resetowania zabezpieczeń | Cofanie dostępu |

Usługa Azure AD zachowuje większość danych inspekcji przez 30 dni i udostępnia dane za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API, aby można było pobrać je do systemów analizy.

### <a name="consider-using-microsoft-cloud-application-security"></a>Rozważ skorzystanie z programu Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) to rozwiązanie programu Cloud Access Security Broker (CASB). Zapewnia wgląd w aplikacje i usługi w chmurze, zapewnia zaawansowane analizy do identyfikacji i zwalczania cyberzagrożeń oraz pozwala kontrolować sposób identyfikowania danych.

Wdrażanie mcas umożliwia:

- Usługa Cloud Discovery umożliwia mapowanie i identyfikowanie środowiska chmury oraz aplikacji w chmurze używanych przez organizację.
- Aplikacje do nakładania sankcji i odosobnienia sankcji w chmurze
- Używaj łatwych do wdrożenia łączników aplikacji, które wykorzystują interfejsy API dostawców, aby widoczność i zarządzać aplikacjami, z którymi się łączysz
- Korzystanie z ochrony kontroli aplikacji dostępu warunkowego w celu uzyskania wglądu w czasie rzeczywistym i kontroli nad dostępem i aktywnością w aplikacjach w chmurze
- Pomaga mieć ciągłą kontrolę przez ustawienie, a następnie ciągłe dostosowywanie zasad.

Kontrola sesji usługi Microsoft Cloud Application Security (MCAS) jest dostępna dla dowolnej przeglądarki na dowolnej głównej platformie w dowolnym systemie operacyjnym. Aplikacje mobilne i klasyczne mogą być również blokowane lub dozwolone. Dzięki natywnie integrując się z usługą Azure AD, wszystkie aplikacje skonfigurowane z SAML lub aplikacjami Open ID Connect z logiem jednokrotnym w usłudze Azure AD mogą być obsługiwane, w tym [kilka polecanych aplikacji.](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

Aby uzyskać informacje o programie MCAS, zobacz [omówienie programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS to usługa subskrypcyjna oparta na użytkownikach. Szczegółowe informacje dotyczące licencjonowania można przejrzeć w [arkuszu danych licencjonowania MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Korzystanie z dostępu warunkowego

Dzięki dostępowi warunkowego można zautomatyzować decyzje dotyczące kontroli dostępu oparte na kryteriach dla aplikacji w chmurze.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego czynnika. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwsza linia obrony dla scenariuszy, takich jak ataki typu odmowa usługi (DoS), ale może używać sygnałów z tych zdarzeń do określenia dostępu. Na przykład można użyć poziomu ryzyka logowania, lokalizacji żądania i tak dalej. Aby uzyskać więcej informacji na temat dostępu [warunkowego,](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) zobacz omówienie i [plan wdrożenia](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Wymagania techniczne usługi Azure dla usługi Azure — sytua w celu zachowania usługi Azure

W poniższej sekcji opisano wymagania dotyczące konfigurowania określonej aplikacji, w tym niezbędne środowiska, punkty końcowe, mapowanie oświadczeń, wymagane atrybuty, certyfikaty i używane protokoły. Te informacje będą potrzebne do skonfigurowania logowania sytego w [portalu usługi Azure AD.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Szczegóły mechanizmu uwierzytelniania

W przypadku wszystkich wstępnie zintegrowanych aplikacji SaaS firma Microsoft udostępnia samouczek i nie będzie potrzebne te informacje. Jeśli aplikacja nie jest w naszym rynku aplikacji / galerii, może być konieczne zebranie następujących fragmentów danych:

- **Bieżący dostawca tożsamości, którego aplikacja używa dla aplikacji SSO, jeśli ma zastosowanie** - Na przykład: AD FS, PingFederate, Okta
- **Protokoły obsługiwane przez aplikację docelową** — na przykład SAML 2.0, OpenID Connect, OAuth, Auth oparte na formularzach, WS-Fed, WS-Trust
- **Protokół konfigurowany z usługą Azure AD** — na przykład SAML 2.0 lub 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Wymagania dotyczące atrybutów

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD a obiektami użytkownika każdej aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów, takimi jak grupy. Zaplanuj mapowanie atrybutów użytkownika z usługi Azure AD do aplikacji i [dostosuj domyślne mapowania atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) zgodnie z potrzebami biznesowymi.

### <a name="certificate-requirements"></a>Wymagania certyfikatu

Certyfikat dla aplikacji musi być aktualny lub istnieje ryzyko, że użytkownicy nie będą mogli uzyskać dostępu do aplikacji. Większość certyfikatów aplikacji SaaS jest dobra przez 36 miesięcy. Można zmienić ten czas trwania certyfikatu w bloku aplikacji. Pamiętaj, aby udokumentować wygaśnięcie i wiedzieć, jak zarządzać odnowieniem certyfikatu. 

Istnieją dwa sposoby zarządzania certyfikatami. 

- **Automatyczne przerzucanie certyfikatów** — firma Microsoft obsługuje [rolowanie kluczy podpisywania w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Chociaż jest to nasza preferowana metoda zarządzania certyfikatami, nie wszystkie firmy isv obsługuje ten scenariusz.

- **Ręczne aktualizowanie** — każda aplikacja ma swój własny certyfikat, który wygasa na podstawie sposobu jej zdefiniowania. Zanim certyfikat aplikacji wygaśnie, utwórz nowy certyfikat i wyślij go do firmy isv. Te informacje można wyciągnąć z metadanych federacji. [Przeczytaj więcej o metadanych federacji tutaj.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Zaimplementuj sytuaj y

Aby zaplanować i wdrożyć rozwiązanie w organizacji, użyj następujących faz:

### <a name="user-configuration-for-sso"></a>Konfiguracja użytkownika dla usługi SSO

- **Identyfikowanie użytkowników testowych**

   Skontaktuj się z właścicielem aplikacji i poproś o utworzenie co najmniej trzech użytkowników testowych w aplikacji. Upewnij się, że informacje, które będą używane jako identyfikator podstawowy jest wypełniany poprawnie i pasuje do atrybutu, który jest dostępny w usłudze Azure AD. W większości przypadków zostanie to mapowane do "NameID" dla aplikacji opartych na SAML. W przypadku tokenów JWT jest to "preferred_username".
   
   Utwórz użytkownika w usłudze Azure AD ręcznie jako użytkownik w chmurze lub zsynchronizować użytkownika z lokalnego przy użyciu aparatu synchronizacji usługi Azure AD Connect. Upewnij się, że informacje są zgodne z oświadczeń wysyłanych do aplikacji.

- **Konfigurowanie logowania jednokrotnego**

   Z [listy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), zlokalizować i otworzyć samouczek samouczek samouczek samouczek dla aplikacji, a następnie wykonaj kroki samouczka, aby pomyślnie skonfigurować aplikację SaaS.

   Jeśli nie możesz zlokalizować aplikacji, zobacz [dokumentacja aplikacji niestandardowej](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Spowoduje to przejście przez sposób dodawania aplikacji, która nie znajduje się w galerii usługi Azure AD.

   Opcjonalnie można użyć oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnej przy użyciu [dokumentacji wskazówki firmy Microsoft.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Upewnij się, że mapy do tego, co oczekujesz, aby otrzymać w odpowiedzi SAML dla aplikacji. Jeśli wystąpią problemy podczas konfiguracji, skorzystaj z naszych wskazówek dotyczących [sposobu debugowania integracji z użytkownikiem SSO.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

Niestandardowe dołączanie aplikacji jest funkcją licencji usługi Azure AD Premium P1 lub P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Zapewnianie komunikacji o zmianie usługi SSO użytkownikom końcowym

Zaimplementuj swój plan komunikacji. Upewnij się, że poinformuj użytkowników końcowych, że nadchodzi zmiana, kiedy nadeszła, co zrobić teraz i jak szukać pomocy.

### <a name="verify-end-user-scenarios-for-sso"></a>Weryfikowanie scenariuszy użytkownika końcowego dla sytego ustawienia użytkownika

Następujące przypadki testowe umożliwiają przeprowadzenie testów na urządzeniach firmowych i osobistych, aby upewnić się, że konfiguracje aplikacji SSO działają zgodnie z oczekiwaniami. Poniższe scenariusze zakładają, że użytkownik przechodzi do adresu URL aplikacji i przechodzi przepływ uwierzytelniania zainicjowany przez dostawcę usług (przepływ uwierzytelniania inicjowany przez dodatek SP).

| Scenariusz | Oczekiwany wynik w przepływie omytów inicjowanym przez sp |
|----------|---------------------------------------------------|
| Zaloguj się do aplikacji za pomocą IE podczas pracy w corpnet. | Zintegrowane uwierzytelnianie systemu Windows (IWA) odbywa się bez dodatkowych monitów. |
| Zaloguj się do aplikacji z IE podczas wyłączenia corpnet z nową próbą logowania. | Monit oparty na formularzach na serwerze AD FS Server. Użytkownik pomyślnie loguje się i monituje przeglądarkę o uwierzytelnianie wieloskładnikowe. |
| Zaloguj się do aplikacji za pomocą IE podczas wyłączania corpnet z bieżącą sesją i nigdy nie wykonywane MFA. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik otrzymuje monit o uwierzytelnianie wieloskładnikowe. |
| Zaloguj się do aplikacji z IE podczas wyłączenia corpnet z bieżącej sesji i już wykonywane MFA w tej sesji. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik nie odbiera usługi MFA. SSO użytkownika do aplikacji. |
| Zaloguj się do aplikacji z Chrome / Firefox / Safari podczas off corpnet z bieżącej sesji i już wykonywane MFA w tej sesji. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik nie odbiera usługi MFA. Użytkownik SSO do aplikacji. |
| Zaloguj się do aplikacji z Chrome / Firefox / Safari, podczas gdy off corpnet z nową próbą logowania. | Monit oparty na formularzach na serwerze AD FS Server. Użytkownik pomyślnie loguje się i monituje przeglądarkę o uwierzytelnianie wieloskładnikowe. |
| Zaloguj się do aplikacji w Chrome/Firefox w sieci firmowej z bieżącą sesją. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik nie odbiera usługi MFA. Użytkownik SSO do aplikacji. |
| Zaloguj się do aplikacji za pomocą aplikacji mobilnej z nową próbą logowania. | Monit oparty na formularzach na serwerze AD FS Server. Użytkownik pomyślnie loguje się i klient ADAL monituje o uwierzytelnianie wieloskładnikowe. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania. | Monit oparty na formularzach na serwerze AD FS Server. Użytkownik nie loguje się przy pierwszym współczynniku. |
| Autoryzowany użytkownik próbuje się zalogować, ale wprowadza nieprawidłowe hasło. | Użytkownik przechodzi do adresu URL aplikacji i otrzymuje zły błąd nazwy użytkownika/hasła. |
| Autoryzowany użytkownik klika łącze w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika adres URL i jest zalogowany do aplikacji bez dodatkowych monitów. |
| Autoryzowany użytkownik klika łącze w wiadomości e-mail i nie jest jeszcze uwierzytelniony. | Użytkownik klika adres URL i jest monitem o uwierzytelnienie przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik loguje się do aplikacji za pomocą aplikacji mobilnej (inicjowane przez SP) z nową próbą logowania. | Monit oparty na formularzach na serwerze AD FS Server. Użytkownik pomyślnie loguje się i klient ADAL monituje o uwierzytelnianie wieloskładnikowe. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania (inicjowanego przez SP). | Monit oparty na formularzach na serwerze AD FS Server. Użytkownik nie loguje się przy pierwszym współczynniku. |
| Autoryzowany użytkownik próbuje się zalogować, ale wprowadza nieprawidłowe hasło.| Użytkownik przechodzi do adresu URL aplikacji i otrzymuje zły błąd nazwy użytkownika/hasła. |
| Autoryzowany użytkownik wylogowuje się, a następnie loguje się ponownie. | Jeśli skonfigurowano adres URL wylogowywania, użytkownik jest wylogowany ze wszystkich usług i monituje o uwierzytelnienie. |
| Autoryzowany użytkownik wylogowuje się, a następnie loguje się ponownie. | Jeśli adres URL wylogowywania nie jest skonfigurowany, użytkownik zostanie automatycznie zalogowany ponownie przy użyciu istniejącego tokenu z istniejącej sesji przeglądarki usługi Azure AD. |
| Autoryzowany użytkownik klika łącze w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika adres URL i jest zalogowany do aplikacji bez dodatkowych monitów. |
| Autoryzowany użytkownik klika łącze w wiadomości e-mail i nie jest jeszcze uwierzytelniony. | Użytkownik klika adres URL i jest monitem o uwierzytelnienie przy użyciu pierwszego czynnika. |

## <a name="manage-sso"></a>Zarządzanie użytkownikami sytego

W tej sekcji przedstawiono wymagania i zalecenia, aby pomyślnie zarządzać sytuajszowy.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

Zawsze używaj roli z najmniejszą liczą uprawnień dostępnych do wykonania wymaganego zadania w usłudze Azure Active Directory. Firma Microsoft zaleca [przejrzenie różnych ról, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) i wybrać odpowiedni, aby rozwiązać swoje potrzeby dla każdej persona dla tej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunięcia po zakończeniu wdrażania.

| Persona| Role | Rola usługi Azure AD (jeśli jest wymagana) |
|--------|-------|-----------------------------|
| Administrator działu pomocy technicznej | Obsługa poziomu 1 | Brak |
| Administrator tożsamości | Konfigurowanie i debugowanie, gdy problemy mają wpływ na usługę Azure AD | Administrator globalny |
| Administrator aplikacji | Zaświadczenie użytkownika w aplikacji, konfiguracja użytkowników z uprawnieniami | Brak |
| Administratorzy infrastruktury | Właściciel najazd cert | Administrator globalny |
| Właściciel firmy/interesariusz | Zaświadczenie użytkownika w aplikacji, konfiguracja użytkowników z uprawnieniami | Brak |

Zaleca się używanie [zarządzania uprzywilejowanymi tożsamościami](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) do zarządzania rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu dla użytkowników z uprawnieniami do katalogów.

### <a name="sso-certificate-lifecycle-management"></a>Zarządzanie cyklem życia certyfikatów SSO

Ważne jest, aby zidentyfikować odpowiednie role i listy dystrybucyjne poczty e-mail, których zadaniem jest zarządzanie cyklem życia certyfikatu podpisywania między usługą Azure AD a aplikacją skonfigurowaną za pomocą logowania jednokrotnego. Oto niektóre z kluczowych ról, które zalecamy w miejscu:

- Właściciel do aktualizowania właściwości użytkownika w aplikacji
- Właściciel on-call dla wsparcia przerwy/naprawy aplikacji
- Ściśle monitorowana lista dystrybucji wiadomości e-mail dla powiadomień o zmianach związanych z certyfikatami

Maksymalny okres ważności certyfikatu wynosi trzy lata. Zaleca się ustanowienie procesu, w jaki sposób będziesz obsługiwać zmiany certyfikatu między usługą Azure AD i aplikacji. Może to pomóc w zapobieganiu lub minimalizowaniu awarii z powodu wygaśnięcia certyfikatu lub wymuszenia przerzucenia certyfikatu.

### <a name="rollback-process"></a>Proces wycofywania

Po zakończeniu testowania na podstawie przypadków testowych, nadszedł czas, aby przejść do produkcji z aplikacją. Przejście do produkcji oznacza, że zaimplementujesz zaplanowane i przetestowane konfiguracje w dzierżawie produkcyjnej i wdrożysz ją dla użytkowników. Jednak należy zaplanować, co należy zrobić w przypadku, gdy wdrożenie nie pójdzie zgodnie z planem. Jeśli konfiguracja SSO nie powiedzie się podczas wdrażania, należy zrozumieć, jak ograniczyć wszelkie awarie i zmniejszyć wpływ na użytkowników.

Dostępność metod uwierzytelniania w aplikacji określi najlepszą strategię. Zawsze upewnij się, że masz szczegółową dokumentację dla właścicieli aplikacji na dokładnie jak wrócić do stanu konfiguracji oryginalnego logowania w przypadku, gdy wdrożenie działa na problemy.

- **Jeśli aplikacja obsługuje wielu dostawców tożsamości**, na przykład LDAP i AD FS i Ping, nie należy usuwać istniejącej konfiguracji jednokrotnego podczas wdrażania. Zamiast tego należy wyłączyć go podczas migracji w przypadku, gdy trzeba przełączyć go z powrotem później. 

- **Jeśli aplikacja nie obsługuje wielu adresów IP,** ale umożliwia użytkownikom zalogowanie się przy użyciu uwierzytelniania opartego na formularzach (nazwa użytkownika/hasło), upewnij się, że użytkownicy mogą wrócić do tego podejścia w przypadku niepowodzenia wdrożenia nowej konfiguracji logowania jednokrotnego.

### <a name="access-management"></a>Zarządzanie dostępem

Zaleca się wybranie podejścia skalowane podczas zarządzania dostępem do zasobów. Typowe podejścia obejmują korzystanie z grup lokalnych przez synchronizację za pośrednictwem usługi Azure AD Connect, [tworzenie grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)lub tworzenie grup [samoobsługowych](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) w usłudze Azure AD zarządzanych przez właściciela zasobu.

### <a name="monitor-security"></a>Monitorowanie zabezpieczeń

Zalecamy skonfigurowanie regularnego rytmu, w którym można przejrzeć różne aspekty zabezpieczeń aplikacji SaaS i wykonać wszelkie wymagane akcje naprawcze.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniższe łącza przedstawiają scenariusze rozwiązywania problemów. Można utworzyć specjalny przewodnik dla personelu pomocy technicznej, który zawiera te scenariusze i kroki, aby je naprawić.

#### <a name="consent-issues"></a>Problemy z zgodą

- [Nieoczekiwany błąd zgody](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Błąd zgody użytkownika](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemy dotyczące logowania

- [Problemy z logowaniem się z portalu niestandardowego](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemy podczas logowania się z poziomu panelu dostępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Błąd na stronie logowania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problem z logowaniem się do aplikacji firmy Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemy z przysłówek sytuacyjnych dla aplikacji wymienionych w Galerii aplikacji platformy Azure

- [Problem z logiem logowania bez podania przyuśmiać dla aplikacji wymienionych w Galerii aplikacji platformy Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problem z federacowanym identyfikatorem myszy dla aplikacji wymienionych w Galerii aplikacji platformy Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemy z przyszyciem dla aplikacji NIE wymienione w Galerii aplikacji platformy Azure

- [Problem z logiem logowania bez podania hasła dla aplikacji NIE wymienionych w Galerii aplikacji platformy Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problem z sfederowanym identyfikatorem myszy dla aplikacji NIE wymieniony w Galerii aplikacji platformy Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Następne kroki

[Debugowanie logowania jednokrotnego opartego na protokole SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapowanie oświadczeń dla aplikacji za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Dostosowywanie oświadczeń wystawionych w tokenie SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protokół SAML logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protokół SAML wylogowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Usługa Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (dla użytkowników zewnętrznych, takich jak partnerzy i dostawcy)

[Dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Dostęp z użyciem logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Samouczek sytego zgłoszenia aplikacji](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
