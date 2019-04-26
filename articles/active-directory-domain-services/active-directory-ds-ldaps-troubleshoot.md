---
title: Rozwiązywanie problemów z bezpiecznego protokołu LDAP (LDAPS) w usługach domenowych Azure AD | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 97a2ee23435d0b29565bc3bf1dcb426e9e83fd31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416759"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Rozwiązywanie problemów z bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD

## <a name="connection-issues"></a>Problemy z połączeniem
Jeśli masz problemy z połączeniem do domeny zarządzanej przy użyciu protokołu secure LDAP:

* Łańcuch wystawcy certyfikat secure LDAP, musi być zaufany na kliencie. Można dodać główny urząd certyfikacji do magazynu zaufanych certyfikatów głównych na kliencie, aby ustanowić relację zaufania.
* Upewnij się, że klient LDAP (na przykład ldp.exe) łączy się z bezpiecznego punktu końcowego protokołu LDAP, przy użyciu nazwy DNS, a nie adresu IP.
* Sprawdź nazwę DNS, który łączy się z klienta LDAP. Należy rozwiązać publiczny adres IP dla protokołu secure LDAP w domenie zarządzanej.
* Sprawdź, czy certyfikat secure LDAP dla domeny zarządzanej ma nazwę DNS, w temacie lub atrybut nazwy alternatywnej podmiotu.
* Ustawienia sieciowej grupy zabezpieczeń dla sieci wirtualnej muszą zezwalać na ruch na porcie 636 z Internetu. Ten krok ma zastosowanie tylko wtedy, gdy włączono dostęp protokołu secure LDAP przez internet.


## <a name="need-help"></a>Potrzebujesz pomocy?
Jeśli nadal masz problemy z połączeniem do domeny zarządzanej przy użyciu protokołu secure LDAP, [skontaktuj się z zespołem produktu](active-directory-ds-contact-us.md) Aby uzyskać pomoc. Podaj następujące informacje ułatwiające skuteczniejsze diagnozowanie problemu:
* Zrzut ekranu przedstawiający ldp.exe nawiązywania połączenia i kończy się niepowodzeniem.
* Identyfikator dzierżawy usługi Azure AD, a nazwa domeny DNS z domeny zarządzanej.
* Nazwa użytkownika dokładnie, które próbujesz powiązać jako.


## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](active-directory-ds-getting-started.md)
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Podstawy kwerendy LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administrowanie zasadami grupy w domenie zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-administer-group-policy.md)
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)
* [Utwórz sieciową grupę zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)
