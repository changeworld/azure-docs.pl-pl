---
title: Powiązań z użyciem bezpiecznego protokołu LDAP (LDAPS) do domeny zarządzanej usług domenowych Azure AD | Dokumentacja firmy Microsoft
description: Powiąż z domeny zarządzanej usług domenowych Azure AD przy użyciu bezpiecznego protokołu LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 62776408675acba1ee8a57252c794e5a1c840155
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417043"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Powiąż z domeny zarządzanej usług domenowych Azure AD przy użyciu bezpiecznego protokołu LDAP (LDAPS)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Pełne [zadanie 4 — Konfigurowanie serwera DNS, dostęp do domeny zarządzanej z Internetu](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Zadanie 5. Powiąż z domeny zarządzanej za pośrednictwem protokołu LDAP, przy użyciu LDP.exe
Można użyć narzędzia LDP.exe znajduje się w pakiecie narzędzia administracji zdalnej serwera w celu powiązania i wyszukiwanie LDAP.

Najpierw należy otworzyć przystawkę LDP i nawiązać połączenie z domeną zarządzaną. Kliknij przycisk **połączenia** i kliknij przycisk **Connect...**  w menu. Określ nazwę domeny DNS z domeny zarządzanej. Określ port używany dla połączenia. W przypadku połączeń LDAP Użyj portu 389. W przypadku połączeń protokołu LDAPS korzystać z portu 636. Kliknij przycisk **OK** przycisk, aby nawiązać połączenie z domeną zarządzaną.

Następnie Powiąż do domeny zarządzanej. Kliknij przycisk **połączenia** i kliknij przycisk **powiązać...**  w menu. Podaj poświadczenia konta użytkownika należącego do grupy "Administratorzy usługi AAD DC".

Wybierz **widoku**, a następnie wybierz pozycję **drzewa** w menu. Bazowa nazwa Wyróżniająca pole jest puste, a następnie kliknij przycisk OK. Przejdź do kontenera, który ma być wyszukiwania, kliknij prawym przyciskiem myszy kontener, a następnie wybierz wyszukiwania.

> [!TIP]
> - Użytkownicy i grupy synchronizowane z usługi Azure AD są przechowywane w **użytkownicy usługi AAD DC** jednostki organizacyjnej. Ścieżka wyszukiwania dla tej jednostki organizacyjnej wygląda ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Konta komputerów dla komputery przyłączone do domeny zarządzanej, są przechowywane w **komputery usługi AAD DC** jednostki organizacyjnej. Ścieżka wyszukiwania dla tej jednostki organizacyjnej wygląda ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Więcej informacji — [podstawy kwerendy LDAP](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Zadanie 6. Zablokować dostęp do Twojej domeny zarządzanej secure LDAP przez internet
> [!NOTE]
> Jeśli nie włączono dostępu protokołu LDAPS do domeny zarządzanej za pośrednictwem Internetu, należy pominąć to zadanie konfiguracji.
>
>

Przed przystąpieniem do wykonywania tego zadania, pełną kroki opisane w [zadanie 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Po włączeniu protokołu LDAPS dostępu przez internet do domeny zarządzanej, tworzy zagrożenie bezpieczeństwa. Domena zarządzana jest dostępny z Internetu na porcie używanym do bezpiecznego protokołu LDAP (czyli portu 636). Można ograniczyć dostęp do domeny zarządzanej do konkretnych znanych adresów IP. Utwórz sieciową grupę zabezpieczeń (NSG) i skojarzyć ją z podsiecią, w której włączono usługi domenowe Azure AD.

Przykład sieciowej grupy zabezpieczeń w poniższej tabeli zablokuje dostęp protokołu secure LDAP przez internet. Reguły w sieciowej grupie zabezpieczeń zezwalają na przychodzący dostęp protokołu secure LDAP za pośrednictwem portu TCP 636 tylko z określonego zestawu adresów IP. Reguła "DenyAll" domyślna jest stosowana do całego ruchu przychodzącego z Internetu. Reguły sieciowej grupy zabezpieczeń, aby zezwolić na dostęp protokołu LDAPS za pośrednictwem Internetu z określonych adresów IP ma wyższy priorytet niż reguły sieciowej grupy zabezpieczeń DenyAll.

![Przykładowe sieciowej grupy zabezpieczeń z bezpiecznego protokołu LDAPS dostępu przez internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Więcej informacji na** - [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).


## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](active-directory-ds-getting-started.md)
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Podstawy kwerendy LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administrowanie zasadami grupy w domenie zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-administer-group-policy.md)
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)
* [Utwórz sieciową grupę zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Następny krok
[Rozwiązywanie problemów z bezpiecznego protokołu LDAP w domenie zarządzanej](active-directory-ds-ldaps-troubleshoot.md)
