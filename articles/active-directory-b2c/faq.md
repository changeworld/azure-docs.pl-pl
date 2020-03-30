---
title: Często zadawane pytania (CZĘSTO ZADAWANE PYTANIA) dotyczące usługi Azure Active Directory B2C
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264403"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Usługa Azure AD B2C: często zadawane pytania (często zadawane pytania)

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory B2C (Azure AD B2C). Sprawdzaj aktualizacje.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Dlaczego nie mogę uzyskać dostępu do rozszerzenia usługi Azure AD B2C w witrynie Azure portal?

Istnieją dwa typowe powody, dla których rozszerzenie usługi Azure AD nie działa dla Ciebie. Usługa Azure AD B2C wymaga, aby rola użytkownika w katalogu była administratorem globalnym. Jeśli uważasz, że powinieneś mieć do niego dostęp, skontaktuj się z administratorem. Jeśli masz uprawnienia administratora globalnego, upewnij się, że jesteś w katalogu usługi Azure AD B2C, a nie w katalogu usługi Azure Active Directory. Można zobaczyć instrukcje dotyczące [tworzenia dzierżawy usługi Azure AD B2C.](tutorial-create-tenant.md)

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Czy mogę używać funkcji usługi Azure AD B2C w mojej istniejącej dzierżawie usługi Azure AD opartej na pracownikach?

Usługi Azure AD i Azure AD B2C są oddzielne oferty produktów i nie mogą współistnieć w tej samej dzierżawy. Dzierżawa usługi Azure AD reprezentuje organizację. Dzierżawy usługi Azure AD B2C reprezentuje kolekcję tożsamości, które mają być używane z aplikacji jednostki uzależniającej. Dodając **nowego dostawcę OpenID Connect** w obszarze **dostawców tożsamości usługi Azure AD B2C >** lub z zasadami niestandardowymi, usługa Azure AD B2C może łączyć się z usługą Azure AD umożliwiając uwierzytelnianie pracowników w organizacji.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Czy mogę używać usługi Azure AD B2C do logowania społecznościowego (Facebook i Google+) do usługi Office 365?

Usługi Azure AD B2C nie można używać do uwierzytelniania użytkowników w usłudze Microsoft Office 365. Usługa Azure AD to rozwiązanie firmy Microsoft do zarządzania dostępem pracowników do aplikacji SaaS i posiada funkcje zaprojektowane do tego celu, takie jak licencjonowanie i dostęp warunkowy. Usługa Azure AD B2C zapewnia platformę zarządzania tożsamościami i dostępem do tworzenia aplikacji sieci web i aplikacji mobilnych. Gdy usługa Azure AD B2C jest skonfigurowana do łączeniu się z dzierżawą usługi Azure AD, dzierżawca usługi Azure AD zarządza dostępem pracowników do aplikacji opartych na usłudze Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Co to są konta lokalne w usłudze Azure AD B2C? Czym różnią się od kont służbowych w usłudze Azure AD?

W dzierżawie usługi Azure AD użytkownicy należący do dzierżawcy logują się przy za pomocą adresu e-mail formularza. `<xyz>@<tenant domain>` Jest `<tenant domain>` to jedna ze zweryfikowanych domen w `<...>.onmicrosoft.com` dzierżawie lub domenie początkowej. Ten typ konta jest kontem służbowym.

W dzierżawie usługi Azure AD B2C większość aplikacji chce, aby użytkownik logować joe@comcast.net bob@gmail.comsię sarah@contoso.comprzy jim@live.comkażdym dowolnym adresie e-mail (na przykład , , , lub ). Ten typ konta jest kontem lokalnym. Obsługujemy również dowolne nazwy użytkowników jako konta lokalne (na przykład joe, bob, sarah lub jim). Możesz wybrać jeden z tych dwóch typów kont lokalnych podczas konfigurowania dostawców tożsamości dla usługi Azure AD B2C w witrynie Azure portal. W dzierżawie usługi Azure AD B2C wybierz pozycję **Dostawcy tożsamości**, wybierz pozycję **Konto lokalne**, a następnie wybierz pozycję Nazwa **użytkownika**.

Konta użytkowników dla aplikacji można tworzyć za pośrednictwem przepływu użytkownika rejestracji, rejestracji lub logowania przepływu użytkownika, interfejsu API programu Microsoft Graph lub w witrynie Azure portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Jakich dostawców tożsamości społecznych wspierasz teraz? Które z nich planujesz wspierać w przyszłości?

Obecnie obsługujemy kilku dostawców tożsamości społecznościowych, w tym Amazon, Facebook, GitHub (wersja zapoznawcza), Google, LinkedIn, Konto Microsoft (MSA), QQ (wersja zapoznawcza), Twitter, WeChat (wersja zapoznawcza) i Weibo (wersja zapoznawcza). Oceniamy dodawanie wsparcia dla innych popularnych dostawców tożsamości społecznościowych na podstawie zapotrzebowania klientów.

Usługa Azure AD B2C obsługuje również [zasady niestandardowe.](custom-policy-overview.md) Zasady niestandardowe umożliwiają tworzenie własnych zasad dla dowolnego dostawcy tożsamości, który obsługuje [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) lub SAML. Zacznij ować z niestandardowymi zasadami, sprawdzając nasz [pakiet startowy zasad niestandardowych.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Czy mogę skonfigurować zakresy, aby zebrać więcej informacji o konsumentach od różnych dostawców tożsamości społecznościowych?

Nie. Domyślne zakresy używane dla naszego obsługiwanego zestawu dostawców tożsamości społecznościowych to:

* Facebook: e-mail
* Google+: poczta e-mail
* Konto Microsoft: otwarty profil poczty e-mail
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Czy moja aplikacja musi być uruchamiana na platformie Azure, aby działała z usługą Azure AD B2C?

Nie, można hostować aplikację w dowolnym miejscu (w chmurze lub lokalnie). Wszystko, czego potrzebuje do interakcji z usługą Azure AD B2C, to możliwość wysyłania i odbierania żądań HTTP w publicznie dostępnych punktach końcowych.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Mam wielu dzierżaw usługi Azure AD B2C. Jak zarządzać nimi w witrynie Azure portal?

Przed otwarciem "Azure AD B2C" w menu po lewej stronie witryny Azure portal, należy przełączyć się do katalogu, który chcesz zarządzać. Przełącz katalogi, klikając swoją tożsamość w prawym górnym rogu witryny Azure portal, a następnie wybierz katalog w wyświetlona stronie rozwijanej.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Jak dostosować wiadomości e-mail weryfikacyjne (zawartość i pole "Od:") wysyłane przez usługę Azure AD B2C?

Możesz użyć [funkcji znakowania firmy,](../active-directory/fundamentals/customize-branding.md) aby dostosować zawartość e-maili weryfikacyjnych. W szczególności te dwa elementy wiadomości e-mail można dostosować:

* **Logo baneru**: wyświetlane w prawym dolnym rogu.
* **Kolor tła**: pokazany u góry.

    ![Zrzut ekranu przedstawiający niestandardową wiadomość e-mail weryfikacyjną](./media/faq/company-branded-verification-email.png)

Podpis e-mail zawiera nazwę dzierżawy usługi Azure AD B2C, która została podana podczas pierwszego utworzenia dzierżawy usługi Azure AD B2C. Nazwę można zmienić, korzystając z następujących instrukcji:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny.
1. Otwórz blok **Azure Active Directory**.
1. Kliknij kartę **Właściwości.**
1. Zmień pole **Nazwa.**
1. Kliknij przycisk **Zapisz** w górnej części strony.

Obecnie nie ma możliwości zmiany pola "Od:" w wiadomości e-mail.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Jak przeprowadzić migrację istniejących nazw użytkowników, haseł i profilów z bazy danych do usługi Azure AD B2C?

Za pomocą interfejsu API programu Microsoft Graph można napisać narzędzie do migracji. Szczegółowe informacje można znaleźć w [podręczniku migracji użytkowników.](user-migration.md)

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Jaki przepływ użytkownika hasła jest używany dla kont lokalnych w usłudze Azure AD B2C?

Przepływ hasła usługi Azure AD B2C dla kont lokalnych jest oparty na zasadach dotyczących usługi Azure AD. Przepływy użytkowników korzystających z rejestracji, rejestracji lub logowania i resetowania hasła usługi Azure AD B2C używają "silnej" siły hasła i nie wygasają żadne hasła. Aby uzyskać więcej informacji, zobacz [Zasady haseł i ograniczenia w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

Aby uzyskać informacje o blokadach kont i hasłach, zobacz [Zarządzanie zagrożeniami dla zasobów i danych w usłudze Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Czy mogę używać usługi Azure AD Connect do migracji tożsamości konsumentów przechowywanych w lokalnej usłudze Active Directory do usługi Azure AD B2C?

Nie, usługa Azure AD Connect nie jest przeznaczona do pracy z usługą Azure AD B2C. Należy rozważyć użycie [interfejsu API programu Microsoft Graph](manage-user-accounts-graph-api.md) do migracji użytkowników. Szczegółowe informacje można znaleźć w [podręczniku migracji użytkowników.](user-migration.md)

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Czy moja aplikacja może otwierać strony usługi Azure AD B2C w ramce iFrame?

Nie, ze względów bezpieczeństwa nie można otworzyć stron usługi Azure AD B2C w ramce iFrame. Nasza usługa komunikuje się z przeglądarką, aby zakazać iFrame. Społeczność zabezpieczeń w ogóle i specyfikacja OAUTH2, zalecają, aby nie używać ramek iFrame dla środowisk tożsamości ze względu na ryzyko kliknięcia.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Czy usługa Azure AD B2C współpracuje z systemami CRM, takimi jak Microsoft Dynamics?

Integracja z portalem Microsoft Dynamics 365 jest dostępna. Zobacz [Konfigurowanie portalu Dynamics 365, aby używać usługi Azure AD B2C do uwierzytelniania.](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Czy usługa Azure AD B2C współpracuje z programem SharePoint lokalnie 2016 lub starszym?

Usługa Azure AD B2C nie jest przeznaczona dla scenariusza udostępniania zewnętrznego partnera programu SharePoint; zobacz [azure ad B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) zamiast.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Czy do zarządzania tożsamościami zewnętrznymi należy używać usługi Azure AD B2C lub B2B?

Przeczytaj [porównanie współpracy B2B i B2C w usłudze Azure AD,](../active-directory/b2b/compare-with-b2c.md) aby dowiedzieć się więcej o stosowaniu odpowiednich funkcji do scenariuszy tożsamości zewnętrznej.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Jakie funkcje raportowania i inspekcji zapewnia usługa Azure AD B2C? Czy są takie same jak w usłudze Azure AD Premium?

Nie, usługa Azure AD B2C nie obsługuje tego samego zestawu raportów co usługa Azure AD Premium. Istnieje jednak wiele podobieństw:

* **Raporty logowania** zawierają rekord każdego logowania z obniżonymi szczegółami.
* **Raporty inspekcji** obejmują zarówno działania administratora, jak i działania aplikacji.
* **Raporty użycia** obejmują liczbę użytkowników, liczbę logowań i liczbę uwierzytelniania Wieloskładnikowego.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Czy mogę zlokalizować interfejs użytkownika stron obsługiwanych przez usługę Azure AD B2C? Jakie języki są obsługiwane?

Tak, zobacz [dostosowywanie języka](user-flow-language-customization.md). Zapewniamy tłumaczenia dla 36 języków i możesz zastąpić dowolny ciąg do własnych potrzeb.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Czy mogę używać własnych adresów URL na stronach rejestracji i logowania obsługiwanych przez usługę Azure AD B2C? Na przykład, czy mogę zmienić adres URL z contoso.b2clogin.com na login.contoso.com?

Obecnie nie. Ta funkcja znajduje się na naszej mapie drogowej. Weryfikowanie domeny na karcie **Domeny** w witrynie Azure portal nie pozwala osiągnąć tego celu. Jednak z b2clogin.com oferujemy [neutralną domenę najwyższego poziomu,](b2clogin.md)a tym samym wygląd zewnętrzny może być zaimplementowany bez wzmianki o Microsoftie.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Jak usunąć dzierżawę usługi Azure AD B2C?

Wykonaj następujące kroki, aby usunąć dzierżawę usługi Azure AD B2C.

Możesz użyć bieżącego **środowiska aplikacji** lub naszego nowego środowiska **rejestracji aplikacji (wersja zapoznawcza).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako *administrator subskrypcji*. Użyj tego samego konta służbowego lub tego samego konta Microsoft, którego użyto do zarejestrowania się na platformie Azure.
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Usuń wszystkie **przepływy użytkownika (zasady)** w dzierżawie usługi Azure AD B2C.
1. Usuń wszystkie **aplikacje zarejestrowane** w dzierżawie usługi Azure AD B2C.
1. Wybierz **pozycję Azure Active Directory** w menu po lewej stronie.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. Wybierz z kolei każdego użytkownika (wyklucz użytkownika *Administratora subskrypcji,* który jest aktualnie zalogowany). Wybierz **pozycję Usuń** u dołu strony i po wyświetleniu monitu wybierz pozycję **TAK.**
1. W obszarze **Zarządzanie**wybierz **pozycję Rejestracje aplikacji** (lub **Rejestracje aplikacji (Starsze wersje)**(
1. Wybierz **pozycję Wyświetl wszystkie aplikacje**
1. Wybierz aplikację o nazwie **b2c-extensions-app**, wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak** po wyświetleniu monitu.
1. W obszarze **Zarządzanie**wybierz **pozycję Ustawienia użytkownika**.
1. Jeśli występuje, w obszarze **Połączenia kont LinkedIn**wybierz pozycję **Nie**, a następnie wybierz pozycję **Zapisz**.
1. W obszarze **Zarządzanie**wybierz pozycję **Właściwości**
1. W obszarze **Zarządzanie dostępem do zasobów platformy Azure** wybierz pozycję **Tak**, a następnie wybierz przycisk **Zapisz**.
1. Wyloguj się z witryny Azure portal, a następnie zaloguj się z powrotem, aby odświeżyć dostęp.
1. Wybierz **pozycję Azure Active Directory** w menu po lewej stronie.
1. Na stronie **Przegląd** wybierz pozycję **Usuń katalog**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zakończyć proces.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako *administrator subskrypcji*. Użyj tego samego konta służbowego lub tego samego konta Microsoft, którego użyto do zarejestrowania się na platformie Azure.
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Usuń wszystkie **przepływy użytkownika (zasady)** w dzierżawie usługi Azure AD B2C.
1. Wybierz **pozycję Rejestracje aplikacji (Podgląd),** a następnie wybierz kartę **Wszystkie aplikacje.**
1. Usuń wszystkie zarejestrowane aplikacje.
1. Usuń **aplikację b2c-extensions .**
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. Wybierz z kolei każdego użytkownika (wyklucz użytkownika *Administratora subskrypcji,* który jest aktualnie zalogowany). Wybierz **pozycję Usuń** u dołu strony i po wyświetleniu monitu wybierz pozycję **Tak.**
1. Wybierz **pozycję Azure Active Directory** w menu po lewej stronie.
1. W obszarze **Zarządzanie**wybierz **pozycję Ustawienia użytkownika**.
1. Jeśli występuje, w obszarze **Połączenia kont LinkedIn**wybierz pozycję **Nie**, a następnie wybierz pozycję **Zapisz**.
1. W obszarze **Zarządzanie**wybierz pozycję **Właściwości**
1. W obszarze **Zarządzanie dostępem do zasobów platformy Azure** wybierz pozycję **Tak**, a następnie wybierz przycisk **Zapisz**.
1. Wyloguj się z witryny Azure portal, a następnie zaloguj się z powrotem, aby odświeżyć dostęp.
1. Wybierz **pozycję Azure Active Directory** w menu po lewej stronie.
1. Na stronie **Przegląd** wybierz pozycję **Usuń katalog**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zakończyć proces.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Czy mogę uzyskać usługę Azure AD B2C w ramach pakietu Enterprise Mobility Suite?

Nie, usługa Azure AD B2C jest usługą pay-as-you-go azure i nie jest częścią pakietu Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Jak zgłosić problemy z usługą Azure AD B2C?

Zobacz [Żądania obsługi plików dla usługi Azure Active Directory B2C](support-options.md).
