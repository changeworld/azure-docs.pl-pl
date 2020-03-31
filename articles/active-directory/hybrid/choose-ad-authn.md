---
title: Uwierzytelnianie dla rozwiązań tożsamości hybrydowej usługi Azure AD
titleSuffix: Active Directory
description: Ten przewodnik pomaga dyrektorom generalnym, dyrektorom obiektów społeczeństwa, jednostkom CISO, głównym architektom tożsamości, architektom przedsiębiorstw oraz decydentom zabezpieczeń i it odpowiedzialnym za wybór metody uwierzytelniania dla ich rozwiązania tożsamości hybrydowej usługi Azure AD w średnich i dużych organizacjach.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e263ecde532a8aaed420932bf355910da201723e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365844"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Wybierz odpowiednią metodę uwierzytelniania dla rozwiązania do tożsamości hybrydowej usługi Azure Active Directory

Wybranie poprawnej metody uwierzytelniania jest pierwszą obawą organizacji, które chcą przenieść swoje aplikacje do chmury. Nie podejmuj tej decyzji pochopnie, z następujących powodów:

1. Jest to pierwsza decyzja dla organizacji, która chce przejść do chmury.

2. Metoda uwierzytelniania jest krytycznym składnikiem obecności organizacji w chmurze. Kontroluje dostęp do wszystkich danych i zasobów w chmurze.

3. Jest podstawą wszystkich innych zaawansowanych funkcji zabezpieczeń i środowiska użytkownika w usłudze Azure AD.

Tożsamość to nowa płaszczyzna kontroli zabezpieczeń IT, więc uwierzytelnianie jest ochroną dostępu organizacji do nowego świata chmury. Organizacje potrzebują płaszczyzny kontroli tożsamości, która zwiększa ich bezpieczeństwo i chroni swoje aplikacje w chmurze przed intruzami.

> [!NOTE]
> Zmiana metody uwierzytelniania wymaga planowania, testowania i potencjalnie przestojów. [Wdrożenie etapowe](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) to świetny sposób na testowanie i stopniowe migrowanie z uwierzytelniania federacji do chmury.

### <a name="out-of-scope"></a>Poza zakresem
Organizacje, które nie mają istniejącego lokalnego śladu katalogowego, nie są przedmiotem tego artykułu. Zazwyczaj te firmy tworzą tożsamości tylko w chmurze, która nie wymaga rozwiązania tożsamości hybrydowej. Tożsamości tylko w chmurze istnieją wyłącznie w chmurze i nie są skojarzone z odpowiednimi tożsamościami lokalnymi.

## <a name="authentication-methods"></a>Metody uwierzytelniania
Gdy rozwiązanie tożsamości hybrydowej usługi Azure AD jest nową płaszczyzną sterowania, uwierzytelnianie jest podstawą dostępu do chmury. Wybranie poprawnej metody uwierzytelniania jest kluczową pierwszą decyzją podczas konfigurowania rozwiązania tożsamości hybrydowej usługi Azure AD. Zaimplementuj metodę uwierzytelniania skonfigurowana przy użyciu usługi Azure AD Connect, która również apowie na użytkowników w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt wdrożenia wyboru. Te czynniki są różne dla każdej organizacji i mogą się zmieniać w czasie.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Usługa Azure AD obsługuje następujące metody uwierzytelniania dla rozwiązań tożsamości hybrydowej.

### <a name="cloud-authentication"></a>Uwierzytelnianie w chmurze
Po wybraniu tej metody uwierzytelniania usługa Azure AD obsługuje proces logowania użytkowników. W połączeniu z bezproblemowym logowaniem jednokrotnym użytkownicy mogą logować się do aplikacji w chmurze bez konieczności ponownego wniesienia poświadczeń. Za pomocą uwierzytelniania w chmurze można wybrać jedną z dwóch opcji:

**Synchronizacja skrótów haseł usługi Azure AD**. Najprostszy sposób włączania uwierzytelniania dla lokalnych obiektów katalogu w usłudze Azure AD. Użytkownicy mogą używać tej samej nazwy użytkownika i hasła, których używają lokalnie bez konieczności wdrażania dodatkowej infrastruktury. Niektóre funkcje usługi Azure AD w wersji Premium, takie jak ochrona tożsamości i [usługi domenowe usługi Azure AD,](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)wymagają synchronizacji skrótów haseł, niezależnie od wybranej metody uwierzytelniania.

> [!NOTE]
> Hasła nigdy nie są przechowywane w postaci zwykłego tekstu ani szyfrowane za pomocą odwracalnego algorytmu w usłudze Azure AD. Aby uzyskać więcej informacji na temat rzeczywistego procesu synchronizacji skrótów haseł, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Uwierzytelnianie przekazywane usługi Azure AD**. Zapewnia proste sprawdzanie poprawności hasła dla usług uwierzytelniania usługi Azure AD przy użyciu agenta oprogramowania, który działa na co najmniej jednym serwerze lokalnym. Serwery sprawdzają poprawność użytkowników bezpośrednio za pomocą lokalnej usługi Active Directory, co zapewnia, że sprawdzanie poprawności hasła nie odbywa się w chmurze.

Firmy z wymogiem zabezpieczeń, aby natychmiast wymusić lokalne stany konta użytkowników, zasady haseł i godziny logowania, mogą używać tej metody uwierzytelniania. Aby uzyskać więcej informacji na temat rzeczywistego procesu uwierzytelniania przekazywanego, zobacz [Logowanie użytkownika za pomocą uwierzytelniania przekazywanego usługi Azure AD.](../../active-directory/hybrid/how-to-connect-pta.md)

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne
Po wybraniu tej metody uwierzytelniania usługa Azure AD przekazuje proces uwierzytelniania do oddzielnego zaufanego systemu uwierzytelniania, takiego jak lokalne usługi federacyjne Active Directory (AD FS), aby zweryfikować hasło użytkownika.

System uwierzytelniania może zapewnić dodatkowe wymagania dotyczące uwierzytelniania zaawansowanego. Przykładami są uwierzytelnianie oparte na kartach inteligentnych lub uwierzytelnianie wieloskładnikowe innych firm. Aby uzyskać więcej informacji, zobacz [Wdrażanie Usług Federacyjnych Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Poniższa sekcja pomaga zdecydować, która metoda uwierzytelniania jest odpowiednia dla Ciebie przy użyciu drzewa decyzyjnego. Pomaga określić, czy wdrożyć uwierzytelnianie w chmurze lub federacyjne dla rozwiązania tożsamości hybrydowej usługi Azure AD.

## <a name="decision-tree"></a>Drzewo decyzyjne

![Drzewo decyzyjne uwierzytelniania usługi Azure AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Szczegółowe informacje na temat pytań decyzyjnych:

1. Usługa Azure AD może obsługiwać logowania dla użytkowników bez polegania na składnikach lokalnych w celu weryfikacji haseł.
2. Usługa Azure AD może przekazać logowanie użytkownika zaufanemu dostawcy uwierzytelniania, takiemu jak usługi AD FS firmy Microsoft.
3. Jeśli musisz zastosować zasady zabezpieczeń usługi Active Directory na poziomie użytkownika, takie jak wygasłe konto, wyłączone konto, wygasłe hasło, zablokowane konto i godziny logowania się na każdym użytkowniku, usługa Azure AD wymaga niektórych składników lokalnych.
4. Funkcje logowania nie są obsługiwane natywnie przez usługę Azure AD:
   * Zaloguj się przy użyciu kart inteligentnych lub certyfikatów.
   * Zaloguj się przy użyciu lokalnego serwera usługi MFA.
   * Zaloguj się przy użyciu rozwiązania uwierzytelniania innych firm.
   * Rozwiązanie uwierzytelniania lokalnego w wielu lokacjach.
5. Usługa Azure AD Identity Protection wymaga synchronizacji hash hasła, niezależnie od wybranej metody logowania, aby zapewnić *użytkownikom raport wyciekań poświadczeń.* Organizacje mogą przejść awaryjnie do synchronizacji skrótów haseł, jeśli ich podstawowa metoda logowania nie powiedzie się i została skonfigurowana przed zdarzeniem błędu.

> [!NOTE]
> Usługa Azure AD Identity Protection wymaga licencji [usługi Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Szczegółowe uwagi

### <a name="cloud-authentication-password-hash-synchronization"></a>Uwierzytelnianie w chmurze: synchronizacja skrótów haseł

* **Nakład pracy**. Synchronizacja skrótów haseł wymaga najmniejszego wysiłku związanego z wdrażaniem, konserwacją i infrastrukturą.  Ten poziom nakładu pracy zazwyczaj dotyczy organizacji, które potrzebują tylko swoich użytkowników do logowania się do usługi Office 365, aplikacji SaaS i innych zasobów opartych na usłudze Azure AD. Po włączeniu synchronizacja skrótów haseł jest częścią procesu synchronizacji usługi Azure AD Connect i jest uruchamiana co dwie minuty.

* **Doświadczenie użytkownika**. Aby usprawnić środowisko logowania użytkowników, wdrożyć bezproblemowe logowanie jednokrotne z synchronizacją skrótów haseł. Bezproblemowe logowanie jednokrotne eliminuje niepotrzebne monity po zalogowaniu się użytkowników.

* **Zaawansowane scenariusze**. Jeśli organizacje zdecydują się na to, można użyć szczegółowych informacji z tożsamości z raportami usługi Azure AD Identity Protection z usługą Azure AD Premium P2. Przykładem jest raport wyciekanych poświadczeń. Windows Hello for Business ma [określone wymagania podczas korzystania z synchronizacji skrótów haseł](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). [Usługi domenowe usługi Azure AD domain services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) wymaga synchronizacji skrótów haseł do inicjowania obsługi administracyjnej użytkowników z ich poświadczenia firmy w domenie zarządzanej.

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego z synchronizacją skrótów haseł, muszą używać uwierzytelniania wieloskładnikowego usługi Azure AD lub [formantów niestandardowych dostępu warunkowego](../../active-directory/conditional-access/controls.md#custom-controls-preview). Te organizacje nie mogą używać metod uwierzytelniania wieloskładnikowego innych firm lub lokalnych, które opierają się na federacji.

> [!NOTE]
> Dostęp warunkowy usługi Azure AD wymaga licencji [usługi Azure AD Premium P1.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Ciągłość biznesowa**. Przy użyciu synchronizacji skrótu haseł z uwierzytelnianiem w chmurze jest wysoce dostępna jako usługa w chmurze, która jest skalowana do wszystkich centrów danych firmy Microsoft. Aby upewnić się, że synchronizacja skrótów haseł nie jest wyłączana przez dłuższy czas, należy wdrożyć drugi serwer usługi Azure AD Connect w trybie przejściowym w konfiguracji wstrzymania.

* **Zagadnienia**. Obecnie synchronizacja skrótów haseł nie wymusza natychmiast zmian w stanach kont lokalnych. W tej sytuacji użytkownik ma dostęp do aplikacji w chmurze, dopóki stan konta użytkownika nie zostanie zsynchronizowany z usługą Azure AD. Organizacje mogą chcieć przezwyciężyć to ograniczenie, uruchamiając nowy cykl synchronizacji po tym, jak administratorzy dokonują zbiorczych aktualizacji lokalnych stanów konta użytkowników. Przykładem jest wyłączenie kont.

> [!NOTE]
> Hasło wygasło, a stany blokady konta nie są obecnie synchronizowane z usługą Azure AD za pomocą usługi Azure AD Connect. Po zmianie hasła użytkownika i ustawić *użytkownik musi zmienić hasło przy następnej* flagi logowania, hash hasło nie zostaną zsynchronizowane z usługą Azure AD z usługą Azure AD Connect, dopóki użytkownik zmieni swoje hasło.

Aby uzyskać instrukcje wdrażania, zapoznaj się [z implementacją synchronizacji skrótów haseł.](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="cloud-authentication-pass-through-authentication"></a>Uwierzytelnianie w chmurze: uwierzytelnianie przekazywane  

* **Nakład pracy**. Do uwierzytelniania przekazywanego potrzebny jest co najmniej jeden (zalecamy trzy) lekkie środki zainstalowane na istniejących serwerach. Agenci ci muszą mieć dostęp do lokalnych Usług domenowych Active Directory, w tym lokalnych kontrolerów domeny usługi AD. Potrzebują wychodzącego dostępu do Internetu i dostępu do kontrolerów domeny. Z tego powodu nie jest obsługiwany do wdrażania agentów w sieci obwodowej.

    Uwierzytelnianie przekazywane wymaga nieograniczonego dostępu do sieci kontrolerów domeny. Cały ruch sieciowy jest szyfrowany i ograniczony do żądań uwierzytelniania. Aby uzyskać więcej informacji na temat tego procesu, zobacz [głębokie zagłębienie zabezpieczeń](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) w zakresie uwierzytelniania przekazywanego.

* **Doświadczenie użytkownika**. Aby usprawnić środowisko logowania użytkowników, wdrożyć bezproblemowe logowanie jednokrotne z uwierzytelnianiem pass-through. Bezproblemowe logowanie jednokrotne eliminuje niepotrzebne monity po zalogowaniu się użytkowników.

* **Zaawansowane scenariusze**. Uwierzytelnianie przekazywane wymusza zasady konta lokalnego w momencie logowania. Na przykład odmowa dostępu, gdy stan konta użytkownika lokalnego jest wyłączony, zablokowany lub jego [hasło wygasa](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) lub próba logowania wykracza poza godziny, gdy użytkownik może się zalogować.

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego z uwierzytelnianiem przekazywającym, muszą używać [niestandardowych formantów](../../active-directory/conditional-access/controls.md#custom-controls-preview)usługi Azure Multi-Factor Authentication (MFA) lub dostępu warunkowego. Te organizacje nie mogą używać metody uwierzytelniania wieloskładnikowego innej firmy lub lokalnej, która opiera się na federacji. Zaawansowane funkcje wymagają, aby synchronizacja skrótów haseł była wdrażana niezależnie od tego, czy użytkownik wybierze uwierzytelnianie przekazywane. Przykładem jest raport wyciekanych poświadczeń ochrony tożsamości.

* **Ciągłość biznesowa**. Zaleca się wdrożenie dwóch dodatkowych agentów uwierzytelniania przekazywania. Te dodatki są dodatkiem do pierwszego agenta na serwerze usługi Azure AD Connect. To dodatkowe wdrożenie zapewnia wysoką dostępność żądań uwierzytelniania. Jeśli masz trzech agentów wdrożonych, jeden agent może nadal zakończyć się niepowodzeniem, gdy inny agent jest w dół do konserwacji.

    Istnieje inna zaleta wdrażania synchronizacji skrótów haseł oprócz uwierzytelniania przekazywanego. Działa jako metoda uwierzytelniania kopii zapasowej, gdy podstawowa metoda uwierzytelniania nie jest już dostępna.

* **Zagadnienia**. Synchronizacja skrótów haseł można użyć jako metody uwierzytelniania kopii zapasowej dla uwierzytelniania przekazywanego, gdy agenci nie mogą sprawdzić poprawność poświadczeń użytkownika z powodu istotnego błędu lokalnego. Synchronizacja skrótu hasła w trybie fail over nie odbywa się automatycznie i należy użyć usługi Azure AD Connect, aby ręcznie przełączyć metodę logowania.

    Aby zapoznać się z innymi zagadnieniami dotyczącymi uwierzytelniania przekazywanego, w tym obsługi [identyfikatorów](../../active-directory/hybrid/how-to-connect-pta-faq.md)alternatywnych, zobacz często zadawane pytania .

Zapoznaj się [z implementacji uwierzytelniania przekazywanego](../../active-directory/hybrid/how-to-connect-pta.md) dla kroków wdrażania.

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne

* **Nakład pracy**. System uwierzytelniania federacyjnego opiera się na zewnętrznym zaufanym systemie do uwierzytelniania użytkowników. Niektóre firmy chcą ponownie wykorzystać swoje istniejące inwestycje w system federacyjny z ich rozwiązania tożsamości hybrydowej usługi Azure AD. Konserwacja i zarządzanie systemem federacyjnym nie podlega kontroli usługi Azure AD. To do organizacji za pomocą systemu federacyjnego, aby upewnić się, że jest bezpiecznie wdrożony i może obsłużyć obciążenia uwierzytelniania.

* **Doświadczenie użytkownika**. Środowisko użytkownika uwierzytelniania federacyjnego zależy od implementacji funkcji, topologii i konfiguracji farmy federacyjnej. Niektóre organizacje potrzebują tej elastyczności, aby dostosować i skonfigurować dostęp do farmy federacyjnej zgodnie z ich wymaganiami dotyczącymi zabezpieczeń. Na przykład można skonfigurować wewnętrznie połączonych użytkowników i urządzeń, aby automatycznie logować się do użytkowników, bez monitowania ich o poświadczenia. Ta konfiguracja działa, ponieważ już zalogowali się do swoich urządzeń. W razie potrzeby niektóre zaawansowane funkcje zabezpieczeń utrudniają proces logowania użytkowników.

* **Zaawansowane scenariusze**. Rozwiązanie uwierzytelniania federacyjnego jest zwykle wymagane, gdy klienci mają wymagania dotyczące uwierzytelniania, które usługa Azure AD nie obsługuje natywnie. Zobacz szczegółowe informacje ułatwiające [wybranie odpowiedniej opcji logowania](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Należy wziąć pod uwagę następujące wspólne wymagania:

  * Uwierzytelnianie, które wymaga kart inteligentnych lub certyfikatów.
  * Lokalne serwery usługi MFA lub dostawcy wielu czynników innych firm wymagający dostawcy tożsamości federacyjnej.
  * Uwierzytelnianie przy użyciu rozwiązań uwierzytelniania innych firm. Zobacz [listę zgodności federacji usługi Azure AD](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Zaloguj się, który wymaga sAMAccountName, na przykład DOMENA\nazwa użytkownika, zamiast głównej user@domain.comnazwy użytkownika (UPN), na przykład .

* **Ciągłość biznesowa**. Systemy federacyjne zazwyczaj wymagają szeregu serwerów z równoważenia obciążenia, znanych jako farma. Ta farma jest skonfigurowana w sieci wewnętrznej i topologii sieci obwodowej, aby zapewnić wysoką dostępność żądań uwierzytelniania.

    Wdrażanie synchronizacji skrótów haseł wraz z uwierzytelnianiem federacyjnym jako metodą uwierzytelniania kopii zapasowej, gdy podstawowa metoda uwierzytelniania nie jest już dostępna. Przykładem jest, gdy serwery lokalne nie są dostępne. Niektóre duże organizacje przedsiębiorstw wymagają rozwiązania federacyjnego do obsługi wielu punktów transferu danych przychodzących w Internecie skonfigurowanych z systemem geo-DNS dla żądań uwierzytelniania o małych opóźnieniach.

* **Zagadnienia**. Systemy federacyjne zazwyczaj wymagają bardziej znaczących inwestycji w infrastrukturę lokalną. Większość organizacji wybiera tę opcję, jeśli ma już inwestycję lokalną federacji. A jeśli jest to silne wymaganie biznesowe, aby korzystać z dostawcy jednej tożsamości. Federacja jest bardziej skomplikowana do obsługi i rozwiązywania problemów w porównaniu do rozwiązań uwierzytelniania w chmurze.

W przypadku domeny nie rutowalnej, której nie można zweryfikować w usłudze Azure AD, potrzebujesz dodatkowej konfiguracji, aby zaimplementować logowanie identyfikatora użytkownika. To wymaganie jest znane jako obsługa alternatywnego identyfikatora logowania. Aby uzyskać ograniczenia i wymagania, zobacz [Konfigurowanie alternatywnego identyfikatora logowania.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) Jeśli zdecydujesz się użyć dostawcy uwierzytelniania wieloskładnikowego innej firmy z federacją, upewnij się, że dostawca obsługuje usługę WS-Trust, aby umożliwić urządzeniom dołączenie do usługi Azure AD.

Aby uzyskać instrukcje wdrażania, zobacz [Wdrażanie serwerów](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) federacyjnych.

> [!NOTE]
> Podczas wdrażania rozwiązania tożsamości hybrydowej usługi Azure AD należy zaimplementować jedną z obsługiwanych topologii usługi Azure AD Connect. Dowiedz się więcej o obsługiwanych i nieobsługiwałych konfiguracjach w [topologii dla usługi Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramy architektury

Na poniższych diagramach opisano składniki architektury wysokiego poziomu wymagane dla każdej metody uwierzytelniania, której można używać z rozwiązaniem tożsamości hybrydowej usługi Azure AD. Zapewniają one przegląd, który pomoże Ci porównać różnice między rozwiązaniami.

* Prostota rozwiązania do synchronizacji skrótów haseł:

    ![Tożsamość hybrydowa usługi Azure AD z synchronizacją skrótów haseł](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Wymagania agenta uwierzytelniania przekazywanego, przy użyciu dwóch agentów do nadmiarowości:

    ![Tożsamość hybrydowa usługi Azure AD z uwierzytelnianiem przekazywalne](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Składniki wymagane dla federacji w obwodzie i wewnętrznej sieci organizacji:

    ![Tożsamość hybrydowa usługi Azure AD z uwierzytelnianiem federatywnym](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porównywanie metod

|Kwestie do rozważenia|Synchronizacja skrótów haseł + bezproblemowe logowanie jednokrotne|Uwierzytelnianie przekazywane + bezproblemowe logowanie jednokrotne|Federacja z usługami AD FS|
|:-----|:-----|:-----|:-----|
|Gdzie odbywa się uwierzytelnianie?|W chmurze|W chmurze po bezpiecznej wymianie weryfikacji hasła z lokalnym agentem uwierzytelniania|Lokalnie|
|Jakie są wymagania serwera lokalnego poza systemem inicjowania obsługi administracyjnej: Azure AD Connect?|Brak|Jeden serwer dla każdego dodatkowego agenta uwierzytelniania|Co najmniej dwa serwery usług AD FS<br><br>Dwa lub więcej serwerów WAP w sieci obwodowej/DMZ|
|Jakie są wymagania dotyczące lokalnego Internetu i sieci poza systemem inicjowania obsługi administracyjnej?|Brak|[Wychodzący dostęp do Internetu](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) z serwerów z uruchomionymi agentami uwierzytelniania|[Przychodzący dostęp do Internetu](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) do serwerów WAP w obwodzie<br><br>Przychodzący dostęp do sieci do serwerów usług AD FS z serwerów WAP w obwodzie<br><br>Równoważenie obciążenia sieciowego|
|Czy istnieje wymóg certyfikatu TLS/SSL?|Nie|Nie|Tak|
|Czy istnieje rozwiązanie do monitorowania stanu zdrowia?|Niewymagane|Stan agenta zapewniany przez [centrum administracyjne usługi Azure Active Directory](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Czy użytkownicy mogą jednokrotnie logować się do zasobów w chmurze z urządzeń przyłączonych do domeny w sieci firmowej?|Tak z [bezproblemowym logowaniem jednokrotnym](../../active-directory/hybrid/how-to-connect-sso.md)|Tak z [bezproblemowym logowaniem jednokrotnym](../../active-directory/hybrid/how-to-connect-sso.md)|Tak|
|Jakie typy logowania są obsługiwane?|UserPrincipalName + hasło<br><br>Uwierzytelnianie zintegrowane z systemem Windows przy użyciu [bezproblemowego logowania jednokrotnego](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatywny identyfikator logowania](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + hasło<br><br>Uwierzytelnianie zintegrowane z systemem Windows przy użyciu [bezproblemowego logowania jednokrotnego](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatywny identyfikator logowania](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + hasło<br><br>sAMAccountName + hasło<br><br>Uwierzytelnianie zintegrowane z systemem Windows<br><br>[Uwierzytelnianie certyfikatów i kart inteligentnych](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatywny identyfikator logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Czy usługa Windows Hello dla firm jest obsługiwana?|[Model zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Model zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Wymaga poziomu funkcjonalności domeny systemu Windows Server 2016*|[Model zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jakie są opcje uwierzytelniania wieloskładnikowego?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Niestandardowe formanty z dostępem warunkowym*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Niestandardowe formanty z dostępem warunkowym*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Serwer usługi Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Pomoc makrofinansowa innych firm](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Niestandardowe formanty z dostępem warunkowym*](../../active-directory/conditional-access/controls.md)|
|Jakie stany konta użytkownika są obsługiwane?|Konta wyłączone<br>(opóźnienie do 30 minut)|Konta wyłączone<br><br>Konto zablokowane<br><br>Konto wygasło<br><br>Hasło wygasło<br><br>Godziny logowania|Konta wyłączone<br><br>Konto zablokowane<br><br>Konto wygasło<br><br>Hasło wygasło<br><br>Godziny logowania|
|Jakie są opcje dostępu warunkowego?|[Dostęp warunkowy usługi Azure AD z usługą Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Dostęp warunkowy usługi Azure AD z usługą Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Dostęp warunkowy usługi Azure AD z usługą Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Reguły oświadczeń usług AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Czy blokowanie starszych protokołów jest obsługiwane?|[Tak](../../active-directory/conditional-access/overview.md)|[Tak](../../active-directory/conditional-access/overview.md)|[Tak](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Czy można dostosować logo, obraz i opis na stronach logowania?|[Tak, dzięki usłudze Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Tak, dzięki usłudze Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Tak](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Jakie zaawansowane scenariusze są obsługiwane?|[Inteligentna blokada hasła](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Raporty przecieków poświadczeń z usługą Azure AD Premium P2](../../active-directory/reports-monitoring/concept-risk-events.md)|[Inteligentna blokada hasła](../../active-directory/authentication/howto-password-smart-lockout.md)|System uwierzytelniania z małymi opóźnieniami w wielu układzie<br><br>[Blokada ekstranetu usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integracja z zewnętrznymi systemami tożsamości](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Formanty niestandardowe w usłudze Azure AD Dostęp warunkowy obecnie nie obsługują rejestracji urządzenia.

## <a name="recommendations"></a>Zalecenia
System tożsamości zapewnia użytkownikom dostęp do aplikacji w chmurze i aplikacji biznesowych, które przeprowadzasz migrację i udostępniasz w chmurze. Aby zapewnić autorom produktywne i złe podmioty z poufnych danych organizacji, uwierzytelnianie kontroluje dostęp do aplikacji.

Użyj lub włącz synchronizację skrótów haseł dla wybranej metody uwierzytelniania, z następujących powodów:

1. **Wysoka dostępność i odzyskiwanie po awarii**. Uwierzytelnianie przekazywane i federacja polegają na infrastrukturze lokalnej. W przypadku uwierzytelniania przekazywanego lokalna footprint zawiera sprzęt serwera i sieci, których wymagają agenci uwierzytelniania przekazywanego. W przypadku federacji ślad lokalny jest jeszcze większy. Wymaga serwerów w sieci obwodowej do żądań uwierzytelniania serwera proxy i wewnętrznych serwerów federacyjnych.

    Aby uniknąć pojedynczych punktów awarii, należy wdrożyć nadmiarowe serwery. Następnie żądania uwierzytelniania będą zawsze obsługiwane, jeśli którykolwiek składnik ulegnie awarii. Zarówno uwierzytelnianie przekazywane, jak i federacja również polegają na kontrolerach domeny, aby odpowiadać na żądania uwierzytelniania, co również może zakończyć się niepowodzeniem. Wiele z tych składników wymaga konserwacji, aby zachować zdrowie. Awarie są bardziej prawdopodobne, gdy konserwacja nie jest zaplanowana i zaimplementowana poprawnie. Unikaj awarii przy użyciu synchronizacji skrótów haseł, ponieważ usługa uwierzytelniania w chmurze usługi Microsoft Azure AD jest skalowana globalnie i jest zawsze dostępna.

2. **Przetrwanie awarii lokalnej**.  Konsekwencje awarii lokalnej z powodu cyberataku lub katastrofy mogą być znaczne, począwszy od utraty reputacji marki do sparaliżowanej organizacji, która nie jest w stanie poradzić sobie z atakiem. Ostatnio wiele organizacji padło ofiarą ataków złośliwego oprogramowania, w tym ukierunkowanego oprogramowania ransomware, które spowodowało, że ich serwery lokalne upadły. Gdy firma Microsoft pomaga klientom radzić sobie z tego rodzaju atakami, widzi dwie kategorie organizacji:

   * Organizacje, które wcześniej włączyły synchronizację skrótów haseł, zmieniły metodę uwierzytelniania, aby używać synchronizacji skrótów haseł. Byli z powrotem online w ciągu kilku godzin. Korzystając z dostępu do poczty e-mail za pośrednictwem usługi Office 365, pracowali nad rozwiązywaniem problemów i uzyskiwaniem dostępu do innych obciążeń w chmurze.

   * Organizacje, które wcześniej nie włączały synchronizacji skrótów haseł, musiały uciekać się do niezaufanych zewnętrznych konsumenckich systemów poczty e-mail w celu rozwiązania problemów. W takich przypadkach zajęło im kilka tygodni, aby przywrócić ich lokalnej infrastruktury tożsamości, zanim użytkownicy mogli zalogować się do aplikacji opartych na chmurze ponownie.

3. **Ochrona tożsamości**. Jednym z najlepszych sposobów ochrony użytkowników w chmurze jest usługa Azure AD Identity Protection z usługą Azure AD Premium P2. Microsoft stale skanuje Internet w poszukiwaniu list użytkowników i haseł, które źli aktorzy sprzedają i udostępniają w ciemnej sieci. Usługa Azure AD może użyć tych informacji, aby sprawdzić, czy którekolwiek z nazw użytkowników i haseł w organizacji są zagrożone. W związku z tym jest bardzo ważne, aby włączyć synchronizację skrótów haseł bez względu na metodę uwierzytelniania, którego używasz, czy jest to uwierzytelnianie federacyjne lub przekazywane. Wyciekające poświadczenia są prezentowane jako raport. Użyj tych informacji, aby zablokować lub zmusić użytkowników do zmiany haseł podczas próby zalogowania się przy użyciu wyciekłych haseł.

## <a name="conclusion"></a>Podsumowanie

W tym artykule opisano różne opcje uwierzytelniania, które organizacje mogą konfigurować i wdrażać w celu obsługi dostępu do aplikacji w chmurze. Aby spełnić różne wymagania biznesowe, dotyczące zabezpieczeń i techniczne, organizacje mogą wybierać między synchronizacją skrótów haseł, uwierzytelnianiem przekazu i federacją.

Należy wziąć pod uwagę każdą metodę uwierzytelniania. Czy wysiłek wdrożenia rozwiązania i środowisko użytkownika procesu logowania spełnia wymagania biznesowe? Oceń, czy twoja organizacja potrzebuje zaawansowanych scenariuszy i funkcji ciągłości biznesowej każdej metody uwierzytelniania. Na koniec należy ocenić zagadnienia dotyczące każdej metody uwierzytelniania. Czy któryś z nich uniemożliwia ci wdrożenie twojego wyboru?

## <a name="next-steps"></a>Następne kroki

W dzisiejszym świecie zagrożenia są obecne 24 godziny na dobę i pochodzą z całego świata. Zaimplementuj metodę uwierzytelniania poprawne, a to zmniejszy zagrożenia bezpieczeństwa i chronić swoje tożsamości.

[Rozpocznij pracę](../../active-directory/fundamentals/get-started-azure-ad.md) z usługą Azure AD i wdrożyć odpowiednie rozwiązanie uwierzytelniania dla twojej organizacji.

Jeśli myślisz o migracji z uwierzytelniania federacyjnego do uwierzytelniania w chmurze, dowiedz się więcej o [zmianie metody logowania.](../../active-directory/hybrid/plan-connect-user-signin.md) Aby ułatwić planowanie i wdrażanie migracji, użyj [tych planów wdrażania projektu](https://aka.ms/deploymentplans) lub rozważ użycie nowej funkcji [stopniowego wdrażania](../../active-directory/hybrid/how-to-connect-staged-rollout.md) w celu migracji użytkowników federowanych do korzystania z uwierzytelniania w chmurze w podejściu etapowym.
