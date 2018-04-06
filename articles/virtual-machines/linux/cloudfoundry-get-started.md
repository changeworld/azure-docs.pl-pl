---
title: Wprowadzenie do korzystania z Foundry chmurze na platformie Microsoft Azure | Dokumentacja firmy Microsoft
description: Uruchom OSS lub Foundry kluczową chmurze na platformie Microsoft Azure
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
ms.openlocfilehash: 25cb758d6d897e03884f77459620e69600a78511
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="cloud-foundry-on-azure"></a>Usługa Cloud Foundry na platformie Azure

Foundry chmury jest open source platformy jako — usługa (PaaS) dotyczące tworzenia, wdrażania i obsługi 12-factor aplikacji utworzonych w różnych języków i struktur. Tym dokumencie opisano opcje, które mają zasilany Foundry chmury Azure i jak możesz rozpocząć pracę.

## <a name="cloud-foundry-offerings"></a>Foundry oferty usług w chmurze

Istnieją dwie formy dostępna do uruchamiania na platformie Azure w chmurze Foundry: Foundry chmury (OSS CF) i kluczową chmury Foundry (PCF) open source. OSS CF jest całkowicie [open source](https://github.com/cloudfoundry) wersji Foundry chmury zarządza Foundation Foundry chmury. Kluczową Foundry chmury jest dystrybucja enterprise Foundry chmury z kluczową Inc. oprogramowania Przyjrzymy się niektóre różnice między dwoma oferty.

### <a name="open-source-cloud-foundry"></a>Chmura open source Foundry

OSS Foundry chmurze na platformie Azure można wdrożyć za najpierw wdrażanie Dyrektor BOSH, a następnie wdrażania Foundry chmury, za pomocą [instrukcje podane w serwisie GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Aby dowiedzieć się więcej o korzystaniu z OSS CF, zobacz [dokumentacji](https://docs.cloudfoundry.org/) podał Foundation Foundry chmury.

Firma Microsoft udostępnia optymalnych obsługę OSS CF za pośrednictwem następujących kanałów społeczności:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>kanał bosh-azure-cpi na [Slack Foundry chmury](https://slack.cloudfoundry.org/)
- [listy adresowej CF bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemy z usługi GitHub dla [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) i [programu service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Poziom obsługi zasobów platformy Azure, takich jak maszyny wirtualne, na którym uruchamiasz Foundry chmury opiera się na Twoje umowy pomocy technicznej platformy Azure. Obsługa społeczności optymalnych dotyczy tylko składniki Foundry specyficzne dla chmury.

### <a name="pivotal-cloud-foundry"></a>Kluczową chmury Foundry

Kluczową Foundry chmury zawiera tę samą platformę core jako dystrybucji OSS, wraz z zestawem narzędzi do zarządzania zastrzeżonych i pomocy technicznej enterprise. Aby uruchomić PCF na platformie Azure, w przypadku uzyskania licencji z Pivotal. Oferta PCF z poziomu portalu Azure marketplace zawiera licencja próbna 90 dni.

Narzędzia te zawierają [kluczową programu Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), aplikacji sieci web, które upraszcza wdrażanie i zarządzanie foundation Foundry chmury, a [kluczową Menedżera aplikacji](https://docs.pivotal.io/pivotalcf/console/), aplikacji sieci web do zarządzania użytkownikami i aplikacji.

Oprócz wymienione powyżej CF OSS kanałów pomocy technicznej licencji PCF uprawnia do kontaktowania się z Pivotal pomocy technicznej. Firma Microsoft i Pivotal również włączono przepływów pracy pomocy technicznej, które umożliwiają skontaktuj się z jednej strony pomocy i ma Twojego zapytania kierowane odpowiednio w zależności od tego, gdzie znajduje się problem.

## <a name="azure-service-broker"></a>Azure Service Broker

Chmura Foundry zachęca ["12 factor aplikacja"](https://12factor.net/) metodologii, która wspiera czyste rozdzielenie procesów aplikacji bezstanowych i stateful kopii usługi. [Usługa brokerzy](https://docs.cloudfoundry.org/services/api.html) oferują spójny sposób obsługi administracyjnej i powiąż zapasowy usług do aplikacji. [Brokera usług Azure](https://github.com/Azure/meta-azure-service-broker) zawiera niektóre z najważniejszych usług Azure za pośrednictwem tego kanału, w tym magazynu Azure i Azure SQL.

Jeśli używasz kluczową Foundry chmury programu service broker jest również [dostępne jako Kafelek](https://docs.pivotal.io/azure-sb/installing.html) z kluczową sieci.

## <a name="related-resources"></a>Powiązane zasoby

### <a name="visual-studio-team-services-plugin"></a>Visual Studio Team Services wtyczki

Foundry chmury jest dobrze nadają się do rozwoju oprogramowania agile, łącznie z użyciem ciągłej integracji (CI) i ciągłego dostarczania (CD). Jeśli w zarządzaniu projektami przy użyciu programu Visual Studio Team Services (VSTS), a chcesz ustawić zapasowej CI/CD potoku przeznaczonych dla Foundry chmury, możesz użyć [rozszerzenie kompilacji programu VSTS chmury Foundry](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Wtyczka ułatwia konfigurowanie i automatyzowania wdrożeń w celu Foundry chmury, czy działa w Azure lub innej środowiska.

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie Foundry kluczową chmury z poziomu portalu Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Wdrażanie aplikacji w chmurze Foundry na platformie Azure](./cloudfoundry-deploy-your-first-app.md)