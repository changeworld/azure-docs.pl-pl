---
title: Konfigurowanie serwera DNS do dostępu do domeny zarządzanej za pośrednictwem Internetu przy użyciu protokołu LDAPS | Dokumentacja firmy Microsoft
description: Konfigurowanie serwera DNS do dostępu do domeny zarządzanej usług domenowych Azure AD za pośrednictwem Internetu przy użyciu protokołu LDAPS
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 205e41727d9e6e360c34e7ac2a99b9ceec74361b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846170"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Konfigurowanie systemu DNS, można uzyskać dostępu do domeny zarządzanej usług domenowych Azure AD przy użyciu bezpiecznego protokołu LDAP (LDAPS)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Pełne [zadanie 3: Włączanie protokołu secure LDAP dla domeny zarządzanej przy użyciu witryny Azure portal](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Zadanie 4. Skonfiguruj serwer DNS, aby uzyskać dostęp do domeny zarządzanej z Internetu
> [!TIP]
> **Zadaniem opcjonalnym** — Jeśli nie planujesz uzyskiwać dostęp do domeny zarządzanej przy użyciu protokołu LDAPS za pośrednictwem Internetu, pomiń to zadanie konfiguracji.
>
>

Przed przystąpieniem do wykonywania tego zadania, pełną kroki opisane w [zadanie 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Po włączeniu dostęp protokołu secure LDAP przez internet, należy zaktualizować DNS tak, aby komputery klienckie można znaleźć tej domeny zarządzanej. Zewnętrzny adres IP zostanie wyświetlony na **właściwości** karcie **IP adresów dla protokołu LDAPS dostępu zewnętrznego**.

Skonfigurować zewnętrznego dostawcy usługi DNS, nazwę DNS domeny zarządzanej (na przykład "ldaps.contoso100.com") wskazuje ten zewnętrzny adres IP. Na przykład utwórz następujący wpis DNS:

    ldaps.contoso100.com  -> 52.165.38.113

Gotowe. Teraz możesz nawiązać połączenie z domeny zarządzanej przy użyciu protokołu secure LDAP przez internet.

> [!WARNING]
> Należy pamiętać, że komputery klienckie muszą ufać wystawcy certyfikatu protokołu LDAPS, aby można było pomyślnie nawiązać połączenie z domeny zarządzanej przy użyciu protokołu LDAPS. Jeśli używane są publicznie zaufany urząd certyfikacji, nie musisz nic robić, ponieważ komputery klienckie zaufania tych wystawców certyfikatów. Jeśli używasz certyfikatu z podpisem własnym, należy zainstalować publiczną część certyfikatu z podpisem własnym do magazynu zaufanych certyfikatów na komputerze klienckim.
>
>

## <a name="next-step"></a>Następny krok
[Task 5: bind to the managed domain and lock down secure LDAP access (Zadanie 5. Tworzenie powiązania z domeną zarządzaną i blokowanie dostępu do bezpiecznego protokołu LDAP)](active-directory-ds-ldaps-bind-lockdown.md)
