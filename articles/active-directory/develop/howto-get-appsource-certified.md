---
title: Jak uzyskać certyfikat usługi Azure Active Directory w usłudze AppSource | Dokumentacja firmy Microsoft
description: Szczegółowe informacje na temat uzyskiwania certyfikatu usługi Azure Active Directory usługi AppSource aplikacji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc42ab8a8cfb0d182c69bd0940e23cffdb2be0af
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807249"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak uzyskać certyfikatu AppSource dla usługi Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) jest docelowym dla użytkowników biznesowych do odnalezienia, spróbuj oraz zarządzanie aplikacjami SaaS line-of-business (SaaS, jak i dodatku do istniejących produktów SaaS firmy Microsoft).

Aby wyświetlić listę samodzielnej aplikacji SaaS w usłudze AppSource, aplikacja musi zaakceptować logowanie jednokrotne z kont służbowych z firmy lub organizacji, która ma usługi Azure Active Directory (Azure AD). Proces logowania, należy użyć [OpenID Connect](v1-protocols-openid-connect-code.md) lub [OAuth 2.0](v1-protocols-oauth-code.md) protokołów. Integracja języka SAML nie jest akceptowana w przypadku certyfikacja usługi AppSource.

## <a name="guides-and-code-samples"></a>Przewodniki i przykłady kodu

Jeśli chcesz dowiedzieć się więcej o integrowaniu aplikacji z usługą Azure AD przy użyciu Identyfikatora Otwórz łączenie, postępuj zgodnie z naszymi przewodnikami i przykłady w kodu [przewodnik dewelopera usługi Azure Active Directory](v1-overview.md#get-started "Rozpoczynanie pracy z usługą Azure AD Deweloperzy").

## <a name="multi-tenant-applications"></a>Aplikacje wielodostępne

A *aplikację wielodostępną* to aplikacja, która akceptuje logowania użytkowników z firmy lub organizacji, które mają usługę Azure AD bez konieczności osobnego wystąpienia, konfiguracji lub wdrożenia. Usługa AppSource zaleca się, że aplikacje implementują wielodostępność, aby włączyć *jednym kliknięciem* bezpłatna wersja próbna.

Aby włączyć wielu dzierżawców w swojej aplikacji, wykonaj następujące kroki:
1. Ustaw `Multi-Tenanted` właściwości `Yes` informacjami rejestrację aplikacji w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Domyślnie aplikacje utworzone w witrynie Azure portal są skonfigurowane jako  *[pojedynczej dzierżawy](#single-tenant-applications)* .
1. Zaktualizuj kod w celu wysyłania żądań do `common` punktu końcowego. Aby to zrobić, należy zaktualizować punkt końcowy z `https://login.microsoftonline.com/{yourtenant}` do `https://login.microsoftonline.com/common*`.
1. W przypadku niektórych platform, takich jak ASP .NET należy również zaktualizować swój kod, aby zaakceptować wielu wystawców.

Aby uzyskać więcej informacji na temat obsługi wielu dzierżawców, zobacz [jak zalogować dowolnego użytkownika usługi Azure Active Directory (Azure AD), za pomocą wzorca aplikacji wielodostępnych](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplikacje z jedną dzierżawą

A *aplikacji z jedną dzierżawą* to aplikacja, która akceptuje tylko operacje logowania użytkowników zdefiniowanych Azure wystąpienia usługi AD. Użytkownicy zewnętrzni (w tym pracy lub kont służbowych z innych organizacji lub kont osobistych) zalogować się do aplikacji pojedynczej dzierżawy, po dodaniu każdego użytkownika jako konto gościa do wystąpienia usługi Azure AD, czy aplikacja jest zarejestrowana. 

Można dodać użytkowników jako konta gościa do usługi Azure AD za pomocą [współpracy B2B usługi Azure AD](../b2b/what-is-b2b.md) i można to zrobić [programowo](../../active-directory-b2c/code-samples.md). Korzystając z B2B, użytkownicy mogą tworzyć portalu samoobsługowego, który nie wymaga zaproszenia do zarejestrowania. Aby uzyskać więcej informacji, zobacz [portalu samoobsługowego do obsługi współpracy B2B usługi Azure AD, Utwórz konto](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Aby umożliwić aplikacji z jedną dzierżawą *skontaktuj się ze mną* środowiska, ale jeśli chcesz włączyć środowisko pojedynczego kliknij/bezpłatnych wersji próbnej, z zaleceniem usługi AppSource, włączanie wielodostępu na aplikacji zamiast tego.

## <a name="appsource-trial-experiences"></a>Środowiska wersji próbnej usługi AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezpłatna wersja próbna (środowisko wersji próbnej prowadzonej przez klienta)

Wersji próbnej prowadzonej przez klienta to środowisko, z zaleceniem usługi AppSource, ponieważ oferuje on dostęp jednym kliknięciem, do aplikacji. Poniższy przykład pokazuje, jak wygląda to środowisko:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Użytkownik znajdzie aplikacji w witrynie sieci Web usługi AppSource</li><li>Wybiera opcję "Bezpłatna wersja próbna"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>Usługa AppSource przekierowuje użytkownika do adresu URL witryny sieci web</li><li>Rozpoczyna się swojej witryny sieci web <i>single-sign-on</i> proces automatycznie (ładowanie strony)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Użytkownik jest przekierowywany na stronę logowania firmy Microsoft</li><li>Użytkownik podaje poświadczenia, aby zalogować się</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Użytkownik wyrazi zgodę dla aplikacji</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Zaloguj się zakończy i użytkownik jest przekierowany z powrotem do witryny sieci web</li><li>Użytkownik uruchamia bezpłatnej wersji próbnej</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontakt ze mną (środowisko wersji próbnej prowadzonej przez partnera)

Podczas ręcznego lub długoterminowej operacja musi zostać przeprowadzona do aprowizacja użytkownika/firmy — na przykład, aplikacja potrzebuje aprowizować maszyny wirtualne, wystąpień bazy danych lub operacji, które zająć dużo czasu, mogą użyć środowiska próbne partnera. W takim przypadku po użytkownik wybiera **żądania wersji próbnej** przycisk i wypełnia formularz AppSource wysyła informacje kontaktowe użytkownika. Po otrzymaniu tych informacji możesz następnie aprowizować środowiska i Wyślij instrukcje użytkownika dotyczące dostępu próbnego środowisko do:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Użytkownik znajdzie aplikacji w witrynie sieci web w usłudze AppSource</li><li>Wybiera opcję "Skontaktuj się z pomocą Me"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Wypełnia formularz z informacji kontaktowych</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Odbieranie informacji o użytkowniku</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Konfigurowanie środowiska</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Skontaktuj się z użytkownikiem z informacjami o wersji próbnej</td>
        </tr>
        </table><br/><br/>
        <ul><li>Otrzymasz informacje i wersji próbnej wystąpienia instalacji użytkownika</li><li>Wyślij hiperłącze uzyskiwanie dostępu do aplikacji dla użytkownika</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Użytkownik uzyskuje dostęp do aplikacji i ukończyć proces logowanie jednokrotne</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Użytkownik wyrazi zgodę dla aplikacji</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Zaloguj się zakończy i użytkownik jest przekierowany z powrotem do witryny sieci web</li><li>Użytkownik uruchamia bezpłatnej wersji próbnej</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat doświadczeń z wersji próbnej usługi AppSource, zobacz [ten film wideo](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia aplikacji, które obsługują usługę Azure AD, logowania, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Aby uzyskać informacje na temat wyświetlić listę aplikacji SaaS w usłudze AppSource, zobacz [informacje dla partnerów usługi AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

W przypadku integracji z usługą Azure AD, używamy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) ze społecznością świadczenia pomocy technicznej.

Zdecydowanie zalecamy najpierw zadać pytanie w witrynie Stack Overflow i przeglądanie istniejących problemów, aby zobaczyć, jeśli ktoś poprosił pytanie przed. Upewnij się, że pytania lub komentarze są oznaczane za pomocą [ `[azure-active-directory]` i `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
