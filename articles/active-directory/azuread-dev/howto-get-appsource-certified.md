---
title: Jak uzyskać certyfikat AppSource dla usługi Azure Active Directory| Dokumenty firmy Microsoft
description: Szczegółowe informacje na temat uzyskania aplikacji AppSource certyfikat dla usługi Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ad4efa3b8126a9b9c6557822f61e3bfff3fe120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154886"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak uzyskać certyfikat AppSource dla usługi Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) to miejsce docelowe dla użytkowników biznesowych do odnajdowania, wypróbowania i zarządzania aplikacjami SaaS (autonomiczne SaaS i dodatek do istniejących produktów Microsoft SaaS).

Aby wyświetlić autonomiczną aplikację SaaS w usłudze AppSource, aplikacja musi akceptować logowanie jednokrotne z kont służbowych od dowolnej firmy lub organizacji z usługą Azure Active Directory (Azure AD). Proces logowania musi używać protokołów [OpenID Connect](v1-protocols-openid-connect-code.md) lub [OAuth 2.0.](v1-protocols-oauth-code.md) Integracja SAML nie jest akceptowana dla certyfikacji usługi AppSource.

## <a name="guides-and-code-samples"></a>Przewodniki i przykłady kodu

Jeśli chcesz dowiedzieć się, jak zintegrować aplikację z usługą Azure AD przy użyciu open id connect, postępuj zgodnie z naszymi przewodnikami i przykładami kodu w [przewodniku dla deweloperów usługi Azure Active Directory.](v1-overview.md#get-started "Wprowadzenie do usługi Azure AD dla deweloperów")

## <a name="multi-tenant-applications"></a>Aplikacje wielodostępne

*Aplikacja z wieloma dzierżawcami* to aplikacja, która akceptuje logowania od użytkowników z dowolnej firmy lub organizacji, które mają usługę Azure AD bez konieczności oddzielnego wystąpienia, konfiguracji lub wdrożenia. AppSource zaleca, aby aplikacje implementować multi-dzierżawy, aby włączyć *jedno kliknięcie bezpłatnej* wersji próbnej.

Aby włączyć wielonajemność w aplikacji, wykonaj następujące kroki:
1. Ustaw `Multi-Tenanted` właściwość `Yes` na informacje rejestracji aplikacji w [witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Domyślnie aplikacje utworzone w witrynie Azure portal są konfigurowane jako *[pojedyncza dzierżawca](#single-tenant-applications)*.
1. Zaktualizuj kod, `common` aby wysłać żądania do punktu końcowego. Aby to zrobić, zaktualizuj punkt końcowy od `https://login.microsoftonline.com/{yourtenant}` do `https://login.microsoftonline.com/common*`.
1. W przypadku niektórych platform, takich jak ASP .NET, należy również zaktualizować kod, aby zaakceptować wielu wystawców.

Aby uzyskać więcej informacji na temat wielu dzierżaw, zobacz [Jak zalogować się dowolny użytkownik usługi Azure Active Directory (Azure AD) przy użyciu wzorca aplikacji z wieloma dzierżawami.](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="single-tenant-applications"></a>Aplikacje dla jednej dzierżawy

*Aplikacja z jedną dzierżawą* to aplikacja, która akceptuje tylko logowania od użytkowników zdefiniowanego wystąpienia usługi Azure AD. Użytkownicy zewnętrzni (w tym konta służbowe lub szkolne z innych organizacji lub konta osobiste) mogą logować się do aplikacji z jedną dzierżawą po dodaniu każdego użytkownika jako konta gościa do wystąpienia usługi Azure AD, które aplikacja jest zarejestrowana. 

Można dodawać użytkowników jako konta gościa do usługi Azure AD za pośrednictwem [współpracy usługi Azure AD B2B](../b2b/what-is-b2b.md) i można to zrobić [programowo.](../../active-directory-b2c/code-samples.md) Korzystając z B2B, użytkownicy mogą utworzyć portal samoobsługowy, który nie wymaga zaproszenia do logowania. Aby uzyskać więcej informacji, zobacz [Portal samoobsługowy dla rejestracji współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Aplikacje z jedną dzierżawą można włączyć *contact me* środowiska, ale jeśli chcesz włączyć jednokliku/bezpłatnej wersji próbnej, że AppSource zaleca, włącz multi-dzierżawy w aplikacji zamiast.

## <a name="appsource-trial-experiences"></a>Środowiska testowe AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezpłatna wersja próbna (doświadczenie próbne prowadzone przez klienta)

Wersja próbna kierowana przez klienta jest procesem zalecanym przez appsource, ponieważ oferuje dostęp jednym kliknięciem do aplikacji. W poniższym przykładzie pokazano, jak wygląda to doświadczenie:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Użytkownik znajduje aplikację w witrynie appsource w sieci Web</li><li>Wybiera opcję "Bezpłatna wersja próbna"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource przekierowuje użytkownika do adresu URL w witrynie sieci Web</li><li>Witryna internetowa automatycznie rozpoczyna proces <i>logowania jednokrotnego</i> (przy wczytywanie strony)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Użytkownik jest przekierowywał do strony logowania firmy Microsoft</li><li>Użytkownik udostępnia poświadczenia do logowania</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Użytkownik wyraża zgodę na zgłoszenie</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Logowanie się kończy, a użytkownik jest przekierowywał z powrotem do witryny sieci Web</li><li>Użytkownik rozpoczyna bezpłatną wersję próbną</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Skontaktuj się ze mną (doświadczenie testowe prowadzone przez partnera)

Można użyć środowiska próbnego partnera, gdy ręczna lub długoterminowa operacja musi się zdarzyć, aby aprowizować użytkownika/firmę — na przykład aplikacja musi aprowizować maszyny wirtualne, wystąpienia bazy danych lub operacje, które zajmują dużo czasu. W takim przypadku po wybraniu przez użytkownika przycisku **Żądanie wersji próbnej** i wypełnieniu formularza, appsource wysyła informacje kontaktowe użytkownika. Po otrzymaniu tych informacji, a następnie aprowizować środowisko i wysłać instrukcje do użytkownika, w jaki sposób uzyskać dostęp do środowiska próbnego:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Użytkownik znajduje aplikację w witrynie sieci Web AppSource</li><li>Wybiera opcję "Skontaktuj się ze mną"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Wypełnia formularz z informacjami kontaktowymi</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Otrzymujesz informacje o użytkowniku</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Środowisko konfiguracji</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Skontaktuj się z użytkownikiem z informacjami o wersji próbnej</td>
        </tr>
        </table><br/><br/>
        <ul><li>Otrzymujesz informacje o użytkowniku i wystąpienie wersji próbnej konfiguracji</li><li>Wysyłasz hiperłącze, aby uzyskać dostęp do aplikacji do użytkownika</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Użytkownik uzyskuje dostęp do aplikacji i zakończy proces rejestracji jednokrotnej</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Użytkownik wyraża zgodę na zgłoszenie</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Logowanie się kończy, a użytkownik jest przekierowywał z powrotem do witryny sieci Web</li><li>Użytkownik rozpoczyna bezpłatną wersję próbną</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat wersji próbnej usługi AppSource, zobacz [ten film .](https://aka.ms/trialexperienceforwebapps) 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia aplikacji obsługujących logowania usługi Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios)
- Aby uzyskać informacje na temat wystawiania aplikacji SaaS w u źródła usług appsource, zobacz [Informacje o partnerach usługi AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

W przypadku integracji z usługą Azure AD używamy [przepełnienia stosu](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) ze społecznością, aby zapewnić obsługę.

Zalecamy, aby najpierw zadać pytania dotyczące przepełnienia stosu i przejrzeć istniejące problemy, aby sprawdzić, czy ktoś zadał Twoje pytanie wcześniej. Upewnij się, że twoje pytania lub komentarze są oznaczone [ `[azure-active-directory]` i `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Skorzystaj z poniższej sekcji komentarzy, aby przekazać opinię i pomóc nam udoskonalić i kształtować naszą zawartość.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
