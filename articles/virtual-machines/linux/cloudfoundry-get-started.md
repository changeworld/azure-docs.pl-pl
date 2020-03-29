---
title: Wprowadzenie do odnaleźnia w chmurze na platformie Microsoft Azure
description: Uruchamianie systemu operacyjnego lub odnaleźnia chmury pivotal na platformie Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: ba0b4a636d8f785f1c169e4066c9cf6d25ba81db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970049"
---
# <a name="cloud-foundry-on-azure"></a>Usługa Cloud Foundry na platformie Azure

Cloud Foundry to platforma jako usługa (PaaS) typu „open source” służąca do tworzenia, wdrażania i obsługi aplikacji 12-factor opracowanych w różnych językach i strukturach. W tym dokumencie opisano opcje uruchamiania odnalezienia w chmurze na platformie Azure oraz sposób uruchamiania.

## <a name="cloud-foundry-offerings"></a>Oferty odlewnicze chmur

Istnieją dwie formy odnaleźnia chmury dostępne do uruchomienia na platformie Azure: open-source Cloud Foundry (OSS CF) i Pivotal Cloud Foundry (PCF). OSS CF jest całkowicie [open-source](https://github.com/cloudfoundry) wersja Cloud Foundry zarządzane przez Cloud Foundry Foundation. Pivotal Cloud Foundry to dystrybucja cloud foundry firmy Pivotal Software Inc. Przyglądamy się niektórym różnicom między tymi dwoma ofertami.

### <a name="open-source-cloud-foundry"></a>Odlewnia chmur typu open source

Odnalezienie chmury systemu OSS można wdrożyć na platformie Azure, najpierw wdrażając dyrektora BOSH, a następnie wdrażając odlewnie w chmurze, korzystając z [instrukcji podanych w usłudze GitHub.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) Aby dowiedzieć się więcej na temat korzystania z OSS CF, zapoznaj się z [dokumentacją](https://docs.cloudfoundry.org/) dostarczoną przez Cloud Foundry Foundation.

Firma Microsoft zapewnia najlepszą obsługę techniczną dla OSS CF za pośrednictwem następujących kanałów społeczności:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>kanał bosh-azure-cpi na [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [lista mailingowa cf-bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemy z usługą GitHub dla [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) i [brokera usług](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Poziom obsługi zasobów platformy Azure, takich jak maszyny wirtualne, na których uruchomisz Odlewnię w chmurze, jest oparty na umowie pomocy technicznej platformy Azure. Najlepsza obsługa społeczności ma zastosowanie tylko do składników specyficznych dla Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Kluczowa odlewnia chmur

Pivotal Cloud Foundry zawiera tę samą podstawową platformę co dystrybucja OSS, wraz z zestawem zastrzeżonych narzędzi do zarządzania i wsparcia dla przedsiębiorstw. Aby uruchomić PCF na platformie Azure, należy uzyskać licencję od pivotal. Oferta PCF z portalu Azure marketplace zawiera 90-dniową licencję próbną.

Narzędzia obejmują [Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), aplikację sieci web, która upraszcza wdrażanie i zarządzanie fundacji Cloud Foundry oraz Pivotal Apps [Manager](https://docs.pivotal.io/pivotalcf/console/), aplikację internetową do zarządzania użytkownikami i aplikacjami.

Oprócz kanałów wsparcia wymienionych dla OSS CF powyżej, licencja PCF uprawnia do kontaktu z Pivotal w celu uzyskania pomocy technicznej. Firmy Microsoft i Pivotal włączyły również przepływy pracy pomocy technicznej, które umożliwiają skontaktowanie się z którąkolwiek ze stron w celu uzyskania pomocy i odpowiednie rozesłanie zapytania w zależności od tego, gdzie leży problem.

## <a name="azure-service-broker"></a>Broker usług platformy Azure

Cloud Foundry zachęca do [metodologii "aplikacji dwunastocenikowej",](https://12factor.net/) która promuje czyste oddzielenie bezstanowych procesów aplikacji i stanowych usług wspierających. [Brokerzy usług](https://docs.cloudfoundry.org/services/api.html) oferują spójny sposób aprowizowania i powiązania usług wspierających aplikacje. [Broker usług platformy Azure](https://github.com/Azure/meta-azure-service-broker) udostępnia niektóre z kluczowych usług platformy Azure za pośrednictwem tego kanału, w tym usługi Azure storage i sql platformy Azure.

Jeśli używasz Pivotal Cloud Foundry, broker usług jest również [dostępny jako kafelek](https://docs.pivotal.io/azure-sb/installing.html) z sieci pivotal.

## <a name="related-resources"></a>Powiązane zasoby

### <a name="azure-devops-services-plugin"></a>Wtyczka usługi Azure DevOps

Cloud Foundry doskonale nadaje się do elastycznego tworzenia oprogramowania, w tym do ciągłej integracji (CI) i ciągłego dostarczania (CD). Jeśli używasz usługi Azure DevOps Services do zarządzania projektami i chcesz skonfigurować potok ciągłej integracji/ciągłego wdrażania dotyczący odnajdywania w chmurze, możesz użyć [rozszerzenia kompilacji odnajdywania usług Azure DevOps Services.](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension) Wtyczka ułatwia konfigurowanie i automatyzację wdrożeń w odnaleźniu w chmurze, niezależnie od tego, czy jest uruchomiona na platformie Azure, czy w innym środowisku.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie odlewnictwa chmury kluczowej z portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Wdrażanie aplikacji do odnaleźnia w chmurze na platformie Azure](./cloudfoundry-deploy-your-first-app.md)
