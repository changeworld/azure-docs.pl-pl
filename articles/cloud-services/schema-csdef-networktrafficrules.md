---
title: Domyślna usługi w chmurze Azure Schemat NetworkTrafficRules | Dokumentacja firmy Microsoft
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 779d3b42aeab04bb93756439a0482f32ade6557e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Definicja schematu NetworkTrafficRules usług w chmurze Azure
`NetworkTrafficRules` Węzeł jest opcjonalny element w pliku definicji usługi, która określa, jak role komunikują się ze sobą. Ogranicza role mogą uzyskiwać dostęp do wewnętrznych punktów końcowych w określonej roli. `NetworkTrafficRules` Nie jest elementem autonomiczny; jest połączona z dwóch lub większej liczby ról w pliku definicji usługi.

Domyślne rozszerzenie pliku definicji usługi jest csdef.

> [!NOTE]
>  `NetworkTrafficRules` Węzeł jest dostępne wyłącznie przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Podstawowa usługa definicji schematu dla reguły ruchu sieciowego
Podstawowy format pliku definicji usługi definicjami ruchu sieciowego ma następującą składnię.

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
`NetworkTrafficRules` Tych elementów, opisano szczegółowo w kolejnych sekcjach tego tematu zawiera węzeł pliku definicji usługi:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Element miejsc docelowych](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

[AllowAllTraffic Element](#AllowAllTraffic)

[WhenSource Element](#WhenSource)

[FromRole Element](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules Element
`NetworkTrafficRules` Określa element role mogą komunikować się z którym punktem końcowym na inną rolą. Usługa może zawierać jeden `NetworkTrafficRules` definicji.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo Element
`OnlyAllowTrafficTo` Element opisuje kolekcję punktów końcowych docelowego i role, które mogą komunikować się z nimi. Można określić wiele `OnlyAllowTrafficTo` węzłów.

##  <a name="Destinations"></a> Element miejsc docelowych
`Destinations` Element opisuje kolekcję RoleEndpoints nie może być komunikowały się z.

##  <a name="RoleEndpoint"></a> RoleEndpoint Element
`RoleEndpoint` Element opisano punkt końcowy dla roli, aby umożliwić komunikację z. Można określić wiele `RoleEndpoint` elementu, jeśli istnieje więcej niż jeden punkt końcowy dla danej roli.

| Atrybut      | Typ     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagany. Nazwa punktu końcowego, aby zezwalać na ruch.|
| `roleName`     | `string` | Wymagany. Nazwa roli sieci web, aby umożliwić komunikację.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
`AllowAllTraffic` Element jest regułę zezwalającą wszystkich ról do komunikowania się z punktów końcowych zdefiniowanych w `Destinations` węzła.

##  <a name="WhenSource"></a> WhenSource Element
`WhenSource` Element opisuje kolekcję ról nie może komunikować się z punktów końcowych zdefiniowanych w `Destinations` węzła.

| Atrybut | Typ     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagany. Określa reguły do zastosowania w przypadku zezwalania na komunikację. Jedyną prawidłową wartością jest obecnie `AnyRule`.|
  
##  <a name="FromRole"></a> FromRole Element
`FromRole` Element określa role, które może komunikować się z punktów końcowych zdefiniowanych w `Destinations` węzła. Można określić wiele `FromRole` elementu, jeśli istnieje więcej niż jednej roli, który może komunikować się z punktami końcowymi.

| Atrybut  | Typ     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagany. Nazwa roli do zezwalania na komunikację.|

## <a name="see-also"></a>Zobacz też
[Schematu definicji (klasyczny) usługi w chmurze](schema-csdef-file.md)