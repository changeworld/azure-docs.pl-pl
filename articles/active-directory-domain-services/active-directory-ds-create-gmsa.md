---
title: 'Azure Active Directory Domain Services: Utwórz konto usługi zarządzanych przez grupę | Dokumentacja firmy Microsoft'
description: Administrowanie domen zarządzanych w usłudze Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: ergreenl
ms.openlocfilehash: ef21383db52d45d996f2c6c1d6900ebba2349635
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359402"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Utwórz konto usługi zarządzanych przez grupę (gMSA) w domenie zarządzanej usług domenowych Azure AD
W tym artykule przedstawiono sposób tworzenia kont usług zarządzanych w domenie zarządzanej usług domenowych Azure AD.

## <a name="managed-service-accounts"></a>Zarządzane konta usług
Autonomiczne zarządzane konta usługi (autonomiczne zarządzane konta usług) jest kontem domeny zarządzanej, którego hasło odbywa się automatycznie. Upraszcza zarządzanie główną nazwę (usługi SPN) usługi i umożliwia delegowane Zarządzanie do innych administratorów. Ten typ konta usługi zarządzanej (MSA) została wprowadzona w systemie Windows Server 2008 R2 i Windows 7.

Konto usługi zarządzane przez grupę (gMSA) zapewnia te same korzyści w przypadku wielu serwerów w domenie. Wszystkie wystąpienia usługi z hostowanej na farmie serwerów należy używać tej samej jednostki usługi dla protokołów uwierzytelniania wzajemnego do pracy. Gdy gMSA jest używana jako nazwa główna usługi, system operacyjny Windows zarządza hasło dla konta, zdejmując przez administratora.

**Więcej informacji:**
- [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Wprowadzenie do kont usługi zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Przy użyciu konta usługi w usługach domenowych Azure AD
Usługa Azure AD Domain Services domeny zarządzane są zablokowane i zarządzany przez firmę Microsoft. Istnieje kilka kluczowych zagadnień podczas korzystania z kont usług za pomocą usług domenowych Azure AD.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Tworzenie konta usług w ramach niestandardowej jednostki organizacyjne (OU) w domenie zarządzanej
Nie można utworzyć konto usługi do wbudowanych "Użytkownicy usługi AAD DC" lub "Komputery usługi AAD DC" jednostek organizacyjnych. [Tworzenie niestandardowej jednostki Organizacyjnej](active-directory-ds-admin-guide-create-ou.md) w domenie zarządzanej, a następnie utwórz kont usług w ramach tej niestandardowej jednostce Organizacyjnej.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Klucz główny dystrybucji KDS usług klucza () jest już utworzony wcześniej
Klucz główny dystrybucji KDS usług klucza () jest wstępnie utworzonych w domenie zarządzanej usług domenowych Azure AD. Nie musisz utworzyć główny KDS klucza i nie masz uprawnień do wykonania tego jednego. Nie można wyświetlić klucz główny KDS albo w domenie zarządzanej.

## <a name="sample---create-a-gmsa-using-powershell"></a>Przykładowy — tworzenie gMSA przy użyciu programu PowerShell
Poniższy przykład pokazuje, jak utworzyć niestandardowe jednostki Organizacyjnej, za pomocą programu PowerShell. Następnie możesz utworzyć gMSA w tej jednostce Organizacyjnej za pomocą ```-Path``` parametru, aby określić jednostkę Organizacyjną.

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
- [New-ADOrganizationalUnit cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Polecenie cmdlet New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie niestandardowej jednostki Organizacyjnej w domenie zarządzanej](active-directory-ds-admin-guide-create-ou.md)
- [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Wprowadzenie do kont usługi zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
