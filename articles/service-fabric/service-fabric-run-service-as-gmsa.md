---
title: Uruchamianie usługi Service Fabric platformy Azure przy użyciu konta gMSA
description: Dowiedz się, jak uruchomić usługę jako gMSA Service Fabric w klastrze autonomicznym systemu Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 99d8089bd12d05e46f91e55c933d58d50baa92f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464262"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Uruchamianie usługi za pomocą zarządzanego konta usługi grupy
W klastrze autonomicznym systemu Windows Server można uruchomić usługę jako konto usługi zarządzane przez grupę (gMSA) przy użyciu zasad RunAs.  Domyślnie aplikacje Service Fabric są uruchamiane w ramach konta, w ramach którego działa proces Fabric. exe. Uruchamianie aplikacji na różnych kontach, nawet w udostępnianym środowisku hostowanym, sprawia, że są one bezpieczniejsze od siebie nawzajem. Należy zauważyć, że ta funkcja używa Active Directory lokalnie w domenie, a nie Azure Active Directory (Azure AD). Korzystając z gMSA, nie ma hasła ani zaszyfrowanego hasła przechowywanego w manifeście aplikacji.  Możesz również uruchomić usługę jako [Active Directory użytkownika lub grupę](service-fabric-run-service-as-ad-user-or-group.md).

Poniższy przykład pokazuje, jak utworzyć konto gMSA o nazwie *SVC-test $* ; Jak wdrożyć to konto usługi zarządzanej w węzłach klastra; i sposób konfigurowania podmiotu zabezpieczeń użytkownika.

Wymagania wstępne:
- Domena wymaga klucza głównego KDS.
- W domenie musi być co najmniej jeden kontroler domeny systemu Windows Server 2012 (lub R2).

1. Aby administrator domeny Active Directory utworzyć konto usługi zarządzane przez grupę przy użyciu `New-ADServiceAccount` polecenia cmdlet i upewnij się, że `PrincipalsAllowedToRetrieveManagedPassword` zawiera wszystkie węzły klastra usługi Service Fabric. `AccountName`, `DnsHostName`i `ServicePrincipalName` muszą być unikatowe.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Na każdym z węzłów klastra Service Fabric (na przykład `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) Zainstaluj i przetestuj gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Skonfiguruj nazwę główną użytkownika i skonfiguruj RunAsPolicy do odwoływania się do użytkownika.
    
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
> Jeśli zastosujesz zasady RunAs do usługi, a manifest usługi deklaruje zasoby punktów końcowych przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych http i https](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Następnym krokiem jest zapoznanie się z następującymi artykułami:
* [Omówienie modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
