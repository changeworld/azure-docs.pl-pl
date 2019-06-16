---
title: Konfigurowanie alertów usług for Windows Desktop wirtualnego — platformy Azure
description: Jak skonfigurować usługi Azure Service Health, aby otrzymywać powiadomienia usługi Windows pulpitu wirtualnego.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: v-chjenk
ms.openlocfilehash: cae75f16da2cad453c74b7e6e9fb62dd789fe5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081371"
---
# <a name="tutorial-set-up-service-alerts"></a>Samouczek: Konfigurowanie alertów usług

Monitorowanie problemów z usługą i porad dotyczących kondycji dla Windows pulpitu wirtualnego, można użyć usługi Azure Service Health. Usługa Azure Service Health może powiadomić o różnych typów alertów (na przykład wiadomości e-mail lub SMS), ułatwić zrozumienie wpływu problemu i Zachowaj zaktualizowane zgodnie z rozwiązuje ten problem. Usługa Azure Service Health może również pomóc ograniczyć przestoje i przygotować się do zaplanowanej konserwacji i zmian, które mogą wpłynąć na dostępność zasobów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i Konfigurowanie alertów usług.

Aby dowiedzieć się więcej na temat usługi Azure Service Health, zobacz [dokumentacji kondycji platformy Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Samouczek: Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Samouczek: Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Samouczek: Utwórz pulę hosta za pomocą portalu Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Tworzenie alertów usług

W tej sekcji przedstawiono sposób konfigurowania usługi Azure Service Health oraz sposobu konfigurowania powiadomień, które są dostępne w witrynie Azure portal. Można skonfigurować różne typy alertów i zaplanować ich, aby otrzymywać powiadomienia, w odpowiednim czasie.

### <a name="recommended-service-alerts"></a>Zalecana usługa alerty

Zalecane jest tworzenie alertów usług dla następujących typów zdarzeń kondycji:

- **Problem z usługą:** Otrzymuj powiadomienia na poważne problemy, które mają wpływ na łączność użytkowników z usługi lub możliwości zarządzania dzierżawą Windows pulpitu wirtualnego.
- **Porada dotycząca kondycji:** Otrzymywanie powiadomień, które wymagają Twojej uwagi. Poniżej przedstawiono kilka przykładów ten typ powiadomienia:
    - Maszyny wirtualne (VM) nie jest prawidłowo skonfigurowany jako otwarty port 3389
    - Ogłoszone jako przestarzałe funkcje

### <a name="configure-service-alerts"></a>Konfigurowanie alertów usług

Aby skonfigurować alerty usług:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz **kondycji usługi.**
3. Postępuj zgodnie z instrukcjami w [Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) skonfigurować alerty i powiadomienia.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób konfigurowania i używania usługi Azure Service Health monitorowania problemów z usługami i porad dotyczących kondycji dla Windows pulpitu wirtualnego. Aby dowiedzieć się więcej o tym, jak zalogować się do Windows pulpitu wirtualnego, nadal połączyć się z wirtualnej How-tos pulpitu Windows.

> [!div class="nextstepaction"]
> [Nawiązać połączenie z klienta pulpitu zdalnego na Windows 7 i Windows 10](./connect-windows-7-and-10.md)
