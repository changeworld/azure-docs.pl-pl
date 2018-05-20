---
title: Uruchamianie usługi sieć szkieletowa usług Azure AD użytkownika lub grupy | Dokumentacja firmy Microsoft
description: Informacje o sposobie uruchamiania usługi jako użytkownika usługi Active Directory lub grupy w klastrze autonomicznej usługi Windows Fabric.
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
ms.openlocfilehash: 624cb54778950a8a973bcf6cefddf66cba2bd0fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Uruchamianie usługi jako grupy lub użytkownika usługi Active Directory
W klastrze systemu Windows Server autonomiczny można uruchomić usługi jako użytkownika usługi Active Directory lub grupy za pomocą zasad RunAs.  Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, działającą proces Fabric.exe. Uruchamianie aplikacji w ramach różnych kont, nawet w środowisku hostowanej udostępnionego umożliwiają bardziej bezpieczne od siebie nawzajem. Należy pamiętać, że używa usługi Active Directory lokalnie w domenie i nie usługi Azure Active Directory (Azure AD).  Można również uruchomić usługi jako [konta grupy usługi zarządzanej (gMSA)](service-fabric-run-service-as-gmsa.md).

Przy użyciu użytkownika domeny lub grupy, można następnie dostęp do innych zasobów domeny (na przykład udziałów plików), które ma odpowiednie uprawnienia.

W poniższym przykładzie przedstawiono użytkownika usługi Active Directory o nazwie *TestUser* z ich domeny o nazwie hasła szyfrowane przy użyciu certyfikatu *MyCert*. Można użyć `Invoke-ServiceFabricEncryptText` polecenie programu PowerShell, aby utworzyć tajny zaszyfrowanego tekstu. Zobacz [Zarządzanie kluczy tajnych w aplikacji usługi Service Fabric](service-fabric-application-secret-management.md) szczegółowe informacje.

Należy wdrożyć klucza prywatnego certyfikatu można odszyfrować hasła na komputerze lokalnym przy użyciu metody poza pasmem (na platformie Azure jest za pośrednictwem usługi Azure Resource Manager). Następnie gdy usługa sieć szkieletowa wdraża pakiet usługi do maszyny, jest w stanie odszyfrować klucza tajnego i (wraz z nazwą użytkownika) uwierzytelniania za pomocą usługi Active Directory do uruchomienia w ramach tych poświadczeń.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Jeśli stosowanie zasad RunAs do usługi i manifestu usługi deklaruje punktu końcowego zasobów przy użyciu protokołu HTTP, należy także określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisać zasady zabezpieczeń dostępu dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Jako kolejny krok przeczytaj następujące artykuły:
* [Zrozumienie model aplikacji](service-fabric-application-model.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
