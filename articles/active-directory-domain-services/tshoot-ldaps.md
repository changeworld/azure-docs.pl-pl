---
title: Rozwiązywanie problemów z Secure LDAP (LDAPs) w Azure AD Domain Services | Microsoft Docs
description: Rozwiązywanie problemów z Secure LDAP (LDAPs) dla domeny zarządzanej Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617044"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Rozwiązywanie problemów z Secure LDAP (LDAPs) dla domeny zarządzanej Azure AD Domain Services

## <a name="connection-issues"></a>Problemy z połączeniem
Jeśli masz problemy z nawiązywaniem połączenia z domeną zarządzaną przy użyciu protokołu Secure LDAP:

* Łańcuch wystawcy certyfikatu bezpiecznego protokołu LDAP musi być zaufany na kliencie. Możesz dodać główny urząd certyfikacji do magazynu zaufanych certyfikatów głównych na kliencie, aby ustanowić relację zaufania.
* Sprawdź, czy klient LDAP (na przykład Ldp. exe) nawiązuje połączenie z punktem końcowym bezpiecznego protokołu LDAP przy użyciu nazwy DNS, a nie adresu IP.
* Sprawdź nazwę DNS, z którą łączy się klient LDAP. Musi on zostać rozpoznany jako publiczny adres IP dla bezpiecznego protokołu LDAP w domenie zarządzanej.
* Sprawdź, czy certyfikat bezpiecznego protokołu LDAP dla domeny zarządzanej ma nazwę DNS w temacie lub alternatywna nazwa podmiotu.
* Ustawienia sieciowej grupy zabezpieczeń dla sieci wirtualnej muszą zezwalać na ruch do portu 636 z Internetu. Ten krok ma zastosowanie tylko wtedy, gdy włączono bezpieczny dostęp do protokołu LDAP za pośrednictwem Internetu.


## <a name="need-help"></a>Potrzebujesz pomocy?
Jeśli nadal występują problemy z nawiązywaniem połączenia z domeną zarządzaną przy użyciu protokołu Secure LDAP, [skontaktuj się z zespołem produktu](contact-us.md) w celu uzyskania pomocy. Podaj następujące informacje, aby ułatwić zdiagnozowanie problemu:
* Zrzut ekranu programu Ldp. exe, który sprawia połączenie i kończy się niepowodzeniem.
* Identyfikator dzierżawy usługi Azure AD i nazwa domeny DNS domeny zarządzanej.
* Dokładna nazwa użytkownika, z którą próbujesz powiązać.


## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](tutorial-create-instance.md)
* [Zarządzanie domeną Azure AD Domain Services](tutorial-create-management-vm.md)
* [Podstawowe informacje o zapytaniach LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Zarządzaj zasady grupy dla Azure AD Domain Services](manage-group-policy.md)
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)
* [Tworzenie sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)
