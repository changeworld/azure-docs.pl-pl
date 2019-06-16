---
title: Zaawansowane scenariusze obejmujące usługę Azure MFA i sieci VPN innych firm — usługi Azure Active Directory
description: Przewodniki krok po kroku konfiguracji dla usługi Azure MFA zintegrować z usługą firmy Cisco, Citrix i Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad2b3075ae9d5ccd7e32f039fbbbc8583cde73c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055961"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Zaawansowane scenariusze obejmujące usługę Azure Multi-Factor Authentication i rozwiązania VPN innych firm

Usługa Azure Multi-Factor Authentication może służyć do bezproblemowe łączenie różnych rozwiązań sieci VPN innych firm. Ten artykuł koncentruje się na urządzenie sieci VPN oprogramowania Cisco® ASA, urządzenie sieci VPN protokołu SSL NetScaler firmy Citrix i Juniper sieci bezpieczny dostęp/Pulse Secure połączyć bezpiecznego protokołu SSL urządzenie sieci VPN. Utworzyliśmy przewodniki konfiguracji, aby rozwiązać te trzy typowe urządzenia. Serwer Multi-Factor Authentication można również zintegrować z najbardziej innymi systemami, które używają usługi RADIUS, LDAP, usług IIS lub uwierzytelniania opartego na oświadczeniach z usługami AD FS. Można znaleźć więcej szczegółów w [konfiguracji serwera usługi MFA](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Począwszy od 1 lipca 2019 firma Microsoft będzie oferować już serwer MFA w przypadku nowych wdrożeń. Nowi klienci, którzy chcesz wymagać uwierzytelniania wieloskładnikowego od użytkowników należy używać oparte na chmurze usługi Azure Multi-Factor Authentication. Istniejący klienci, którzy aktywowali usługę MFA Server przed 1 lipca będzie można pobrać najnowszą wersję, a przyszłe aktualizacje i Generuj poświadczenia aktywacji w zwykły sposób.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Urządzenie sieci VPN ASA Cisco i usługi Azure Multi-Factor Authentication
Uwierzytelnianie wieloskładnikowe systemu Azure integruje się z urządzeniem sieci VPN oprogramowania Cisco® ASA w celu zapewnienia dodatkowych zabezpieczeń sieci VPN oprogramowania Cisco AnyConnect® logowania i dostępu do portalu.  Możesz użyć protokołu LDAP lub serwera RADIUS.  Wybierz jedną z następujących czynności, aby pobrać przewodniki szczegółowe instrukcje krok po kroku konfiguracji.

| Przewodnik po konfiguracji | Opis |
| --- | --- |
| [Cisco ASA z Anyconnect sieci VPN i usługi Azure MFA konfiguracji LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrowanie urządzenie sieci VPN oprogramowania Cisco ASA za pomocą usługi Azure MFA przy użyciu protokołu LDAP |
| [Cisco ASA za pomocą oprogramowania Anyconnect sieci VPN i platformą Azure konfiguracji uwierzytelniania Wieloskładnikowego dla serwera RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrowanie urządzenie sieci VPN oprogramowania Cisco ASA za pomocą usługi Azure MFA przy użyciu usługi RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL sieci VPN i usługi Azure Multi-Factor Authentication
Uwierzytelnianie wieloskładnikowe systemu Azure integruje się z urządzeniem Citrix NetScaler SSL sieci VPN w celu zapewnienia dodatkowych zabezpieczeń logowania Citrix NetScaler SSL sieci VPN i dostępu do portalu.  Możesz użyć protokołu LDAP lub serwera RADIUS.  Wybierz jedną z następujących czynności, aby pobrać przewodniki szczegółowe instrukcje krok po kroku konfiguracji.

| Przewodnik po konfiguracji | Opis |
| --- | --- |
| [Citrix NetScaler SSL sieci VPN i usługi Azure MFA konfiguracji LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integracja sieci VPN Citrix NetScaler protokołu SSL przy użyciu urządzenia z usługi Azure MFA przy użyciu protokołu LDAP |
| [Citrix NetScaler SSL sieci VPN i Azure MFA konfiguracji usługi RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrowanie urządzenie Citrix NetScaler SSL sieci VPN z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Urządzenie sieci VPN protokołu SSL juniper/Pulse Secure i usługi Azure Multi-Factor Authentication
Uwierzytelnianie wieloskładnikowe systemu Azure integruje się z urządzeniem sieci VPN protokołu SSL Juniper/Pulse Secure w celu zapewnienia dodatkowych zabezpieczeń logowania Juniper/Pulse Secure SSL VPN i dostępu do portalu.  Możesz użyć protokołu LDAP lub serwera RADIUS.  Wybierz jedną z następujących czynności, aby pobrać przewodniki szczegółowe instrukcje krok po kroku konfiguracji.

| Przewodnik po konfiguracji | Opis |
| --- | --- |
| [Juniper/impulsu bezpiecznego SSL sieci VPN i Azure MFA konfiguracji LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integracja usługi Juniper/Pulse Secure SSL sieci VPN z urządzenia usługi Azure MFA przy użyciu protokołu LDAP |
| [Juniper/impulsu bezpiecznego SSL sieci VPN i platformą Azure konfiguracji uwierzytelniania Wieloskładnikowego dla serwera RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrowanie urządzenia Juniper/Pulse Secure SSL VPN z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="next-steps"></a>Kolejne kroki

- [Rozszerzaj istniejącej infrastruktury uwierzytelniania za pomocą rozszerzenia serwera NPS dla usługi Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurowanie ustawień usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
