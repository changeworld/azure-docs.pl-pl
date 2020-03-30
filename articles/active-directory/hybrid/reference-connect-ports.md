---
title: Porty i protokoły wymagane przez tożsamość hybrydową — Azure | Dokumenty firmy Microsoft
description: Ta strona jest techniczną stroną referencyjną dla portów, które muszą być otwarte dla usługi Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331089"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Wymagane porty i protokoły dotyczące tożsamości hybrydowej
Poniższy dokument jest odniesienie techniczne na wymagane porty i protokoły do implementowania rozwiązania tożsamości hybrydowej. Użyj poniższej ilustracji i zapoznaj się z odpowiednią tabelą.

![Co to jest program Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 — usługa Azure AD Connect i lokalna usługa AD
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect a lokalną usługą AD.

| Protocol (Protokół) | Porty | Opis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Wyszukiwania DNS w lesie docelowym. |
| Kerberos |88 (TCP/UDP) |Uwierzytelnianie Kerberos w lesie AD. |
| Ms-RPC |135 (TCP) |Używane podczas początkowej konfiguracji kreatora usługi Azure AD Connect, gdy wiąże się z lasem usługi AD, a także podczas synchronizacji haseł. |
| LDAP |389 (TCP/UDP) |Służy do importowania danych z usługi AD. Dane są szyfrowane za pomocą protokołu Kerberos Sign & Seal. |
| SMB | 445 (TCP) |Używane przez bezproblemowe logowanie jednokrotne do tworzenia konta komputera w lesie AD. |
| LDAP/SSL |636 (TCP/UDP) |Służy do importowania danych z usługi AD. Transfer danych jest podpisany i zaszyfrowany. Używane tylko w przypadku korzystania z protokołu TLS. |
| Zdalne wywołanie procedury |49152- 65535 (Losowy port RPC o wysokiej wysokości) (TCP) |Używane podczas początkowej konfiguracji usługi Azure AD Connect, gdy wiąże się z lasami usługi AD i podczas synchronizacji haseł. Zobacz [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)i [KB224196,](https://support.microsoft.com/kb/224196) aby uzyskać więcej informacji. |
|WinRM  | 5985 (TCP) |Używane tylko w przypadku instalowania usług AD FS z gMSA przez Kreatora usługi Azure AD Connect|
|Usługi sieci Web usług SIECI WEB usług AD DS | 9389 (TCP) |Używane tylko w przypadku instalowania usług AD FS z gMSA przez Kreatora usługi Azure AD Connect |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 — usługa Azure AD Connect i usługa Azure AD
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect a usługą Azure AD.

| Protocol (Protokół) | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP) |Służy do pobierania list CRL (list odwołania certyfikatów) w celu weryfikacji certyfikatów TLS/SSL. |
| HTTPS |443(TCP) |Służy do synchronizowania z usługą Azure AD. |

Aby uzyskać listę adresów URL i adresów IP, które chcesz otworzyć w zaporze, zobacz [Adresy URL i zakresy adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) oraz [rozwiązywanie problemów z łącznością usługi Azure AD Connect](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 — serwery federacyjne usługi Azure AD Connect i usługi AD FS/WAP
W tej tabeli opisano porty i protokoły wymagane do komunikacji między serwerem usługi Azure AD Connect a serwerami federacyjnymi/WAP usług AD FS.  

| Protocol (Protokół) | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP) |Służy do pobierania list CRL (list odwołania certyfikatów) w celu weryfikacji certyfikatów TLS/SSL. |
| HTTPS |443(TCP) |Służy do synchronizowania z usługą Azure AD. |
| WinRM |5985 |Odbiornik WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 — serwery WAP i federacyjne
W tej tabeli opisano porty i protokoły wymagane do komunikacji między serwerami Federacyjnymi a serwerami WAP.

| Protocol (Protokół) | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP) |Służy do uwierzytelniania. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP i użytkownicy
W tej tabeli opisano porty i protokoły wymagane do komunikacji między użytkownikami a serwerami WAP.

| Protocol (Protokół) | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP) |Służy do uwierzytelniania urządzeń. |
| TCP |49443 (TCP) |Służy do uwierzytelniania certyfikatów. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b — uwierzytelnianie przekazywane za pomocą logowania jednokrotnego (logowania jednokrotnego) i synchronizacji hash hasła z logiem jednokrotnym (logowanie jednokrotne)
W poniższych tabelach opisano porty i protokoły, które są wymagane do komunikacji między usługą Azure AD Connect i usługą Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a — uwierzytelnianie przekazywane za pomocą uwierzytelniania sytłego
|Protocol (Protokół)|Numer portu|Opis
| --- | --- | ---
|HTTP|80|Włącz wychodzący ruch HTTP w celu sprawdzania poprawności zabezpieczeń, takiego jak SSL. Potrzebne jest również, aby funkcja automatycznej aktualizacji złącza działała poprawnie.
|HTTPS|443| Włącz wychodzący ruch HTTPS dla operacji, takich jak włączanie i wyłączanie funkcji, rejestrowanie łączników, pobieranie aktualizacji łączników i obsługa wszystkich żądań logowania użytkownika.

Ponadto usługa Azure AD Connect musi mieć możliwość nawiązywania bezpośrednich połączeń IP z [zakresami adresów IP centrum danych platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - Synchronizacja hash hasła z logiem typu SSO

|Protocol (Protokół)|Numer portu|Opis
| --- | --- | ---
|HTTPS|443| Włącz rejestrację rejestracji jedno i przesuwu (wymagane tylko dla procesu rejestracji rejestracji jedno i zgłoszenie).

Ponadto usługa Azure AD Connect musi mieć możliwość nawiązywania bezpośrednich połączeń IP z [zakresami adresów IP centrum danych platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Ponownie jest to wymagane tylko w procesie rejestracji jedno i zarejestrowania.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7b — agent kondycji usługi Azure AD Connect dla (AD FS/Sync) i usługi Azure AD
W poniższych tabelach opisano punkty końcowe, porty i protokoły, które są wymagane do komunikacji między agentami usługi Azure AD Connect Health a usługą Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a — porty i protokoły dla agenta kondycji usługi Azure AD Connect dla (AD FS/Sync) i usługi Azure AD
W tej tabeli opisano następujące porty i protokoły wychodzące, które są wymagane do komunikacji między agentami usługi Azure AD Connect Health a usługą Azure AD.  

| Protocol (Protokół) | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP) |Wychodzący |
| Azure Service Bus |5671 (TCP) |Wychodzący |

Port usługi Azure Service Bus 5671 nie jest już wymagany dla najnowszej wersji agenta. Najnowsza wersja agenta usługi Azure AD Connect Health wymagała tylko portu 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b — punkty końcowe dla agenta kondycji usługi Azure AD Connect dla (AD FS/Sync) i usługi Azure AD
Aby uzyskać listę punktów końcowych, zobacz [sekcję Wymagania dla agenta kondycji usługi Azure AD Connect](how-to-connect-health-agent-install.md#requirements).

