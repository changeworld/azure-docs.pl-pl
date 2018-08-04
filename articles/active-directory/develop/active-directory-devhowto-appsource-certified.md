---
title: Jak uzyskać certyfikat usługi Azure Active Directory w usłudze AppSource | Dokumentacja firmy Microsoft
description: Szczegółowe informacje na temat uzyskiwania certyfikatu usługi Azure Active Directory usługi AppSource aplikacji.
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
ms.openlocfilehash: 9c2140d0e482089be632d9a21560349a6381968e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495241"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak uzyskać certyfikatu AppSource dla usługi Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) jest docelowym dla użytkowników biznesowych do odnalezienia, spróbuj oraz zarządzanie aplikacjami SaaS line-of-business (SaaS, jak i dodatku do istniejących produktów SaaS firmy Microsoft).

Aby wyświetlić listę samodzielnej aplikacji SaaS w usłudze AppSource, aplikacja musi zaakceptować logowanie jednokrotne z kont służbowych z firmy lub organizacji, która ma usługę Azure Active Directory. Proces logowania, należy użyć [OpenID Connect](./active-directory-protocols-openid-connect-code.md) lub [OAuth 2.0](./active-directory-protocols-oauth-code.md) protokołów. Integracja języka SAML nie jest akceptowana w przypadku certyfikacja usługi AppSource.

## <a name="guides-and-code-samples"></a>Przewodniki i przykłady kodu
Jeśli chcesz dowiedzieć się więcej o integrowaniu aplikacji za pomocą usługi Azure Active Directory przy użyciu Identyfikatora Otwórz łączenie, postępuj zgodnie z naszymi przewodnikami i przykłady w kodu [przewodnik dewelopera usługi Azure Active Directory](azure-ad-developers-guide.md#get-started "Rozpoczynanie pracy z platformą Azure Usługi AD dla deweloperów").

## <a name="multi-tenant-applications"></a>Aplikacje wielodostępne

Aplikacja, która akceptuje logowania użytkowników z firmy lub organizacji, które mają usługę Azure Active Directory bez konieczności osobnego wystąpienia, konfiguracji lub wdrażania jest znane jako *aplikację wielodostępną*. Usługa AppSource zaleca się, że aplikacje implementują wielodostępność, aby włączyć *jednym kliknięciem* bezpłatna wersja próbna.

Aby włączyć obsługę wielu dzierżawców w swojej aplikacji:
- Ustaw `Multi-Tenanted` właściwości `Yes` informacjami rejestrację aplikacji w [witryny Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (domyślnie aplikacje utworzone w witrynie Azure Portal są skonfigurowane jako *pojedynczejdzierżawy*)
- Zaktualizuj kod w celu wysyłania żądań do "`common`" punktu końcowego (Zaktualizuj punkt końcowy z *https://login.microsoftonline.com/{yourtenant}* do *https://login.microsoftonline.com/common*)
- W przypadku niektórych platform, takich jak ASP.NET należy również zaktualizować swój kod, aby zaakceptować wielu wystawców

Aby uzyskać więcej informacji na temat obsługi wielu dzierżawców, zobacz: [jak zalogować dowolnego użytkownika usługi Azure Active Directory (AD), za pomocą wzorca aplikacji wielodostępnych](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Aplikacje z jedną dzierżawą
Aplikacje, które akceptują tylko operacje logowania użytkowników zdefiniowanych wystąpienia usługi Azure Active Directory są znane jako *aplikacji z jedną dzierżawą*. Użytkownicy zewnętrzni (w tym kont służbowych z innych organizacji lub konta osobistego) zalogować się do aplikacji pojedynczej dzierżawy, po dodaniu każdego użytkownika jako *konta gościa* z usługą Azure Active Directory wystąpienia aplikacja będzie zarejestrowana. Można dodać użytkowników jako konta gościa do usługi Azure Active Directory za pośrednictwem [ *współpracy B2B usługi Azure AD* ](../b2b/what-is-b2b.md) — i może być wykonywane [programowo](../b2b/code-samples.md). Po dodaniu użytkownika jako konta gościa do usługi Azure Active Directory wiadomość e-mail z zaproszeniem są wysyłane do użytkownika, który musi zaakceptować zaproszenie, klikając łącze w wiadomości e-mail z zaproszeniem. Zaproszenia, które są wysyłane do dodatkowych użytkowników w organizacji zapraszającej będącego również członkiem organizacji partnera nie są wymagane, aby zaakceptować zaproszenie, aby zalogować się.

Aby umożliwić aplikacji z jedną dzierżawą *skontaktuj się ze mną* środowiska, ale jeśli chcesz włączyć jednym kliknięciem / bezpłatna wersja próbna z zaleceniem usługi AppSource, włączanie wielodostępu na aplikacji zamiast tego.


## <a name="appsource-trial-experiences"></a>Środowiska wersji próbnej usługi AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezpłatna wersja próbna (środowisko wersji próbnej prowadzonej przez klienta) 
*Wersji próbnej prowadzonej przez klienta* to środowisko, z zaleceniem usługi AppSource, ponieważ oferuje on dostęp jednym kliknięciem, do aplikacji. Poniżej w jaki sposób to środowisko wygląda następująco:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Użytkownik znajdzie aplikacji w witrynie sieci Web usługi AppSource</li><li>Wybiera opcję "Bezpłatna wersja próbna"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>Usługa AppSource przekierowuje użytkownika do adresu URL witryny sieci web</li><li>Rozpoczyna się swojej witryny sieci web <i>single-sign-on</i> proces automatycznie (ładowanie strony)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Użytkownik jest przekierowywany na stronę logowania firmy Microsoft</li><li>Użytkownik podaje poświadczenia, aby zalogować się</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Użytkownik wyrazi zgodę dla aplikacji</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Zaloguj się zakończy i użytkownik jest przekierowany z powrotem do witryny sieci web</li><li>Użytkownik uruchamia bezpłatnej wersji próbnej</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontakt ze mną (środowisko wersji próbnej prowadzonej przez partnera)
*Partnera wersja próbna* mogą być używane podczas ręcznego lub długoterminowej operacja ma miejsce aprowizacja użytkownika / company: na przykład Twoja aplikacja potrzebuje do aprowizowania maszyn wirtualnych, wystąpień bazy danych lub operacji który zająć dużo czasu. W takim przypadku po użytkownik wybiera *"Żądania okres próbny"* przycisk i wypełnia formularz AppSource wysyła informacje kontaktowe użytkownika. Po otrzymaniu tych informacji, możesz następnie aprowizować środowiska i Wyślij instrukcje użytkownika dotyczące dostępu próbnego środowisko do:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Użytkownik znajdzie aplikacji w witrynie sieci web w usłudze AppSource</li><li>Wybiera opcję "Skontaktuj się z pomocą Me"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Wypełnia formularz z informacji kontaktowych</li></ul></td>
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
        <ul><li>Otrzymasz informacje i wersji próbnej wystąpienia instalacji użytkownika</li><li>Wyślij hiperłącze uzyskiwanie dostępu do aplikacji dla użytkownika</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Użytkownik uzyskuje dostęp do aplikacji i ukończyć proces logowanie jednokrotne</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Użytkownik wyrazi zgodę dla aplikacji</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Zaloguj się zakończy i użytkownik jest przekierowany z powrotem do witryny sieci web</li><li>Użytkownik uruchamia bezpłatnej wersji próbnej</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Więcej informacji
Aby uzyskać więcej informacji na temat doświadczeń z wersji próbnej usługi AppSource, zobacz [ten film wideo](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia aplikacji, które obsługują usługę Azure Active Directory, logowania, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Aby uzyskać informacje na temat wyświetlić listę aplikacji SaaS w usłudze AppSource, zobacz [informacje dla partnerów usługi AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Uzyskaj pomoc techniczną
W przypadku integracji usługi Azure Active Directory, używamy [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) ze społecznością świadczenia pomocy technicznej. 

Zdecydowanie zalecamy najpierw zadać pytanie w witrynie Stack Overflow i przeglądanie istniejących problemów, aby zobaczyć, jeśli ktoś poprosił pytanie przed. Upewnij się, że pytania lub komentarze są oznaczane za pomocą [ `[azure-active-directory]` i `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: azure-ad-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: azure-ad-developers-guide.md#get-started


<!--Image references-->