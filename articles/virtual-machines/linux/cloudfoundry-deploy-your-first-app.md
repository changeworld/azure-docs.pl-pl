---
title: Wdrażanie pierwszej aplikacji do usługi Cloud Foundry w systemie Microsoft Azure | Dokumentacja firmy Microsoft
description: Wdrażanie aplikacji usługi Cloud Foundry na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 5a43ce3f09ce9695fa5add58b52271a46e2a271a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60388509"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Wdrażanie pierwszej aplikacji do usługi Cloud Foundry w systemie Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) to platforma popularnych aplikacji typu open source dostępnych w systemie Microsoft Azure. W tym artykule przedstawiono, jak wdrażać i zarządzać nimi aplikację Cloud Foundry w środowisku platformy Azure.

## <a name="create-a-cloud-foundry-environment"></a>Tworzenie środowiska usługi Cloud Foundry

Istnieje kilka opcji umożliwiających tworzenie środowiska usługi Cloud Foundry na platformie Azure:

- Użyj [oferty Pivotal Cloud Foundry] [ pcf-azuremarketplace] w witrynie Azure Marketplace, aby utworzyć środowisko standardowe, obejmującej menedżera operacji PCF i Broker usług platformy Azure. Możesz znaleźć [wykonaj instrukcje] [ pcf-azuremarketplace-pivotaldocs] wdrażania w portalu marketplace oferty w rozwiązaniu Pivotal dokumentacji.
- Utwórz dostosowane środowisko przez [wdrażania ręcznego Pivotal Cloud Foundry][pcf-custom].
- [Wdrażanie pakietów usługi Cloud Foundry typu open-source bezpośrednio] [ oss-cf-bosh] , konfigurując [BOSH](https://bosh.io) Dyrektor, maszynę Wirtualną, która służy do koordynowania wdrożenia środowiska Cloud Foundry.

> [!IMPORTANT] 
> Jeśli wdrażasz PCF z witryny Azure Marketplace, zanotuj SYSTEMDOMAINURL i poświadczenia administratora wymagane do uzyskania dostępu Pivotal Menedżera aplikacji, które są opisane w przewodniku wdrażania w portalu marketplace. Są one niezbędne do ukończenia tego samouczka. W przypadku wdrożeń w portalu marketplace SYSTEMDOMAINURL ma postać https://system. *adres IP*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Połączyć się z kontrolerem chmury

Kontroler chmury jest podstawowy punkt wejścia dla usługi Cloud Foundry środowiska wdrażania aplikacji i zarządzaniem nimi. Core chmury kontrolera interfejsu API (CCAPI) to interfejs API REST, ale nie jest dostępny za pośrednictwem różnych narzędzi. W tym przypadku będziemy korzystać z niego za pośrednictwem [Cloud Foundry interfejs wiersza polecenia][cf-cli]. Interfejs wiersza polecenia można zainstalować w systemie Linux, MacOS lub Windows, ale jeśli użytkownik nie chce go zainstalować na wszystkich, jest dostępny wstępnie zainstalowany w [usługi Azure Cloud Shell][cloudshell-docs].

Aby zalogować się, należy poprzedzić `api` do SYSTEMDOMAINURL, uzyskany z wdrożenia w portalu marketplace. Ponieważ domyślnym wdrożeniu używa certyfikatu z podpisem własnym, powinny również obejmować `skip-ssl-validation` przełącznika.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Monit, zaloguj się do kontrolera chmury. Użyj poświadczeń konta administratora, którzy uzyskali z kroków wdrażania portalu marketplace.

Udostępnia usługi cloud Foundry *organizacje* i *miejsca do magazynowania* jako przestrzenie nazw do izolowania zespołami i środowiskami, w ramach wdrożenia udostępnionych. Wdrożenie rozwiązania PCF w portalu marketplace zawiera domyślne *systemu* organizacji i zestawu miejsca do magazynowania zawiera podstawowe składniki, takie jak usługi skalowania automatycznego i broker usług platformy Azure. Teraz wybierz *systemu* miejsca.


## <a name="create-an-org-and-space"></a>Tworzenie organizacji i miejsca

Jeśli wpiszesz `cf apps`, zostanie wyświetlony zestaw aplikacji systemu, które zostały wdrożone w przestrzeni system w całej organizacji systemu 

Należy zachować *systemu* organizacji zarezerwowane dla aplikacji systemu dzięki czemu można tworzyć organizacji i miejsce do przechowywania naszą przykładową aplikacją.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Aby przełączyć się do nowej organizacji i miejsce, należy użyć polecenia docelowego:

```bash
cf target -o testorg -s dev
```

Teraz po wdrożeniu aplikacji go jest automatycznie tworzony w nowej organizacji i miejsca. Aby upewnić się, że obecnie nie istnieją żadne aplikacje w nowej organizacji/przestrzeni, wpisz `cf apps` ponownie.

> [!NOTE] 
> Aby uzyskać więcej informacji na temat organizacje i miejsca do magazynowania oraz sposób ich może służyć do kontroli dostępu opartej na rolach (RBAC), zobacz [dokumentacji usługi Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Wdrażanie aplikacji

Użyjmy o nazwie Hello Spring Cloud, która jest napisany w języku Java na podstawie przykładowej aplikacji usługi Cloud Foundry [Spring Framework](https://spring.io) i [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Sklonuj repozytorium Hello Spring Cloud

Przykładowa aplikacja Hello Spring Cloud jest dostępna w witrynie GitHub. Sklonuj je do środowiska i zmienić do nowego katalogu:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Kompilowanie aplikacji

Tworzenie aplikacji za pomocą [narzędzia Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Wdrażanie aplikacji za pomocą wypychania usługi cf

Możesz wdrożyć większości aplikacji do korzystania z usługi Cloud Foundry `push` polecenia:

```bash
cf push
```

Po użytkownik *wypychania* aplikację Cloud Foundry wykrywa typu aplikacji (w tym przypadku aplikacji w języku Java) i identyfikuje jego zależności (w takim Spring framework). Pakiety następnie wszystko, co jest wymagane do uruchomienia kodu do obrazu kontenera autonomiczny, nazywany *dropletu*. Na koniec Cloud Foundry planuje aplikację na jednej z dostępnych maszyn w środowisku i tworzy adres URL, w której możesz uzyskiwać dostęp, który jest dostępny w danych wyjściowych polecenia.

![Dane wyjściowe polecenia wypychania usługi cf][cf-push-output]

Aby wyświetlić aplikację hello spring chmury, należy otworzyć podanego adresu URL w przeglądarce:

![Domyślny interfejs użytkownika dla Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Aby dowiedzieć się więcej na temat zachodzących podczas `cf push`, zobacz [jak aplikacje są umieszczane] [ cf-push-docs] w dokumentacji usługi Cloud Foundry.

## <a name="view-application-logs"></a>Wyświetlanie dzienników aplikacji

Cloud Foundry interfejs wiersza polecenia można użyć, aby wyświetlić dzienniki aplikacji za pomocą nazwy:

```bash
cf logs hello-spring-cloud
```

Domyślnie dzienniki polecenie używa *tail*, który wskazuje nowe dzienniki, jak zostały napisane. Aby wyświetlić nowe dzienniki są wyświetlane, Odśwież aplikację hello spring chmury w przeglądarce.

Aby wyświetlić dzienniki, które już zostały zapisane, Dodaj `recent` przełącznika:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skalowanie aplikacji

Domyślnie `cf push` tworzy tylko jedno wystąpienie aplikacji. Aby zapewnić wysoką dostępność i włączyć skalowanie dla większej przepływności, zazwyczaj chcesz uruchomić więcej niż jedno wystąpienie aplikacji. Można łatwo skalować w poziomie już wdrożonej aplikacji za pomocą `scale` polecenia:

```bash
cf scale -i 2 hello-spring-cloud
```

Uruchamianie `cf app` polecenia aplikacji pokazuje, że Cloud Foundry tworzy inne wystąpienie aplikacji. Po rozpoczęciu aplikacji Cloud Foundry automatycznie uruchamia Równoważenie obciążenia ruchu do niego.


## <a name="next-steps"></a>Kolejne kroki

- [Zapoznaj się z dokumentacją usługi Cloud Foundry][cloudfoundry-docs]
- [Konfigurowanie wtyczki usługom DevOps platformy Azure dla usługi Cloud Foundry][vsts-plugin]
- [Skonfiguruj dodatek Microsoft Log Analytics Nozzle dla usługi Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
