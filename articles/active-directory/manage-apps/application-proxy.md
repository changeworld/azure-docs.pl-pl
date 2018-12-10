---
title: Jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych
description: Opisano, jak użyć serwera Proxy aplikacji usługi Azure AD, aby zapewnić bezpieczny dostęp zdalny do aplikacji w środowisku lokalnym.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: ec5c75b5de912988efeb5167107f6d0dfe07da2e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139962"
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych

Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Firma chce pracować nad swoimi własnymi urządzeniami, czy są to tabletach, telefonach lub komputery przenośne. Oczekują można było uzyskać dostęp do swoich aplikacji, obie aplikacje SaaS w chmurze i aplikacji firmowych w środowisku lokalnym. Zapewniając dostęp do aplikacji lokalnych tradycyjnie został użyty, wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowaną (sieci obwodowej). Te rozwiązania nie tylko są skomplikowane i trudne do zabezpieczenia, ale też mają wysokie koszty konfigurowania i zarządzania.

Jest lepszy sposób

Współczesnych pracowników w pierwszym mobile, chmurowych wymaga rozwiązanie nowoczesnych dostępu zdalnego. Serwer Proxy aplikacji usługi Azure AD jest funkcją usługi Azure Active Directory, która oferuje dostępu zdalnego jako usługi. Oznacza to, że można łatwo wdrażać i użycia oraz zarządzać nimi.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Co to jest serwer Proxy usługi Azure Active Directory aplikacji?
Serwer Proxy aplikacji usługi Azure AD udostępnia logowanie jednokrotne (SSO) i bezpieczny dostęp zdalny do aplikacji sieci web hostowanych lokalnie. Niektóre aplikacje, które chcesz opublikować obejmują witryny programu SharePoint, programu Outlook Web Access lub wszelkich innych używanych internetowych aplikacji LOB masz. Te lokalne aplikacje sieci web są zintegrowane z usługą Azure AD, w tej samej tożsamości i platformy kontroli, który jest używany przez usługi Office 365. Użytkownicy końcowi mogą uzyskać dostęp do aplikacji w środowisku lokalnym taki sam sposób uzyskiwania dostępu do usług O365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie ma potrzeby zmiany infrastruktury sieciowej, lub wymagać sieci VPN w celu zapewnienia tego rozwiązania dla użytkowników.

## <a name="why-is-application-proxy-a-better-solution"></a>Dlaczego jest serwer Proxy aplikacji lepszym rozwiązaniem?
Serwer Proxy aplikacji usługi Azure AD zapewnia rozwiązanie proste, bezpieczne i niedrogie dostępu zdalnego do wszystkich aplikacji w środowisku lokalnym.

Serwer Proxy aplikacji usługi Azure AD jest:

* **Proste**
   * Nie trzeba zmienić lub zaktualizować aplikacji do pracy z serwerem Proxy aplikacji. 
   * Użytkownicy pobierają spójny proces uwierzytelniania. Aby uzyskać logowanie jednokrotne do aplikacji SaaS, zarówno w chmurze i aplikacji lokalnych, będą używać portalu MyApps. 
* **Bezpieczeństwo**
   * Podczas publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD, możesz korzystać z zalet zaawansowanych kontrolek autoryzacji i analizy zabezpieczeń, na platformie Azure. Możesz korzystać z zabezpieczeń w skali chmury i funkcji zabezpieczeń platformy Azure, takich jak warunkowego dostępu i dwuetapowej weryfikacji.
   * Nie trzeba otwierać żadnych połączeń przychodzących przez zaporę w taki sposób, aby zapewnić użytkownikom dostęp zdalny. 
* **Cost-effective**
   * Serwer Proxy aplikacji działa w chmurze, co pozwala Ci oszczędzać czas i pieniądze. Lokalnymi rozwiązaniami zwykle wymagają konfiguracji i utrzymania sieci obwodowej, serwery krawędzi lub innych złożonych infrastruktury.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Jakiego rodzaju działanie aplikacji przy użyciu serwera Proxy aplikacji?
Serwer Proxy aplikacji usługi Azure AD umożliwia dostęp do różnych typów aplikacji wewnętrznych:

* Aplikacje, które używają sieci Web [zintegrowane uwierzytelnianie Windows](application-proxy-configure-single-sign-on-with-kcd.md) uwierzytelniania  
* Aplikacje korzystające z opartego na formularzach sieci Web lub [opartej na nagłówkach](application-proxy-configure-single-sign-on-with-ping-access.md) dostępu  
* Interfejsy API, który chcesz udostępnić rozbudowane aplikacje na różnych urządzeniach w sieci Web  
* Aplikacji hostowanych za [bramy usług pulpitu zdalnego](application-proxy-integrate-with-remote-desktop-services.md)  
* Rozbudowane aplikacje klienckie, które są zintegrowane z Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Jak działa serwer Proxy aplikacji
Istnieją dwa składniki, które należy skonfigurować, aby serwer Proxy aplikacji, które działają: łącznika i punktu końcowego. 

Łącznik jest uproszczone agenta, który znajduje się w systemie Windows Server w Twojej sieci. Łącznik umożliwia przepływ ruchu z serwera Proxy aplikacji usługi w chmurze do aplikacji lokalnych. Funkcja ta używa tylko połączeń wychodzących, dzięki czemu nie trzeba otworzyć żadnych portów przychodzących lub umieść niczego w sieci obwodowej. Łączniki są bezstanowe i pobierania informacji z chmury, zgodnie z potrzebami. Aby uzyskać więcej informacji na temat łączników, takich jak jak one Równoważenie obciążenia i uwierzytelniania, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md). 

Punkt końcowy może być adresem URL lub [portalu użytkownika](end-user-experiences.md). Użytkownicy mogą korzystać z aplikacji znajduje się poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w Twojej sieci dostęp do aplikacji za pomocą adresu URL lub portalu użytkownika końcowego. Użytkownicy, przejdź do jednego z tych punktów końcowych, uwierzytelniania w usłudze Azure AD i następnie są kierowane za pośrednictwem łącznika do aplikacji w środowisku lokalnym.

 ![Diagram serwera Proxy aplikacji usługi Azure AD](./media/application-proxy/azureappproxxy.png)

1. Po użytkownik uzyskał dostęp do aplikacji za pośrednictwem punktu końcowego, użytkownik jest kierowany do strony logowania usługi Azure AD. 
2. Po pomyślnym zalogowaniu token jest generowane i wysyłane do urządzenia klienckiego przez użytkownika.
3. Klient wysyła ten token do usługi serwera Proxy aplikacji, która pobiera główna nazwa użytkownika (UPN) i nazwy podmiotu zabezpieczeń (SPN) z tokenu, a następnie kieruje żądanie do łącznika serwera Proxy aplikacji.
4. Po skonfigurowaniu rejestracji jednokrotnej, łącznik wykonuje wszelkie dodatkowe uwierzytelnianie, wymagana w imieniu użytkownika.
5. Łącznik wysyła żądanie do aplikacji w środowisku lokalnym.  
6. Odpowiedź jest wysyłana za pośrednictwem usługi Serwer Proxy aplikacji i łączników do użytkownika.

### <a name="single-sign-on"></a>Logowanie jednokrotne
Serwer Proxy aplikacji usługi Azure AD zapewnia logowanie jednokrotne (SSO) do aplikacji, które używają zintegrowanego Windows Authentication (Zintegrowane) lub aplikacji obsługujących oświadczenia. Jeśli aplikacja używa IWA, serwer Proxy aplikacji personifikuje użytkownika przy użyciu ograniczonego delegowania protokołu Kerberos w celu zapewnienia logowania jednokrotnego. Jeśli aplikacja obsługująca oświadczenia, które ufają usłudze Azure Active Directory, logowania jednokrotnego działa, ponieważ użytkownik został już uwierzytelniony przez usługę Azure AD.

Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz [wszystkie chcesz wiedzieć o Kerberos ograniczone delegowanie (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Zarządzanie aplikacjami
Po opublikowaniu aplikacji za pomocą serwera Proxy aplikacji można nim zarządzać, takich jak każdą inną aplikację przedsiębiorstwa, w witrynie Azure portal. Można za pomocą funkcji zabezpieczeń usługi Azure Active Directory, takie jak warunkowego dostępu i dwuetapowej weryfikacji, kontrolować uprawnienia użytkowników i dostosowanie oznakowania dla aplikacji. 

## <a name="get-started"></a>Rozpoczęcie pracy

Przed skonfigurowaniem serwera Proxy aplikacji, upewnij się, że istnieje obsługiwana [wersji usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) i katalog usługi Azure AD, dla której jesteś administratorem globalnym.

Rozpoczynanie pracy z usługą serwera Proxy aplikacji w dwóch etapach:

1. [Włącz serwer Proxy aplikacji i skonfigurować łącznik](application-proxy-add-on-premises-application.md).    
2. [Publikowanie aplikacji](application-proxy-add-on-premises-application.md) — uruchamiaj swoje aplikacje w środowisku lokalnym opublikowane i jest dostępny zdalnie za pomocą Kreatora szybkie i łatwe.

## <a name="whats-next"></a>Co dalej?
Gdy publikujesz swoją pierwszą aplikację, istnieje wiele innych, które można wykonać przy użyciu serwera Proxy aplikacji:

* [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
* [Publikowanie aplikacji przy użyciu własnej nazwy domeny](application-proxy-configure-custom-domain.md)
* [Dowiedz się więcej na temat łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
* [Praca z istniejących serwerów Proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md) 
* [Ustaw niestandardową stronę główną](application-proxy-configure-custom-home-page.md)

Aby zapoznać się z najnowszymi informacjami i aktualizacjami, zobacz [blog dotyczący serwera proxy aplikacji](https://blogs.technet.com/b/applicationproxyblog/)

