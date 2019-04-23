---
title: Chmura roli usługi konfiguracji XPath cheat-sheet | Dokumentacja firmy Microsoft
description: Różne ustawienia języka XPath można w konfiguracji roli usługi chmury uwidaczniają ustawienia jako zmienną środowiskową.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 53a262af421dd986e6b70af173a6e8b3f7c06f64
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798763"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Udostępnianie ustawień konfiguracji roli jako zmienną środowiskową za pomocą wyrażenia XPath
W proces roboczy usług chmury lub w pliku definicji usługi roli sieci web mogą uwidocznić wartości konfiguracji środowiska uruchomieniowego, jako zmienne środowiskowe. Obsługiwane są następujące wartości XPath, (które odnoszą się do wartości interfejsu API).

Dostępne są także te wartości XPath [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) biblioteki. 

## <a name="app-running-in-emulator"></a>Aplikacja uruchomiona w emulatorze
Wskazuje, że aplikacja jest uruchomiona w emulatorze.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Kod |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Identyfikator wdrożenia
Pobiera identyfikator wdrożenia dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Kod |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Identyfikator roli
Pobiera bieżący identyfikator roli dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Kod |Identyfikator var = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizowanie domeny
Pobiera domena aktualizacji wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kod |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domena błędów
Pobiera wystąpienia domeny błędów.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kod |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nazwa roli
Pobiera nazwę roli wystąpień.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Kod |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Ustawienia konfiguracji
Pobiera wartość ustawienia konfiguracji.

| Typ | Przykład |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Kod |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Ścieżka magazynu lokalnego
Pobiera ścieżkę magazynu lokalnego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |wyrażenie XPath = "/ RoleEnvironment/wartość parametru CurrentInstance/LocalResources/LocalResource [@name="LocalStore1"]/@path" |
| Kod |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). Właściwość RootPath; |

## <a name="local-storage-size"></a>Rozmiar magazynu lokalnego
Pobiera informacje o rozmiarze magazynu lokalnego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |wyrażenie XPath = "/ RoleEnvironment/wartość parametru CurrentInstance/LocalResources/LocalResource [@name="LocalStore1"]/@sizeInMB" |
| Kod |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Punkt końcowy protokołu
Pobiera protokół punktu końcowego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |wyrażenie XPath = "/ RoleEnvironment/wartość parametru CurrentInstance/punkty końcowe/punktu końcowego [@name= 'Punk końcowy 1']/@protocol" |
| Kod |Port var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokół; |

## <a name="endpoint-ip"></a>Punkt końcowy IP
Pobiera określony punkt końcowy adres IP.

| Typ | Przykład |
| --- | --- |
| XPath |wyrażenie XPath = "/ RoleEnvironment/wartość parametru CurrentInstance/punkty końcowe/punktu końcowego [@name= 'Punk końcowy 1']/@address" |
| Kod |adres var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Port punktu końcowego
Pobiera port punktu końcowego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |wyrażenie XPath = "/ RoleEnvironment/wartość parametru CurrentInstance/punkty końcowe/punktu końcowego [@name= 'Punk końcowy 1']/@port" |
| Kod |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Przykład
Oto przykład roli procesu roboczego, który tworzy zadanie uruchamiania przy użyciu zmiennej środowiskowej o nazwie `TestIsEmulated` równa [ @emulated wartość wyrażenia xpath](#app-running-in-emulator). 

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

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) pliku.

Tworzenie [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) pakietu.

Włącz [pulpitu zdalnego](cloud-services-role-enable-remote-desktop-new-portal.md) dla roli.

