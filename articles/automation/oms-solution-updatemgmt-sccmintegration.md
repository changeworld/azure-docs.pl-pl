---
title: Kierować aktualizacje przy użyciu kolekcji programu SCCM w usłudze Azure Automation — rozwiązanie Update Management
description: Ten artykuł ma na celu ułatwienie konfigurowania programu System Center Configuration Manager za pomocą tego rozwiązania do zarządzania aktualizacjami komputerów zarządzanych przez program SCCM.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 92a93982cdd042a92b006cab7052ad4a6fee6fff
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478204"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integracja programu System Center Configuration Manager przy użyciu rozwiązania Update Management

Klienci, którzy zainwestowali w program System Center Configuration Manager do zarządzania komputerami, serwerami i urządzeniami przenośnymi polegają również na ich sile i dojrzałości w zarządzania aktualizacjami oprogramowania w ramach cyklu zarządzania aktualizacjami oprogramowania (SUM).

Można zgłaszać i aktualizować zarządzane serwery Windows, tworząc i wstępnie przemieszczając wdrożenia aktualizacji oprogramowania w programie Configuration Manager i uzyskać szczegółowy stan ukończonych wdrożeń aktualizacji przy użyciu [rozwiązania do zarządzania aktualizacjami](automation-update-management.md). Jeśli używasz programu Configuration Manager do raportowania zgodności aktualizacji, ale nie do zarządzania wdrożeniami aktualizacji z serwerami Windows, możesz kontynuować raportowanie do programu Configuration Manager, gdy aktualizacje zabezpieczeń są zarządzane za pomocą rozwiązania do zarządzania aktualizacjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Konieczne jest posiadanie [rozwiązanie do zarządzania aktualizacjami](automation-update-management.md) dodane do Twojego konta usługi Automation.
* Serwery z systemem Windows obecnie zarządzane przez środowisko programu System Center Configuration Manager również muszą się zgłaszać do obszaru roboczego usługi Log Analytics, która także ma włączone rozwiązanie zarządzania aktualizacjami.
* Ta funkcja jest włączona w programie System Center Configuration Manager wersji current branch 1606 i wyższej. Aby zintegrować programu Configuration Manager centralnej lokacji administracyjnej lub autonomicznej lokacji głównej przy użyciu dzienników usługi Azure Monitor i zaimportować kolekcje, zapoznaj się z [dzienniki Connect Configuration Manager do usługi Azure Monitor](../azure-monitor/platform/collect-sccm.md).  
* Agenci dla systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) albo mieć dostęp do usługi Microsoft Update, jeśli nie otrzymują aktualizacji zabezpieczeń z programu Configuration Manager.   

To, w jaki sposób zarządzasz klientami hostowanymi w usłudze Azure IaaS za pomocą istniejącego środowiska programu Configuration Manager, zależy głównie od połączenia istniejącego między centrami danych platformy Azure a Twoją infrastrukturą. Połączenie to ma wpływ na wszelkie zmiany projektowe, których wprowadzenie do infrastruktury programu Configuration Manager może być konieczne, oraz wpływa na powiązane koszty obsługi tych niezbędnych zmian. Aby zrozumieć, jakie kwestie planowania należy ocenić przed kontynuowaniem, zobacz [Menedżer konfiguracji Azure — często zadawane pytania](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurowanie

### <a name="manage-software-updates-from-configuration-manager"></a>Zarządzanie aktualizacjami oprogramowania z programu Configuration Manager 

Jeśli chcesz kontynuować zarządzanie wdrożeniami aktualizacji z programu Configuration Manager, wykonaj następujące kroki. Usługa Azure Automation nawiązuje połączenie z programem Configuration Manager do stosowania aktualizacji na komputerach klienckich, połączonego z obszarem roboczym usługi Log Analytics. Zawartość aktualizacji jest dostępna w pamięci podręcznej komputera klienckiego tak, jakby wdrożenie było zarządzane przez program Configuration Manager.

1. Tworzenie wdrożenia aktualizacji oprogramowania z lokacji najwyższego poziomu w hierarchii programu Configuration Manager przy użyciu procesu opisanego w [procesie wdrażania aktualizacji oprogramowania](/sccm/sum/deploy-use/deploy-software-updates). Jedynym ustawieniem, które musi być skonfigurowane inaczej niż dla standardowego wdrożenia, jest opcja **Nie instaluj aktualizacji oprogramowania** służąca do sterowania zachowaniem pakietu wdrożeniowego podczas pobierania. To zachowanie jest zarządzane przez rozwiązanie do zarządzania aktualizacjami poprzez utworzenie planowego wdrożenia aktualizacji w następnym kroku.

1. W usłudze Azure Automation, wybierz **rozwiązania Update Management**. Utwórz nowe wdrożenie wykonując kroki opisane w [tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz **zaimportowane grupy** na **typu** listy rozwijanej można wybrać odpowiednią Kolekcja programu Configuration Manager. Należy pamiętać o następujących kwestiach:. Jeśli okno obsługi jest zdefiniowane w wybranej kolekcji urządzeń programu Configuration Manager, elementy członkowskie kolekcji przestrzegać go zamiast **czas trwania** ustawień zdefiniowanych w zaplanowanym wdrożeniu.
    b. Członkowie kolekcji docelowej muszą mieć połączenie z Internetem (bezpośrednio, za pośrednictwem serwera proxy lub za pośrednictwem bramy usługi Log Analytics).

Po zakończeniu wdrażania aktualizacji za pomocą usługi Azure Automation, komputerów docelowych, które są członkami wybranej grupy komputerów zainstalują aktualizacje o zaplanowanym czasie z ich lokalnej pamięci podręcznej klienta. Możesz [wyświetlić stan wdrożenia aktualizacji](automation-tutorial-update-management.md#view-results-of-an-update-deployment), aby monitorować wyniki swojego wdrożenia.

### <a name="manage-software-updates-from-azure-automation"></a>Zarządzanie aktualizacjami oprogramowania z usługi Azure Automation

Do zarządzania aktualizacjami maszyn wirtualnych z systemem Windows Server, które są klientami programu Configuration Manager, należy skonfigurować zasady klienta, aby wyłączyć funkcję zarządzania aktualizacjami oprogramowania dla wszystkich klientów zarządzanych przez to rozwiązanie. Domyślnie ustawienia klienta dotyczą wszystkich urządzeń w hierarchii. Aby uzyskać więcej informacji na temat tego ustawienia, zasad i sposobu ich konfigurowania, przejrzyj artykuł [Jak skonfigurować ustawienia klienta w programie System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Po wprowadzeniu tej zmiany konfiguracji, możesz utworzyć nowe wdrożenie wykonując kroki opisane w [tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz **zaimportowane grupy** na **typu** listy rozwijanej można wybrać odpowiednią kolekcję programu Configuration Manager.

## <a name="next-steps"></a>Kolejne kroki

