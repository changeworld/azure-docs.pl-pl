---
title: Usługa w chmurze Rola config XPath ściągawki | Dokumenty firmy Microsoft
description: Różne ustawienia XPath, których można użyć w konfiguracji roli usługi w chmurze, aby udostępnić ustawienia jako zmienną środowiskową.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386089"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Udostępnianie ustawień konfiguracji roli jako zmiennej środowiskowej za pomocą programu XPath
W pliku definicji definicji usługi usługi usługi w chmurze lub roli sieci web można udostępnić wartości konfiguracji środowiska uruchomieniowego jako zmienne środowiskowe. Obsługiwane są następujące wartości XPath (które odpowiadają wartościom interfejsu API).

Te wartości XPath są również dostępne za pośrednictwem biblioteki [Microsoft.WindowsAzure.ServiceRuntime.](/previous-versions/azure/reference/ee773173(v=azure.100)) 

## <a name="app-running-in-emulator"></a>Aplikacja uruchomiona w emulatorze
Wskazuje, że aplikacja jest uruchomiona w emulatorze.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Code |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Identyfikator wdrożenia
Pobiera identyfikator wdrożenia dla wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Code |var deploymentId = RoleEnvironment.DeploymentIdId; |

## <a name="role-id"></a>Identyfikator roli
Pobiera bieżący identyfikator roli dla wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Code |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizowanie domeny
Pobiera domenę aktualizacji wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Code |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domena błędów
Pobiera domenę błędów wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Code |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nazwa roli
Pobiera nazwę roli wystąpień.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Code |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Ustawienie konfiguracja
Pobiera wartość określonego ustawienia konfiguracji.

| Typ | Przykład |
| --- | --- |
| XPath |@namexpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[='Setting1']/@value" |
| Code |ustawienie var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Ścieżka magazynu lokalnego
Pobiera ścieżkę magazynu lokalnego dla wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Code |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). Ścieżka główna; |

## <a name="local-storage-size"></a>Lokalny rozmiar magazynu
Pobiera rozmiar magazynu lokalnego dla wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Code |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protokół punktu końcowego
Pobiera protokół punktu końcowego dla wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoints[='Endpoint1']@name/@protocol" |
| Code |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Punkt końcowy1"]. protokołu; |

## <a name="endpoint-ip"></a>Adres IP punktu końcowego
Pobiera adres IP określonego punktu końcowego.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoints[='Endpoint1']@name/@address" |
| Code |adres var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Punkt końcowy1"]. Adres IPEndpoint.Address |

## <a name="endpoint-port"></a>Port punktu końcowego
Pobiera port punktu końcowego dla wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoints[='Endpoint1']@name/@port" |
| Code |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Punkt końcowy1"]. IPEndpoint.Port; |

## <a name="example"></a>Przykład
Oto przykład roli procesu roboczego, która tworzy zadanie `TestIsEmulated` uruchamiania ze zmienną środowiskową o nazwie ustawiona na [ @emulated wartość xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o pliku [ServiceConfiguration.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)

Utwórz pakiet [ServicePackage.cspkg.](cloud-services-model-and-package.md#servicepackagecspkg)

Włącz [pulpit zdalny](cloud-services-role-enable-remote-desktop-new-portal.md) dla roli.




