---
title: Azure Cloud Services def. NetworkTrafficRules schemat | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e6d156810b9fdee69ddac122eec06db7267ddf36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449038"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Schemat NetworkTrafficRules definicji Cloud Services platformy Azure
Węzeł `NetworkTrafficRules` to opcjonalny element w pliku definicji usługi, który określa, jak role komunikują się ze sobą. Ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych określonej roli. `NetworkTrafficRules` nie jest elementem autonomicznym; jest on połączony z co najmniej dwiema rolami w pliku definicji usługi.

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

> [!NOTE]
>  Węzeł `NetworkTrafficRules` jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Podstawowy schemat definicji usługi dla reguł ruchu sieciowego
Poniżej przedstawiono podstawowy format pliku definicji usługi zawierającego definicje ruchu sieciowego.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
Węzeł `NetworkTrafficRules` pliku definicji usługi zawiera te elementy, które opisano szczegółowo w kolejnych sekcjach tego tematu:

[NetworkTrafficRules, element](#NetworkTrafficRules)

[OnlyAllowTrafficTo, element](#OnlyAllowTrafficTo)

[Element Destinations](#Destinations)

[RoleEndpoint, element](#RoleEndpoint)

AllowAllTraffic, element

[WhenSource, element](#WhenSource)

[FromRole, element](#FromRole)

##  <a name="NetworkTrafficRules"></a>NetworkTrafficRules, element
`NetworkTrafficRules` element określa, które role mogą komunikować się z punktem końcowym w innej roli. Usługa może zawierać jedną `NetworkTrafficRules` definicji.

##  <a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo, element
Element `OnlyAllowTrafficTo` opisuje zbiór docelowych punktów końcowych i role, które mogą się z nimi komunikować. Można określić wiele węzłów `OnlyAllowTrafficTo`.

##  <a name="Destinations"></a>Element Destinations
Element `Destinations` opisuje zbiór RoleEndpoints niż można przekazać do programu.

##  <a name="RoleEndpoint"></a>RoleEndpoint, element
Element `RoleEndpoint` opisuje punkt końcowy roli, aby umożliwić komunikację z usługą. Można określić wiele elementów `RoleEndpoint`, jeśli w danej roli istnieje więcej niż jeden punkt końcowy.

| Atrybut      | Typ     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagany. Nazwa punktu końcowego, do którego ma być dozwolony ruch.|
| `roleName`     | `string` | Wymagany. Nazwa roli sieci Web, w której ma być dozwolona komunikacja.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic, element
Element `AllowAllTraffic` jest regułą, która umożliwia wszystkim rolom komunikowanie się z punktami końcowymi zdefiniowanymi w węźle `Destinations`.

##  <a name="WhenSource"></a>WhenSource, element
Element `WhenSource` opisuje zbiór ról niż może komunikować się z punktami końcowymi zdefiniowanymi w węźle `Destinations`.

| Atrybut | Typ     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagany. Określa regułę, która ma zostać zastosowana podczas zezwalania na komunikację. Jedyną prawidłową wartością jest obecnie `AnyRule`.|
  
##  <a name="FromRole"></a>FromRole, element
`FromRole` element określa role, które mogą komunikować się z punktami końcowymi zdefiniowanymi w węźle `Destinations`. Można określić wiele elementów `FromRole`, jeśli istnieje więcej niż jedna rola, która może komunikować się z punktami końcowymi.

| Atrybut  | Typ     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagany. Nazwa roli, z której ma być dozwolona komunikacja.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)




