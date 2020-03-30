---
title: Dostęp zdalny do aplikacji lokalnych — serwer proxy aplikacji usługi Azure AD
description: Serwer proxy aplikacji usługi Azure Active Directory zapewnia bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web. Po pojedynczym logowaniu do usługi Azure AD użytkownicy mogą uzyskiwać dostęp zarówno do aplikacji w chmurze, jak i lokalnych za pośrednictwem zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji może udostępniać dostęp zdalny i logowanie jednokrotne do pulpitu zdalnego, programu SharePoint, teams, tableau, qlik i aplikacji biznesowych (LOB).
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
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274832"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Zdalny dostęp do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji usługi Azure Active Directory 

Serwer proxy aplikacji usługi Azure Active Directory zapewnia bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web. Po pojedynczym logowaniu do usługi Azure AD użytkownicy mogą uzyskiwać dostęp zarówno do aplikacji w chmurze, jak i lokalnych za pośrednictwem zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji może udostępniać dostęp zdalny i logowanie jednokrotne do pulpitu zdalnego, programu SharePoint, teams, tableau, qlik i aplikacji biznesowych (LOB).

Serwer proxy aplikacji usługi Azure AD charakteryzuje się następującymi cechami:

- **Prosty w użyciu**. Użytkownicy mogą uzyskiwać dostęp do aplikacji lokalnych w taki sam sposób, w jaki uzyskują dostęp do usługi O365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmieniać ani aktualizować aplikacji, aby móc pracować z serwerem proxy aplikacji. 

- **Bezpieczne**. Aplikacje lokalne mogą używać formantów autoryzacji platformy Azure i analizy zabezpieczeń. Na przykład aplikacje lokalne mogą używać dostępu warunkowego i weryfikacji dwuetapowej. Serwer proxy aplikacji nie wymaga otwierania połączeń przychodzących za pośrednictwem zapory.
 
- **Opłacalne**. Rozwiązania lokalne zazwyczaj wymagają konfigurowania i utrzymywania stref zdemilitaryzowanych (DMZ), serwerów brzegowych lub innych złożonych infrastruktur. Serwer proxy aplikacji działa w chmurze, co ułatwia korzystanie z niego. Aby korzystać z serwera proxy aplikacji, nie trzeba zmieniać infrastruktury sieciowej ani instalować dodatkowych urządzeń w środowisku lokalnym.

## <a name="what-is-application-proxy"></a>Co to jest serwer proxy aplikacji?
Serwer proxy aplikacji jest funkcją usługi Azure AD, która umożliwia użytkownikom dostęp do lokalnych aplikacji sieci web z klienta zdalnego. Serwer proxy aplikacji zawiera zarówno usługę serwera proxy aplikacji, która działa w chmurze, jak i łącznik serwera proxy aplikacji, który działa na serwerze lokalnym. Usługa Azure AD, usługa serwera proxy aplikacji i łącznik serwera proxy aplikacji współpracują ze sobą, aby bezpiecznie przekazać token logowania użytkownika z usługi Azure AD do aplikacji sieci web.

Proxy aplikacji współpracuje z:

* Aplikacje sieci Web, które używają [zintegrowanego uwierzytelniania systemu Windows](application-proxy-configure-single-sign-on-with-kcd.md) do uwierzytelniania  
* Aplikacje sieci Web korzystające z dostępu opartego na formularzach lub [nagłówkach](application-proxy-configure-single-sign-on-with-ping-access.md)  
* Internetowe interfejsy API, które chcesz udostępnić bogatym aplikacjom na różnych urządzeniach  
* Aplikacje hostowane za [bramą pulpitu zdalnego](application-proxy-integrate-with-remote-desktop-services.md)  
* Zaawansowane aplikacje klienckie zintegrowane z biblioteką uwierzytelniania usługi Active Directory (ADAL)

Serwer proxy aplikacji obsługuje logowanie jednokrotne. Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [Wybieranie metody logowania jednokrotnego.](what-is-single-sign-on.md#choosing-a-single-sign-on-method)

Serwer proxy aplikacji jest zalecany do udzielania użytkownikom zdalnym dostępu do zasobów wewnętrznych. Serwer proxy aplikacji zastępuje potrzebę korzystania z serwera VPN lub odwrotnego serwera proxy. Nie jest przeznaczony dla użytkowników wewnętrznych w sieci firmowej.  Ci użytkownicy, którzy niepotrzebnie korzystają z serwera proxy aplikacji, mogą powodować nieoczekiwane i niepożądane problemy z wydajnością.

## <a name="how-application-proxy-works"></a>Jak działa serwer proxy aplikacji

Na poniższym diagramie pokazano, jak usługi Azure AD i serwer proxy aplikacji współpracują ze sobą w celu zapewnienia logowania jednokrotnego do aplikacji lokalnych.

![Diagram serwera proxy aplikacji AzureAD](./media/application-proxy/azureappproxxy.png)

1. Po użytkownik ma dostęp do aplikacji za pośrednictwem punktu końcowego, użytkownik jest kierowany do strony logowania usługi Azure AD. 
2. Po pomyślnym zalogowaniu usługa Azure AD wysyła token do urządzenia klienckiego użytkownika.
3. Klient wysyła token do usługi proxy aplikacji, która pobiera nazwę główną użytkownika (UPN) i nazwę głównego zabezpieczeń (SPN) z tokenu. Następnie serwer proxy aplikacji wysyła żądanie do łącznika serwera proxy aplikacji.
4. Jeśli skonfigurowano logowanie jednokrotne, łącznik wykonuje wszelkie dodatkowe uwierzytelnianie wymagane w imieniu użytkownika.
5. Łącznik wysyła żądanie do aplikacji lokalnej.  
6. Odpowiedź jest wysyłana za pośrednictwem łącznika i usługi proxy aplikacji do użytkownika.

| Składnik | Opis |
| --------- | ----------- |
| Endpoint  | Punktem końcowym jest adres URL lub [portal użytkowników końcowych](end-user-experiences.md). Użytkownicy mogą korzystać z aplikacji poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w sieci mogą uzyskiwać dostęp do aplikacji za pośrednictwem adresu URL lub portalu użytkownika końcowego. Gdy użytkownicy przejść do jednego z tych punktów końcowych, uwierzytelniają się w usłudze Azure AD, a następnie są kierowane za pośrednictwem łącznika do aplikacji lokalnej.|
| Azure AD | Usługa Azure AD wykonuje uwierzytelnianie przy użyciu katalogu dzierżawy przechowywanego w chmurze. |
| Usługa proxy aplikacji | Ta usługa serwera proxy aplikacji jest uruchamiana w chmurze w ramach usługi Azure AD. Przekazuje token logowania od użytkownika do łącznika serwera proxy aplikacji. Serwer proxy aplikacji przesyła dalej wszystkie dostępne nagłówki w żądaniu i ustawia nagłówki zgodnie z protokołem, na adres IP klienta. Jeśli żądanie przychodzące do serwera proxy ma już ten nagłówek, adres IP klienta jest dodawany na końcu listy oddzielonych przecinkami, która jest wartością nagłówka.|
| Łącznik serwera proxy aplikacji | Łącznik jest lekkim agentem, który działa na serwerze Windows Server wewnątrz sieci. Łącznik zarządza komunikacją między usługą proxy aplikacji w chmurze a aplikacją lokalną. Łącznik używa tylko połączeń wychodzących, więc nie trzeba otwierać żadnych portów przychodzących ani umieszczać niczego w strefie DMZ. Łączniki są bezstanowe i ściągają informacje z chmury w razie potrzeby. Aby uzyskać więcej informacji na temat łączników, takich jak sposób równoważenia obciążenia i uwierzytelniania, zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md).|
| Usługa Active Directory (AD) | Usługa Active Directory jest uruchamiana lokalnie w celu uwierzytelniania kont domeny. Po skonfigurowaniu logowania jednokrotnego łącznik komunikuje się z usługą AD w celu wykonania dodatkowego wymaganego uwierzytelniania.
| Aplikacja lokalna | Na koniec użytkownik może uzyskać dostęp do aplikacji lokalnej. 

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji](application-proxy-add-on-premises-application.md). 

Najnowsze informacje i aktualizacje można znaleźć w [blogu serwera proxy aplikacji](https://blogs.technet.com/b/applicationproxyblog/)


