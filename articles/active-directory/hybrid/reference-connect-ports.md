---
title: Tożsamość hybrydowa wymagane porty i protokoły — Azure | Dokumentacja firmy Microsoft
description: Ta strona jest stroną informacje techniczne dotyczące portów, które muszą być otwarte na potrzeby usługi Azure AD Connect
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
ms.date: 08/02/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48d2ef0de9ae59e63cd9957200c46c788e2d785f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387324"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Wymagane porty i protokoły dotyczące tożsamości hybrydowej
Ten dokument jest dokumentacja techniczna wymagane porty i protokoły dotyczące implementowania rozwiązania z tożsamością hybrydową. Użyj poniższej ilustracji i odnoszą się do odpowiedniej tabeli.

![Co to jest program Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - usługi Azure AD Connect i lokalnej usługi AD
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect i lokalnej usługi AD.

| Protokół | Porty | Opis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Wyszukiwanie DNS w lesie docelowym. |
| Kerberos |88 (TCP/UDP) |Uwierzytelnianie Kerberos z lasem usługi AD. |
| MS-RPC |135 (TCP/UDP) |Używane podczas wstępnej konfiguracji kreatora Azure AD Connect, podczas nawiązywania połączenia z lasem AD, a także podczas synchronizacji haseł. |
| LDAP |389 (TCP/UDP) |Używane do importowania danych z usługi AD. Dane są szyfrowane za pomocą protokołu Kerberos logowania & zamknięcia. |
| SMB | 445 (TCP/UDP) |Używane przez bezproblemowe logowanie Jednokrotne, aby utworzyć konto komputera w lesie usługi AD. |
| LDAP/SSL |636 (TCP/UDP) |Używane do importowania danych z usługi AD. Transfer danych jest podpisane i szyfrowane. Ta opcja jest używana tylko, jeśli używasz protokołu SSL. |
| RPC |Od 49152 do 65535 (Random wysokiej RPC Port)(TCP/UDP) |Używane podczas początkowej konfiguracji programu Azure AD Connect, podczas nawiązywania połączenia lasów usługi AD i synchronizacji haseł. Zobacz [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), i [KB224196](https://support.microsoft.com/kb/224196) Aby uzyskać więcej informacji. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - usługi Azure AD Connect a usługą Azure AD
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect a usługą Azure AD.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Używane do pobierania listy CRL (list odwołania certyfikatów) aby zweryfikować certyfikaty SSL. |
| HTTPS |443(TCP/UDP) |Używane do synchronizacji z usługą Azure AD. |

Aby uzyskać listę adresów URL i IP adresów, należy otworzyć w zaporze, patrz [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 — Usługa Azure AD Connect i AD FS federacyjnych serwerów/WAP
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect i serwery usługi AD FS federacyjne/WAP.  

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Używane do pobierania listy CRL (list odwołania certyfikatów) aby zweryfikować certyfikaty SSL. |
| HTTPS |443(TCP/UDP) |Używane do synchronizacji z usługą Azure AD. |
| WinRM |5985 |Odbiornik usługi WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - proxy aplikacji sieci Web i serwery federacyjne
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerami federacyjnymi i serwerami proxy aplikacji sieci Web.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Używany do uwierzytelniania. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - proxy aplikacji sieci Web i użytkownicy
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między użytkownikami i serwerów proxy aplikacji sieci Web.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Używany do uwierzytelniania urządzeń. |
| TCP |49443 (TCP) |Używany do uwierzytelniania certyfikatu. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b — uwierzytelnianie przekazywane za pomocą funkcji logowania jednokrotnego (SSO) i synchronizacją skrótów haseł za pomocą funkcji logowania jednokrotnego (SSO)
W poniższej tabeli opisano porty i protokoły, które są wymagane do komunikacji między program Azure AD Connect a usługą Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - uwierzytelniania przekazywanego przy użyciu logowania jednokrotnego
|Protokół|Numer portu|Opis
| --- | --- | ---
|HTTP|80|Zezwolenie na wychodzący ruch protokołu HTTP do weryfikacji zabezpieczeń, takich jak protokół SSL. Również wymagane dla funkcji automatycznej aktualizacji łącznik działa prawidłowo.
|HTTPS|443| Zezwolenie na wychodzący ruch protokołu HTTPS dla operacji takich jak włączanie i wyłączanie funkcji, rejestrowanie łączników, pobieranie aktualizacji łącznika i obsługuje wszystkie żądania logowania użytkowników.

Ponadto program Azure AD Connect potrzebuje mogli nawiązywać połączenia IP bezpośrednio z [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b — synchronizacja skrótów haseł przy użyciu logowania jednokrotnego

|Protokół|Numer portu|Opis
| --- | --- | ---
|HTTPS|443| Zezwolenie na zarejestrowanie logowania jednokrotnego (wymagane tylko dla procesu rejestracji logowania jednokrotnego).

Ponadto program Azure AD Connect potrzebuje mogli nawiązywać połączenia IP bezpośrednio z [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ponownie jest to tylko wymagane dla procesu rejestracji usługa rejestracji Jednokrotnej.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7b — program Azure AD Connect Health agent dla (AD FS/Sync) i Azure AD
W poniższych tabelach opisano punkty końcowe, portów i protokołów, które są wymagane do komunikacji między agentami programu Azure AD Connect Health i usługa Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a — porty i protokoły dla programu Azure AD Connect Health agent dla (AD FS/Sync) i Azure AD
Ta tabela zawiera opis następujących portów wychodzących i protokołów, które są wymagane do komunikacji między agentami programu Azure AD Connect Health i usługa Azure AD.  

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Wychodzący |
| Azure Service Bus |5671 (TCP/UDP) |Wychodzący |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b — punkty końcowe dla usługi Azure AD Connect Health agent dla (AD FS/Sync) i Azure AD
Aby uzyskać listę punktów końcowych, zobacz [w sekcji wymagań dla agenta programu Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).

