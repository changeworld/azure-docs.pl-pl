---
title: Przypisywanie zasad dostępu do punktów końcowych usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać zabezpieczeń zasady dostępu do protokołu HTTP lub HTTPS punktów końcowych w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614187"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS
Jeśli zastosujesz zasady Uruchom jako i manifestu usługi deklaruje zasobów punkt końcowy HTTP, należy określić **SecurityAccessPolicy**.  **SecurityAccessPolicy** gwarantuje, że porty przydzielone do tych punktów końcowych poprawnie są ograniczone do konta użytkownika, który działa jako usługa. W przeciwnym razie **http.sys** ma dostęp do usługi i uzyskujesz błędy z wywołania od klienta. Poniższy przykład dotyczy punktu końcowego o nazwie konto serwer Customer1 **Nazwapunktukoncowego**, co daje pełne prawa dostępu.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Dla punktu końcowego protokołu HTTPS należy również wskazywać nazwę certyfikatu, aby powrócić do klienta. Możesz odwoływać się do certyfikatu za pomocą **EndpointBindingPolicy**.  Certyfikat jest zdefiniowany w **certyfikaty** części manifestu aplikacji.

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
> Przy użyciu protokołu HTTPS, nie należy używać tych samych portów i certyfikatu dla wystąpień różne usługi (niezależnie od aplikacji), które zostały wdrożone na tym samym węźle. Uaktualnianie dwóch różnych usług za pomocą tego samego portu w różne wystąpienia aplikacji spowoduje niepowodzenia uaktualnienia. Aby uzyskać więcej informacji, zobacz [uaktualniania wielu aplikacji za pomocą punktów końcowych HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Dalsze czynności przeczytaj następujące artykuły:
* [Informacje o modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
