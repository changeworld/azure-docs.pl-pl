---
title: Korzystanie z programu Chef z platformą Azure
description: Wprowadzenie do konfigurowania i testowania infrastruktury platformy Azure przy użyciu usługi Chef
keywords: Azure, Chef, DevOps, Virtual Machines, omówienie, Automatyzowanie
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4ad8b79b42c9d8d7942f391223c052f63579b11b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158006"
---
# <a name="using-chef-with-azure"></a>Korzystanie z programu Chef z platformą Azure
[Chef](https://www.chef.io) to zaawansowana platforma automatyzacji, która przekształca infrastrukturę maszyny wirtualnej na platformę Azure w kod. Chef automatyzuje, jak infrastruktura jest konfigurowana, wdrażana i zarządzana w sieci, niezależnie od jej rozmiaru.

W tym artykule opisano zalety korzystania z programu Chef do zarządzania infrastrukturą platformy Azure.

## <a name="chef-extension-on-azure"></a>Rozszerzenie Chef na platformie Azure
Zainicjuj obsługę administracyjną maszyny wirtualnej z Chef klientem działającym jako usługa w tle z [rozszerzeniem Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) w witrynie Azure Portal. Po zainicjowaniu obsługi te maszyny wirtualne są gotowe do zarządzania przez serwer Chef.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Używaj stacji roboczej Chef bezpośrednio w Azure Cloud Shell! Uruchom wszystkie narzędzia Chef i niewłaściwe cechy dotyczące Cloud Shell. Polecenia Chef można wykorzystać z:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [zlew](https://docs.chef.io/ctl_kitchen.html)
* [dane niespecyfikacja](https://www.inspec.io/docs/reference/cli/)
* [Nóż](https://docs.chef.io/knife.html)
* [Cooka](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [Chef — Uruchom](https://www.chef.sh/docs/chef-workstation/getting-started/)

Połącz nasze narzędzia poleceń z innymi narzędziami dostępnymi w Cloud Shell, takimi jak `git`, `az-cli`i `terraform`, i zanotuj automatyzację infrastruktury i zgodności z przeglądarki.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatyzowanie infrastruktury, aplikacji i zgodności z jedną platformą
Firmy wymagają szybkości, szybkości i bezpieczeństwa do konkurowania w witrynie Digital Marketplace. Razem z Chef i firmą Microsoft, zespoły i przedsiębiorstwa, które spełniają wszystkie te rzeczy. Dzięki jednej platformie, Chef automatyzuje, możesz teraz zautomatyzować i stale dostarczać infrastrukturę, aplikacje i zgodność w ramach platformy Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Testowanie Chef na platformie Azure
Obsługiwane przez program Chef [rozwiązanie Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) , które umożliwia tworzenie i wdrażanie infrastruktury oraz aplikacji oraz zarządzanie nimi wspólnie. Jedno kliknięcie umożliwia natychmiastowe uzyskanie dostępu do wszystkich funkcji komercyjnych dostępnych w ramach automatyzacji Chef. Uzyskaj kompleksową widoczność całej floty, Włącz ciągłą zgodność i Zarządzaj wszystkimi zmianami za pomocą ujednoliconego przepływu pracy.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Chef](/azure/virtual-machines/windows/chef-automation)
