---
title: Przy użyciu programu Chef z platformą Azure
description: Wprowadzenie do konfigurowania i testowania infrastruktury platformy Azure przy użyciu programu Chef
ms.service: virtual-machines-linux
keywords: Azure, programu chef, metodyki devops, maszyny wirtualne, przegląd, automatyzacja
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 5c4e20177c1b334a34f7ce9328dfaa7bd8c66d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656578"
---
# <a name="using-chef-with-azure"></a>Przy użyciu programu Chef z platformą Azure
[Program chef](https://www.chef.io) platforma zaawansowanej automatyzacji, która przekształca infrastruktury maszyny wirtualnej na platformie Azure w kodzie. Program chef automatyzuje jak infrastruktura jest skonfigurowany, wdrażane i zarządzane w sieci, niezależnie od jego rozmiaru.

W tym artykule opisano zalety Zarządzaj infrastrukturą platformy Azure przy użyciu programu Chef.

## <a name="chef-extension-on-azure"></a>Rozszerzenie programu chef na platformie Azure
Aprowizowanie maszyny wirtualnej za pomocą klienta programu Chef, uruchomione jako usługę w tle za pomocą [rozszerzenia Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) w witrynie Azure Portal. Po zainicjowaniu obsługi administracyjnej, te maszyny wirtualne są gotowe do zarządzane przez serwer programu Chef.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Stacja robocza Chef do użycia bezpośrednio w usłudze Azure Cloud Shell! Uruchom wszystkie narzędzia Chef i InSpec prawo z usługi Cloud Shell. Korzystanie z poleceń programu Chef z:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kuchenne](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [nóż](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Łączenie nasze narzędzia polecenia przy użyciu innych narzędzi dostępnych w usłudze Cloud Shell, takich jak `git`, `az-cli`, i `terraform`i zapis automatyzacji infrastruktury i zgodności z przeglądarki.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatyzacja infrastruktury, aplikacji i zgodność z platformami
Firmy wymagają szybkość, szybkość dostępu i bezpieczeństwa, aby wziąć udział w rynku cyfrowych. Razem program Chef i Microsoft pomagają osób, zespołów, przedsiębiorstw i wykonać wszystkie czynności. Jedna platforma, Chef Automate, możesz teraz zautomatyzować i ciągłe dostarczanie infrastruktury, aplikacji i zgodności między infrastruktury firmy Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Przetestuj Chef Automate na platformie Azure
Obsługiwane przez program Chef, [rozwiązania Chef Automatyzacja Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) pozwala na tworzenie, wdrażanie i zarządzanie infrastruktury i aplikacji wspólnie. Jednym kliknięciem pobiera natychmiastowy dostęp do wszystkich komercyjnych funkcji rozwiązania Chef automate; Zyskaj widoczność end-to-end w całej flocie, umożliwiasz ciągłą i zarządzasz wszystkimi zmianami w ramach ujednoliconego przepływu pracy.

## <a name="next-steps"></a>Kolejne kroki

* [Utwórz maszynę wirtualną Windows na platformie Azure przy użyciu programu Chef](/azure/virtual-machines/windows/chef-automation)
