---
title: Często zadawane pytania dotyczące usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1b56cdd119bac28eec819dbf38afcd2be5aaf907
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710736"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Usługa Azure AD B2C: Często zadawane pytania (FAQ) 
Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory (Azure AD) B2C. Sprawdzanie wstecz do aktualizacji.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Funkcji usługi Azure AD B2C można korzystać w mojej istniejącej, na podstawie pracownika dzierżawy usługi Azure AD?
Usługi Azure AD i Azure AD B2C ofert osobnego produktu i nie mogą współistnieć w tej samej dzierżawy.  Dzierżawa usługi Azure AD reprezentuje organizacji.  Dzierżawy usługi Azure AD B2C reprezentuje kolekcję tożsamości do użycia z aplikacjami danej firmy.  Przy użyciu zasad niestandardowych (w publicznej wersji zapoznawczej) usługi Azure AD B2C można było wykonać Federację do usługi Azure AD stosowanie uwierzytelniania pracowników w organizacji.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Czy za pomocą usługi Azure AD B2C można podać logowania społecznościowych (Facebook i Google +) do usługi Office 365
Azure AD B2C nie może być używany do uwierzytelniania użytkowników dla usługi Microsoft Office 365.  Usługi Azure AD jest rozwiązanie firmy Microsoft do zarządzania dostępu pracowników do aplikacji SaaS i zawiera funkcji służących do tego celu, takich jak licencjonowania i warunkowego dostępu.  Usługa Azure AD B2C zapewnia platformy do zarządzania tożsamościami i dostępem do tworzenia sieci web i aplikacji dla urządzeń przenośnych.  Po skonfigurowaniu usługi Azure AD B2C możliwości utworzenia Federacji do dzierżawy usługi Azure AD dzierżawy usługi Azure AD zarządza dostępu pracowników do aplikacji, które zależą od usługi Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Co to są lokalne konta w usłudze Azure AD B2C? Jak różnią się one od kont służbowych w usłudze Azure AD?
W dzierżawie usługi Azure AD, użytkownicy, którzy należą do dzierżawy logowania przy użyciu adresu e-mail w postaci `<xyz>@<tenant domain>`.  `<tenant domain>` Jest jednym z zweryfikowanych domen w dzierżawie lub początkowej `<...>.onmicrosoft.com` domeny. Ten typ konta jest konta firmowego lub szkolnego.

W dzierżawie usługi Azure AD B2C większości aplikacji ma użytkownikowi na logowanie z dowolnego adresu e-mail dowolnego (na przykład joe@comcast.net, bob@gmail.com, sarah@contoso.com, lub jim@live.com). Ten typ konta jest kontem lokalnym.  Obsługujemy również nazwy dowolnego użytkownika jako kont lokalnych (na przykład Jan, Roberta, Anetą lub jim). Podczas konfigurowania dostawców tożsamości dla usługi Azure AD B2C w portalu Azure można wybrać jedną z tych dwóch typów kont lokalnych. W dzierżawie usługi Azure AD B2C, kliknij przycisk **dostawców tożsamości** , a następnie wybierz **Username** w obszarze kont lokalnych. 

Zawsze należy utworzyć konta użytkowników dla aplikacji za pośrednictwem zasad rejestracji rejestracji lub znak — w zasadach lub przy użyciu interfejsu API Azure AD Graph. Konta użytkowników utworzone w portalu Azure są używane tylko do administrowania dzierżawcy.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Które dostawców tożsamości społecznościowych możesz obsługują teraz? Te, które zamierzasz obsługiwać w przyszłości?
Firma Microsoft obsługuje obecnie Facebook, Google + LinkedIn, Amazon, usługi Twitter (wersja zapoznawcza), WeChat (wersja zapoznawcza), Weibo (wersja zapoznawcza) i q (wersja zapoznawcza). Dodamy obsługę innych dostawców tożsamości społecznościowych popularnych na życzenie klientów.

Usługa Azure AD B2C dodano również obsługę [niestandardowych zasad](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Te [zasady niestandardowe](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom) umożliwia deweloperom tworzenie własnych zasad, który z dowolnego dostawcy tożsamości, która obsługuje [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) lub SAML. 

Wprowadzenie do zasad niestandardowych przez wyewidencjonowanie naszych [pakiet początkowy zasady niestandardowe](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Można skonfigurować zakresy, aby zebrać więcej informacji na temat użytkowników z różnych dostawców tożsamości społecznościowych?
Nie, ale ta funkcja jest naszego planu. Zakresy domyślna używana dla naszego zestawu obsługiwanych dostawców tożsamości społecznościowych są:

* Facebook: wiadomości e-mail
* Google +: wiadomości e-mail
* Konto Microsoft: profil poczty e-mail protokołu openid
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Czy Moja aplikacja musi działać na platformie Azure, do jego działania w usłudze Azure AD B2C?
Nie można obsługiwać aplikacji z dowolnego miejsca (w chmurze lub lokalnie). Wszystkie niezbędne do interakcji z usługą Azure AD B2C jest możliwość wysyłania i odbierania żądań HTTP na publicznie punktów końcowych.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mam wiele dzierżaw usługi Azure AD B2C. Jak zarządzać nimi w portalu Azure?
Przed otwarciem usługi Azure AD B2C w menu po lewej stronie portalu Azure, musisz przełączyć się do katalogu, którą chcesz zarządzać.  Przełącz katalogi, klikając swoją tożsamość w prawym górnym rogu portalu Azure, a następnie wybierz katalog na liście rozwijanej, która pojawia się.  Aby uzyskać szczegółowe instrukcje wraz z obrazów, zobacz [przejdź do ustawień usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Jak dostosować weryfikacyjnych wiadomości e-mail (zawartość i "od:" pole) wysyłane przez usługę Azure AD B2C?
Można użyć [firmowe funkcji](../active-directory/customize-branding.md) Aby dostosować zawartość weryfikacyjnych wiadomości e-mail. W szczególności można dostosować te dwa elementy wiadomości e-mail:

* **Banner Logo**: wyświetlany w prawym dolnym rogu.
* **Kolor tła**: wyświetlaną u góry.

    ![Zrzut ekranu przedstawiający wiadomość e-mail z dostosowanych weryfikacji](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Podpis e-mail zawiera nazwę dzierżawy usługi Azure AD B2C, podane podczas tworzenia dzierżawy usługi Azure AD B2C. Można zmienić nazwę, korzystając z tych instrukcji:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) jako Administrator globalny.
1. Otwórz **usługi Azure Active Directory** bloku.
1. Kliknij przycisk **właściwości** kartę.
1. Zmień **nazwa** pola.
1. Kliknij przycisk **zapisać** w górnej części strony.

Obecnie nie istnieje sposób zmiany "od:" w wiadomości e-mail. Głosowania [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) planuje się Dostosowywanie treść wiadomości e-mail weryfikacji.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak można przeprowadzić migrację mojej istniejącej nazwy użytkownika, hasła i profile z bazy danych usługi Azure AD B2C?
Można użyć interfejsu API programu Azure AD Graph do zapisania narzędzia do migracji. Zobacz [Przewodnik po migracji użytkownika](active-directory-b2c-user-migration.md) szczegółowe informacje.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jakie zasady haseł jest używana dla kont lokalnych w usłudze Azure AD B2C?
Zasady usługi Azure AD B2C haseł dla kont lokalnych jest na podstawie zasad dla usługi Azure AD. Usługi Azure AD B2C do tworzenia konta, zapisywania lub logowania i hasło zresetować używa zasad siły "silnego" hasła i nie wygasa hasła. Odczyt [zasady haseł usługi Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) więcej szczegółów.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Aby przeprowadzić migrację tożsamości użytkownika, które są przechowywane na moje lokalnej usługi Active Directory do usługi Azure AD B2C można użyć Azure AD Connect?
Nie, usługi Azure AD Connect nie jest przeznaczona do pracy z usługą Azure AD B2C. Należy rozważyć użycie [interfejsu API programu Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) przypadku migracji użytkownika.  Zobacz [Przewodnik po migracji użytkownika](active-directory-b2c-user-migration.md) szczegółowe informacje.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Moja aplikacja może otwarcie strony usługi Azure AD B2C w obrębie elementu iFrame?
Nie, ze względów bezpieczeństwa nie można otworzyć usługi Azure AD B2C stron w ramce.  Nasza usługa komunikuje się za pośrednictwem przeglądarki, aby zabronić ramek IFRAME.  Zaleca się przy użyciu ramek iframe dla środowiska tożsamości z powodu ryzyka miejsca kliknij społeczności zabezpieczeń w ogólne i specyfikację OAUTH2.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Usługi Azure AD B2C działa z systemów CRM, takich jak Microsoft Dynamics?
Integracja z usługą Microsoft Dynamics 365 portalu jest dostępny.  Zobacz [Konfigurowanie Dynamics 365 portalu usługi Azure AD B2C do uwierzytelniania](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Usługi Azure AD B2C jest korzystanie z programu SharePoint 2016 lokalnymi lub starszym?
Usługa Azure AD B2C nie jest przeznaczona dla programu SharePoint zewnętrznych udostępnianie partnera scenariusza; zobacz [B2B usługi Azure AD](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) zamiast tego.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Do zarządzania tożsamościami zewnętrznego należy używać usługi Azure AD B2C lub B2B?
Przeczytaj ten artykuł o [tożsamości zewnętrznych](../active-directory/active-directory-b2b-compare-external-identities.md) Aby dowiedzieć się więcej na temat stosowania odpowiednie funkcje do scenariuszy tożsamości zewnętrznych.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jakie raportowania i inspekcji funkcje usługi Azure AD B2C oferuje? Są one takie same jak Azure AD Premium?
Nie, usługi Azure AD B2C obsługuje ten sam zestaw raportów, jako Azure AD Premium. Istnieje jednak wiele commonalities:

* **Zaloguj się w raportach** są dostępne tylko w portalu Azure (Azure Active Directory > działania > logowania) i nie są dostępne za pośrednictwem interfejsu API programu Graph. Udostępniają one rejestr każdego logowania z obniżonych szczegółów.
* **Raporty dotyczące inspekcji** są dostępne tylko w portalu Azure (Azure Active Directory > działania > Dzienniki inspekcji) i nie są dostępne za pośrednictwem interfejsu API programu Graph. Obejmują one zarówno aktywność administratora, jak i działanie aplikacji. 
* **Raporty użycia** są dostępne za pośrednictwem tylko [użycia interfejsu API raportowania](active-directory-b2c-reference-usage-reporting-api.md) i nie są dostępne za pośrednictwem portalu Azure. Obejmują one liczbę użytkowników, liczba logowań i wolumin MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Czy można zlokalizować interfejsu użytkownika obsługiwanych przez usługę Azure AD B2C stron? Jakich języków są obsługiwane?
Tak!  Przeczytaj informacje o [dostosowywania języka](active-directory-b2c-reference-language-customization.md), która znajduje się w publicznej wersji zapoznawczej.  Firma Microsoft udostępnia tłumaczenia dla 36 języków, można zmienić dowolny ciąg, w zależności od potrzeb.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Czy można użyć własnych adresów URL na stronach rejestracji i logowania, które są obsługiwane przez usługę Azure AD B2C? Na przykład można zmienić adres URL z login.microsoftonline.com na login.contoso.com?
Nie można obecnie. Ta funkcja jest naszego planu. Weryfikowanie domeny w **domen** kartę w portalu Azure nie realizację tego celu.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Jak usunąć mojej dzierżawy usługi Azure AD B2C?
Wykonaj następujące kroki, aby usunąć dzierżawy usługi Azure AD B2C:

1. Wykonaj następujące kroki, aby [przejdź do ustawień usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
1. Przejdź do **aplikacji**, **dostawców tożsamości**, i **wszystkich zasad** i Usuń wszystkie wpisy w każdej z nich.
1. Zaloguj się teraz do [portalu Azure](https://portal.azure.com/) jako Administrator subskrypcji. (Użyj tej samej pracy lub konta służbowego lub tego samego konta Microsoft, który był używany podczas tworzenia konta platformy Azure).
1. Przełącz do dzierżawy usługi Azure AD B2C chcesz usunąć.
2. Przejdź do menu usługi Active Directory po lewej stronie.
3. Wybierz **użytkowników i grup**.
4. Wybierz każdego użytkownika z kolei (wykluczyć użytkownika administratora subskrypcji, który jest obecnie zalogowany jako). Kliknij przycisk **usunąć** w dolnej części strony i kliknij przycisk **tak** po wyświetleniu monitu.
5. Kliknij przycisk **rejestracji aplikacji**.
6. Wybierz aplikację o nazwie **b2c rozszerzeń aplikacji**. Kliknij przycisk **usunąć** i kliknij przycisk **tak** po wyświetleniu monitu.
7. Wybierz pozycję **Przegląd**.
8. Kliknij przycisk **Usuń katalog**. Aby ukończyć proces, postępuj zgodnie z instrukcjami na ekranie.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Czy można uzyskać usługi Azure AD B2C jako część pakietu Enterprise Mobility Suite?
Nie, usługi Azure AD B2C jest płatność za rzeczywiste użycie usługi Azure, nie jest częścią pakietu Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Jak zgłosić problemy z usługą Azure AD B2C?
Zobacz [pliku żądania pomocy technicznej usługi Azure Active Directory B2C](active-directory-b2c-support.md).
