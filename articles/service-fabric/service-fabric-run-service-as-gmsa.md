---
title: Uruchamianie usługi Azure Service Fabric przy użyciu konta gMSA | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i uruchom usługę jako gMSA w klastrze usługi Service Fabric Windows autonomicznych.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 5c3781c2111fff7483a7fb65bd7b2e69c2011d18
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666430"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Uruchamianie usługi za pomocą zarządzanego konta usługi grupy
W klastrze systemu Windows Server autonomiczny można uruchomić usługi jako zarządzanego konta usługi (gMSA) przy użyciu polecenia Uruchom jako zasad grupy.  Domyślnie aplikacje usługi Service Fabric uruchamiana na koncie, zgodną z procesu Fabric.exe. Uruchamianie aplikacji w ramach różnych kont, nawet w środowisku współdzielonym hostowanej sprawia, że jest ich bardziej bezpieczne od siebie nawzajem. Należy pamiętać, że używane są usługi Active Directory w środowisku lokalnym w ramach domeny i nie usługi Azure Active Directory (Azure AD). Za pomocą gMSA, nie ma hasła lub zaszyfrowanego hasła przechowywane w manifeście aplikacji.  Można również uruchomić usługę jako [użytkownika usługi Active Directory lub grupie](service-fabric-run-service-as-ad-user-or-group.md).

Poniższy przykład pokazuje, jak utworzyć konto gMSA, o nazwie *svc-Test$*; sposobu wdrażania tego konta usługi zarządzanej do węzłów klastra; oraz konfigurowania głównej nazwy użytkownika.

Wymagania wstępne:
- Domena musi mieć klucz główny KDS.
- Musi znajdować się w systemie Windows Server 2012 lub nowszym poziom funkcjonalności domeny.

1. Mieć administratora domeny usługi Active Directory, tworzenie zarządzanych przez grupę usługi konta za pomocą `New-ADServiceAccount` polecenia cmdlet i upewnij się, że `PrincipalsAllowedToRetrieveManagedPassword` obejmuje wszystkie węzły klastra usługi Service fabric. `AccountName`, `DnsHostName`, i `ServicePrincipalName` muszą być unikatowe.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Na każdym z usługi Service Fabric węzły klastra (na przykład `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), zainstalować i przetestować opcją gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Skonfiguruj głównej nazwy użytkownika i skonfiguruj RunAsPolicy można odwoływać się do użytkownika.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> Jeśli zastosujesz zasady RunAs do usługi i manifestu usługi deklaruje zasobów punktu końcowego przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Kolejnym krokiem przeczytaj następujące artykuły:
* [Informacje o modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
