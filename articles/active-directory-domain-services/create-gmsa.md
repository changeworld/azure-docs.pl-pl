---
title: 'Azure Active Directory Domain Services: Tworzenie konta usługi zarządzanego przez grupę | Microsoft Docs'
description: Dowiedz się, jak utworzyć konto usługi zarządzane przez grupę (gMSA) do użycia z domenami zarządzanymi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: 3742aed7ff39e0a2f6bdf353fb9f261176027422
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612949"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Tworzenie konta usługi zarządzanego przez grupę (gMSA) w domenie zarządzanej Azure AD Domain Services
W tym artykule pokazano, jak utworzyć zarządzane konta usług w Azure AD Domain Services domenie zarządzanej.

## <a name="managed-service-accounts"></a>Zarządzane konta usług
Konto usługi zarządzane autonomicznie (autonomiczne) to konto domeny zarządzanej, którego hasło jest zarządzane automatycznie. Upraszcza to zarządzanie główną nazwą usługi (SPN) i umożliwia delegowanie zarządzania innym administratorom. Tego typu zarządzane konto usługi (MSA) zostało wprowadzone w systemie Windows Server 2008 R2 i Windows 7.

Konto usługi zarządzane przez grupę (gMSA) zapewnia te same korzyści dla wielu serwerów w domenie. Wszystkie wystąpienia usługi hostowanej w farmie serwerów muszą używać tej samej jednostki usługi dla protokołów uwierzytelniania wzajemnego do pracy. Gdy gMSA jest używany jako nazwa główna usługi, system operacyjny Windows zarządza hasłem konta, a nie polega na administratora.

**Więcej informacji:**
- [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Wprowadzenie do kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Korzystanie z kont usług w usługach domenowych Azure AD
Azure AD Domain Services domeny zarządzane są blokowane i zarządzane przez firmę Microsoft. W przypadku korzystania z kont usług z Azure AD Domain Servicesymi należy wziąć pod uwagę kilka najważniejszych zagadnień.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Utwórz konta usług w ramach niestandardowych jednostek organizacyjnych (OU) w domenie zarządzanej
Nie można utworzyć konta usługi w wbudowanych jednostkach organizacyjnych "AADDC users" lub "komputery AADDC". [Utwórz niestandardową jednostkę organizacyjną](create-ou.md) w domenie zarządzanej, a następnie Utwórz konta usług w ramach tej niestandardowej jednostki organizacyjnej.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Klucz główny usług dystrybucji kluczy (KDS) jest już wstępnie utworzony
Klucz główny usług dystrybucji kluczy (KDS) jest wstępnie utworzony w domenie zarządzanej Azure AD Domain Services. Nie musisz tworzyć klucza głównego KDS i nie masz uprawnień do wykonania tych czynności. Nie można wyświetlić klucza głównego KDS w domenie zarządzanej.

## <a name="sample---create-a-gmsa-using-powershell"></a>Przykład — tworzenie gMSA przy użyciu programu PowerShell
Poniższy przykład pokazuje, jak utworzyć niestandardową jednostkę organizacyjną przy użyciu programu PowerShell. Następnie można utworzyć gMSA w tej jednostce organizacyjnej za pomocą ```-Path``` parametru, aby określić jednostkę organizacyjną.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=contoso,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso.com  `
-Path "OU=MYNEWOU,DC=contoso,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
http/WebFarmSvc.contoso.com/contoso,  `
http/WebFarmSvc/contoso.com, http/WebFarmSvc/contoso  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Dokumentacja poleceń cmdlet programu PowerShell:**
- [Polecenie cmdlet New-ADOrganizationalUnit](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Polecenie cmdlet New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Następne kroki
- [Tworzenie niestandardowej jednostki organizacyjnej w domenie zarządzanej](create-ou.md)
- [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Wprowadzenie do kont usług zarządzanych przez grupę](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
