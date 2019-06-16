---
title: Uruchom usługę Azure Service Fabric jako użytkownik usługi AD lub grupy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić usługi jako użytkownika usługi Active Directory lub grupie na klastra autonomicznego usługi Service Fabric Windows.
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
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837729"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Uruchamianie usługi za użytkownika usługi Active Directory lub grupy
W klastrze autonomicznego systemu Windows Server można uruchomić usługi jako użytkownika usługi Active Directory lub grupie za pomocą zasad RunAs.  Domyślnie aplikacje usługi Service Fabric uruchamiana na koncie, zgodną z procesu Fabric.exe. Uruchamianie aplikacji w ramach różnych kont, nawet w środowisku współdzielonym hostowanej sprawia, że jest ich bardziej bezpieczne od siebie nawzajem. Należy pamiętać, że używane są usługi Active Directory w środowisku lokalnym w ramach domeny i nie usługi Azure Active Directory (Azure AD).  Można również uruchomić usługę jako [konta grupy usługi zarządzanej (gMSA)](service-fabric-run-service-as-gmsa.md).

Za pomocą użytkownika domeny lub grupy, można następnie dostęp do innych zasobów w domenie (np. udziały plików), które ma odpowiednie uprawnienia.

W poniższym przykładzie pokazano użytkownika usługi Active Directory o nazwie *TestUser* z ich domeny o nazwie hasła szyfrowane przy użyciu certyfikatu *MyCert*. Możesz użyć `Invoke-ServiceFabricEncryptText` polecenie programu PowerShell w celu utworzenia wpisu tajnego zaszyfrowanego tekstu. Zobacz [zarządzać kluczami tajnymi w aplikacji usługi Service Fabric](service-fabric-application-secret-management.md) Aby uzyskać szczegółowe informacje.

Należy wdrożyć klucza prywatnego certyfikatu można odszyfrować haseł na komputerze lokalnym przy użyciu metody out-of-band (na platformie Azure, jest to za pomocą usługi Azure Resource Manager). Następnie gdy Usługa Service Fabric wdroży pakiet usługi do maszyny, będzie mógł odszyfrować klucz tajny i (wraz z nazwą użytkownika) uwierzytelniania w usłudze Active Directory, aby działać w ramach tych poświadczeń.

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
> Jeśli zastosujesz zasady RunAs do usługi i manifestu usługi deklaruje zasobów punktu końcowego przy użyciu protokołu HTTP, należy także określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Kolejnym krokiem przeczytaj następujące artykuły:
* [Informacje o modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
