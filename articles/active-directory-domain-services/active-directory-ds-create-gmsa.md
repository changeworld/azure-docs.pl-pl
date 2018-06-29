---
title: 'Azure Active Directory Domain Services: Utwórz konto usługi zarządzane przez grupę | Dokumentacja firmy Microsoft'
description: Administrowanie domeny zarządzanej usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 4b4ce876760d024170020ae3faf618c7e9e76773
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036063"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Utwórz konto usługi zarządzane przez grupę (gMSA) w domenie zarządzanej usług domenowych Azure AD
W tym artykule przedstawiono sposób tworzenia kont usług zarządzanych przez w domenie zarządzanej usług domenowych Azure AD.

## <a name="managed-service-accounts"></a>Zarządzane konta usług
Autonomiczne zarządzane konta usługi (autonomiczne) jest kontem domeny zarządzanej, którego hasło odbywa się automatycznie. Takie rozwiązanie upraszcza zarządzanie usługami głównej nazwy (usługi SPN), i umożliwia delegowane zarządzania do innych administratorów. Tego typu zarządzanego konta usług (MSA) została wprowadzona w systemie Windows Server 2008 R2 i Windows 7.

Konto usługi zarządzane przez grupę (gMSA) zapewnia te same korzyści dla wielu serwerów w domenie. Wszystkie wystąpienia z usługą hostowaną w farmie serwerów należy używać tej samej nazwy głównej usługi dla protokołów uwierzytelniania wzajemnego do pracy. Gdy grupę jest używane jako nazwy głównej usługi, system operacyjny Windows zarządza hasło dla konta, zdejmując to zadanie z administratora.

**Więcej informacji:**
- [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Wprowadzenie do korzystania z konta usług zarządzane przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Przy użyciu konta usług w usługach domenowych Azure AD
Azure domeny zarządzanej usług domenowych w usłudze AD są zablokowane i zarządzany przez firmę Microsoft. Istnieje kilka kluczowych kwestii dotyczących podczas korzystania z konta usługi z usług domenowych Azure AD.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Utwórz konta usług w niestandardowej jednostki organizacyjnej (OU) w domenie zarządzanej
Nie można utworzyć konto usługi do wbudowanych "AADDC użytkowników" lub "Komputery AADDC" jednostek organizacyjnych. [Tworzenie niestandardowych jednostki Organizacyjnej](active-directory-ds-admin-guide-create-ou.md) na domeny zarządzanej, a następnie utwórz konta usług w ramach tej niestandardowej jednostce Organizacyjnej.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Klucz główny usługi dystrybucji kluczy (KDS) jest już utworzony wcześniej
Klucz główny usługi dystrybucji kluczy (KDS) jest wstępnie utworzone w domenie zarządzanej usług domenowych Azure AD. Nie musisz utworzyć główny KDS klucza i nie masz uprawnień do wykonania tego jednego. Nie można wyświetlić zarówno klucz główny KDS na domeny zarządzanej.

## <a name="sample---create-a-gmsa-using-powershell"></a>Przykładowe — Utwórz grupę przy użyciu programu PowerShell
Poniższy przykład przedstawia sposób tworzenia niestandardowych OU, przy użyciu programu PowerShell. Następnie można utworzyć grupę w tej jednostce Organizacyjnej za pomocą ```-Path``` parametr, aby określić jednostkę Organizacyjną.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Dokumentację poleceń cmdlet programu PowerShell:**
- [ADOrganizationalUnit nowe polecenia cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Polecenia cmdlet New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie niestandardowych jednostki Organizacyjnej w domenie zarządzanej](active-directory-ds-admin-guide-create-ou.md)
- [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Wprowadzenie do korzystania z konta usług zarządzane przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
