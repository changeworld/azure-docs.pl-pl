---
title: Przypisywanie zasad dostępu do punktów końcowych usługi
description: Dowiedz się, jak przypisać zasady dostępu zabezpieczeń do punktów końcowych HTTP lub HTTPS w usłudze sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614659"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Przypisywanie zasad dostępu do zabezpieczeń dla punktów końcowych HTTP i HTTPS
W przypadku zastosowania zasady uruchamiania, a manifest usługi deklaruje zasoby punktu końcowego HTTP, należy określić **SecurityAccessPolicy**.  **SecurityAccessPolicy** zapewnia, że porty przydzielone do tych punktów końcowych są poprawnie ograniczone do konta użytkownika, które usługa jest uruchamiana jako. W przeciwnym razie **http.sys** nie ma dostępu do usługi i otrzymasz błędy z wywołaniami od klienta. Poniższy przykład stosuje konto Customer1 do punktu końcowego o nazwie **EndpointName**, co daje mu pełne prawa dostępu.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Dla punktu końcowego HTTPS należy również wskazać nazwę certyfikatu, aby powrócić do klienta. Odwołanie się do certyfikatu przy użyciu **endpointbindingpolicy**.  Certyfikat jest zdefiniowany w sekcji **Certyfikaty** manifestu aplikacji.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Korzystając z protokołu HTTPS, nie należy używać tego samego portu i certyfikatu dla różnych wystąpień usługi (niezależnie od aplikacji) wdrożonych w tym samym węźle. Uaktualnienie dwóch różnych usług przy użyciu tego samego portu w różnych wystąpieniach aplikacji spowoduje niepowodzenie uaktualnienia. Aby uzyskać więcej informacji, zobacz [Uaktualnianie wielu aplikacji za pomocą punktów końcowych HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Aby uzyskać następujące kroki, przeczytaj następujące artykuły:
* [Opis modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
