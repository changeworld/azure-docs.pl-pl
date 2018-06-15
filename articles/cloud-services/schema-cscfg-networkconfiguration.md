---
title: Konfiguracja sieci schematu usług w chmurze platformy Azure | Dokumentacja firmy Microsoft
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: 28
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: ebe81b2e4dea347eb22b173ff1e9baf1ee6bb75d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359649"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services schematu konfiguracji konfiguracja sieci

`NetworkConfiguration` Element pliku konfiguracji usługi określa wartości sieci wirtualnej i DNS. Te ustawienia są opcjonalne dla usług w chmurze.

Aby dowiedzieć się więcej o sieciach wirtualnych i skojarzonych schematów, można użyć następujących zasobów:

- [Schemat konfiguracji chmury usługi (klasyczne)](schema-cscfg-file.md)
- [Schematu definicji (klasyczny) usługi w chmurze](schema-csdef-file.md)
- [Tworzenie sieci wirtualnej (klasyczne)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Element Konfiguracja sieci
W poniższym przykładzie przedstawiono `NetworkConfiguration` elementu i jego elementów podrzędnych.

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
    <VirtualNetworkSite name="<site-name>"/>
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

W poniższej tabeli opisano elementy podrzędne `NetworkConfiguration` elementu.

| Element       | Opis |
| ------------- | ----------- |
| AccessControl | Opcjonalny. Określa zasady dostępu do punktów końcowych w usłudze w chmurze. Nazwa kontroli dostępu jest zdefiniowana przez ciąg dla `name` atrybutu. `AccessControl` Elementu zawiera jeden lub więcej `Rule` elementów. Więcej niż jeden `AccessControl` można zdefiniować elementu.|
| Reguła | Opcjonalny. Określa akcję, która należy do podsieci określony zakres adresów IP. Kolejność reguły jest definiowana za pomocą wartość ciągu dla `order` atrybutu. Im niższa reguły numer wyższy priorytet. Na przykład reguły może być określony o numerach porządkowych 100, 200 i 300. Reguły z numerem porządkowym 100 ma pierwszeństwo przed regułą, z kolejnością 200.<br /><br /> Akcji reguły jest definiowana za pomocą ciągu dla `action` atrybutu. Możliwe wartości:<br /><br /> -   `permit` — Określa, że tylko pakiety z zakresu określonej podsieci może komunikować się z punktem końcowym.<br />-   `deny` — Umożliwia określenie, jest odmowa dostępu do punktów końcowych z zakresu określonej podsieci.<br /><br /> Adresy zakresu adresów IP podsieci, które wpływają reguły są definiowane przez ciąg `remoteSubnet` atrybutu. Opis reguły jest definiowana za pomocą ciągu dla `description` atrybutu.|
| EndpointAcl | Opcjonalny. Określa przypisania zasady kontroli dostępu do punktu końcowego. Nazwa roli, która zawiera punkt końcowy jest zdefiniowany przez ciąg `role` atrybutu. Nazwa punktu końcowego jest zdefiniowany przez ciąg `endpoint` atrybutu. Nazwa zestawu `AccessControl` reguł, które mają być stosowane do punktu końcowego są zdefiniowane w parametrach dla `accessControl` atrybutu. Więcej niż jeden `EndpointAcl` można zdefiniować elementów.|
| Serwer DNS | Opcjonalny. Określa ustawienia dla serwera DNS. Można określić ustawienia dla serwerów DNS bez sieci wirtualnej. Nazwa serwera DNS jest zdefiniowana przez ciąg `name` atrybutu. Adres IP serwera DNS jest definiowana za pomocą ciągu dla `IPAddress` atrybutu. Adres IP musi być prawidłowym adresem IPv4.|
| VirtualNetworkSite | Opcjonalny. Określa nazwę lokacji sieci wirtualnej, w którym chcesz wdrożyć usługi w chmurze. To ustawienie nie powoduje utworzenia lokacji sieci wirtualnej. Odwołuje się on do lokacji, która została wcześniej zdefiniowana w pliku sieci dla sieci wirtualnej. Usługi w chmurze można tylko członek jedną sieć wirtualną. Jeśli nie określisz to ustawienie, nie można wdrożyć usługę w chmurze do sieci wirtualnej. Nazwa lokacji sieci wirtualnej jest zdefiniowany przez ciąg `name` atrybutu.|
| InstanceAddress | Opcjonalny. Określa skojarzenie roli do podsieci lub zestaw podsieci w sieci wirtualnej. Po skojarzeniu nazwę roli na adres wystąpienia można określić podsieci, do której ma zostać tej roli do skojarzenia. `InstanceAddress` Zawiera element podsieci. Nazwa roli, który jest skojarzony z podsiecią lub podsieci jest zdefiniowany przez ciąg `roleName` atrybutu.|
| Podsieć | Opcjonalny. Określa podsieci, który odpowiada nazwie podsieci w pliku konfiguracji sieci. Nazwa podsieci jest zdefiniowana przez ciąg `name` atrybutu.|
| Zastrzeżony adres IP | Opcjonalny. Określa zastrzeżonego adresu IP, która powinna być skojarzona z wdrożenia. Tworzenie zastrzeżonego adresu IP należy użyć do utworzenia zastrzeżonego adresu IP. Każdego wdrożenia w usłudze w chmurze może być skojarzony z jednym zastrzeżonego adresu IP. Nazwa zastrzeżonego adresu IP jest zdefiniowany przez ciąg `name` atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji chmury usługi (klasyczne)](schema-cscfg-file.md)