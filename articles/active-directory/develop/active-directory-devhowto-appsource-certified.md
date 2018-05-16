---
title: Jak uzyskać AppSource certyfikowane dla usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat aplikacji AppSource certyfikowane dla usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 72516ce84fc9a4cf2657548be4ef98e75e46d550
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak uzyskać AppSource certyfikowane dla usługi Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) jest docelowy dla użytkowników biznesowych wykrywanie, spróbuj i zarządzanie nimi z biznesowych aplikacji SaaS (SaaS, jak i dodatku do istniejących produktów Microsoft SaaS).

Aby wyświetlić listę autonomicznym aplikacji SaaS na AppSource, aplikacja musi akceptować rejestracji jednokrotnej z konta służbowego z firmy lub organizacji, która ma usługę Azure Active Directory. Proces logowania, należy użyć [OpenID Connect](./active-directory-protocols-openid-connect-code.md) lub [OAuth 2.0](./active-directory-protocols-oauth-code.md) protokołów. Integrację SAML nie jest akceptowane AppSource certyfikacji.

## <a name="guides-and-code-samples"></a>Wskazówek i przykładów kodu
Jeśli chcesz dowiedzieć się więcej o integracji aplikacji w usłudze Azure Active Directory za pomocą Identyfikatora otwarte połączenie, wykonaj nasze wskazówki i kodu próbek w [przewodnik dewelopera usługi Azure Active Directory](./active-directory-developers-guide.md#get-started "Rozpoczynanie pracy z usługą Azure AD dla deweloperów").

## <a name="multi-tenant-applications"></a>Aplikacje wielodostępne

Aplikacja, która akceptuje logowania użytkowników z firmy lub organizacji mających usługi Azure Active Directory bez konieczności oddzielnego wystąpienia, konfiguracji lub wdrożenia jest znany jako *wielodostępnych aplikacji*. AppSource zaleca się, że aplikacje wdrożyć wielodostępność, aby włączyć *jednym kliknięciem* wolne środowisko wersji próbnej.

Aby włączyć obsługi wielu dzierżawców w swojej aplikacji:
- Ustaw `Multi-Tenanted` właściwości `Yes` na informacje o rejestracji aplikacji w [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (domyślnie aplikacje utworzone w portalu Azure są skonfigurowane jako *pojedynczej dzierżawy*)
- Zaktualizuj kod do wysyłania żądań do "`common`" punktu końcowego (zaktualizować punktu końcowego z *https://login.microsoftonline.com/{yourtenant}* do *https://login.microsoftonline.com/common*)
- Dla niektórych platform, takich jak ASP.NET należy również zaktualizować swój kod, aby zaakceptować wielu wystawców

Aby uzyskać więcej informacji na temat obsługi wielu dzierżawców, zobacz: [jak zarejestrować każdy użytkownik usługi Azure Active Directory (AD) przy użyciu wzorca wielodostępnych aplikacji](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Aplikacje pojedynczej dzierżawy
Aplikacje, które akceptują tylko logowania użytkowników zdefiniowanych wystąpienia usługi Azure Active Directory są określane jako *aplikacji pojedynczej dzierżawy*. Użytkownicy zewnętrzni (łącznie z konta służbowego z innych organizacji lub osobiste konto) można Zaloguj się do aplikacji pojedynczej dzierżawy, po dodaniu każdego użytkownika jako *konta gościa* do wystąpienia usługi Azure Active Directory, że aplikacja jest zarejestrowany. Jako konta gościa można dodać użytkowników do usługi Azure Active Directory za pośrednictwem [ *współpracy B2B usługi Azure AD* ](../active-directory-b2b-what-is-azure-ad-b2b.md) - i mogą to robić [programowo](../active-directory-b2b-code-samples.md). Gdy użytkownik zostanie dodany jako konta gościa do usługi Azure Active Directory, wiadomość e-mail z zaproszeniem są wysyłane do użytkownika, który musi zaakceptować zaproszenie, klikając łącze w wiadomości e-mail z zaproszeniem. Zaproszenia, które są wysyłane do dodatkowych użytkowników w organizacji zaproszenia, która jest również członkiem organizacji partnerskiej odpowiedzialnej za nie są wymagane, aby zaakceptować zaproszenie do logowania.

Aplikacje pojedynczego dzierżawcy można włączyć *kontaktu mnie* środowisko, ale jeśli chcesz włączyć jednym kliknięciem / bezpłatnej wersji próbnej środowisko zalecający AppSource włączenia obsługi wielu dzierżawców w swojej aplikacji zamiast tego.


## <a name="appsource-trial-experiences"></a>Napotyka AppSource wersji próbnej

### <a name="free-trial-customer-led-trial-experience"></a>Bezpłatna wersja próbna (środowisko wersji próbnej doprowadziły klienta) 
*Doprowadziły klienta wersji próbnej* to środowisko, które AppSource zaleca jak oferuje jednym kliknięciem uzyskać dostęp do aplikacji. Poniżej w jaki sposób to środowisko wygląda następująco:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Użytkownik wyszukuje aplikacji w witrynie sieci Web AppSource</li><li>Wybiera opcję "Bezpłatnej wersji próbnej"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource przekierowuje użytkownika do adresu URL witryny sieci web</li><li>Z witryny sieci web uruchamia <i>single-sign-on</i> proces automatycznie (ładowania strony)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Użytkownik zostanie przekierowany do strony logowania firmy Microsoft</li><li>Użytkownik podaje poświadczenia do zalogowania</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Użytkownik wyrazi zgodę dla aplikacji</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Logowanie kończy i użytkownik zostanie przekierowany do witryny sieci web</li><li>Użytkownik uruchamia bezpłatnej wersji próbnej</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Skontaktować się ze mną (doprowadziły partnera środowisko wersji próbnej)
*Partnera środowisko wersji próbnej* można użyć w przypadku ręcznego lub długoterminowej operacja musi zostać przeprowadzona do obsługi administracyjnej użytkownika / firmę: na przykład, aplikacja musi udostępniać maszyn wirtualnych, wystąpień bazy danych lub operacji, które zająć dużo czasu. W takim przypadku po użytkownik wybiera *"Żądania wersji próbnej"* przycisk i wypełnienia formularz AppSource wysyła informacje kontaktowe użytkownika. Po otrzymaniu tych informacji, możesz następnie udostępnić środowiska i wysłać instrukcje dla użytkownika na temat sposobu dostępu środowisko wersji próbnej:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Użytkownik wyszukuje aplikacji w witrynie sieci web AppSource</li><li>Wybiera opcję "Skontaktuj się z Me"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Wypełnia formularz informacje kontaktowe</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Odbieranie informacji o użytkowniku</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Konfigurowanie środowiska</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Skontaktuj się z użytkownikiem z informacjami o wersji próbnej</td>
        </tr>
        </table><br/><br/>
        <ul><li>Odbieranie informacji i wersji próbnej wystąpienia ustawienia użytkownika</li><li>Wyślij hiperłącze, aby dostęp do aplikacji dla użytkownika</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Użytkownik uzyskuje dostęp do aplikacji i ukończyć proces single-sign-on</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Użytkownik wyrazi zgodę dla aplikacji</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Logowanie kończy i użytkownik zostanie przekierowany do witryny sieci web</li><li>Użytkownik uruchamia bezpłatnej wersji próbnej</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Więcej informacji
Aby uzyskać więcej informacji na temat środowisko wersji próbnej AppSource, zobacz [ten film](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji dotyczących tworzenia aplikacji, które obsługują usługi Azure Active Directory logowania, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Aby uzyskać informacje dotyczące listy aplikacji SaaS w AppSource, zobacz [informacje o partnerze AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Uzyskaj pomoc techniczną
Integracja usługi Azure Active Directory, używamy [przepełnienie stosu](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) społeczności, aby zapewnić obsługę. 

Zdecydowanie zaleca się najpierw zadać pytania w witrynie Stack Overflow i Przeglądaj istniejących problemów, aby zobaczyć, jeśli ktoś poprosił pytanie przed. Upewnij się, że pytania lub komentarze są oznaczane [ `[azure-active-directory]` i `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->