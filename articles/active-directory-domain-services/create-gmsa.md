---
title: Grupowe konta usług zarządzanych dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć konto usługi zarządzanej grupy (gMSA) do użytku z domenami zarządzanymi Usług domenowych Usługi active directory platformy Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 58749e4518f6fa73c8641ce38483c101576047aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614077"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Tworzenie konta usługi zarządzanej grupy (gMSA) w usługach domenowych usługi Azure AD

Aplikacje i usługi często potrzebują tożsamości, aby uwierzytelnić się za pomocą innych zasobów. Na przykład usługa sieci web może wymagać uwierzytelnienia za pomocą usługi bazy danych. Jeśli aplikacja lub usługa ma wiele wystąpień, takich jak farma serwerów sieci web, ręczne tworzenie i konfigurowanie tożsamości dla tych zasobów staje się czasochłonne.

Zamiast tego konto usługi zarządzanej grupy (gMSA) można utworzyć w domenie zarządzanej usług domenowych Usługi azure AD (Azure AD DS) platformy Azure. System operacyjny Windows automatycznie zarządza poświadczeniami gMSA, co upraszcza zarządzanie dużymi grupami zasobów.

W tym artykule pokazano, jak utworzyć gMSA w domenie zarządzanej usług Ad DS platformy Azure przy użyciu programu Azure PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną do zarządzania][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Omówienie kont zarządzanych usług

Samodzielne konto usługi zarządzanej (sMSA) to konto domeny, którego hasło jest zarządzane automatycznie. Takie podejście upraszcza zarządzanie główną nazwą usługi (SPN) i umożliwia delegowane zarządzanie innym administratorom. Nie trzeba ręcznie tworzyć i obracać poświadczenia dla konta.

Konto usługi zarządzanej grupy (gMSA) zapewnia takie samo uproszczenie zarządzania, ale dla wielu serwerów w domenie. GMSA umożliwia wszystkie wystąpienia usługi hostowane w farmie serwerów używać tej samej jednostki usługi dla protokołów wzajemnego uwierzytelniania do pracy. Gdy gMSA jest używany jako podmiot usługi, system operacyjny Windows ponownie zarządza hasłem konta zamiast polegać na administratorze.

Aby uzyskać więcej informacji, zobacz [omówienie kont zarządzanych usług grupy (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Korzystanie z kont usług w usługach AD DS

Ponieważ domeny zarządzane usług Azure AD DS są zablokowane i zarządzane przez firmę Microsoft, istnieją pewne zagadnienia podczas korzystania z kont usług:

* Tworzenie kont usług w niestandardowych jednostkach organizacyjnych (OU) w domenie zarządzanej.
    * Nie można utworzyć konta usługi we wbudowanych *użytkownikach AADDC* lub *komputerach AADDC.*
    * Zamiast tego [utwórz niestandardową jednostkę organizacyjną][create-custom-ou] w domenie zarządzanej usług Azure AD DS, a następnie utwórz konta usługi w tej niestandardowej jednostki organizacyjnej.
* Klucz główny usług dystrybucji kluczy (KDS) jest wstępnie utworzony.
    * Klucz główny KDS służy do generowania i pobierania haseł dla gMSA. W usłudze Azure AD DS katalog główny kds jest tworzony dla Ciebie.
    * Nie masz uprawnień do tworzenia innego lub wyświetlania domyślnego klucza głównego KDS.

## <a name="create-a-gmsa"></a>Tworzenie gMSA

Najpierw utwórz niestandardową jednostkę organizacyjną przy użyciu polecenia cmdlet [New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Aby uzyskać więcej informacji na temat tworzenia niestandardowych procesorów operacyjnych i zarządzania nimi, zobacz [Niestandardowe instalacje organizacyjne w usługach Azure AD DS][create-custom-ou].

> [!TIP]
> Aby wykonać te kroki w celu utworzenia gMSA, [użyj maszyny Wirtualnej zarządzania][tutorial-create-management-vm]. Ta maszyna wirtualna zarządzania powinna mieć już wymagane polecenia cmdlet programu AD PowerShell i połączenie z domeną zarządzaną.

Poniższy przykład tworzy niestandardową jednostkę organizacyjną o nazwie *myNewOU* w domenie zarządzanej usługi Azure AD DS o nazwie *aaddscontoso.com*. Użyj własnej jednostki organizacyjnej i nazwy domeny zarządzanej:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Teraz utwórz gMSA przy użyciu polecenia cmdlet [New-ADServiceAccount.][New-ADServiceAccount] Zdefiniowano następujące przykładowe parametry:

* **-Nazwa** jest ustawiona na *WebFarmSvc*
* **-Path** Parametr określa niestandardową jednostkę organizacyjną dla gMSA utworzonej w poprzednim kroku.
* Wpisy DNS i nazwy główne usługi są ustawiane dla *WebFarmSvc.aaddscontoso.com*
* Podmioty w *AADDSCONTOSO-SERVER$* mogą pobrać hasło używać tożsamości.

Określ własne nazwy i nazwy domen.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Aplikacje i usługi można teraz skonfigurować tak, aby w razie potrzeby korzystały z gMSA.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat gMSA, zobacz [Wprowadzenie do grupowych kont usług zarządzanych][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
