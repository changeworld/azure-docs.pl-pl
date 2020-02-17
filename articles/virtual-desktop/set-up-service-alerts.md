---
title: Konfigurowanie alertów usługi dla pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować Azure Service Health do odbierania powiadomień usługi dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
ms.openlocfilehash: 8c062c1ce8e938ac426a10c41161eb7e28f3dcba
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368772"
---
# <a name="tutorial-set-up-service-alerts"></a>Samouczek: Konfigurowanie alertów usługi

Azure Service Health służy do monitorowania problemów z usługami i klasyfikatorów kondycji dla pulpitu wirtualnego systemu Windows. Azure Service Health może powiadomić użytkownika o różnych typach alertów (np. wiadomości e-mail lub wiadomości SMS), pomóc Ci zrozumieć efekt problemu i aktualizować je w miarę rozwiązywania problemu. Azure Service Health może również pomóc w ograniczeniu przestoju i przygotowaniu się do planowanej konserwacji i zmian, które mogą mieć wpływ na dostępność zasobów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i Konfigurowanie alertów usługi.

Aby dowiedzieć się więcej na temat Azure Service Health, zobacz dokumentację dotyczącą [kondycji platformy Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Samouczek: Tworzenie dzierżawy w programie Virtual Desktop systemu Windows](tenant-setup-azure-active-directory.md)
- [Samouczek: tworzenie jednostek usługi i przypisań ról przy użyciu programu PowerShell](create-service-principal-role-powershell.md)
- [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Tworzenie alertów usługi

W tej sekcji przedstawiono sposób konfigurowania Azure Service Health i konfigurowania powiadomień, do których można uzyskać dostęp na Azure Portal. Można skonfigurować różne typy alertów i zaplanować ich powiadomienie w odpowiednim czasie.

### <a name="recommended-service-alerts"></a>Zalecane alerty usługi

Zalecamy tworzenie alertów usługi dla następujących typów zdarzeń kondycji:

- **Problem z usługą:** Otrzymuj powiadomienia o najważniejszych problemach, które mają wpływ na łączność użytkowników z usługą lub z możliwością zarządzania dzierżawcą pulpitów wirtualnych systemu Windows.
- **Poradnik dotyczący kondycji:** Otrzymuj powiadomienia, które wymagają Twojej uwagi. Poniżej przedstawiono kilka przykładów tego typu powiadomienia:
    - Virtual Machines (maszyny wirtualne) nie są bezpiecznie skonfigurowane jako otwarty port 3389
    - Wycofanie funkcjonalności

### <a name="configure-service-alerts"></a>Konfigurowanie alertów usługi

Aby skonfigurować alerty usługi:

1. Zaloguj się do [Azure portal](https://portal.azure.com/).
2. Wybierz **Service Health.**
3. Aby skonfigurować alerty i powiadomienia, użyj instrukcji w temacie [tworzenie alertów dziennika aktywności w ramach powiadomień dotyczących usług](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania i używania Azure Service Health do monitorowania problemów z usługami i klasyfikatorów kondycji dla pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak zalogować się do pulpitu wirtualnego systemu Windows, przejdź do programu Windows Virtual Desktop how-OTS.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z klientem Pulpit zdalny w systemach Windows 7 i Windows 10](./connect-windows-7-and-10.md)
