---
title: Usługi Azure Cloud Services Def. Networktrafficrules — schemat | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 71c791c9ac6f679f0f67b014c8fb5dd915d1a3e3
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004408"
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

[Networktrafficrules — Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Element miejsc docelowych](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

[AllowAllTraffic Element](#AllowAllTraffic)

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

| Atrybut      | Typ     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagany. Nazwa punktu końcowego, aby zezwolić na ruch.|
| `roleName`     | `string` | Wymagany. Nazwa roli sieci web, aby umożliwić komunikację.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
`AllowAllTraffic` Element jest regułę zezwalającą na wszystkich ról w celu komunikowania się z punktów końcowych zdefiniowanych w `Destinations` węzła.

##  <a name="WhenSource"></a> WhenSource Element
`WhenSource` Element opisuje kolekcję ról nie może komunikować się z punktów końcowych zdefiniowanych w `Destinations` węzła.

| Atrybut | Typ     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagany. Określa reguła była stosowana w przypadku zezwalania komunikacji. Jedyna prawidłowa wartość jest obecnie `AnyRule`.|
  
##  <a name="FromRole"></a> Wartości elementów FromRole — Element
`FromRole` Element określa role, które mogą komunikować się z punktów końcowych zdefiniowanych w `Destinations` węzła. Można określić wiele `FromRole` elementów, jeśli istnieje więcej niż jednej roli, który może komunikować się z punktami końcowymi.

| Atrybut  | Typ     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagany. Nazwa roli do zezwalania na komunikację.|

## <a name="see-also"></a>Zobacz też
[Chmury usługi (model klasyczny) definicji schematu](schema-csdef-file.md)