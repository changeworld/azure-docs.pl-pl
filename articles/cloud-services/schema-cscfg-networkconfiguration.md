---
title: Azure Cloud Services NetworkConfiguration Schema | Microsoft Docs
ms.custom: ''
origin.date: 12/07/2016
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: 28
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: fb833904502c0c42b46201fd46a368de0376277c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130272"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services Config NetworkConfiguration Schema

`NetworkConfiguration` Element pliku konfiguracji usługi określa wartości na sieć wirtualną i DNS. Te ustawienia są opcjonalne dla usług w chmurze.

Aby dowiedzieć się więcej na temat sieci wirtualnych i skojarzonych schematów, można użyć następujących zasobów:

- [Schemat konfiguracji chmury usługi (model klasyczny)](schema-cscfg-file.md)
- [Chmury usługi (model klasyczny) definicji schematu](schema-csdef-file.md)
- [Tworzenie sieci wirtualnej (model klasyczny)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Element Konfiguracja sieci
W poniższym przykładzie przedstawiono `NetworkConfiguration` elementu i jego elementy podrzędne.

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
| AccessControl | Opcjonalny. Określa reguły, aby uzyskać dostęp do punktów końcowych w usłudze w chmurze. Nazwa kontroli dostępu jest definiowany przez ciąg `name` atrybutu. `AccessControl` Elementu zawiera jeden lub więcej `Rule` elementów. Więcej niż jeden `AccessControl` można zdefiniować element.|
| Reguła | Opcjonalny. Określa akcję, które powinny zostać podjęte dla określonej podsieci zakres adresów IP. Kolejność reguły jest definiowany przez wartość ciągu dla `order` atrybutu. Im niższa reguły numer wyższy priorytet. Na przykład reguły można określić o numerach porządkowych 100, 200 i 300. Reguła z numerem porządkowym 100 mają pierwszeństwo przed reguły z kolejnością 200.<br /><br /> Akcja reguły jest definiowany przez ciąg `action` atrybutu. Możliwe wartości:<br /><br /> -   `permit` — Określa, że tylko pakiety z zakresu określonej podsieci, może komunikować się z punktem końcowym.<br />-   `deny` — Umożliwia określenie odmowa dostępu do punktów końcowych w zakresie określonej podsieci.<br /><br /> Adresy zakresu adresów IP podsieci, których dotyczy reguła są definiowane przez ciąg `remoteSubnet` atrybutu. Opis reguły jest definiowany przez ciąg `description` atrybutu.|
| EndpointAcl | Opcjonalny. Określa przypisanie zasad kontroli dostępu do punktu końcowego. Nazwa roli, która zawiera punkt końcowy jest definiowany przez ciąg `role` atrybutu. Nazwa punktu końcowego jest definiowany przez ciąg `endpoint` atrybutu. Nazwa zestawu `AccessControl` reguł, które mają być stosowane do punktu końcowego są zdefiniowane w ciągu uzyskać `accessControl` atrybutu. Więcej niż jeden `EndpointAcl` można definiować elementów.|
| DnsServer | Opcjonalny. Określa ustawienia dla serwera DNS. Możesz określić ustawienia dla serwerów DNS bez sieci wirtualnej. Nazwa serwera DNS jest definiowany przez ciąg `name` atrybutu. Adres IP serwera DNS jest definiowany przez ciąg `IPAddress` atrybutu. Adres IP musi być prawidłowym adresem IPv4.|
| VirtualNetworkSite | Opcjonalny. Określa nazwę lokacji sieci wirtualnej, w której chcesz wdrożyć usługę w chmurze. To ustawienie nie powoduje utworzenia wirtualnej lokacji sieciowej. Odwołuje się lokacji, która została wcześniej zdefiniowana w pliku sieci dla sieci wirtualnej. Usługa w chmurze można tylko członek jednej sieci wirtualnej. Jeśli to ustawienie nie jest określona, nie można wdrożyć usługę w chmurze z siecią wirtualną. Nazwa lokacji sieci wirtualnej jest definiowany przez ciąg `name` atrybutu.|
| InstanceAddress | Opcjonalny. Określa skojarzenie roli z podsiecią lub zestaw podsieci w sieci wirtualnej. Po skojarzeniu nazwę roli na adres wystąpienia można określić podsieci, z którymi chce się z tą rolą mają być skojarzone. `InstanceAddress` Zawiera element podsieci. Nazwa roli, który jest skojarzony z podsiecią lub podsieci jest definiowany przez ciąg `roleName` atrybutu.|
| Podsieć | Opcjonalny. Określa podsieć, która odnosi się do nazwy podsieci w pliku konfiguracji sieci. Nazwa podsieci jest definiowany przez ciąg `name` atrybutu.|
| ReservedIP | Opcjonalny. Określa zastrzeżonego adresu IP, które powinny być skojarzone z wdrożeniem. Utwórz zastrzeżony adres IP musi być tworzenie zastrzeżonego adresu IP. Każde wdrożenie w usłudze w chmurze może być skojarzony z jednego zastrzeżonego adresu IP. Nazwa zastrzeżonego adresu IP jest definiowany przez ciąg `name` atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji chmury usługi (model klasyczny)](schema-cscfg-file.md)