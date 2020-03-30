---
title: Schemat konfiguracji sieci usług w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o elementach podrzędnych elementu Konfiguracja sieci pliku konfiguracji usługi, który określa wartości sieci wirtualnej i DNS.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529292"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schemat konfiguracji konfiguracji usługi w chmurze platformy Azure

Element `NetworkConfiguration` pliku konfiguracji usługi określa wartości sieci wirtualnej i DNS. Te ustawienia są opcjonalne dla usług w chmurze.

Aby dowiedzieć się więcej o sieciach wirtualnych i skojarzonych schematach, można użyć następującego zasobu:

- [Schemat konfiguracji usługi w chmurze (klasyczny)](schema-cscfg-file.md)
- [Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)
- [Tworzenie sieci wirtualnej (klasyczna)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Element konfiguracji sieci
Poniższy przykład `NetworkConfiguration` pokazuje element i jego elementy podrzędne.

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

W poniższej tabeli opisano `NetworkConfiguration` elementy podrzędne elementu.

| Element       | Opis |
| ------------- | ----------- |
| Kontrola dostępu | Element opcjonalny. Określa reguły dostępu do punktów końcowych w usłudze w chmurze. Nazwa kontroli dostępu jest zdefiniowana `name` przez ciąg dla atrybutu. Element `AccessControl` zawiera jeden `Rule` lub więcej elementów. Można zdefiniować więcej niż jeden `AccessControl` element.|
| Reguła | Element opcjonalny. Określa akcję, która powinna zostać podjęta dla określonego zakresu adresów IP podsieci. Kolejność reguły jest zdefiniowana przez wartość `order` ciągu dla atrybutu. Im niższy numer reguły, tym wyższy priorytet. Na przykład reguły można określić z numerami porządków 100, 200 i 300. Reguła o numerze zamówienia 100 ma pierwszeństwo przed regułą, która ma kolejność 200.<br /><br /> Akcja dla reguły jest zdefiniowana `action` przez ciąg dla atrybutu. Możliwe wartości:<br /><br /> -   `permit`– Określa, że tylko pakiety z określonego zakresu podsieci mogą komunikować się z punktem końcowym.<br />-   `deny`– Określa, że odmówiono dostępu do punktów końcowych w określonym zakresie podsieci.<br /><br /> Zakres podsieci adresów IP, których dotyczy reguła, `remoteSubnet` jest definiowany przez ciąg dla atrybutu. Opis reguły jest zdefiniowany przez `description` ciąg dla atrybutu.|
| Punkt końcowyAcl | Element opcjonalny. Określa przypisanie reguł kontroli dostępu do punktu końcowego. Nazwa roli, która zawiera punkt końcowy jest zdefiniowana `role` przez ciąg dla atrybutu. Nazwa punktu końcowego jest zdefiniowana przez `endpoint` ciąg dla atrybutu. Nazwa zestawu `AccessControl` reguł, które powinny być stosowane do punktu końcowego są `accessControl` zdefiniowane w ciągu dla atrybutu. Można zdefiniować więcej niż jeden `EndpointAcl` element.|
| Serwer Dns | Element opcjonalny. Określa ustawienia serwera DNS. Można określić ustawienia dla serwerów DNS bez sieci wirtualnej. Nazwa serwera DNS jest definiowana przez `name` ciąg dla atrybutu. Adres IP serwera DNS jest definiowany przez `IPAddress` ciąg dla atrybutu. Adres IP musi być prawidłowym adresem IPv4.|
| Wirtualna siećsite | Element opcjonalny. Określa nazwę witryny sieci wirtualnej, w której chcesz wdrożyć usługę w chmurze. To ustawienie nie powoduje utworzenia witryny sieci wirtualnej. Odwołuje się do lokacji, która została wcześniej zdefiniowana w pliku sieciowym dla sieci wirtualnej. Usługa w chmurze może być tylko członkiem jednej sieci wirtualnej. Jeśli to ustawienie nie zostanie określone, usługa w chmurze nie zostanie wdrożona w sieci wirtualnej. Nazwa witryny sieci wirtualnej jest zdefiniowana `name` przez ciąg dla atrybutu.|
| Adres wystąpienia | Element opcjonalny. Określa powiązanie roli z podsiecią lub zestawem podsieci w sieci wirtualnej. Po skojarzeniu nazwy roli z adresem wystąpienia można określić podsieci, z którymi ma być skojarzona ta rola. Zawiera `InstanceAddress` podsieci element. Nazwa roli skojarzonej z podsiecią lub podsieciami jest `roleName` definiowana przez ciąg dla atrybutu.|
| Podsieć | Element opcjonalny. Określa podsieć odpowiadającą nazwie podsieci w pliku konfiguracji sieci. Nazwa podsieci jest zdefiniowana przez `name` ciąg dla atrybutu.|
| ZastrzeżoneIP | Element opcjonalny. Określa zastrzeżony adres IP, który powinien być skojarzony z wdrożeniem. Aby utworzyć zastrzeżony adres IP, należy użyć funkcji Utwórz zarezerwowany adres IP. Każde wdrożenie w usłudze w chmurze może być skojarzone z jednym zastrzeżonym adresem IP. Nazwa zastrzeżonego adresu IP jest definiowana `name` przez ciąg dla atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasyczny)](schema-cscfg-file.md)
