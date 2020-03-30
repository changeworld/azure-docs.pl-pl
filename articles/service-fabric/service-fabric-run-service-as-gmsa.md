---
title: Uruchamianie usługi sieci szkieletowej usług Azure na koncie gMSA
description: Dowiedz się, jak uruchomić usługę jako konto usługi zarządzanej w grupie (gMSA) w samodzielnym klastrze systemu Windows sieci szkieletowej.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988400"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Uruchamianie usługi za pomocą zarządzanego konta usługi grupy

W klastrze autonomicznym systemu Windows Server można uruchomić usługę jako *konto usługi zarządzanej grupy* (gMSA) przy użyciu zasad *RunAs.*  Domyślnie aplikacje sieci szkieletowej usług `Fabric.exe` są uruchamiane na koncie, pod które działa proces. Uruchamianie aplikacji na różnych kontach, nawet w udostępnionym środowisku hostowanym, czyni je bezpieczniejszymi od siebie nawzajem. Za pomocą gMSA, nie ma hasła lub zaszyfrowane hasło przechowywane w manifeście aplikacji.  Można również uruchomić usługę jako [użytkownik lub grupa usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

W poniższym przykładzie pokazano, jak utworzyć konto gMSA o nazwie *svc-Test$*, jak wdrożyć to konto usługi zarządzanej w węzłach klastra i jak skonfigurować podmiotu zabezpieczeń użytkownika.

> [!NOTE]
> Korzystanie z gMSA z autonomicznym klastrem sieci szkieletowej usług wymaga usługi Active Directory lokalnie w domenie (a nie usługi Azure Active Directory (Azure AD)).

Wymagania wstępne:

- Domena potrzebuje klucza głównego KDS.
- W domenie musi znajdować się co najmniej jeden kontroler domeny systemu Windows Server 2012 (lub R2).

1. Niech administrator domeny usługi Active Directory utworzy `New-ADServiceAccount` konto usługi zarządzanej `PrincipalsAllowedToRetrieveManagedPassword` przez grupę przy użyciu polecenia cmdlet i zapewni, że obejmuje wszystkie węzły klastra sieci szkieletowej usług. `AccountName`, `DnsHostName`i `ServicePrincipalName` musi być unikatowy.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. W każdym z węzłów klastra sieci `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`szkieletowej usług (na przykład ) zainstaluj i przetestuj gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Skonfiguruj podmiot zabezpieczeń `RunAsPolicy` użytkownika i skonfiguruj, aby odwoływać się do [użytkownika](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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
> W przypadku zastosowania zasady RunAs do usługi, a manifest usługi deklaruje zasoby punktu końcowego za pomocą protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

Następujące artykuły poprowadzą Cię przez kolejne kroki:

- [Opis modelu aplikacji](service-fabric-application-model.md)
- [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
- [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
