---
title: Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure
description: Wdrażanie aplikacji w odnaleźnie w chmurze na platformie Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 793a8f291be4fcca6fad19d486849253dddc089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294793"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) to popularna platforma aplikacji typu open source dostępna na platformie Microsoft Azure. W tym artykule pokazujemy, jak wdrożyć aplikację w odnaleźniu w chmurze i zarządzać nią w środowisku platformy Azure.

## <a name="create-a-cloud-foundry-environment"></a>Tworzenie środowiska odlewniczego chmury

Istnieje kilka opcji tworzenia środowiska odlewniczego chmury na platformie Azure:

- Skorzystaj z [oferty Pivotal Cloud Foundry][pcf-azuremarketplace] w portalu Azure Marketplace, aby utworzyć standardowe środowisko obejmujące menedżera operacji PCF i brokera usług Azure. [Pełne instrukcje dotyczące][pcf-azuremarketplace-pivotaldocs] wdrażania oferty portalu Marketplace można znaleźć w dokumentacji pivotal.
- Utwórz dostosowane środowisko, [ręcznie wdrażając pivotal cloud foundry][pcf-custom].
- [Wdrażaj pakiety cloud foundry typu open source bezpośrednio,][oss-cf-bosh] konfigurując dyrektora [BOSH,](https://bosh.io) maszynę wirtualną, która koordynuje wdrażanie środowiska odlewniczego chmury.

> [!IMPORTANT] 
> Jeśli wdrażasz PCF z portalu Azure Marketplace, zanotuj poświadczenia SYSTEMDOMAINURL i poświadczenia administratora wymagane do uzyskania dostępu do Menedżera aplikacji kluczykowych, które są opisane w przewodniku wdrażania portalu Marketplace. Są one potrzebne do ukończenia tego samouczka. W przypadku wdrożeń w portalu Marketplace `https://system.*ip-address*.cf.pcfazure.com`systemdomainurl jest w formularzu .

## <a name="connect-to-the-cloud-controller"></a>Łączenie się z kontrolerem chmury

Kontroler chmury jest podstawowym punktem wejścia do środowiska odlewniczego chmury do wdrażania aplikacji i zarządzania nimi. Podstawowy interfejs API kontrolera chmury (CCAPI) jest interfejsem API REST, ale jest dostępny za pomocą różnych narzędzi. W tym przypadku wchodzimy w interakcję z nim za pośrednictwem [Cloud Foundry CLI][cf-cli]. Cli można zainstalować na Linuksie, macOS lub Windows, ale jeśli wolisz nie instalować go w ogóle, jest on dostępny fabrycznie zainstalowany w [usłudze Azure Cloud Shell][cloudshell-docs].

Aby się zalogować, należy dołączyć `api` do narzędzia SYSTEMDOMAINURL uzyskanego z wdrożenia w portalu marketplace. Ponieważ wdrożenie domyślne używa certyfikatu z podpisem `skip-ssl-validation` własnym, należy również dołączyć przełącznik.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zostanie wyświetlony monit o zalogowanie się do kontrolera w chmurze. Użyj poświadczeń konta administratora nabytych w krokach wdrażania portalu Marketplace.

Cloud Foundry udostępnia *orgs* i *przestrzenie* jako przestrzenie nazw do izolowania zespołów i środowisk w ramach udostępnionego wdrożenia. Wdrożenie w portalu PCF marketplace zawiera domyślną warstwę *systemu* i zestaw spacji utworzonych w celu zawierające składniki podstawowe, takie jak usługa skalowania automatycznego i broker usług platformy Azure. Na razie wybierz przestrzeń *systemową.*


## <a name="create-an-org-and-space"></a>Tworzenie organizacji i przestrzeni

W przypadku `cf apps`wpisywał , zostanie wyświetlony zestaw aplikacji systemowych, które zostały wdrożone w przestrzeni systemowej w organizacji systemu. 

Należy zachować *system* org zarezerwowane dla aplikacji systemowych, więc utworzyć org i miejsca do przechowywania naszej przykładowej aplikacji.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Użyj polecenia docelowego, aby przełączyć się na nową organizacji i spacji:

```bash
cf target -o testorg -s dev
```

Teraz po wdrożeniu aplikacji jest ona automatycznie tworzona w nowej organizacji i przestrzeni. Aby potwierdzić, że obecnie w nowej organizacji/spacji nie ma żadnych aplikacji, wpisz ponownie. `cf apps`

> [!NOTE] 
> Aby uzyskać więcej informacji na temat organizacji i spacji oraz sposobu ich wykorzystania do kontroli dostępu opartej na rolach (RBAC), zobacz [dokumentację Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Wdrażanie aplikacji

Użyjmy przykładowej aplikacji Cloud Foundry o nazwie Hello Spring Cloud, która jest napisana w javie i oparta na [Spring Framework](https://spring.io) i [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonowanie repozytorium Hello Spring Cloud

Przykładowa aplikacja Hello Spring Cloud jest dostępna w usłudze GitHub. Sklonuj go do swojego środowiska i zmień do nowego katalogu:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Kompilowanie aplikacji

Tworzenie aplikacji za pomocą [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Wdrażanie aplikacji za pomocą przycisku cf push

Większość aplikacji można wdrożyć w `push` odnaleźniu w chmurze za pomocą polecenia:

```bash
cf push
```

Podczas *wypychania* aplikacji Cloud Foundry wykrywa typ aplikacji (w tym przypadku aplikacji Java) i identyfikuje jej zależności (w tym przypadku platformę Wiosna). Następnie pakuje wszystko, co jest wymagane do uruchomienia kodu, w samodzielny obraz kontenera, znany jako *kropla.* Na koniec Cloud Foundry planuje aplikację na jednym z dostępnych komputerów w środowisku i tworzy adres URL, do którego można do niego dotrzeć, który jest dostępny w danych wyjściowych polecenia.

![Wyjście z polecenia cf push][cf-push-output]

Aby wyświetlić aplikację hello-spring-cloud, otwórz podany adres URL w przeglądarce:

![Domyślny interfejs użytkownika hello spring cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Aby dowiedzieć się `cf push`więcej o tym, co dzieje się podczas , zobacz [Jak aplikacje są przemieszczane][cf-push-docs] w dokumentacji Cloud Foundry.

## <a name="view-application-logs"></a>Wyświetlanie dzienników aplikacji

Za pomocą interfejsu wiersza polecenia odnaleznia w chmurze można wyświetlać dzienniki aplikacji o jej nazwie:

```bash
cf logs hello-spring-cloud
```

Domyślnie polecenie dzienniki używa *tail*, który pokazuje nowe dzienniki, jak są one zapisywane. Aby wyświetlić nowe dzienniki, odśwież aplikację hello-spring-cloud w przeglądarce.

Aby wyświetlić dzienniki, które zostały `recent` już zapisane, dodaj przełącznik:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skaluj aplikację

Domyślnie `cf push` tworzy tylko jedno wystąpienie aplikacji. Aby zapewnić wysoką dostępność i włączyć skalowanie w poziomie dla wyższej przepływności, zazwyczaj chcesz uruchomić więcej niż jedno wystąpienie aplikacji. Za pomocą `scale` polecenia można łatwo skalować w poziomie już wdrożone aplikacje:

```bash
cf scale -i 2 hello-spring-cloud
```

Uruchomienie `cf app` polecenia w aplikacji pokazuje, że Odnajdowanie w chmurze tworzy inne wystąpienie aplikacji. Po uruchomieniu aplikacji Cloud Foundry automatycznie uruchamia ruch równoważenia obciążenia.


## <a name="next-steps"></a>Następne kroki

- [Przeczytaj dokumentację Cloud Foundry][cloudfoundry-docs]
- [Konfigurowanie wtyczki usługi Azure DevOps dla odlewnictwa w chmurze][vsts-plugin]
- [Konfigurowanie dyszy usługi Microsoft Log Analytics dla odlewnictwa w chmurze][loganalytics-nozzle]

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
