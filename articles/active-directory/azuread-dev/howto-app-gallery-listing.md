---
title: Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyświetlić listę aplikacji obsługujących logowanie jednokrotne w galerii aplikacji usługi Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: fbdae8b8506731f13ff08e877ea8eedb6d57fa78
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666941"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

W tym artykule pokazano, jak wyświetlić listę aplikacji w galerii aplikacji usługi Azure Active Directory (Azure AD), zaimplementować logowanie jednokrotne (SSO) i zarządzać listą.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest galeria aplikacji usługi Azure AD?

- Klienci znajdują najlepsze możliwe środowisko logowania jednokrotnego.
- Konfiguracja aplikacji jest prosta i minimalna.
- Szybkie wyszukiwanie znajduje aplikację w galerii.
- Klienci bezpłatnej, podstawowej i premium usługi Azure AD mogą korzystać z tej integracji.
- Klienci wzajemni otrzymują samouczek konfiguracji krok po kroku.
- Klienci korzystający z systemu zarządzania tożsamościami między domenami[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)mogą używać inicjowania obsługi administracyjnej dla tej samej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- W przypadku aplikacji federacyjnych (Open ID i SAML/WS-Fed) aplikacja musi obsługiwać model oprogramowania jako usługi (SaaS) do uzyskania listy w galerii aplikacji usługi Azure AD. Aplikacje galerii przedsiębiorstwa muszą obsługiwać wiele konfiguracji klientów, a nie żadnych konkretnych klientów.
- W przypadku open id connect aplikacja musi być wielodostępna, a [struktura zgody usługi Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) musi być poprawnie zaimplementowana dla aplikacji. Użytkownik może wysłać żądanie logowania do wspólnego punktu końcowego, dzięki czemu każdy klient może udzielić zgody na aplikację. Można kontrolować dostęp użytkownika na podstawie identyfikatora dzierżawy i nazwy UPN użytkownika odebrane w tokenie.
- W przypadku SAML 2.0/WS-Fed aplikacja musi mieć możliwość integracji SSO SAML/WS-Fed w trybie SP lub IDP. Upewnij się, że ta funkcja działa poprawnie przed przesłaniem żądania.
- W przypadku logowania jednokrotnego hasła upewnij się, że aplikacja obsługuje uwierzytelnianie formularzy, dzięki czemu można wykonać przechowanie haseł, aby uzyskać logowanie jednokrotne do pracy zgodnie z oczekiwaniami.
- Potrzebujesz stałego konta do testowania z co najmniej dwoma zarejestrowanymi użytkownikami.

**Jak uzyskać usługę Azure AD dla deweloperów?**

Możesz uzyskać bezpłatne konto testowe ze wszystkimi funkcjami usługi Azure AD premium — 90 dni za darmo i możesz zostać przedłużony, o ile pracujesz z deweloperami:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Prześlij żądanie w portalu

Po przetestowaniu, czy integracja aplikacji współpracuje z usługą Azure AD, prześlij żądanie aplikacji w [portalu sieci Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Jeśli po zalogowaniu się pojawi się następująca strona, skontaktuj się z [zespołem integracji logowania sytuane usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Podaj konto e-mail, którego chcesz użyć do przesłania żądania. Preferowany [name@yourbusiness.com](mailto:name@yourbusiness.com) jest biznesowy adres e-mail. Zespół usługi Azure AD doda konto w portalu sieci Microsoft Application Network.

![Komunikat żądania dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Po dodaniu konta można zalogować się do portalu sieci Microsoft Application Network.

Jeśli po zalogowaniu się pojawi się następująca strona, podaj uzasadnienie biznesowe potrzeby dostępu w polu tekstowym. Następnie wybierz pozycję **Poproś o dostęp**.

  ![Pole Uzasadnienie biznesowe w portalu programu SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nasz zespół sprawdza szczegóły i daje ci dostęp. Po zatwierdzeniu żądania można zalogować się do portalu i przesłać żądanie, wybierając kafelek **Wyślij żądanie (ISV)** na stronie głównej.

![Kafelek Wyślij żądanie (ISV) na stronie głównej](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemy z logowaniem do portalu

Jeśli widzisz ten błąd podczas logowania, oto szczegóły dotyczące problemu i w ten sposób możesz go naprawić.

* Jeśli logowanie zostało zablokowane, jak pokazano poniżej:

  ![rozwiązywanie problemów z aplikacją w galerii](./media/howto-app-gallery-listing/blocked.png)

**Co się dzieje:**

Użytkownik-gość jest sfederowany do dzierżawy domowej, która jest również usługą Azure AD. Użytkownik-gość jest na wysokie ryzyko. Firma Microsoft nie zezwala użytkownikom wysokiego ryzyka na dostęp do swoich zasobów. Wszyscy użytkownicy wysokiego ryzyka (pracownicy lub goście / dostawcy) muszą skorygować / zamknąć swoje ryzyko dostępu do zasobów firmy Microsoft. Dla użytkowników-gościa to ryzyko użytkownika pochodzi z dzierżawy domowej i zasady pochodzi z dzierżawy zasobów (Microsoft w tym przypadku).
 
**Bezpieczne rozwiązania:**

* Zarejestrowani użytkownicy-goście zarejestrowanych usługi MFA korygują własne ryzyko użytkownika. Może to zrobić użytkownik-gość wykonujący zabezpieczoną zmianę hasła lub resethttps://aka.ms/sspr) (w ich dzierżawie domowej (wymaga to uwierzytelniania WIELOWŁADNEGO i spr w dzierżawie domowej). Zabezpieczona zmiana hasła lub resetowanie musi być zainicjowana w usłudze Azure AD, a nie w przedpremie.

* Użytkownicy-goście mają swoich administratorów skorygować swoje ryzyko. W takim przypadku administrator wykona resetowanie hasła (tymczasowe generowanie hasła). Nie wymaga to ochrony tożsamości. Administrator użytkownika gościa może https://aka.ms/RiskyUsers przejść do i kliknąć na "Reset hasło".

* Użytkownicy-goście mają swoich administratorów blisko / odrzucić swoje ryzyko. Ponownie, to nie wymaga ochrony tożsamości. Administrator może przejść https://aka.ms/RiskyUsers do i kliknąć na "Odrzuć ryzyko użytkownika". Jednak administrator musi dołożyć należytej staranności, aby upewnić się, że była to fałszywie dodatnia ocena ryzyka przed zamknięciem ryzyka użytkownika. W przeciwnym razie narażają swoje zasoby i microsoftu na ryzyko, tłumiąc ocenę ryzyka bez badania.

> [!NOTE]
> Jeśli masz jakiekolwiek problemy z dostępem, skontaktuj się z [zespołem integracji sytuaj usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementowanie sytuowania przy użyciu protokołu federacyjnego

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, należy najpierw zaimplementować jeden z następujących protokołów federacyjnej obsługiwanych przez usługę Azure AD. Należy również wyrazić zgodę na warunki i warunki galerii aplikacji usługi Azure AD. Przeczytaj warunki galerii aplikacji usługi Azure AD w [tej witrynie sieci Web.](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)

- **OpenID Connect:** Aby zintegrować aplikację z usługą Azure AD przy użyciu protokołu Open ID Connect, postępuj zgodnie z [instrukcjami deweloperów.](v1-authentication-scenarios.md)

    ![Wyświetlanie w galerii aplikacji OpenID Connect](./media/howto-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać aplikację do listy w galerii za pomocą OpenID Connect, wybierz **OpenID Connect & OAuth 2.0,** jak pokazano na rysunku.
    * Jeśli masz jakiekolwiek problemy z dostępem, skontaktuj się z [zespołem integracji sytuaj usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** lub **WS-Fed:** Jeśli aplikacja obsługuje SAML 2.0, można zintegrować ją bezpośrednio z dzierżawą usługi Azure AD, postępując zgodnie z [instrukcjami, aby dodać aplikację niestandardową](../active-directory-saas-custom-apps.md).

  ![Wyświetlanie w galerii aplikacji SAML 2.0 lub WS-Fed](./media/howto-app-gallery-listing/saml.png)

  * Jeśli chcesz dodać aplikację do listy w galerii za pomocą **SAML 2.0** lub **WS-Fed**, wybierz **SAML 2.0/WS-Fed,** jak pokazano.

  * Jeśli masz jakiekolwiek problemy z dostępem, skontaktuj się z [zespołem integracji sytuaj usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Zaimplementuj logowanie sytuane przy użyciu hasła logowania przy logowania przy logowania przed logiem

Utwórz aplikację sieci Web, która ma stronę logowania HTML, aby skonfigurować [logowanie jednookie](../manage-apps/what-is-single-sign-on.md). Logowanie logowania opartego na hasłach, nazywane również magazynowanie haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać jedno konto, na przykład na kontach aplikacji mediów społecznościowych w organizacji.

![Wyświetlanie hasła aplikacji SSO w galerii](./media/howto-app-gallery-listing/passwordsso.png)

* Jeśli chcesz dodać aplikację do listy w galerii przy użyciu hasła logować doonich, wybierz **opcję Loguchuj hasło,** jak pokazano.
* Jeśli masz jakiekolwiek problemy z dostępem, skontaktuj się z [zespołem integracji sytuaj usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Żądanie inicjowania obsługi administracyjnej użytkowników

Postępuj zgodnie z procesem pokazanym na poniższej ilustracji, aby zażądać inicjowania obsługi administracyjnej przez użytkowników.

   ![Żądanie inicjowania obsługi administracyjnej użytkowników](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Aktualizowanie lub usuwanie istniejącej listy

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji usługi Azure AD, należy najpierw przesłać żądanie w [portalu sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto usługi Office 365, użyj go, aby zalogować się do tego portalu. Jeśli nie, zaloguj się za pomocą konta Microsoft, takiego jak Outlook lub Hotmail.

- Wybierz odpowiednią opcję, jak pokazano na poniższej ilustracji.

    ![Wyświetlanie aplikacji SAML w galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Aby zaktualizować istniejącą aplikację, wybierz odpowiednią opcję zgodnie z wymaganiami.
    * Aby usunąć istniejącą aplikację z galerii aplikacji usługi Azure AD, wybierz pozycję **Usuń moją listę aplikacji z galerii**.
    * Jeśli masz jakiekolwiek problemy z dostępem, skontaktuj się z [zespołem integracji sytuaj usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Lista żądań klientów

Klienci mogą przesłać żądanie do listy aplikacji, wybierając **żądania aplikacji przez klientów** > **Prześlij nowe żądanie**.

![Pokazuje kafelek aplikacji żądanych przez klienta](./media/howto-app-gallery-listing/customer-submit-request.png)

Oto przepływ aplikacji żądanych przez klienta.

![Pokazuje przepływ aplikacji żądanych przez klienta](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Osie czasu

Harmonogram procesu wyświetlania w galerii aplikacji SAML 2.0 lub WS-Fed wynosi od 7 do 10 dni roboczych.

  ![Oś czasu umieszczenia aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline.png)

Oś czasu procesu wyświetlania aplikacji OpenID Connect w galerii wynosi od 2 do 5 dni roboczych.

  ![Oś czasu wyświetlania aplikacji OpenID Connect w galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskalacji

W przypadku eskalacji wyślij wiadomość e-mail do [zespołu integracji jednosojowej usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) pod adresem SaaSApplicationIntegrations@service.microsoft.com, a my odpowiemy tak szybko, jak to możliwe.