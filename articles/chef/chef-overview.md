---
title: Korzystanie z programu Chef z platformą Azure
description: Wprowadzenie do konfigurowania i testowania infrastruktury platformy Azure za pomocą programu Chef
keywords: lazur, szef kuchni, devops, maszyny wirtualne, przegląd, zautomatyzować
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586346"
---
# <a name="using-chef-with-azure"></a>Korzystanie z programu Chef z platformą Azure
[Chef](https://www.chef.io) to zaawansowana platforma automatyzacji, która przekształca infrastrukturę maszyn wirtualnych na platformie Azure w kod. Program chef automatyzuje sposób konfigurowania, wdrażania i zarządzania infrastrukturą w całej sieci, niezależnie od jej rozmiaru.

W tym artykule opisano korzyści wynikające z używania programu Chef do zarządzania infrastrukturą platformy Azure.

## <a name="chef-extension-on-azure"></a>Rozszerzenie szefa kuchni na platformie Azure
Aprowizuj maszynę wirtualną z klientem szefa kuchni uruchomionym jako usługa w tle z [rozszerzeniem szefa kuchni](https://docs.microsoft.com/azure/chef/chef-extension-portal) w [witrynie Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Po zainicjowaniu obsługi administracyjnej te maszyny wirtualne są gotowe do zarządzania przez serwer chef.

## <a name="chef-cloud-shell"></a>Powłoka chmury szefa kuchni
Użyj chef workstation bezpośrednio w usłudze Azure Cloud Shell! Uruchom wszystkie narzędzia szefa kuchni i InSpec bezpośrednio z Cloud Shell. Polecenia szefa kuchni można korzystać z:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [Kuchnia](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [Nóż](https://docs.chef.io/knife.html)
* [styl gotowania](https://docs.chef.io/cookstyle.html)
* [szef kuchni](https://www.chef.sh/docs/chef-workstation/getting-started/)

Połącz nasze narzędzia poleceń z innymi narzędziami `git` `az-cli`dostępnymi `terraform`w usłudze Cloud Shell, takimi jak , i , i zapisz swoją infrastrukturę i automatyzację zgodności z przeglądarką.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatyzacja infrastruktury, aplikacji i zgodności z jedną platformą
Firmy wymagają szybkości, prędkości i bezpieczeństwa, aby konkurować na rynku cyfrowym. Wspólnie program Chef i firma Microsoft pomagają osobom, zespołom i przedsiębiorstwom w osiąganiu wszystkich tych czynności. Dzięki jednej platformie Chef Automate możesz teraz automatyzować i stale dostarczać infrastrukturę, aplikacje i zgodność z przepisami w całej firmie Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Szefowa kuchni na dysku testowym automatyzuj na platformie Azure
Obsługiwane przez szefa kuchni [rozwiązanie Chef Automate Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) umożliwia wspólne tworzenie, wdrażanie i zarządzanie infrastrukturą i aplikacjami. Jedno kliknięcie zapewnia natychmiastowy dostęp do wszystkich funkcji komercyjnych dołączonych do Chef Automate; zyskaj kompleksową widoczność w całej flocie, zapewnij ciągłą zgodność i zarządzaj wszystkimi zmianami dzięki ujednoliconemu przepływowi pracy.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie maszyny wirtualnej systemu Windows na platformie Azure przy użyciu programu Chef](chef-automation.md)
