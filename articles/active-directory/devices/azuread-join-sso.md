---
title: Jak samouszeńców do zasobów lokalnych działa na urządzeniach przyłączonych do usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672718"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Jak samouszeńców do zasobów lokalnych działa na urządzeniach przyłączonych do usługi Azure AD

Prawdopodobnie nie jest zaskoczeniem, że urządzenie przyłączone do usługi Azure Active Directory (Azure AD) zapewnia środowisko logowania jednokrotnego w aplikacjach w chmurze dzierżawy. Jeśli w środowisku znajduje się lokalna usługa Active Directory (AD), można rozszerzyć środowisko samoużycieniu na tych urządzeniach.

W tym artykule wyjaśniono, jak to działa.

## <a name="prerequisites"></a>Wymagania wstępne

 Jeśli maszyny przyłączone do usługi Azure AD nie są połączone z siecią organizacji, wymagana jest infrastruktura sieci VPN lub inna infrastruktura sieciowa. Lokalne samouszeńców sieciowych wymaga komunikacji line-of-sight z lokalnymi kontrolerami domeny usług AD DS.

## <a name="how-it-works"></a>Jak to działa 

Ponieważ należy pamiętać tylko jedną nazwę użytkownika i hasło, samouszczenie zabezpieczeń upraszcza dostęp do zasobów i zwiększa bezpieczeństwo środowiska. Dzięki urządzeniu przyłączone do usługi Azure AD użytkownicy mają już środowisko rejestracji w chmurze w aplikacjach w chmurze w Twoim środowisku. Jeśli w środowisku jest usługa Azure AD i lokalna usługa AD, prawdopodobnie chcesz rozszerzyć zakres środowiska samouszeńców do lokalnych aplikacji biznesowych (LOB), udziałów plików i drukarek.

Urządzenia przyłączone do usługi Azure AD nie mają wiedzy na temat lokalnego środowiska usługi AD, ponieważ nie są do niego przyłączone. Można jednak podać dodatkowe informacje na temat lokalnej usługi AD do tych urządzeń za pomocą usługi Azure AD Connect.

Środowisko, które ma zarówno, usługi Azure AD i lokalnej usługi AD, jest również znany ma środowisko hybrydowe. Jeśli masz środowisko hybrydowe, jest prawdopodobne, że masz już wdrożony usługi Azure AD Connect do synchronizowania lokalnych informacji o tożsamości do chmury. W ramach procesu synchronizacji usługa Azure AD Connect synchronizuje lokalne informacje o użytkowniku z usługą Azure AD. Gdy użytkownik loguje się do urządzenia przyłączanego do usługi Azure AD w środowisku hybrydowym:

1. Usługa Azure AD wysyła nazwę domeny lokalnej, do użytkownik jest członkiem z powrotem do urządzenia.
1. Usługa lokalnego urzędu zabezpieczeń (LSA) umożliwia uwierzytelnianie Kerberos na urządzeniu.

Podczas próby dostępu do zasobu żądającego protokołu Kerberos w środowisku lokalnym użytkownika urządzenie:

1. Wysyła informacje o domenie lokalnej i poświadczenia użytkownika do znajdującego się kontrolera domeny, aby uzyskać uwierzytelnienie użytkownika.
1. Odbiera bilet przyznający bilet Kerberos [(TGT),](/windows/desktop/secauthn/ticket-granting-tickets) który jest używany do uzyskiwania dostępu do zasobów przyłączonych do usługi AD. Jeśli próba uzyskania TGT dla domeny połączenia usługi AAD nie powiedzie się (powiązany limit czasu lokalizatora kontrolera domeny może spowodować opóźnienie), próbowano podjąć próbę wprowadzenia Menedżera poświadczeń lub użytkownik może otrzymać wyskakujące okienko uwierzytelniania żądające poświadczeń dla zasobu docelowego.

Wszystkie aplikacje skonfigurowane do **uwierzytelniania zintegrowanego z systemem Windows** bezproblemowo uzyskują logowanie jednokrotne, gdy użytkownik próbuje uzyskać do nich dostęp.

Funkcja Windows Hello dla firm wymaga dodatkowej konfiguracji, aby włączyć lokalne przysyłanie uprawnień sytuowanych z urządzenia przyłączanego do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzeń przyłączonych do usługi Azure AD dla lokalnych urządzeń jednokrotnego logowania przy użyciu funkcji Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Efekty

Za pomocą usługi SSO na urządzeniu przyłączanym do usługi Azure AD można: 

- Uzyskiwanie dostępu do ścieżki UNC na serwerze członkowskim usługi AD
- Uzyskiwanie dostępu do serwera sieci Web członka usługi AD skonfigurowanego z dostępem do zabezpieczeń zintegrowanych z systemem Windows 

Jeśli chcesz zarządzać lokalną usługą AD z urządzenia z systemem Windows, zainstaluj [narzędzia administracji zdalnej serwera dla systemu Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Możesz użyć:

- Przystawka Użytkownicy i komputery usługi Active Directory (ADUC) do administrowania wszystkimi obiektami usługi AD. Należy jednak określić domenę, z którą chcesz połączyć się ręcznie.
- Przystawka DHCP do administrowania serwerem DHCP przyłączonym do usługi AD. Może jednak być konieczne określenie nazwy lub adresu serwera DHCP.
 
## <a name="what-you-should-know"></a>Co należy wiedzieć

Może być konieczne dostosowanie [filtrowania opartego na domenie](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) w usłudze Azure AD Connect, aby upewnić się, że dane dotyczące wymaganych domen są synchronizowane.

Aplikacje i zasoby zależne od uwierzytelniania maszynowego usługi Active Directory nie działają, ponieważ urządzenia przyłączone do usługi Azure AD nie mają obiektu komputera w usłudze AD. 

Nie można udostępniać plików innym użytkownikom na urządzeniu przyłączanym do usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie urządzeniami w usłudze Azure Active Directory?](overview.md) 
