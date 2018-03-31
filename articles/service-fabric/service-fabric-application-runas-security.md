---
title: Uruchamianie usługi sieć szkieletowa usług Azure w obszarze kont zabezpieczeń lokalnych i system | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do uruchamiania aplikacji usługi Service Fabric w systemie i kont zabezpieczeń lokalnych.  Tworzenie podmiotów zabezpieczeń i stosowanie zasad Uruchom jako, aby bezpiecznie uruchomić usługi.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3df5374911ee6381f25d08d23d565cdf8a7cd12f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Uruchamianie usługi jako konto użytkownika lokalnego lub lokalnego konta systemowego
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacji uruchomionych w klastrze, w obszarze konta innego użytkownika. Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, działającą proces Fabric.exe. Usługi sieć szkieletowa dostępne są także możliwość uruchamiania aplikacji w ramach konta użytkownika lokalnego lub konta system lokalny, które jest realizowane za pośrednictwem zasad RunAs w manifeście aplikacji. Obsługiwany system lokalny typy kont to **LocalUser**, **NetworkService**, **Usługa lokalna**, i **LocalSystem**.  Jeśli korzystasz z usługi Service Fabric w klastrze autonomicznego Windows, można uruchomić usługi w ramach [kont domen usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

Można również zdefiniować i Utwórz grupy użytkowników, dzięki czemu można dodać co najmniej jednego użytkownika do każdej grupy do wspólnego zarządzania. Jest to przydatne, jeśli jest wielu użytkowników do innej usługi, punkty wejścia i muszą mieć niektórych typowych uprawnień, które są dostępne na poziomie grupy.

> [!NOTE] 
> Jeśli stosowanie zasad RunAs do usługi i manifestu usługi deklaruje punktu końcowego zasobów przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisać zasady zabezpieczeń dostępu dla punktów końcowych HTTP i HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Tworzenie grup użytkowników lokalnych
Można zdefiniować i Utwórz grupy użytkowników, zezwalających na co najmniej jednego użytkownika, które mają zostać dodane do grupy. Jest to przydatne, jeśli jest wielu użytkowników do innej usługi, punkty wejścia i muszą mieć niektórych typowych uprawnień, które są dostępne na poziomie grupy. W poniższym przykładzie pokazano to grupa lokalna o nazwie **LocalAdminGroup** z uprawnieniami administratora. Dwóch użytkowników, Customer1 i Customer2, zostają członkami tej grupy lokalnej w tym przykładzie manifestu aplikacji:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>Tworzenie użytkowników lokalnych
Można utworzyć użytkownika lokalnego, który może służyć do zabezpieczania usług w aplikacji. Gdy **LocalUser** typ konta jest określone w sekcji podmiotów manifestu aplikacji usługi sieć szkieletowa usług tworzy lokalne konta użytkowników na komputerach, na którym aplikacja jest wdrażana. Domyślnie te konta nie mają tej samej nazwy jak określone w manifeście aplikacji (na przykład Customer3 w poniższym przykładzie manifestu aplikacji). Zamiast tego są generowane dynamicznie i losowego hasła.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Jeśli aplikacja wymaga, aby konto użytkownika i hasło być taka sama na wszystkich komputerach (na przykład, aby włączyć uwierzytelnianie NTLM), należy ustawić manifestu klastra **NTLMAuthenticationEnabled** na wartość true. Manifestu klastra musi również określać **NTLMAuthenticationPasswordSecret** używany do generowania tego samego hasła na wszystkich komputerach.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Zasady przypisać pakietów kodu usługi
**RunAsPolicy** sekcji **ServiceManifestImport** Określa konto, z sekcji podmiotów zabezpieczeń, które mają być używane do uruchamiania pakiet kodu. Pakiety kodu z manifestu usługi również kojarzy z kontami użytkowników w sekcji podmiotów zabezpieczeń. Określenie tej konfiguracji lub punkty wejścia głównego, lub możesz określić `All` do obu. W poniższym przykładzie przedstawiono różne zasady są stosowane:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Jeśli **EntryPointType** nie zostanie określony, domyślnie jest zaznaczona `EntryPointType=”Main”`. Określanie **SetupEntryPoint** jest szczególnie przydatne, gdy chcesz uruchomić pewnych operacji ustawienia wysokiego poziomu przy użyciu konta system. Aby uzyskać więcej informacji, zobacz [uruchomienia skryptu uruchamiania usługi jako konto użytkownika lub systemu lokalnego](service-fabric-run-script-at-service-startup.md). Kod rzeczywiste usługi mogą być uruchamiane w konta dolna uprawnień.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Zastosuj zasady domyślne na wszystkie pakiety kodu usługi
Możesz użyć **DefaultRunAsPolicy** sekcji, aby określić konto użytkownika domyślna dla całego kodu pakietami, które nie mają określonego **RunAsPolicy** zdefiniowane. Jeśli większość pakietów kodu, które są określone w manifeście usługi używany przez aplikację muszą działać w ramach tego samego użytkownika, aplikacja właśnie można zdefiniować zasady RunAs domyślne przy użyciu tego konta użytkownika. Poniższy przykład określa, że jeśli pakiet kodu nie ma **RunAsPolicy** określony pakiet kodu należy uruchamiać w ramach **MyDefaultAccount** określone w sekcji podmiotów zabezpieczeń.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Zrozumienie model aplikacji](service-fabric-application-model.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
