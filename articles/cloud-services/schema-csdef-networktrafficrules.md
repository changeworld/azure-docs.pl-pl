---
title: Azure Cloud Services Def. NetworkTrafficRules Schema | Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 8925943b0a5d151d55adedcfe3f01b5a14c63c1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613883"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services, definicja networktrafficrules — schemat
`NetworkTrafficRules` Węzeł jest elementem opcjonalnym w pliku definicji usługi, która określa, jak role komunikują się ze sobą. Limity, które role mogą uzyskiwać dostęp do wewnętrznych punktów końcowych w określonej roli. `NetworkTrafficRules` Nie jest elementem autonomiczny; jest połączona z dwóch lub więcej ról w pliku definicji usługi.

Domyślnym rozszerzeniem dla pliku definicji usługi jest csdef.

> [!NOTE]
>  `NetworkTrafficRules` Węzła jest dostępne tylko przy użyciu zestawu Azure SDK w wersji 1.3 lub nowszej.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Schemat definicji usług w warstwie podstawowa dla reguły ruchu sieciowego
Podstawowy format pliku definicji usługi, zawierający definicje ruchu sieciowego jest w następujący sposób.

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
`NetworkTrafficRules` Węzła pliku definicji usługi zawiera te elementy, opisano szczegółowo w kolejnych sekcjach tego tematu:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Element miejsc docelowych](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

AllowAllTraffic Element

[WhenSource Element](#WhenSource)

[Wartości elementów FromRole — Element](#FromRole)

##  <a name="NetworkTrafficRules"></a> Networktrafficrules — Element
`NetworkTrafficRules` Element określa, które role mogą komunikować się z którym punktem końcowym w innej roli. Usługa może zawierać jeden `NetworkTrafficRules` definicji.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo Element
`OnlyAllowTrafficTo` Element w tym artykule opisano kolekcji docelowych punktów końcowych i role, które mogą komunikować się z nimi. Można określić wiele `OnlyAllowTrafficTo` węzłów.

##  <a name="Destinations"></a> Element miejsc docelowych
`Destinations` Element opisuje kolekcję RoleEndpoints nie mogą być przekazywane za pomocą.

##  <a name="RoleEndpoint"></a> RoleEndpoint Element
`RoleEndpoint` Element w tym artykule opisano punktu końcowego dla roli, aby umożliwić komunikację z. Można określić wiele `RoleEndpoint` elementu, jeśli istnieje więcej niż jeden punkt końcowy dla danej roli.

| Atrybut      | Type     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagany. Nazwa punktu końcowego, aby zezwolić na ruch.|
| `roleName`     | `string` | Wymagany. Nazwa roli sieci web, aby umożliwić komunikację.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
`AllowAllTraffic` Element jest regułę zezwalającą na wszystkich ról w celu komunikowania się z punktów końcowych zdefiniowanych w `Destinations` węzła.

##  <a name="WhenSource"></a> WhenSource Element
`WhenSource` Element opisuje kolekcję ról nie może komunikować się z punktów końcowych zdefiniowanych w `Destinations` węzła.

| Atrybut | Type     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagany. Określa reguła była stosowana w przypadku zezwalania komunikacji. Jedyna prawidłowa wartość jest obecnie `AnyRule`.|
  
##  <a name="FromRole"></a> Wartości elementów FromRole — Element
`FromRole` Element określa role, które mogą komunikować się z punktów końcowych zdefiniowanych w `Destinations` węzła. Można określić wiele `FromRole` elementów, jeśli istnieje więcej niż jednej roli, który może komunikować się z punktami końcowymi.

| Atrybut  | Type     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagany. Nazwa roli do zezwalania na komunikację.|

## <a name="see-also"></a>Zobacz też
[Chmury usługi (model klasyczny) definicji schematu](schema-csdef-file.md)
