---
title: Jak działa logowanie Jednokrotne do zasobów lokalnych w usłudze Azure AD urządzenia przyłączone do | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45941de6a90a5824ebc1e5d31b18b68f5fd9d493
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520551"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Jak działa logowanie Jednokrotne do zasobów lokalnych w usłudze Azure AD urządzenia przyłączone do

Prawdopodobnie nie jest zaskoczeniem, że urządzenia przyłączone do usługi Azure Active Directory (Azure AD) zapewnia środowisko logowania jednokrotnego (SSO) do aplikacji w chmurze Twojej dzierżawy. W środowisku zawierającym lokalnej usługi Active Directory (AD), można rozszerzyć środowisko logowania jednokrotnego na tych urządzeniach do niego.

W tym artykule opisano, jak to działa.

## <a name="how-it-works"></a>Jak to działa 

Ponieważ muszą zapamiętać tylko jedną jedną nazwę użytkownika i hasło, logowanie Jednokrotne upraszcza dostęp do zasobów i zwiększa bezpieczeństwo środowiska. Urządzenia przyłączone do usługi Azure AD użytkownicy już środowisko logowania jednokrotnego do aplikacji w chmurze w swoim środowisku. Jeśli w środowisku zawierającym usługi Azure AD a lokalną AD, prawdopodobnie chcesz rozszerzyć zakres środowisko logowania jednokrotnego do lokalnych aplikacji Line Of Business (LOB), udziały plików i drukarek.  


Urządzenia usługi Azure AD, przyłączone do mają nie wiedzą na temat lokalnej usługi AD środowiska, ponieważ nie są one podłączone do niego. Może jednak zapewniać dodatkowe informacje dotyczące lokalnej usługi AD na tych urządzeniach za pomocą usługi Azure AD Connect.
Środowisko, które ma obydwa, moduł usługi Azure AD a lokalną AD, jest również znane ma środowisku hybrydowym. Jeśli masz w środowisku hybrydowym, jest prawdopodobne, że masz już program Azure AD Connect wdrożone do synchronizowania danych tożsamości lokalnych do chmury. W ramach procesu synchronizacji Azure AD Connect synchronizuje informacje o domenie lokalnej do usługi Azure AD. Gdy użytkownik loguje się do usługi Azure AD dołączenia urządzenia w środowisku hybrydowym:

1. Usługa Azure AD wysyła nazwę domeny w środowisku lokalnym, użytkownik jest członkiem wstecz do urządzenia. 

2. Usługi (LSA) urzędu zabezpieczeń lokalnych Włącza uwierzytelnianie Kerberos na urządzeniu.

Podczas próby dostępu do zasobu w domenie lokalnej użytkownika, urządzenia:

1. Używa informacji o domenie do zlokalizowania kontrolera domeny (DC). 

2. Wysyła lokalnej domeny informacji i poświadczenia użytkownika znajduje się kontroler domeny można pobrać użytkownik uwierzytelniony.

3. Odbiera Kerberos [bilet uprawniający do przyznania BILETU](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) umożliwiający dostęp do zasobów dołączonych do usługi AD.

Wszystkie aplikacje, które są skonfigurowane dla **Windows Integrated authentication** bezproblemowo uzyskać logowanie Jednokrotne, gdy użytkownik próbuje uzyskać do nich dostęp.  

Windows Hello dla firm wymaga dodatkowej konfiguracji w celu włączenia funkcji logowania jednokrotnego w środowisku lokalnym, z urządzenia dołączonego do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [urządzeń przyłączonych do usługi Azure AD konfigurowanie do lokalnego logowania jednokrotnego na przy użyciu Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Efekty

Przy użyciu logowania jednokrotnego w usłudze Azure AD urządzenia połączonego z możesz: 

- Dostęp do ścieżki UNC na serwerze członkowskim AD

- Dostęp do serwera sieci web elementu członkowskiego AD skonfigurowany pod kątem zabezpieczeń zintegrowanych Windows 



Jeśli chcesz zarządzać lokalnej usługi AD z urządzenia Windows, zainstaluj [zdalnego serwera Administracja Tools for Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520).

Możesz użyć:

- Użytkownicy usługi Active Directory i komputery (narzędziu ADUC) przystawki administrowanie wszystkimi obiektami usługi AD. Jednakże należy określić domeny, który chcesz połączyć się ręcznie.

- Przystawki DHCP do administrowania serwerem DHCP przyłączonych do usługi AD. Jednak należy określić nazwę serwera DHCP lub adres.

 
## <a name="what-you-should-know"></a>Co należy wiedzieć

Może być konieczne dostosowanie swoje [filtrowania opartego na domenie](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) w Azure AD Connect, aby zapewnić synchronizację danych dotyczących wymaganych domen.

Aplikacje i zasoby, które są zależne od usługi Active Directory, uwierzytelnianie komputera nie działają, ponieważ urządzeń przyłączonych do usługi Azure AD nie ma obiektu komputera w AD. 

Plików nie można udostępniać innym użytkownikom Azure urządzenia przyłączone do usługi AD.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanie urządzeniami w usłudze Azure Active Directory?](overview.md) 
