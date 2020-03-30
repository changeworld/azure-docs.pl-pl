---
title: Rozwiązywanie problemów z bezpiecznym programem LDAP w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z bezpiecznym programem LDAP (LDAPS) w domenie zarządzanej usług domenowych Usługi domenowe Active Directory azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132163"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Rozwiązywanie problemów z bezpieczną łącznością LDAP w domenie zarządzanej usług domenowych Usługi active directory platformy Azure

Aplikacje i usługi korzystające z protokołu LDAP (Lightweight Directory Access Protocol) do komunikowania się z usługami domenowymi Usługi Azure Active Directory (Azure AD DS) można [skonfigurować do używania bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md). Aby bezpieczny LDAP działał poprawnie, musi być otwarty odpowiedni certyfikat i wymagane porty sieciowe.

Ten artykuł ułatwia rozwiązywanie problemów z bezpiecznym dostępem LDAP w usługach Azure AD DS.

## <a name="common-connection-issues"></a>Typowe problemy z połączeniem

Jeśli masz problemy z nawiązaniem połączenia z domeną zarządzaną usługą Azure AD DS przy użyciu bezpiecznego protokołu LDAP, zapoznaj się z poniższymi krokami rozwiązywania problemów. Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z domeną zarządzana usługą Azure AD DS:

* Łańcuch wystawców bezpiecznego certyfikatu LDAP musi być zaufany na kliencie. Można dodać główny urząd certyfikacji (CA) do zaufanego magazynu certyfikatów głównych na kliencie, aby ustanowić zaufanie.
    * Upewnij się, że [eksportujesz i stosujesz certyfikat do komputerów klienckich][client-cert].
* Sprawdź, czy bezpieczny certyfikat LDAP dla domeny zarządzanej ma nazwę DNS w atrybutie *Podmiot* lub *Nazwy alternatywne podmiotu.*
    * Przejrzyj [wymagania bezpiecznego certyfikatu LDAP][certs-prereqs] i w razie potrzeby utwórz certyfikat zastępczy.
* Sprawdź, czy klient LDAP, taki jak *ldp.exe,* łączy się z bezpiecznym punktem końcowym LDAP przy użyciu nazwy DNS, a nie adresu IP.
    * Certyfikat zastosowany do domeny zarządzanej usługi Azure AD DS nie zawiera adresów IP usługi, tylko nazw DNS.
* Sprawdź nazwę DNS, z którymi łączy się klient LDAP. Musi rozpoznać publiczny adres IP dla bezpiecznego protokołu LDAP w domenie zarządzanej usług Azure AD DS.
    * Jeśli nazwa DNS jest rozpoznawana na wewnętrzny adres IP, zaktualizuj rekord DNS, aby rozpoznać go na zewnętrzny adres IP.
* W przypadku łączności zewnętrznej sieciowa grupa zabezpieczeń musi zawierać regułę umożliwiającą ruch do portu TCP 636 z Internetu.
    * Jeśli można połączyć się z domeną zarządzaną usługą Azure AD DS przy użyciu bezpiecznego protokołu LDAP z zasobów bezpośrednio połączonych z siecią wirtualną, ale nie z połączeniami zewnętrznymi, należy [utworzyć regułę sieciowej grupy zabezpieczeń, aby umożliwić bezpieczny ruch LDAP][ldaps-nsg].

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
