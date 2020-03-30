---
title: Schemat przetwarzania usług w chmurze platformy Azure Def. NetworkTrafficRules | Dokumenty firmy Microsoft
description: Dowiedz się więcej o NetworkTrafficRules, który ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych roli. Łączy się z rolami w pliku definicji usługi.
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
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534732"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Schemat definicji usług w chmurze azure
Węzeł `NetworkTrafficRules` jest opcjonalnym elementem w pliku definicji usługi, który określa sposób komunikowania się ról ze sobą. Ogranicza, które role mogą uzyskać dostęp do wewnętrznych punktów końcowych określonej roli. Nie `NetworkTrafficRules` jest elementem autonomicznym; jest połączona z co najmniej dwiema rolami w pliku definicji usługi.

Domyślnym rozszerzeniem pliku definicji usługi jest .csdef.

> [!NOTE]
>  Węzeł `NetworkTrafficRules` jest dostępny tylko przy użyciu narzędzia Azure SDK w wersji 1.3 lub nowszej.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Podstawowy schemat definicji usługi dla reguł ruchu sieciowego
Podstawowy format pliku definicji usługi zawierającego definicje ruchu sieciowego jest następujący.

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
Węzeł `NetworkTrafficRules` pliku definicji usługi zawiera te elementy, opisane szczegółowo w kolejnych sekcjach w tym temacie:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Element docelowych](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

AllowAllTraffic Element

[WhenSource Element](#WhenSource)

[Element OdRola](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>NetworkTrafficRules Element
Element `NetworkTrafficRules` określa, które role mogą komunikować się z jakim punktem końcowym w innej roli. Usługa może zawierać `NetworkTrafficRules` jedną definicję.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo Element
Element `OnlyAllowTrafficTo` opisuje kolekcję punktów końcowych docelowych i role, które mogą komunikować się z nimi. Można określić `OnlyAllowTrafficTo` wiele węzłów.

##  <a name="destinations-element"></a><a name="Destinations"></a>Element docelowych
Element `Destinations` opisuje kolekcję RoleEndpoints niż mogą być komunikowane z.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>RoleEndpoint Element
Element `RoleEndpoint` opisuje punkt końcowy roli, aby umożliwić komunikację z. Można określić `RoleEndpoint` wiele elementów, jeśli istnieje więcej niż jeden punkt końcowy w roli.

| Atrybut      | Typ     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagany. Nazwa punktu końcowego, aby umożliwić ruch.|
| `roleName`     | `string` | Wymagany. Nazwa roli sieci web, aby umożliwić komunikację.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
Element `AllowAllTraffic` jest regułą, która umożliwia wszystkim role do komunikowania się z punktów końcowych zdefiniowanych w węźle. `Destinations`

##  <a name="whensource-element"></a><a name="WhenSource"></a>WhenSource Element
Element `WhenSource` opisuje zbiór ról niż może komunikować się z `Destinations` punktów końcowych zdefiniowanych w węźle.

| Atrybut | Typ     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagany. Określa regułę, która ma być stosowana podczas zezwalania na komunikację. Jedyną prawidłową wartością jest obecnie `AnyRule`.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>Element OdRola
Element `FromRole` określa role, które mogą komunikować się z `Destinations` punktami końcowymi zdefiniowanymi w węźle. Można określić `FromRole` wiele elementów, jeśli istnieje więcej niż jedna rola, która może komunikować się z punktami końcowymi.

| Atrybut  | Typ     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagany. Nazwa roli, z której ma być dozwolona komunikacja.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)




