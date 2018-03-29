---
title: Przypisz zasady dostępu do punktów końcowych usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisywanie zabezpieczeń zasady dostępu do punktów końcowych HTTP lub HTTPS w usłudze sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 65f47d238d4e591ddde8937d0eb3c55931c01c3f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Przypisać zasady zabezpieczeń dostępu dla punktów końcowych HTTP i HTTPS
Jeśli możesz zastosować zasady Uruchom jako i manifestu usługi deklaruje zasobów punkt końcowy HTTP, należy określić **SecurityAccessPolicy**.  **SecurityAccessPolicy** gwarantuje, że porty przydzielone z tymi punktami końcowymi poprawnie są ograniczone do konta użytkownika, który działa jako usługa. W przeciwnym razie **http.sys** nie ma dostępu do usługi i uzyskać awarii przy wywołaniach z klienta. Poniższy przykład dotyczy punktu końcowego o nazwie konto Customer1 **EndpointName**, które zapewnia pełne prawa dostępu.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Dla punktu końcowego protokołu HTTPS należy również wskazywać nazwę certyfikatu do zwrócenia do klienta. Możesz odwoływać się do certyfikatu przy użyciu **EndpointBindingPolicy**.  Certyfikat jest zdefiniowany w **certyfikaty** części dla manifest aplikacji.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Dalsze czynności przeczytaj następujące artykuły:
* [Zrozumienie model aplikacji](service-fabric-application-model.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
