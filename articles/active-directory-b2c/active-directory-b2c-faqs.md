---
title: Często zadawane pytania dotyczące usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Często zadawane pytania (FAQ) dotyczących usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7a30aecc3cc2259072ea33ae018c371a1f05741a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318030"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Często zadawane pytania 
Ta strona odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory (Azure AD) B2C. Wracaj aktualizacje.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Dlaczego nie mogę uzyskać dostępu do rozszerzenia usługi Azure AD B2C w witrynie Azure portal?
Istnieją dwie typowe przyczyny Dlaczego rozszerzenia usługi Azure AD nie działa dla Ciebie.  Usługa Azure AD B2C wymaga usługi roli użytkownika w katalogu, aby być administratorem globalnym.  Jeśli uważasz, że powinni mieć dostęp, skontaktuj się z administratorem.  Jeśli masz uprawnienia administratora globalnego, upewnij się, że znajdują się w katalogu usługi Azure AD B2C, a nie katalog usługi Azure Active Directory.  Możesz zobaczyć instrukcje dotyczące [tworzenie dzierżawy usługi Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Czy można używać funkcji usługi Azure AD B2C, w ramach mojej dzierżawy usługi Azure AD istniejących, na podstawie pracownik?
Usługa Azure AD i usługi Azure AD B2C są osobne oferty nie mogą współistnieć w tej samej dzierżawy.  Organizacja dzierżawę usługi Azure AD.  Dzierżawy usługi Azure AD B2C reprezentuje kolekcję tożsamości, które mają być używane z aplikacjami danej firmy.  Za pomocą niestandardowych zasad (w publicznej wersji zapoznawczej) usługi Azure AD B2C może tworzyć federacje z usługą Azure AD, dzięki czemu uwierzytelnienia pracowników w organizacji.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Czego służy usługa Azure AD B2C do udostępniania funkcji logowania społecznościowych (Facebook i Google +) do usługi Office 365?
Usługa Azure AD B2C nie może służyć do uwierzytelniania użytkowników w usłudze Microsoft Office 365.  Usługa Azure AD to rozwiązanie firmy Microsoft w celu zarządzania dostępem pracowników do aplikacji SaaS i ma funkcje przeznaczone do tego celu, takich jak licencjonowania i warunkowego dostępu.  Usługa Azure AD B2C zapewnia platformę zarządzania tożsamościami i dostępem do tworzenia aplikacji internetowych i mobilnych.  Po skonfigurowaniu usługi Azure AD B2C w celu sfederowania do dzierżawy usługi Azure AD dzierżawy usługi Azure AD umożliwia zarządzanie dostępem pracowników do aplikacji, które zależą od usługi Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Jakie są lokalne konta w usłudze Azure AD B2C? Czym różnią się one z kont służbowych w usłudze Azure AD?
W dzierżawie usługi Azure AD użytkownicy, którzy należą do dzierżawy Zaloguj się przy użyciu adresu e-mail w formularzu `<xyz>@<tenant domain>`.  `<tenant domain>` Jest jednym ze zweryfikowanym domenom w dzierżawie lub początkowego `<...>.onmicrosoft.com` domeny. Ten typ konta jest konta firmowego lub szkolnego.

W dzierżawie usługi Azure AD B2C, większość aplikacji ma użytkownikowi Zaloguj się przy użyciu dowolnego adresu e-mail dowolnego (na przykład joe@comcast.net, bob@gmail.com, sarah@contoso.com, lub jim@live.com). Ten typ konta jest kontem lokalnym.  Obsługujemy również nazwy użytkownika jest swobodny, jak w przypadku kont lokalnych (na przykład Jan, Robert, sarah lub jim). Można wybrać jedną z tych dwóch typów konta lokalnego, podczas konfigurowania dostawców tożsamości dla usługi Azure AD B2C w witrynie Azure portal. W swojej dzierżawie usługi Azure AD B2C, kliknij przycisk **dostawców tożsamości** , a następnie wybierz **Username** w ramach kont lokalnych. 

Zawsze należy utworzyć konta użytkowników dla aplikacji za pośrednictwem przepływu rejestracji użytkownika, przepływ użytkownika rejestracji lub logowania, lub za pomocą interfejsu API programu Graph usługi Azure AD. Konta użytkowników utworzone w witrynie Azure portal są używane tylko do administrowania dzierżawy.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Dostawców tożsamości społecznościowych, które są obsługiwane teraz? Te, które planujesz obsługiwać w przyszłości?
Obecnie obsługujemy Facebook, Google +, LinkedIn, Amazon, usługi Twitter (wersja zapoznawcza), WeChat (wersja zapoznawcza), Weibo (wersja zapoznawcza) i q (wersja zapoznawcza). Dodamy obsługę innych popularnych społecznościowych dostawców tożsamości na podstawie oczekiwań klienta.

Usługa Azure AD B2C dodano również obsługę [zasady niestandardowe](active-directory-b2c-overview-custom.md).  Te [zasady niestandardowe](active-directory-b2c-overview-custom.md) umożliwia deweloperom tworzenie własnych zasad, który za pomocą dowolnego dostawcy tożsamości, która obsługuje [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) lub SAML. 

Wprowadzenie do zasad niestandardowych, sprawdzając naszych [pakiet startowy niestandardowe zasady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Można skonfigurować zakresy, aby zebrać więcej informacji na temat użytkowników z różnych dostawców tożsamości społecznościowych?
Nie. Zakresy domyślne używane do naszego zestawu obsługiwanych dostawców tożsamości dla sieci społecznościowej są następujące:

* Facebook: email
* Google +: wiadomości e-mail
* Konto Microsoft: profil poczty e-mail protokołu openid
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Moja aplikacja ma działające na platformie Azure, do jego działania w usłudze Azure AD B2C?
Nie można hostować swoją aplikację w dowolnym miejscu (w chmurze lub lokalnie). Wszystko, czego potrzeba interakcji z usługą Azure AD B2C jest możliwość wysyłania i odbierania żądań HTTP na dostępny publicznie punktów końcowych.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mam wiele dzierżaw usługi Azure AD B2C. Jak mogę zarządzać nimi w witrynie Azure portal?
Przed otwarciem usługi Azure AD B2C w witrynie Azure portal w menu po lewej stronie, musisz przełączyć się do katalogu w którym chcesz zarządzać.  Przełącz katalogi, klikając swoją tożsamość w prawym górnym rogu witryny Azure portal, a następnie wybierz katalog, w menu rozwijanym, pojawia się.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>W jaki sposób dostosować weryfikacji wiadomości e-mail (zawartość i "od:" pole) wysyłane przez usługę Azure AD B2C?
Możesz użyć [funkcji znakowania firmowego](../active-directory/fundamentals/customize-branding.md) Dostosowywanie zawartości wiadomości e-mail z weryfikacji. W szczególności można dostosować te dwa elementy wiadomości e-mail:

* **Baner Logo**: Wyświetlany w prawym dolnym rogu.
* **Kolor tła**: Wyświetlane u góry.

    ![Zrzut ekranu przedstawiający dostosowany weryfikacyjnej wiadomości e-mail](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Podpis wiadomości e-mail zawiera nazwę dzierżawy usługi Azure AD B2C, podane podczas tworzenia dzierżawy usługi Azure AD B2C. Możesz zmienić nazwę przy użyciu tych instrukcji:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Administrator globalny.
1. Otwórz **usługi Azure Active Directory** bloku.
1. Kliknij przycisk **właściwości** kartę.
1. Zmiana **nazwa** pola.
1. Kliknij przycisk **Zapisz** w górnej części strony.

Obecnie nie ma możliwości zmiany "od:" na adres e-mail.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak można przeprowadzić migrację mojej istniejącej nazwy użytkownika, hasła i profile z bazy danych usługi Azure AD B2C?
Można zapisać narzędzie do migracji, można użyć interfejsu API programu Graph usługi Azure AD. Zobacz [Przewodnik po migracji użytkownika](active-directory-b2c-user-migration.md) Aby uzyskać szczegółowe informacje.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jakie przepływ użytkownika hasła jest używany dla kont lokalnych w usłudze Azure AD B2C?
Przepływ użytkownika hasła usługi Azure AD B2C dla kont lokalnych jest na podstawie zasad dla usługi Azure AD. Usługa Azure AD B2C jego rejestracji, zarejestruj się lub logowanie i hasła przepływy użytkownika resetowania korzystanie z poziomu "silnego" hasła i nie wygasa hasła. Odczyt [zasad haseł usługi Azure AD](/previous-versions/azure/jj943764(v=azure.100)) Aby uzyskać więcej informacji. Aby uzyskać informacje dotyczące blokady konta i hasła, zobacz [zarządza zagrożeń bezpieczeństwa zasobów i danych w usłudze Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Aby przeprowadzić migrację tożsamości klientów, które są przechowywane na mojej lokalnej usługi Active Directory do usługi Azure AD B2C można używać program Azure AD Connect?
Nie, usługa Azure AD Connect nie jest przeznaczona do pracy z usługą Azure AD B2C. Należy rozważyć użycie [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) przypadku migracji użytkownika.  Zobacz [Przewodnik po migracji użytkownika](active-directory-b2c-user-migration.md) Aby uzyskać szczegółowe informacje.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Strony usługi Azure AD B2C w elemencie iFrame otworzyć mojej aplikacji?
Nie, ze względów bezpieczeństwa strony usługi Azure AD B2C nie można otworzyć w elemencie iFrame.  Nasza usługa komunikuje się za pośrednictwem przeglądarki, aby uniemożliwić ramek IFRAME.  Zaleca się korzystania ramek iframe dla środowiska tożsamości ze względu na ryzyko miejsca kliknij branży zabezpieczeń w ogóle i specyfikację OAUTH2.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Usługa Azure AD B2C działa z systemów CRM, takich jak Microsoft Dynamics?
Integracja z portalu usługi Microsoft Dynamics 365 jest dostępna.  Zobacz [Konfigurowanie portalu Dynamics 365, aby używać usługi Azure AD B2C do uwierzytelniania](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Usługa Azure AD B2C jest działają z lokalnego programu SharePoint 2016 lub starszym?
Usługa Azure AD B2C nie jest przeznaczona dla programu SharePoint zewnętrzne udostępnianie partnera scenariusza; zobacz [usługi Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) zamiast tego.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Zarządzanie tożsamościami zewnętrznego należy użyć usługi Azure AD B2C lub B2B?
Przeczytaj ten artykuł [tożsamości zewnętrznych](../active-directory/active-directory-b2b-compare-external-identities.md) Aby dowiedzieć się więcej na temat stosowania funkcji odpowiednich do scenariuszy z tożsamości zewnętrznej.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jakie raportowania i inspekcji funkcji usługi Azure AD B2C zapewnia? Są one takie same jak w usłudze Azure AD w wersji Premium?
Nie, usługa Azure AD B2C nie obsługuje ten sam zestaw raportów co program Azure AD Premium. Istnieją jednak wiele commonalities:

* **Raportów logowania** udostępniają zapis każdym logowaniu się ze szczegółami mniejsze.
* **Raporty inspekcji** zawierają zarówno działania administratora, jak i działanie aplikacji. 
* **Raporty użycia** obejmują liczbę użytkowników, liczba logowań i ilości usługi MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Czy można zlokalizować interfejsu użytkownika strony obsługiwanych przez usługę Azure AD B2C? Jakie języki są obsługiwane?
Tak!  Przeczytaj o [Dostosowywanie języka](active-directory-b2c-reference-language-customization.md), która znajduje się w publicznej wersji zapoznawczej.  Firma Microsoft zapewnia tłumaczeń w językach 36, a może zastąpić dowolny ciąg, w zależności od potrzeb.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Na stronach rejestracji i logowania, które są obsługiwane przez usługę Azure AD B2C można używać własnego adresy URL? Na przykład można zmienić adres URL z: login.microsoftonline.com na domeny login.contoso.com?
Obecnie nie. Ta funkcja jest w naszym harmonogramie działania. Weryfikowanie Twojej domeny w **domen** kartę w witrynie Azure portal nie realizację tego celu.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Jak usunąć moją dzierżawę usługi Azure AD B2C?
Wykonaj następujące kroki, aby usunąć dzierżawę usługi Azure AD B2C:

1. Usuń wszystkie przepływy użytkownika (zasady) w dzierżawie usługi Azure AD B2C.
1. Usuń wszystkie aplikacje, zarejestrowanych w dzierżawie usługi Azure AD B2C.
1. Teraz Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Administrator subskrypcji. (Użyj tej samej pracy lub konta służbowego lub tego samego konta Microsoft, którego użyto podczas tworzenia konta na platformie Azure).
1. Przełącz do dzierżawy usługi Azure AD B2C chcesz usunąć.
2. Przejdź do menu po lewej stronie usługi Active Directory.
3. Wybierz pozycję **Użytkownicy i grupy**.
4. Zaznacz każdy użytkownik z kolei (Wyklucz użytkownika administratora subskrypcji, który jest aktualnie zalogowany jako). Kliknij przycisk **Usuń** w dolnej części strony i kliknij przycisk **tak** po wyświetleniu monitu.
5. Kliknij przycisk **rejestracje aplikacji**.
6. Wybierz aplikację o nazwie **b2c-extensions-app**. Kliknij przycisk **Usuń** i kliknij przycisk **tak** po wyświetleniu monitu.
7. Wybierz pozycję **Przegląd**.
8. Kliknij przycisk **usunąć katalog**. Aby ukończyć proces, postępuj zgodnie z instrukcjami na ekranie.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Czy można uzyskać usługi Azure AD B2C, jako część pakietu Enterprise Mobility Suite?
Nie, usługa Azure AD B2C jest rozliczana według bieżącego użycia usług platformy Azure, nie jest częścią pakietu Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Jak zgłosić problemy z usługą Azure AD B2C?
Zobacz [pliku żądania pomocy technicznej dla usługi Azure Active Directory B2C](active-directory-b2c-support.md).
