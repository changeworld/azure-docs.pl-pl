---
title: Dostęp zdalny do lokalnych aplikacji — serwer Proxy usługi Azure Active Directory Application | Docx firmy Microsoft
description: Serwer Proxy aplikacji usługi Azure Active Directory zapewnia bezpieczny dostęp zdalny do aplikacji sieci web w środowisku lokalnym. Po logowanie jednokrotne do usługi Azure AD użytkownicy mogą korzystać zarówno z chmury i lokalnych aplikacji za pomocą zewnętrznego adresu URL lub portalu wewnętrznych aplikacji. Na przykład serwera Proxy aplikacji, może zapewnić dostępu zdalnego i logowania jednokrotnego do usług pulpitu zdalnego, programu SharePoint, Teams, Tableau, Qlik i aplikacje biznesowe (LOB).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb3a3d85c9cac85b6658167806cea59b6d5163de
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782847"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Dostęp zdalny do aplikacji lokalnych za pośrednictwem serwera Proxy aplikacji usługi Azure Active Directory 

Serwer Proxy aplikacji usługi Azure Active Directory zapewnia bezpieczny dostęp zdalny do aplikacji sieci web w środowisku lokalnym. Po logowanie jednokrotne do usługi Azure AD użytkownicy mogą korzystać zarówno z chmury i lokalnych aplikacji za pomocą zewnętrznego adresu URL lub portalu wewnętrznych aplikacji. Na przykład serwera Proxy aplikacji, może zapewnić dostępu zdalnego i logowania jednokrotnego do usług pulpitu zdalnego, programu SharePoint, Teams, Tableau, Qlik i aplikacje biznesowe (LOB).

Serwer Proxy aplikacji usługi Azure AD jest:

- **Łatwa w użyciu**. Użytkownicy mogą uzyskiwać dostęp do aplikacji w środowisku lokalnym taki sam sposób uzyskiwania dostępu do usług O365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmienić lub zaktualizować aplikacji do pracy z serwerem Proxy aplikacji. 

- **Zabezpieczanie**. Aplikacje lokalne, można użyć kontrolek autoryzacji i analizy zabezpieczeń platformy Azure. Na przykład aplikacje lokalne, można użyć dostępu warunkowego i weryfikacji dwuetapowej. Serwer Proxy aplikacji nie wymaga otwarcia połączeń przychodzących przez zaporę.
 
- **Ekonomiczne**. Rozwiązania lokalne zwykle wymagają skonfigurowania i obsługi stref zdemilitaryzowaną (stref DMZ), serwerów brzegowych lub innych złożonych infrastruktur. Uruchamia serwer Proxy aplikacji w chmurze, co pozwala na łatwe w użyciu. Aby użyć serwera Proxy aplikacji, nie trzeba zmienić infrastruktury sieciowej, lub zainstaluj dodatkowe urządzenia w środowisku w środowisku lokalnym.

## <a name="what-is-application-proxy"></a>Co to jest serwer Proxy aplikacji?
Serwer Proxy aplikacji jest funkcją usługi Azure AD, która umożliwia użytkownikom dostęp do lokalnych aplikacji sieci web ze zdalnego komputera klienckiego. Serwer Proxy aplikacji obejmuje zarówno usługi Serwer Proxy aplikacji, która jest uruchamiana w chmurze, jak i łącznika serwera Proxy aplikacji, która jest uruchamiana na serwerze w środowisku lokalnym. Usługa Azure AD, usługa serwera Proxy aplikacji i działają łącznika serwera Proxy aplikacji razem w celu bezpiecznego przekazania tokenu logowania użytkownika z usługi Azure AD do aplikacji sieci web.

Serwer Proxy aplikacji w programach:

* Aplikacje, które używają sieci Web [zintegrowane uwierzytelnianie Windows](application-proxy-configure-single-sign-on-with-kcd.md) uwierzytelniania  
* Aplikacje korzystające z opartego na formularzach sieci Web lub [opartej na nagłówkach](application-proxy-configure-single-sign-on-with-ping-access.md) dostępu  
* Interfejsy API, który chcesz udostępnić rozbudowane aplikacje na różnych urządzeniach w sieci Web  
* Aplikacji hostowanych za [bramy usług pulpitu zdalnego](application-proxy-integrate-with-remote-desktop-services.md)  
* Rozbudowane aplikacje klienckie, które są zintegrowane z Active Directory Authentication Library (ADAL)

Serwer Proxy aplikacji obsługuje logowanie jednokrotne. Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [wybranie jednej metody logowania jednokrotnego](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Serwer Proxy aplikacji jest zalecana dla użytkowników zewnętrznych zdalnego zapewniające dostęp do zasobów wewnętrznych. Serwer Proxy aplikacji zastępuje potrzebę sieci VPN lub zwrotnego serwera proxy. Nie jest on przeznaczony dla użytkowników wewnętrznych w sieci firmowej.  Użytkownicy wewnętrzni, którzy niepotrzebnie Użyj serwera Proxy aplikacji mogą powodować problemy nieoczekiwany i niepożądanych wydajność.

## <a name="how-application-proxy-works"></a>Jak działa serwer Proxy aplikacji

Poniższy diagram pokazuje jak usługa Azure AD i serwer Proxy aplikacji współpracują ze sobą, aby zapewnić logowanie jednokrotne do aplikacji lokalnych.

![Diagram serwera Proxy aplikacji usługi Azure AD](./media/application-proxy/azureappproxxy.png)

1. Po użytkownik uzyskał dostęp do aplikacji za pośrednictwem punktu końcowego, użytkownik jest kierowany do strony logowania usługi Azure AD. 
2. Po pomyślnym zalogowaniu usługi Azure AD wysyła token do urządzenia klienckiego przez użytkownika.
3. Klient wysyła ten token do usługi serwera Proxy aplikacji, która pobiera główna nazwa użytkownika (UPN) i nazwy podmiotu zabezpieczeń (SPN) z tokenem. Serwer Proxy aplikacji wysyła następnie żądanie do łącznika serwera Proxy aplikacji.
4. Po skonfigurowaniu rejestracji jednokrotnej, łącznik wykonuje wszelkie dodatkowe uwierzytelnianie, wymagana w imieniu użytkownika.
5. Łącznik wysyła żądanie do aplikacji w środowisku lokalnym.  
6. Odpowiedź jest wysyłana za pośrednictwem łącznika i usługę serwera Proxy aplikacji dla użytkownika.

| Składnik | Opis |
| --------- | ----------- |
| Endpoint  | Punkt końcowy jest adresem URL lub [portalu użytkownika](end-user-experiences.md). Użytkownicy mogą korzystać z aplikacji znajduje się poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w Twojej sieci dostęp do aplikacji za pomocą adresu URL lub portalu użytkownika końcowego. Użytkownicy, przejdź do jednego z tych punktów końcowych, uwierzytelniania w usłudze Azure AD i następnie są kierowane za pośrednictwem łącznika do aplikacji w środowisku lokalnym.|
| Azure AD | Usługa Azure AD przeprowadza uwierzytelnianie przy użyciu katalogu dzierżawy, przechowywane w chmurze. |
| Usługa serwera Proxy aplikacji | Ta usługa serwera Proxy aplikacji działa w chmurze w ramach usługi Azure AD. Przekazaniem tokenu logowania użytkownika do łącznika serwera Proxy aplikacji. Serwer Proxy aplikacji przekazuje wszelkie dostępne nagłówki dla żądania i ustawia nagłówki zgodnie z jego protokół adresu IP klienta. Jeśli przychodzące żądanie do serwera proxy już nagłówka, adres IP klienta zostanie dodany na końcu listy rozdzielanej przecinkami, który jest wartość nagłówka.|
| Łącznik serwera Proxy aplikacji | Łącznik jest uproszczone agenta, który działa w systemie Windows Server w Twojej sieci. Łącznik zarządza komunikacją między usługą serwera Proxy aplikacji w chmurze i aplikacji w środowisku lokalnym. Łącznik używa tylko połączeń wychodzących, dzięki czemu nie trzeba otwierać żadnych portów przychodzących ani umieszczać niczego w strefie DMZ. Łączniki są bezstanowe i pobierania informacji z chmury, zgodnie z potrzebami. Aby uzyskać więcej informacji na temat łączników, takich jak jak one Równoważenie obciążenia i uwierzytelniania, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md).|
| Active Directory (AD) | Usługi Active Directory działa lokalnie do uwierzytelniania dla kont domeny. Podczas logowania jednokrotnego jest skonfigurowany, łącznik komunikuje się z usługą AD, aby wykonać wszelkie wymagane dodatkowe uwierzytelnianie.
| Aplikacja lokalna | Ponadto użytkownik jest w stanie uzyskać dostęp do aplikacji w środowisku lokalnym. 

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć korzystanie z serwera Proxy aplikacji, zobacz [samouczka: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji](application-proxy-add-on-premises-application.md). 

Aby uzyskać najnowsze wiadomości i aktualizacji, zobacz [Blog dotyczący serwera Proxy aplikacji](https://blogs.technet.com/b/applicationproxyblog/)


