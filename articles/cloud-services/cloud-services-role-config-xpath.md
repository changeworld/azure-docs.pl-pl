---
title: Chmura roli usługi konfiguracji XPath ściągawka | Dokumentacja firmy Microsoft
description: Różne ustawienia języka XPath można w konfiguracji roli usługi w chmurze uwidocznić ustawienia jako zmiennej środowiskowej.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e71adbca34390bda3a7d4067742ffb3a28201449
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2017
ms.locfileid: "24860375"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Udostępnianie ustawień konfiguracji roli jako zmiennej środowiskowej XPath
W chmurze usługi worker lub pliku definicji usługi roli sieci web można uwidocznić wartości konfiguracji środowiska uruchomieniowego jako zmienne środowiskowe. Obsługiwane są następujące wartości XPath, (które odpowiadają wartościom interfejsu API).

Dostępne są także te wartości XPath [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) biblioteki. 

## <a name="app-running-in-emulator"></a>Aplikacji uruchomionej w emulatorze
Wskazuje, że aplikacja działa w emulatorze.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/RoleEnvironment/Deployment/@emulated" |
| Kod |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Identyfikator wdrożenia
Pobiera identyfikator wdrożenia dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/RoleEnvironment/Deployment/@id" |
| Kod |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Identyfikator roli
Pobiera bieżący identyfikator roli dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/RoleEnvironment/CurrentInstance/@id" |
| Kod |Identyfikator var = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizowanie domeny
Pobiera domeny aktualizacji wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kod |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domena awarii
Pobiera wystąpienia domeny błędów.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kod |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nazwa roli
Pobiera nazwę wystąpienia roli.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/RoleEnvironment/CurrentInstance/@roleName" |
| Kod |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Ustawienia konfiguracji
Pobiera wartość ustawienia określonej konfiguracji.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/ RoleEnvironment/CurrentInstance/appSettings/ConfigurationSetting [@name="Setting1"]/@value" |
| Kod |Ustawienie var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Ścieżki do lokalnego magazynu
Pobiera ścieżkę lokalnej pamięci masowej dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name="LocalStore1"]/@path" |
| Kod |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). Właściwość RootPath; |

## <a name="local-storage-size"></a>Rozmiar magazynu lokalnego
Pobiera rozmiar magazynu lokalnego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name="LocalStore1"]/@sizeInMB" |
| Kod |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Punkt końcowy protokołu
Pobiera protokół punktu końcowego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/ RoleEnvironment/CurrentInstance/punkty końcowe/punktu końcowego [@name= 'Punk końcowy 1']/@protocol" |
| Kod |ochronę var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokół; |

## <a name="endpoint-ip"></a>Punkt końcowy IP
Pobiera określony punkt końcowy adres IP.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/ RoleEnvironment/CurrentInstance/punkty końcowe/punktu końcowego [@name= 'Punk końcowy 1']/@address" |
| Kod |adres var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Port punktu końcowego
Pobiera port punktu końcowego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| Wyrażenie XPath |wyrażenie XPath = "/ RoleEnvironment/CurrentInstance/punkty końcowe/punktu końcowego [@name= 'Punk końcowy 1']/@port" |
| Kod |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Przykład
Oto przykład roli procesu roboczego, który tworzy zadanie uruchamiania przy użyciu zmiennej środowiskowej o nazwie `TestIsEmulated` ustawioną [ @emulated wartość wyrażenia xpath](#app-running-in-emulator). 

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
Dowiedz się więcej o [pliku ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) pliku.

Utwórz [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) pakietu.

Włącz [pulpitu zdalnego](cloud-services-role-enable-remote-desktop-new-portal.md) dla roli.

