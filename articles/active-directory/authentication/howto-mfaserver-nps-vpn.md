---
title: Serwer usługi Azure MFA i sieci VPN innych firm — usługa Azure Active Directory
description: Przewodniki konfiguracji krok po kroku dla usługi Azure MFA Server do integracji z cisco, Citrix i Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652844"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Zaawansowane scenariusze z usługą Azure MFA Server i rozwiązaniami sieci VPN innych firm

Usługa Azure Multi-Factor Authentication Server (Azure MFA Server) może służyć do bezproblemowego łączenia się z różnymi rozwiązaniami sieci VPN innych firm. Ten artykuł koncentruje się&reg; na urządzeniu Cisco ASA VPN, urządzeniu Citrix NetScaler SSL VPN i urządzeniu Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN. Stworzyliśmy przewodniki konfiguracji, aby rozwiązać te trzy typowe urządzenia. Usługa Azure MFA Server może również integrować się z większością innych systemów korzystających z uwierzytelniania radius, LDAP, IIS lub uwierzytelniania opartego na oświadczeniach w usługach AD FS. Więcej informacji można znaleźć w [konfiguracjach usługi Azure MFA Server](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Urządzenie sieci VPN Cisco ASA i serwer usługi Azure MFA
Usługa Azure MFA Server integruje się z urządzeniem Sieci VPN Cisco&reg; ASA, aby zapewnić dodatkowe zabezpieczenia dla logowania sieci VPN cisco AnyConnect&reg; i dostępu do portalu.  Można użyć protokołu LDAP lub RADIUS.  Wybierz jedną z następujących opcji, aby pobrać szczegółowe przewodniki konfiguracji krok po kroku.

| Przewodnik konfiguracji | Opis |
| --- | --- |
| [Cisco ASA z konfiguracją usługi Anyconnect VPN i usługi Azure MFA dla usługi LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Zintegruj urządzenie Cisco ASA VPN z usługą Azure MFA przy użyciu protokołu LDAP |
| [Cisco ASA z konfiguracją usługi Anyconnect VPN i usługi Azure MFA dla usługi RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Zintegruj urządzenie Cisco ASA VPN z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN i serwer usługi Azure MFA
Usługa Azure MFA Server integruje się z urządzeniem sieci VPN Citrix NetScaler SSL, aby zapewnić dodatkowe zabezpieczenia dla logowania sieci VPN citrix NetScaler SSL i dostępu do portalu.  Można użyć protokołu LDAP lub RADIUS.  Wybierz jedną z następujących opcji, aby pobrać szczegółowe przewodniki konfiguracji krok po kroku.

| Przewodnik konfiguracji | Opis |
| --- | --- |
| [Citrix NetScaler SSL VPN i konfiguracja usługi Azure MFA dla usługi LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Zintegruj swoją sieć VPN Citrix NetScaler SSL z urządzeniem Usługi Azure MFA przy użyciu protokołu LDAP |
| [Konfiguracja sieci VPN SSL i usługi Azure MFA firmy Citrix dla usługi RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Zintegruj urządzenie Citrix NetScaler SSL VPN z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Bezpieczne urządzenie SSL VPN i serwer usługi Azure MFA
Usługa Azure MFA Server integruje się z urządzeniem Juniper/Pulse Secure SSL VPN, aby zapewnić dodatkowe zabezpieczenia dla logowań sieci VPN Juniper/Pulse Secure SSL i dostępu do portalu.  Można użyć protokołu LDAP lub RADIUS.  Wybierz jedną z następujących opcji, aby pobrać szczegółowe przewodniki konfiguracji krok po kroku.

| Przewodnik konfiguracji | Opis |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN i konfiguracja usługi Azure MFA dla LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Zintegruj bezpieczną sieć VPN SSL Juniper/Pulse z urządzeniem Usługi Azure MFA przy użyciu protokołu LDAP |
| [Juniper/Pulse Secure SSL VPN i konfiguracja usługi Azure MFA dla usługi RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Zintegruj swoje urządzenie Juniper/Pulse Secure SSL VPN z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="next-steps"></a>Następne kroki

- [Powiększ istniejącą infrastrukturę uwierzytelniania o rozszerzenie serwera NPS dla uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-nps-extension.md)

- [Konfigurowanie ustawień uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-mfasettings.md)
