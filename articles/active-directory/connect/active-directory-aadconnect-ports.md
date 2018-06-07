---
title: Tożsamość hybrydowa wymagane porty i protokoły - Azure | Dokumentacja firmy Microsoft
description: Ta strona jest stroną informacje techniczne dotyczące portów, które muszą być otwarte dla usługi Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 317c94abdf14d3d88e07e32ab16769bd1f641438
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591300"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Wymagane porty i protokoły dotyczące tożsamości hybrydowej
Ten dokument jest techniczne wymagane porty i protokoły dotyczące implementowania rozwiązania z tożsamością hybrydową. Użyj poniższej ilustracji i odwoływać się do tej tabeli.

![Co to jest program Azure AD Connect](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - usługi Azure AD Connect i lokalnej usłudze AD
Ta tabela opisuje porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect i lokalnej usłudze AD.

| Protokół | Porty | Opis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Wyszukiwania DNS lasu docelowego. |
| Kerberos |88 (TCP/UDP) |Uwierzytelnianie Kerberos do lasu usługi AD. |
| MS-RPC |135 (TCP/UDP) |Używane podczas wstępnej konfiguracji Kreator Azure AD Connect, gdy jest on powiązany z lasem usługi AD, a także podczas synchronizacji haseł. |
| LDAP |389 (TCP/UDP) |Używany do importowania danych z usługi Active Directory. Dane są szyfrowane z protokołu Kerberos i zamknięcia. |
| RPC | 445 (TCP/UDP) |Używany przez bezproblemowe logowania jednokrotnego do tworzenia konta komputera w lesie usługi AD. |
| LDAP/SSL |636 (TCP/UDP) |Używany do importowania danych z usługi Active Directory. Transfer danych jest podpisane i zaszyfrowane. Używane, jeśli jest używany protokół SSL. |
| RPC |Od 49152 do 65535 (losowe wysokiej RPC Port)(TCP/UDP) |Używane podczas konfiguracji początkowej programu Azure AD Connect, gdy jest on powiązany z lasów usługi AD i podczas synchronizacji haseł. Zobacz [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), i [KB224196](https://support.microsoft.com/kb/224196) Aby uzyskać więcej informacji. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - usługi Azure AD Connect i Azure AD
Ta tabela opisuje porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect i usługi Azure AD.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Używany do pobierania listy odwołania certyfikatów (listy odwołania) do weryfikowania certyfikatów SSL. |
| HTTPS |443(TCP/UDP) |Używane do synchronizacji z usługą Azure AD. |

Aby uzyskać listę adresów URL i IP adresów, należy otworzyć w zaporze, zobacz [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 - usługi Azure AD Connect i serwery federacyjne FS AD/WAP
Ta tabela opisuje porty i protokoły, które są wymagane do komunikacji między serwerem usługi Azure AD Connect i serwery usługi AD FS Federation/protokół WAP usług.  

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Używany do pobierania listy odwołania certyfikatów (listy odwołania) do weryfikowania certyfikatów SSL. |
| HTTPS |443(TCP/UDP) |Używane do synchronizacji z usługą Azure AD. |
| WinRM |5985 |Odbiornik usługi WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - WAP i serwerów federacyjnych
Ta tabela opisuje porty i protokoły, które są wymagane do komunikacji między serwerami federacyjnymi a serwerami proxy.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Używany do uwierzytelniania. |

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP i użytkowników
Ta tabela opisuje porty i protokoły, które są wymagane do komunikacji między użytkownikami i serwerów proxy.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Używany do uwierzytelniania urządzeń. |
| TCP |49443 (TCP) |Używany do uwierzytelniania certyfikatów. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & 6b - uwierzytelniania przekazywanego z logowania jednokrotnego (SSO) i synchronizacją skrótów haseł z logowania jednokrotnego (SSO)
Poniższych tabelach opisano porty i protokoły, które są wymagane do komunikacji między Azure AD Connect i Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a - uwierzytelniania przekazywanego z logowania jednokrotnego
|Protokół|Numer portu|Opis
| --- | --- | ---
|HTTP|80|Włącz wychodzącego ruchu HTTP na potrzeby weryfikacji zabezpieczeń na przykład protokołu SSL. Również wymagane dla funkcji automatycznej aktualizacji łącznik działa prawidłowo.
|HTTPS|443| Włącz ruch wychodzący protokołu HTTPS dla operacji, takich jak włączanie i wyłączanie funkcji, rejestrowanie łączniki pobierania aktualizacji łącznika i obsługi wszystkich żądań logowania użytkownika.

Ponadto, Azure AD Connect potrzebuje mogli nawiązywać połączenia IP bezpośrednio z [zakresy IP centrum danych Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6b - synchronizacji skrótów haseł z logowania jednokrotnego

|Protokół|Numer portu|Opis
| --- | --- | ---
|HTTPS|443| Włącz rejestrację logowania jednokrotnego (wymagane tylko dla procesu rejestracji logowania jednokrotnego).

Ponadto, Azure AD Connect potrzebuje mogli nawiązywać połączenia IP bezpośrednio z [zakresy IP centrum danych Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Ponownie, jest to tylko wymagane dla procesu rejestracji logowania jednokrotnego.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7b — usługi Azure AD i Azure AD Connect Health agent dla (AD FS/Sync)
W poniższych tabelach opisano punktów końcowych, porty i protokoły, które są wymagane do komunikacji między agentami Azure AD Connect Health i usługi Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a - porty i protokoły dla usługi Azure AD Connect Health agent dla (AD FS/Sync) i Azure AD
W tej tabeli opisano następujące wychodzącego porty i protokoły, które są wymagane do komunikacji między agentami Azure AD Connect Health i usługi Azure AD.  

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Wychodzący |
| Azure Service Bus |5671 (TCP/UDP) |Wychodzący |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b — punkty końcowe dla usługi Azure AD Connect Health agent dla (AD FS/Sync) i Azure AD
Aby uzyskać listę punktów końcowych, zobacz [w sekcji wymagań dla agenta programu Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).

