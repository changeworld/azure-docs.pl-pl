---
title: Wdrażanie pierwszej aplikacji do Foundry chmury w systemie Microsoft Azure | Dokumentacja firmy Microsoft
description: Wdrażanie aplikacji Foundry chmurze na platformie Azure
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
ms.openlocfilehash: 5e7b321c9fc8f8568cd8109cea0ae877048d3663
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Wdrażanie pierwszej aplikacji do Foundry chmury w systemie Microsoft Azure

[Chmury Foundry](http://cloudfoundry.org) jest dostępna w systemie Microsoft Azure platformy popularnych aplikacji open source. W tym artykule zostanie przedstawiony sposób wdrażania i zarządzania nimi aplikacji w chmurze Foundry w środowisku platformy Azure.

## <a name="create-a-cloud-foundry-environment"></a>Utwórz środowisko chmury Foundry

Dostępnych jest kilka opcji tworzenia środowiska Foundry chmurze na platformie Azure:

- Użyj [oferta kluczową Foundry chmury] [ pcf-azuremarketplace] w portalu Azure Marketplace do utworzenia standardowe środowisko obejmujące PCF Operations Manager i usługi Azure Service Broker. Można znaleźć [wykonać instrukcje] [ pcf-azuremarketplace-pivotaldocs] wdrażania portalu marketplace oferują w kluczową dokumentacji.
- Utwórz dostosowane środowisko przez [wdrażania ręcznego kluczową Foundry chmury][pcf-custom].
- [Wdrażanie pakietów Foundry chmury open source bezpośrednio] [ oss-cf-bosh] poprzez ustawienie [BOSH](http://bosh.io) dyrektora, maszynę Wirtualną, która koordynuje wdrożenia w środowisku chmury Foundry.

> [!IMPORTANT] 
> Jeśli wdrażasz PCF z portalu Azure Marketplace, zanotuj SYSTEMDOMAINURL i poświadczeń administratora wymagane do uzyskania dostępu kluczową Menedżera aplikacji, które zostały opisane w przewodniku wdrażania portalu marketplace. Są one niezbędne do ukończenia tego samouczka. W przypadku wdrożeń marketplace SYSTEMDOMAINURL ma postać https://system. *adres IP*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Połączyć się z kontrolerem chmury

Kontroler chmury jest podstawowy punkt wejścia do środowiska chmury Foundry wdrażania i zarządzania aplikacjami. Core chmury kontrolera interfejsu API (CCAPI) to interfejs API REST, ale nie jest dostępny za pośrednictwem różnych narzędzi. W takim przypadku możemy korzystać z niego za pośrednictwem [interfejsu wiersza polecenia chmury Foundry][cf-cli]. Interfejsu wiersza polecenia można zainstalować w systemie Linux, MacOS lub systemu Windows, ale jeśli użytkownik nie chce go zainstalować na wszystkich, jest dostępny wstępnie zainstalowane w [powłoki chmury Azure][cloudshell-docs].

Aby zalogować się, dołączenie wartości `api` do SYSTEMDOMAINURL, uzyskany z wdrożenia w witrynie marketplace. Ponieważ instalacja domyślna używa certyfikatu z podpisem własnym, należy także uwzględnić `skip-ssl-validation` przełącznika.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zostanie wyświetlony monit logowania do kontrolera chmury. Użyj poświadczeń konta administratora, które uzyskane z portalu marketplace procedury wdrażania.

Udostępnia chmury Foundry *organizacjami* i *spacji* jako przestrzenie nazw, aby wykrywać zespołów i środowisk, w ramach wdrożenia udostępnionych. Wdrożenie marketplace PCF zawiera domyślne *systemu* organizacji i zestaw spacje zawiera podstawowe składniki, takie jak usługi Skalowanie automatyczne i brokera usług Azure. Teraz, wybierz *systemu* miejsca.


## <a name="create-an-org-and-space"></a>Tworzenie schematu i miejsca

W przypadku wpisania `cf apps`, należy zapoznać się z zestawem aplikacji systemu, które zostały wdrożone w miejscu systemu w ramach org. systemu 

Zachowaj *systemu* org zastrzeżone dla aplikacji systemu, dlatego Utwórz organizacji i miejsca do przechowywania naszych przykładowej aplikacji.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Przełącz się do nowego schematu i miejsca za pomocą polecenia docelowych:

```bash
cf target -o testorg -s dev
```

Teraz gdy wdrażana jest aplikacja została ona utworzona automatycznie w nowej organizacji i miejsca. Aby upewnić się, że żadne aplikacje nie są aktualnie w nowej organizacji/miejsca, wpisz `cf apps` ponownie.

> [!NOTE] 
> Aby uzyskać więcej informacji na temat organizacjami i spacje i jak one używane do kontroli dostępu opartej na rolach (RBAC), zobacz [Foundry chmury dokumentacji][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Wdrażanie aplikacji

Użyjmy Foundry chmury przykładową aplikację o nazwie Hello Spring chmury, co jest napisany w języku Java i na podstawie [Spring Framework](http://spring.io) i [rozruchu Spring](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonuj repozytorium Hello Spring chmury

Przykładowa aplikacja Hello Spring chmury jest dostępna w witrynie GitHub. Sklonować go do środowiska i zmień do nowego katalogu:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Kompilowanie aplikacji

Tworzenie aplikacji w programie [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Wdrażanie aplikacji z cf wypychania

Można wdrożyć większość aplikacji przy użyciu chmury Foundry `push` polecenia:

```bash
cf push
```

Gdy użytkownik *wypychania* aplikacji, chmury Foundry wykrywa typu aplikacji (w tym przypadku aplikacji Java) i jego zależności (w tym przypadku Spring framework) identyfikuje. Pakiety następnie wszystko, co jest wymagane do uruchomienia kodu w obrazie kontenera autonomiczny, nazywany *dropletu*. Na koniec Foundry chmury planuje aplikacji na jednym z dostępnych maszyn w środowisku i tworzy adres URL, w której możesz uzyskiwać dostęp, która jest dostępna w danych wyjściowych polecenia.

![Dane wyjściowe polecenia wypychania cf][cf-push-output]

Aby wyświetlić aplikacji hello spring chmury, należy otworzyć podanego adresu URL w przeglądarce:

![Domyślny interfejs użytkownika dla chmury Hello Spring][hello-spring-cloud-basic]

> [!NOTE] 
> Aby dowiedzieć się więcej na temat zachodzących podczas `cf push`, zobacz [jak aplikacje są umieszczane] [ cf-push-docs] w dokumentacji Foundry chmury.

## <a name="view-application-logs"></a>Wyświetl dzienniki aplikacji

CLI Foundry chmury służy do wyświetlania dzienników dla aplikacji za pomocą nazwy:

```bash
cf logs hello-spring-cloud
```

Domyślnie dzienniki polecenia używa *tail*, który wskazuje nowe dzienniki, ponieważ są one zapisywane. Aby zobaczyć nowe dzienniki są wyświetlane, Odśwież aplikacji hello spring chmury w przeglądarce.

Aby wyświetlić dzienniki, które już zostały zapisane, Dodaj `recent` przełącznika:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skalowanie aplikacji

Domyślnie `cf push` tworzy tylko jedno wystąpienie aplikacji. Aby zapewnić wysoką dostępność i włączyć skalowania w poziomie wyższej przepustowości, zazwyczaj chcesz uruchomić więcej niż jedno wystąpienie aplikacji. Możesz łatwo skalować w poziomie już wdrożone aplikacje za pomocą `scale` polecenia:

```bash
cf scale -i 2 hello-spring-cloud
```

Uruchomiona `cf app` polecenia w aplikacji pokazuje, że Foundry chmury wprowadza inne wystąpienie aplikacji. Po uruchomieniu aplikacji, w chmurze Foundry ruchu do niego równoważenia obciążenia jest uruchamiana automatycznie.


## <a name="next-steps"></a>Kolejne kroki

- [Zapoznaj się z dokumentacją chmury Foundry][cloudfoundry-docs]
- [Konfigurowanie dodatku Visual Studio Team Services dla chmury Foundry][vsts-plugin]
- [Konfigurowanie końcówkę Analiza dzienników Microsoft dla chmury Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png