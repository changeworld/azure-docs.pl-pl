---
title: Schemat NetworkConfiguration platformy Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: cacc8b1f2909965594fdf0d841963e792acf648c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385428"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schemat NetworkConfiguration usługi Azure Cloud Services config

`NetworkConfiguration` element pliku konfiguracji usługi określa Virtual Network i wartości DNS. Te ustawienia są opcjonalne dla usług Cloud Services.

Aby dowiedzieć się więcej o sieciach wirtualnych i skojarzonych schematach, można użyć poniższego zasobu:

- [Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)
- [Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)
- [Tworzenie Virtual Network (klasyczny)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration, element
Poniższy przykład pokazuje element `NetworkConfiguration` i jego elementy podrzędne.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

W poniższej tabeli opisano elementy potomne elementu `NetworkConfiguration`.

| Element       | Opis |
| ------------- | ----------- |
| AccessControl | Element opcjonalny. Określa reguły dostępu do punktów końcowych w usłudze w chmurze. Nazwa kontroli dostępu jest definiowana przez ciąg dla atrybutu `name`. Element `AccessControl` zawiera jeden lub więcej elementów `Rule`. Można zdefiniować więcej niż jeden element `AccessControl`.|
| Reguła | Element opcjonalny. Określa akcję, która ma zostać wykonana dla określonego zakresu podsieci adresów IP. Kolejność reguły jest definiowana przez wartość ciągu dla atrybutu `order`. Dolna reguła ma wyższy priorytet. Na przykład można określić reguły o numerach porządkowych 100, 200 i 300. Reguła o numerze porządkowym 100 ma pierwszeństwo przed regułą z kolejnością 200.<br /><br /> Akcja dla reguły jest definiowana przez ciąg dla atrybutu `action`. Możliwe wartości:<br /><br /> -   `permit` — określa, że tylko pakiety z określonego zakresu podsieci mogą komunikować się z punktem końcowym.<br />-   `deny` — określa, że dostęp do punktów końcowych w określonym zakresie podsieci zostanie odrzucony.<br /><br /> Zakres podsieci adresów IP, na które ma wpływ reguła, jest definiowany przez ciąg dla atrybutu `remoteSubnet`. Opis reguły jest definiowany przez ciąg dla atrybutu `description`.|
| EndpointAcl | Element opcjonalny. Określa przypisanie reguł kontroli dostępu do punktu końcowego. Nazwa roli zawierającej punkt końcowy jest definiowana przez ciąg dla atrybutu `role`. Nazwa punktu końcowego jest definiowana przez ciąg dla atrybutu `endpoint`. Nazwa zestawu reguł `AccessControl`, które mają być stosowane do punktu końcowego, są definiowane w ciągu dla atrybutu `accessControl`. Można zdefiniować więcej niż jeden element `EndpointAcl`.|
| Serwer DNS | Element opcjonalny. Określa ustawienia dla serwera DNS. Możesz określić ustawienia dla serwerów DNS bez Virtual Network. Nazwa serwera DNS jest definiowana przez ciąg dla atrybutu `name`. Adres IP serwera DNS jest definiowany przez ciąg dla atrybutu `IPAddress`. Adres IP musi być prawidłowym adresem IPv4.|
| VirtualNetworkSite | Element opcjonalny. Określa nazwę witryny Virtual Network, w której ma zostać wdrożona usługa w chmurze. To ustawienie nie powoduje utworzenia witryny Virtual Network. Odwołuje się do lokacji, która została wcześniej zdefiniowana w pliku sieciowym dla Virtual Network. Usługa w chmurze może być tylko składową jednego Virtual Network. Jeśli to ustawienie nie zostanie określone, usługa w chmurze nie zostanie wdrożona do Virtual Network. Nazwa witryny Virtual Network jest definiowana przez ciąg dla atrybutu `name`.|
| InstanceAddress | Element opcjonalny. Określa skojarzenie roli z podsiecią lub zestawem podsieci w Virtual Network. W przypadku kojarzenia nazwy roli z adresem wystąpienia można określić podsieci, do których ta rola ma być skojarzona. `InstanceAddress` zawiera element podsieci. Nazwa roli skojarzonej z podsiecią lub podsieciami jest definiowana przez ciąg `roleName` atrybutu.|
| Podsieć | Element opcjonalny. Określa podsieć zgodną z nazwą podsieci w pliku konfiguracyjnym sieci. Nazwa podsieci jest definiowana przez ciąg dla atrybutu `name`.|
| ReservedIP | Element opcjonalny. Określa zastrzeżony adres IP, który ma zostać skojarzony z wdrożeniem. Aby utworzyć zastrzeżony adres IP, należy użyć Utwórz adres Zastrzeżony adres IP. Każde wdrożenie w usłudze w chmurze może być skojarzone z jednym zastrzeżonym adresem IP. Nazwa zastrzeżonego adresu IP jest definiowana przez ciąg dla atrybutu `name`.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)
