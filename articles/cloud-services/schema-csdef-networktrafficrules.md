---
title: Platforma Azure Cloud Services def. Schemat NetworkTrafficRules | Microsoft Docs
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
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: e99b9f0f601841fe6ff32eba0a43bfafd652e941
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945947"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Schemat NetworkTrafficRules definicji Cloud Services platformy Azure
`NetworkTrafficRules` Węzeł jest opcjonalnym elementem w pliku definicji usługi, który określa, jak role komunikują się ze sobą. Ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych określonej roli. Nie `NetworkTrafficRules` jest elementem autonomicznym; jest on połączony z co najmniej dwiema rolami w pliku definicji usługi.

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

> [!NOTE]
>  `NetworkTrafficRules` Węzeł jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

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
`NetworkTrafficRules` Węzeł pliku definicji usługi zawiera te elementy, które opisano szczegółowo w kolejnych sekcjach w tym temacie:

[NetworkTrafficRules, element](#NetworkTrafficRules)

[OnlyAllowTrafficTo, element](#OnlyAllowTrafficTo)

[Element Destinations](#Destinations)

[RoleEndpoint, element](#RoleEndpoint)

AllowAllTraffic, element

[WhenSource, element](#WhenSource)

[FromRole, element](#FromRole)

##  <a name="NetworkTrafficRules"></a>NetworkTrafficRules, element
`NetworkTrafficRules` Element określa, które role mogą komunikować się z punktem końcowym w innej roli. Usługa może zawierać jedną `NetworkTrafficRules` definicję.

##  <a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo, element
`OnlyAllowTrafficTo` Element opisuje zbiór docelowych punktów końcowych i role, które mogą się z nimi komunikować. Można określić wiele `OnlyAllowTrafficTo` węzłów.

##  <a name="Destinations"></a>Element Destinations
`Destinations` Element zawiera opis kolekcji RoleEndpoints niż może być przekazywany za pomocą.

##  <a name="RoleEndpoint"></a>RoleEndpoint, element
`RoleEndpoint` Element opisuje punkt końcowy roli, aby zezwalać na komunikację z usługą. Można określić wiele `RoleEndpoint` elementów, jeśli w danej roli istnieje więcej niż jeden punkt końcowy.

| Atrybut      | Type     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagane. Nazwa punktu końcowego, do którego ma być dozwolony ruch.|
| `roleName`     | `string` | Wymagana. Nazwa roli sieci Web, w której ma być dozwolona komunikacja.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic, element
Element jest regułą, która umożliwia wszystkim rolom komunikowanie się z punktami końcowymi `Destinations` zdefiniowanymi w węźle. `AllowAllTraffic`

##  <a name="WhenSource"></a>WhenSource, element
Element opisuje zbiór ról niż może komunikować się z punktami końcowymi zdefiniowanymi `Destinations` w węźle. `WhenSource`

| Atrybut | Type     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagane. Określa regułę, która ma zostać zastosowana podczas zezwalania na komunikację. Jedyną prawidłową wartością jest obecnie `AnyRule`.|
  
##  <a name="FromRole"></a>FromRole, element
Element określa role, które mogą komunikować się z punktami końcowymi zdefiniowanymi `Destinations` w węźle. `FromRole` Można określić wiele `FromRole` elementów, jeśli istnieje więcej niż jedna rola, która może komunikować się z punktami końcowymi.

| Atrybut  | Type     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagane. Nazwa roli, z której ma być dozwolona komunikacja.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)
