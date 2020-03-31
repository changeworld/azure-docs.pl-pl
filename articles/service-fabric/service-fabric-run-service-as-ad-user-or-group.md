---
title: Uruchamianie usługi sieci szkieletowej usług Azure jako użytkownika lub grupy usług AD
description: Dowiedz się, jak uruchomić usługę jako użytkownik lub grupę usługi Active Directory w autonomicznym klastrze systemu Windows sieci szkieletowej.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464246"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Uruchamianie usługi jako użytkownika lub grupy usługi Active Directory
W klastrze autonomicznym systemu Windows Server można uruchomić usługę jako użytkownik lub grupę usługi Active Directory przy użyciu zasad RunAs.  Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, pod które działa proces Fabric.exe. Uruchamianie aplikacji na różnych kontach, nawet w udostępnionym środowisku hostowanym, czyni je bezpieczniejszymi od siebie nawzajem. Należy zauważyć, że używa usługi Active Directory lokalnie w domenie, a nie usługi Azure Active Directory (Azure AD).  Można również uruchomić usługę jako [grupowe konto usługi zarządzanej (gMSA).](service-fabric-run-service-as-gmsa.md)

Korzystając z użytkownika lub grupy domeny, można następnie uzyskać dostęp do innych zasobów w domenie (na przykład udziałów plików), którym przyznano uprawnienia.

W poniższym przykładzie pokazano użytkownika usługi Active Directory o nazwie *TestUser* z ich hasłem domeny zaszyfrowanym przy użyciu certyfikatu o nazwie *MyCert*. Za pomocą `Invoke-ServiceFabricEncryptText` polecenia Programu PowerShell można utworzyć tajny tekst szyfru. Zobacz [zarządzanie wpisami tajnymi w aplikacjach sieci szkieletowej usług,](service-fabric-application-secret-management.md) aby uzyskać szczegółowe informacje.

Należy wdrożyć klucz prywatny certyfikatu, aby odszyfrować hasło na komputerze lokalnym przy użyciu metody poza pasmem (na platformie Azure jest to za pośrednictwem usługi Azure Resource Manager). Następnie, gdy usługa Service Fabric wdraża pakiet usługi na komputerze, jest w stanie odszyfrować klucz tajny i (wraz z nazwą użytkownika) uwierzytelnić się za pomocą usługi Active Directory do uruchomienia w ramach tych poświadczeń.

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
> W przypadku zastosowania zasady RunAs do usługi, a manifest usługi deklaruje zasoby punktu końcowego za pomocą protokołu HTTP, należy również określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
W następnym kroku przeczytaj następujące artykuły:
* [Opis modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
