---
title: Konfigurowanie alertów usługi dla pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować usługę Azure Service Health do odbierania powiadomień o usługach dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127715"
---
# <a name="tutorial-set-up-service-alerts"></a>Samouczek: Konfigurowanie alertów usługi

Usługi Azure Service Health można używać do monitorowania problemów z usługą i poradatoriów kondycji pulpitu wirtualnego systemu Windows. Usługa Azure Service Health może powiadamiać o różnych typach alertów (na przykład wiadomości e-mail lub SMS), pomaga zrozumieć efekt problemu i aktualizować użytkownika w miarę rozwiązywania problemu. Usługa Azure Service Health może również pomóc w ograniczeniu przestojów i przygotować się do planowanej konserwacji i zmian, które mogą mieć wpływ na dostępność zasobów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i konfigurowanie alertów usługi.

Aby dowiedzieć się więcej o usłudze Azure Service Health, zobacz [dokumentację kondycji platformy Azure.](https://docs.microsoft.com/azure/service-health/)

## <a name="prerequisites"></a>Wymagania wstępne

- [Samouczek: Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows](tenant-setup-azure-active-directory.md)
- [Samouczek: Tworzenie podmiotów zabezpieczeń usługi i przypisań ról za pomocą programu PowerShell](create-service-principal-role-powershell.md)
- [Samouczek: Tworzenie puli hostów w portalu Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Tworzenie alertów usługi

W tej sekcji pokazano, jak skonfigurować usługę Azure Service Health i jak skonfigurować powiadomienia, do których można uzyskać dostęp w witrynie Azure portal. Można skonfigurować różne typy alertów i zaplanować ich powiadomienie w odpowiednim czasie.

### <a name="recommended-service-alerts"></a>Zalecane alerty serwisowe

Zaleca się tworzenie alertów usługi dla następujących typów zdarzeń kondycji:

- **Problem z usługą:** Otrzymuj powiadomienia o głównych problemach, które wpływają na łączność użytkowników z usługą lub z możliwością zarządzania dzierżawą pulpitu wirtualnego systemu Windows.
- **Doradztwo zdrowotne:** Otrzymuj powiadomienia, które wymagają twojej uwagi. Oto kilka przykładów tego typu powiadomień:
    - Maszyny wirtualne (maszyny wirtualne) nie są bezpiecznie skonfigurowane jako otwarty port 3389
    - Wycofanie funkcjonalności

### <a name="configure-service-alerts"></a>Konfigurowanie alertów usługi

Aby skonfigurować alerty usługi:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Wybierz **pozycję Kondycja usługi.**
3. Użyj instrukcji w [Tworzenie alertów dziennika aktywności w powiadomieniach o usługach,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) aby skonfigurować alerty i powiadomienia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak skonfigurować i używać usługi Azure Service Health do monitorowania problemów z usługą i poradatoriów kondycji dla pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak zalogować się do pulpitu wirtualnego systemu Windows, przejdź do funkcji Podłączanie do pulpitu wirtualnego systemu Windows.

> [!div class="nextstepaction"]
> [Łączenie się z klientem pulpitu zdalnego w systemach Windows 7 i Windows 10](./connect-windows-7-and-10.md)
