---
title: Często zadawane pytania dotyczące serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak odpowiedzi na często zadawane pytania (CZĘSTO ZADAWANE PYTANIA) dotyczące używania serwera proxy aplikacji usługi Azure AD do publikowania wewnętrznych aplikacji lokalnych użytkownikom zdalnym.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d1929f937d86001a0f2a399b1ebd92e47bbd2c86
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990909"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Często zadawane pytania dotyczące serwera proxy aplikacji usługi Active Directory (Azure AD)

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące serwera proxy aplikacji usługi Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Włączanie serwera proxy aplikacji usługi Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Jaka licencja jest wymagana do korzystania z serwera proxy aplikacji usługi Azure AD?

Aby korzystać z serwera proxy aplikacji usługi Azure AD, musisz mieć licencję Usługi Azure AD Premium P1 lub P2. Aby uzyskać więcej informacji na temat licencjonowania, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Dlaczego przycisk "Włącz serwer proxy aplikacji jest wyszarzony?

Upewnij się, że masz co najmniej licencję usługi Azure AD Premium P1 lub P2 i zainstalowany łącznik serwera proxy aplikacji usługi Azure AD. Po pomyślnym zainstalowaniu pierwszego łącznika usługa serwera proxy aplikacji usługi Azure AD zostanie włączona automatycznie.

## <a name="connector-configuration"></a>Konfiguracja łącznika

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Czy usługi łącznika serwera proxy aplikacji mogą być uruchamiane w innym kontekście użytkownika niż domyślne?

Nie, ten scenariusz nie jest obsługiwany. Ustawienia domyślne to:

- Złącze serwera proxy aplikacji Microsoft AAD — WAPCSvc — usługa sieciowa
- Aktualizacja łącznika serwera proxy aplikacji Microsoft AAD — WAPCUpdaterSvc — nt authority\system

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Moja aplikacja zaplecza jest hostowana na wielu serwerach sieci web i wymaga trwałości sesji użytkownika (lepkość). Jak mogę osiągnąć trwałość sesji? 

Aby uzyskać zalecenia, zobacz [Wysoka dostępność i równoważenie obciążenia łączników i aplikacji serwera proxy aplikacji](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Czy zakończenie protokołu TLS (inspekcja lub przyspieszanie protokołu TLS/HTTPS) jest obsługiwane w ruchu z serwerów łączników na platformie Azure?

Łącznik serwera proxy aplikacji wykonuje uwierzytelnianie oparte na certyfikatach na platformie Azure. Zakończenie protokołu TLS (inspekcja lub przyspieszenie protokołu TLS/HTTPS) przerywa tę metodę uwierzytelniania i nie jest obsługiwane. Ruch z łącznika do platformy Azure należy pominąć wszystkie urządzenia, które wykonują zakończenie TLS.  

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Czy można umieścić urządzenie proxy do przodu między serwerami łączników a serwerem aplikacji zaplecza?
Tak, ten scenariusz jest obsługiwany począwszy od wersji łącznika 1.5.1526.0. Zobacz [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Czy należy utworzyć dedykowane konto, aby zarejestrować łącznik za pomocą serwera proxy aplikacji usługi Azure AD?

Nie ma powodu. Działa każde konto administratora lub administratora aplikacji. Poświadczenia wprowadzone podczas instalacji nie są używane po procesie rejestracji. Zamiast tego certyfikat jest wystawiany do łącznika, który jest używany do uwierzytelniania od tego momentu.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Jak monitorować wydajność łącznika serwera proxy aplikacji usługi Azure AD?

Istnieją liczniki Monitora wydajności, które są zainstalowane wraz ze złączem. Aby je wyświetlić:  

1. Wybierz **start**, wpisz "Perfmon" i naciśnij klawisz ENTER.
2. Wybierz **Pozycję Monitor** wydajności **+** i kliknij zieloną ikonę.
3. Dodaj liczniki **łącznika serwera proxy aplikacji AAD** firmy Microsoft, które chcesz monitorować.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Czy łącznik serwera proxy aplikacji usługi Azure AD musi znajdować się w tej samej podsieci co zasób?

Łącznik nie musi znajdować się w tej samej podsieci. Jednak potrzebuje rozpoznawania nazw (DNS, hostów pliku) do zasobu i niezbędnej łączności sieciowej (routing do zasobu, porty otwarte na zasób, itp.). Aby uzyskać zalecenia, zobacz [Zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure Active Directory](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Jaka jest długość domyślnego i "długiego" limitu czasu zaplecza? Czy limit czasu może zostać przedłużony?

Domyślna długość wynosi 85 sekund. Ustawienie "długie" to 180 sekund. Nie można przedłużyć limitu czasu.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Jak zmienić stronę docelową, na która wczytuje się moja aplikacja?

Na stronie Rejestracje aplikacji możesz zmienić adres URL strony głównej na żądany zewnętrzny adres URL strony docelowej. Określona strona zostanie załadowana po uruchomieniu aplikacji z aplikacji Moje aplikacje lub portalu usługi Office 365. Aby zapoznać się z instrukcjami konfiguracji, zobacz [Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Czy można publikować tylko aplikacje oparte na iis? Co z aplikacjami internetowymi działającymi na serwerach sieci Web innych niż Windows? Czy łącznik musi być zainstalowany na serwerze z zainstalowanymi usługami IIS?

Nie, nie ma żadnych wymagań IIS dla aplikacji, które są publikowane. Aplikacje sieci Web uruchomione na serwerach innych niż Windows Server można publikować. Jednak w zależności od tego, czy serwer sieci web obsługuje funkcję Negocjuj (uwierzytelnianie Kerberos), można nie używać uwierzytelniania wstępnego z serwerem innym niż Windows Server. Usługi IIS nie są wymagane na serwerze, na którym jest zainstalowany łącznik.

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kiedy należy używać metody PrincipalsAllowedToDelegateToAccount podczas konfigurowania delegowania ograniczonego protokołu Kerberos (KCD)?

Metoda PrincipalsAllowedToDelegateToAccount jest używana, gdy serwery łączników znajdują się w innej domenie niż konto usługi aplikacji sieci web. Wymaga użycia ograniczonego delegowania opartego na zasobach.
Jeśli serwery łączników i konto usługi aplikacji sieci web znajdują się w tej samej domenie, można użyć przystawki Użytkownicy i komputery usługi Active Directory, aby skonfigurować ustawienia delegowania na każdym z kont komputera łącznika, umożliwiając im delegowanie do docelowej nazwy SPN.

Jeśli serwery łączników i konto usługi aplikacji sieci web znajdują się w różnych domenach, jest używane delegowanie oparte na zasobach. Uprawnienia delegowania są konfigurowane na docelowym serwerze sieci web i koncie usługi aplikacji sieci web. Ta metoda ograniczonego delegowania jest stosunkowo nowa. Metoda ta została wprowadzona w systemie Windows Server 2012, która obsługuje delegowanie między domenami, umożliwiając właścicielowi zasobu (usługi sieci web) kontrolowanie, które konta komputera i usługi mogą do niego delegować. Nie ma interfejsu użytkownika, aby pomóc w tej konfiguracji, więc należy użyć programu PowerShell.
Aby uzyskać więcej informacji, zobacz oficjalny dokument [Opis delegowania ograniczonego protokołu Kerberos z serwerem proxy aplikacji](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Czy mogę używać zasad dostępu warunkowego dla aplikacji opublikowanych z uwierzytelnianiem przekazu?

Zasady dostępu warunkowego są wymuszane tylko dla pomyślnie wstępnie uwierzytelnionych użytkowników w usłudze Azure AD. Uwierzytelnianie przekazywane nie wyzwala uwierzytelniania usługi Azure AD, więc nie można wymusić zasad dostępu warunkowego. W przypadku uwierzytelniania przekazywanego zasady usługi MFA muszą być implementowane na serwerze lokalnym, jeśli to możliwe, lub przez włączenie uwierzytelniania wstępnego za pomocą serwera proxy aplikacji usługi Azure AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Czy mogę opublikować aplikację sieci web z wymaganiem uwierzytelniania certyfikatu klienta?

Nie, ten scenariusz nie jest obsługiwany, ponieważ serwer proxy aplikacji zakończy ruch TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publikowanie bramy usług pulpitu zdalnego

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Jak opublikować bramę usług pulpitu zdalnego za pomocą serwera proxy aplikacji usługi Azure AD?

Zapoznaj się [z tematą Publikowania pulpitu zdalnego za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Czy w scenariuszu publikowania bramy usług pulpitu zdalnego można używać delegowania ograniczonego protokołu Kerberos (logowanie jednokrotne — uwierzytelnianie zintegrowane systemu Windows) ?

Nie, ten scenariusz nie jest obsługiwany.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Moi użytkownicy nie korzystają z programu Internet Explorer 11, a scenariusz wstępnego uwierzytelniania nie działa dla nich. Czy to się spodziewa?

Tak, jest to oczekiwane. Scenariusz wstępnego uwierzytelniania wymaga formantu ActiveX, który nie jest obsługiwany w przeglądarkach innych firm.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Czy klient sieci Web pulpitu zdalnego (HTML5) jest obsługiwany?

Nie, ten scenariusz nie jest obecnie obsługiwany. Aby uzyskać informacje na temat tej funkcji, skorzystaj z naszego forum opinii [uservoice.](https://aka.ms/aadapuservoice)

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Po skonfigurowaniu scenariusza wstępnego uwierzytelniania zdałem sobie sprawę, że użytkownik musi uwierzytelnić się dwa razy: najpierw w formularzu logowania usługi Azure AD, a następnie w formularzu logowania RDWeb. Czy to się spodziewa? Jak mogę zmniejszyć to do jednego logowania?

Tak, jest to oczekiwane. Jeśli komputer użytkownika jest przyłączony do usługi Azure AD, użytkownik loguje się automatycznie do usługi Azure AD. Użytkownik musi podać swoje poświadczenia tylko w formularzu logowania RDWeb.

## <a name="sharepoint-publishing"></a>Publikowanie programu SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Jak opublikować program SharePoint za pomocą serwera proxy aplikacji usługi Azure AD?

Zapoznaj się [z opcję Włączanie dostępu zdalnego do programu SharePoint za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Czy mogę uzyskać dostęp do opublikowanego serwera programu SharePoint za pomocą aplikacji mobilnej SharePoint (iOS/Android)?

[Aplikacja mobilna programu SharePoint](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) nie obsługuje obecnie wstępnego uwierzytelniania usługi Azure Active Directory.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publikowanie usług federacyjnych Active Directory (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Czy mogę używać serwera proxy aplikacji usługi Azure AD jako serwera proxy usług AD FS (takiego jak serwer proxy aplikacji sieci Web)?

Nie. Usługa Azure AD Application Proxy jest przeznaczony do pracy z usługą Azure AD i nie spełnia wymagań do działania jako serwer proxy usług AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Czy websocket obsługuje aplikacje inne niż QlikSense?

Obecnie obsługa protokołu WebSocket jest nadal w publicznej wersji zapoznawczej i może nie działać dla innych aplikacji. Niektórzy klienci odnieśli mieszany sukces przy użyciu protokołu WebSocket z innymi aplikacjami. Jeśli przetestujesz takie scenariusze, chcielibyśmy usłyszeć Twoje wyniki. Prześlij nam aadapfeedback@microsoft.comswoją opinię na .

Funkcje (Eventlogs, PowerShell i Usługi pulpitu zdalnego) w Centrum administracyjnym systemu Windows (WAC) lub kliencie sieci Web pulpitu zdalnego (HTML5) nie działają obecnie za pośrednictwem serwera proxy aplikacji usługi Azure AD.

## <a name="link-translation"></a>Tłumaczenie linków

### <a name="does-using-link-translation-affect-performance"></a>Czy używanie tłumaczenia linku wpływa na wydajność?

Tak. Tłumaczenie łącza wpływa na wydajność. Usługa Proxy aplikacji skanuje aplikację w poszukiwaniu linków zakodowanych na stałe i zastępuje je odpowiednimi, opublikowanymi zewnętrznymi adresami URL przed przedstawieniem ich użytkownikowi. 

Aby uzyskać najlepszą wydajność, zalecamy używanie identycznych wewnętrznych i zewnętrznych adresów URL przez [konfigurowanie domen niestandardowych](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Jeśli korzystanie z domen niestandardowych nie jest możliwe, możesz poprawić wydajność tłumaczenia łączy za pomocą rozszerzenia Bezpieczne logowanie w moich aplikacjach lub przeglądarki Microsoft Edge na urządzeniach przenośnych. Zobacz [Przekierowanie łączy zakodowanych na stałe dla aplikacji opublikowanych za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Symbole wieloznaczne

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Jak używać symboli wieloznacznych do publikowania dwóch aplikacji o tej samej niestandardowej nazwie domeny, ale z różnymi protokołami, jednej dla protokołu HTTP i jednej dla protokołu HTTPS?

Ten scenariusz nie jest obsługiwany bezpośrednio. Dostępne opcje w tym scenariuszu to:

1. Opublikuj adresy URL HTTP i HTTPS jako oddzielne aplikacje z symbolami wieloznacznych, ale nadaj każdej z nich inną domenę niestandardową. Ta konfiguracja będzie działać, ponieważ mają różne zewnętrzne adresy URL.

2. Opublikuj adres URL HTTPS za pośrednictwem aplikacji z symbolami wieloznaczymi. Publikowanie aplikacji HTTP oddzielnie przy użyciu następujących poleceń cmdlet programu PowerShell serwera proxy aplikacji:
   - [Zarządzanie aplikacjami proxy aplikacji](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Zarządzanie łącznikami serwera proxy aplikacji](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
