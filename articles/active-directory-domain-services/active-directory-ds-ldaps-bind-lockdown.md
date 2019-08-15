---
title: Powiązywanie przy użyciu Secure LDAP (LDAPs) z Azure AD Domain Services domeną zarządzaną | Microsoft Docs
description: Powiąż z domeną zarządzaną Azure AD Domain Services przy użyciu protokołu Secure LDAP (LDAPs)
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: cbc5bee0f4cc59f59af6e3f57219279cd8fcb030
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988577"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Powiąż z domeną zarządzaną Azure AD Domain Services przy użyciu protokołu Secure LDAP (LDAPs)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Ukończ [zadanie 4 — Skonfiguruj system DNS, aby uzyskać dostęp do domeny zarządzanej za pośrednictwem Internetu](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Zadanie 5. Powiązywanie z domeną zarządzaną za pośrednictwem protokołu LDAP przy użyciu narzędzia LDP. exe
Aby powiązać i przeszukiwać przy użyciu protokołu LDAP, można użyć narzędzia LDP. exe, które jest zawarte w pakiecie narzędzi administracji zdalnej serwera.

Najpierw otwórz program LDP i Połącz się z domeną zarządzaną. Kliknij pozycję **połączenie** , a następnie kliknij pozycję **Połącz...** w menu. Określ nazwę domeny DNS domeny zarządzanej. Określ port, który ma być używany dla połączeń. W przypadku połączeń LDAP należy użyć portu 389. W przypadku połączeń LDAPs należy użyć portu 636. Kliknij przycisk **OK** , aby połączyć się z domeną zarządzaną.

Następnie powiąż z domeną zarządzaną. Kliknij pozycję **połączenie** , a następnie kliknij pozycję **bind...** w menu. Podaj poświadczenia konta użytkownika należącego do grupy "Administratorzy usługi AAD DC".

> [!IMPORTANT]
> Użytkownicy (i konta usług) nie mogą wykonać prostych powiązań LDAP, jeśli synchronizacja skrótów haseł NTLM została wyłączona w wystąpieniu Azure AD Domain Services.  Aby uzyskać więcej informacji na temat wyłączania synchronizacji skrótów haseł NTLM, przeczytaj artykuł [zabezpieczanie Azure AD Domain Services domeny zarządzanej](secure-your-domain.md).
>
>

Wybierz pozycję **Widok**, a następnie wybierz pozycję **drzewo** w menu. Pozostaw pole podstawowa nazwa wyróżniająca puste, a następnie kliknij przycisk OK. Przejdź do kontenera, który chcesz wyszukać, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję Wyszukaj.

> [!TIP]
> - Użytkownicy i grupy synchronizowane z usługą Azure AD są przechowywane w jednostce organizacyjnej **Użytkownicy AADDC** . Ścieżka wyszukiwania dla tej jednostki organizacyjnej wygląda ```OU=AADDC Users,DC=CONTOSO100,DC=COM```następująco.
> - Konta komputerów przyłączone do domeny zarządzanej są przechowywane w jednostce organizacyjnej **komputery AADDC** . Ścieżka wyszukiwania dla tej jednostki organizacyjnej wygląda ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```następująco.
>
>

Więcej informacji — [podstawowe](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter) informacje o zapytaniach LDAP


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Zadanie 6: Zablokuj bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu
> [!NOTE]
> Jeśli nie włączono dostępu LDAP do domeny zarządzanej za pośrednictwem Internetu, pomiń to zadanie konfiguracyjne.
>
>

Przed rozpoczęciem tego zadania wykonaj kroki opisane w [zadanie 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Włączenie dostępu do protokołu LDAP przez Internet do domeny zarządzanej powoduje utworzenie zagrożenia bezpieczeństwa. Domena zarządzana jest dostępna z Internetu na porcie używanym do bezpiecznego protokołu LDAP (czyli portu 636). Można ograniczyć dostęp do domeny zarządzanej do określonych znanych adresów IP. Utwórz sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) i skojarz ją z podsiecią, w której włączono obsługę Azure AD Domain Services.

Przykład sieciowej grupy zabezpieczeń w poniższej tabeli blokuje bezpieczny dostęp do protokołu LDAP za pośrednictwem Internetu. Reguły w sieciowej grupy zabezpieczeń zezwalają na przychodzące zabezpieczenia protokołu LDAP za pośrednictwem portu TCP 636 tylko z określonego zestawu adresów IP. Domyślna reguła "DenyAll" dotyczy całego ruchu przychodzącego z Internetu. Reguła sieciowej grupy zabezpieczeń zezwalająca na dostęp za pośrednictwem protokołu LDAP przez Internet z określonych adresów IP ma wyższy priorytet niż reguła DenyAll sieciowej grupy zabezpieczeń.

![Przykład sieciowej grupy zabezpieczeń do zabezpieczania dostępu do protokołu LDAP za pośrednictwem Internetu](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Więcej informacji** - [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).


## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](create-instance.md)
* [Zarządzanie domeną Azure AD Domain Services](manage-domain.md)
* [Podstawowe informacje o zapytaniach LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Zarządzaj zasady grupy dla Azure AD Domain Services](manage-group-policy.md)
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)
* [Tworzenie sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Następny krok
[Rozwiązywanie problemów z zabezpieczaniem protokołu LDAP w domenie zarządzanej](tshoot-ldaps.md)
