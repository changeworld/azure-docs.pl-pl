---
title: Uruchamianie usługi sieć szkieletowa usług Azure w ramach konta gMSA | Dokumentacja firmy Microsoft
description: Informacje o sposobie uruchamiania usługi jako gMSA w klastrze autonomicznej usługi Windows Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 5f93285061708172b9b6ac40dc97fce08f7b2a86
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Uruchamianie usługi za pomocą zarządzanego konta usługi grupy
W klastrze systemu Windows Server autonomiczny można uruchomić usługi jako konto usługi zarządzane (gMSA) przy użyciu zasad RunAs grupy.  Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, działającą proces Fabric.exe. Uruchamianie aplikacji w ramach różnych kont, nawet w środowisku hostowanej udostępnionego umożliwiają bardziej bezpieczne od siebie nawzajem. Należy pamiętać, że używa usługi Active Directory lokalnie w domenie i nie usługi Azure Active Directory (Azure AD). Za pomocą grupę, brak hasła i zaszyfrowane hasło przechowywane w manifeście aplikacji.  Można również uruchomić usługi jako [użytkownika usługi Active Directory lub grupy](service-fabric-run-service-as-ad-user-or-group.md).

Poniższy przykład pokazuje, jak utworzyć konto usługi zarządzane przez grupę o nazwie *testu svc$*; sposobu wdrażania tego konta usługi zarządzanego przez węzły klastra; i sposobie konfigurowania głównej nazwy użytkownika.

Wymagania wstępne:
- Domena musi mieć klucz główny KDS.
- Domena musi być na poziomie systemu Windows Server 2012 lub nowszego poziomu funkcjonalności.

1. Poproś administratora domeny usługi Active Directory utworzyć, używając konta usług zarządzane przez grupę `New-ADServiceAccount` apletu polecenia i upewnij się, że `PrincipalsAllowedToRetrieveManagedPassword` zawiera wszystkie węzły klastra sieci szkieletowej usług. `AccountName`, `DnsHostName`, i `ServicePrincipalName` muszą być unikatowe.

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. W każdej sieci szkieletowej usług węzłami klastra (na przykład `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), zainstalować i przetestować przez grupę.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Skonfiguruj głównej nazwy użytkownika oraz RunAsPolicy, aby odwołać się użytkownika.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Jeśli stosowanie zasad RunAs do usługi i manifestu usługi deklaruje punktu końcowego zasobów przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisać zasady zabezpieczeń dostępu dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Jako kolejny krok przeczytaj następujące artykuły:
* [Zrozumienie model aplikacji](service-fabric-application-model.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
