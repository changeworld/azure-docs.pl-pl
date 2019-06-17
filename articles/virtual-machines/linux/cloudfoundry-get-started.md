---
title: Wprowadzenie do usługi Cloud Foundry na platformie Microsoft Azure | Dokumentacja firmy Microsoft
description: Uruchom OSS lub rozwiązania Pivotal Cloud Foundry na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 68ae01b814de08098c0ba6b5713f420cfebc3d97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127031"
---
# <a name="cloud-foundry-on-azure"></a>Usługa Cloud Foundry na platformie Azure

Cloud Foundry to platforma jako usługa (PaaS) typu „open source” służąca do tworzenia, wdrażania i obsługi aplikacji 12-factor opracowanych w różnych językach i strukturach. W tym dokumencie opisano opcje, które mają do uruchamiania usługi Cloud Foundry na platformie Azure i jak możesz rozpocząć pracę.

## <a name="cloud-foundry-offerings"></a>Oferty Foundry w chmurze

Istnieją dwa rodzaje Cloud Foundry dostępna do uruchamiania na platformie Azure: open source Cloud Foundry (OSS CF) i Pivotal Cloud Foundry (PCF). OSS CF jest całkowicie [typu open-source](https://github.com/cloudfoundry) wersji usługi Cloud Foundry zarządzane przez program Cloud Foundry Foundation. Rozwiązanie Pivotal Cloud Foundry to dystrybucję enterprise Cloud Foundry firmy Pivotal Inc. oprogramowania Przyjrzymy się niektóre różnice między dwoma ofert.

### <a name="open-source-cloud-foundry"></a>Rozwiązania typu open-source Cloud Foundry

W celu wdrożenia OSS Cloud Foundry na platformie Azure najpierw wdrażanie Dyrektor BOSH, a następnie wdrożenie usługi Cloud Foundry przy użyciu [instrukcji udostępnionych w serwisie GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Aby dowiedzieć się więcej o korzystaniu z usługi CF OSS, zobacz [dokumentacji](https://docs.cloudfoundry.org/) dostarczane przez program Cloud Foundry Foundation.

Firma Microsoft oferuje obsługę największej staranności dla usługi CF OSS za pośrednictwem poniższych kanałów społeczności:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>kanał bosh-azure-cpi na [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [listy adresowej CF bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub generuje dla [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) i [programu service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Poziom obsługi zasobów platformy Azure, takie jak maszyny wirtualne, na którym uruchamiasz Cloud Foundry zależy od umowy dotyczącej pomocy technicznej platformy Azure. Pomoc techniczna w społeczności optymalnych dotyczy tylko składniki Foundry specyficzne dla chmury.

### <a name="pivotal-cloud-foundry"></a>Uruchamianie usługi Pivotal Cloud Foundry

Usługa Pivotal Cloud Foundry zawiera tę samą platformę podstawowe, jak w przypadku dystrybucji OSS wraz z zestawem narzędzi do zarządzania własnościowych i pomoc techniczną dla przedsiębiorstw. Rozwiązanie PCF działające na platformie Azure, konieczne jest uzyskanie licencji firmy Pivotal. Oferta PCF z witryny Azure marketplace zawiera 90-dniowej licencji próbnej.

Narzędzia te obejmują [Pivotal programu Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), aplikacji sieci web, która upraszcza wdrażanie i zarządzanie nimi Cloud Foundry foundation i [Pivotal Menedżera aplikacji](https://docs.pivotal.io/pivotalcf/console/), aplikacji sieci web do zarządzania Użytkownicy i aplikacje.

Oprócz kanałów pomocy technicznej dla usługi OSS CF powyżej licencji PCF uprawnia do kontaktowania się z firm Pivotal, pomocy technicznej. Firma Microsoft i Pivotal włączono również przepływy pracy pomocy technicznej, które pozwalają uzyskać pomoc skontaktuj się z każdej ze stron i mają swoje zapytanie kierowane odpowiednio w zależności od tego, gdzie znajduje się problem.

## <a name="azure-service-broker"></a>Azure Service Broker

Usługi cloud Foundry zachęca ["aplikacji 12 factor"](https://12factor.net/) metodologii, która promuje wyraźne oddzielenie procesów aplikacji bezstanowych i stanowych kopii usługi. [Usługa brokerów](https://docs.cloudfoundry.org/services/api.html) oferują spójny sposób, aby aprowizować i wiązania usług zapasowych do aplikacji. [Brokera usług platformy Azure](https://github.com/Azure/meta-azure-service-broker) zawiera niektóre z najważniejszych usług platformy Azure za pośrednictwem tego kanału, w tym usługi Azure storage i Azure SQL.

Jeśli używasz Pivotal Cloud Foundry programu service broker jest również [dostępne jako Kafelek](https://docs.pivotal.io/azure-sb/installing.html) z Pivotal sieci.

## <a name="related-resources"></a>Powiązane zasoby

### <a name="azure-devops-services-plugin"></a>Wtyczka usługom DevOps platformy Azure

Cloud Foundry to dobrze nadaje się do tworzenia oprogramowania agile, łącznie z użyciem ciągłą integrację (CI) i ciągłe dostarczanie (CD). Jeśli używasz usługi DevOps platformy Azure do zarządzania projektami i chcesz ustawić się ciągłej integracji/ciągłego wdrażania potoku przeznaczone dla usługi Cloud Foundry, możesz użyć [Azure DevOps usługi Cloud Foundry skompilowania rozszerzenia](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Wtyczka pozwala w prosty sposób konfigurowania i automatyzacji wdrożeń do usługi Cloud Foundry czy uruchomiona na platformie Azure lub innego środowiska.

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie rozwiązania Pivotal Cloud Foundry w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Wdrażanie aplikacji Cloud Foundry na platformie Azure](./cloudfoundry-deploy-your-first-app.md)
