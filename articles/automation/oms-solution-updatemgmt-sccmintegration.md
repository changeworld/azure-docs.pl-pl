---
title: Kierować aktualizacje przy użyciu kolekcji SCCM automatyzacji Azure — zarządzanie aktualizacjami
description: Ten artykuł ma na celu ułatwienie konfigurowania programu System Center Configuration Manager za pomocą tego rozwiązania do zarządzania aktualizacjami komputerów zarządzanych przez program SCCM.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3ea95899d48b68c78af5fdc45167b08b5e0fc1ee
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integracja programu System Center Configuration Manager z zarządzania aktualizacjami

Klienci, którzy zainwestowali w program System Center Configuration Manager do zarządzania komputerami, serwerami i urządzeniami przenośnymi polegają również na ich sile i dojrzałości w zarządzania aktualizacjami oprogramowania w ramach cyklu zarządzania aktualizacjami oprogramowania (SUM).

Raport i zaktualizować zarządzanych systemów Windows Server, tworząc i wstępnie przemieszczania wdrożenia aktualizacji oprogramowania w programie Configuration Manager i uzyskać szczegółowy stan wdrożenia aktualizacji ukończone przy użyciu [rozwiązania do zarządzania aktualizacji](automation-update-management.md). Jeśli używasz programu Configuration Manager do raportowania zgodności aktualizacji, ale nie do zarządzania wdrożeniami aktualizacji z serwerów z systemem Windows, możesz kontynuować raportowania do programu Configuration Manager, podczas gdy aktualizacje zabezpieczeń są zarządzane za pomocą rozwiązania do zarządzania aktualizacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Musi mieć [rozwiązania do zarządzania aktualizacjami](automation-update-management.md) dodany do Twojego konta automatyzacji.
* Serwery z systemem Windows obecnie zarządzane przez środowisko programu System Center Configuration Manager również muszą się zgłaszać do obszaru roboczego usługi Log Analytics, która także ma włączone rozwiązanie zarządzania aktualizacjami.
* Ta funkcja jest włączona w System Center Configuration Manager bieżącą wersję gałęzi 1606 i wyższych. Aby zintegrować swoją centralną lokację administracyjną lub autonomiczną lokację główną programu Configuration Manager z usługą Log Analytics i kolekcjami importowania, zobacz [Connect Configuration Manager to Log Analytics](../log-analytics/log-analytics-sccm.md) (Łączenie programu Configuration Manager z usługą Log Analytics).  
* Agenci dla systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) albo mieć dostęp do usługi Microsoft Update, jeśli nie otrzymują aktualizacji zabezpieczeń z programu Configuration Manager.   

To, w jaki sposób zarządzasz klientami hostowanymi w usłudze Azure IaaS za pomocą istniejącego środowiska programu Configuration Manager, zależy głównie od połączenia istniejącego między centrami danych platformy Azure a Twoją infrastrukturą. Połączenie to ma wpływ na wszelkie zmiany projektowe, których wprowadzenie do infrastruktury programu Configuration Manager może być konieczne, oraz wpływa na powiązane koszty obsługi tych niezbędnych zmian. Aby zrozumieć, jakie kwestie planowania należy ocenić przed kontynuowaniem, zobacz [Menedżer konfiguracji Azure — często zadawane pytania](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurowanie

### <a name="manage-software-updates-from-configuration-manager"></a>Zarządzanie aktualizacjami oprogramowania z programu Configuration Manager 

Jeśli chcesz kontynuować zarządzanie wdrożeniami aktualizacji z programu Configuration Manager, wykonaj następujące kroki. Automatyzacja Azure łączy do programu Configuration Manager do stosowania aktualizacji na komputerach klienckich podłączone do obszaru roboczego analizy dzienników. Zawartość aktualizacji jest dostępna w pamięci podręcznej komputera klienckiego tak, jakby wdrożenie było zarządzane przez program Configuration Manager.

1. Tworzenie wdrożenia aktualizacji oprogramowania z lokacji najwyższego poziomu w hierarchii programu Configuration Manager przy użyciu procesu opisanego w [procesie wdrażania aktualizacji oprogramowania](/sccm/sum/deploy-use/deploy-software-updates). Jedynym ustawieniem, które musi być skonfigurowane inaczej niż dla standardowego wdrożenia, jest opcja **Nie instaluj aktualizacji oprogramowania** służąca do sterowania zachowaniem pakietu wdrożeniowego podczas pobierania. To zachowanie jest zarządzana przez rozwiązanie do zarządzania aktualizacji przez tworzenie wdrożenia zaplanowanych aktualizacji w następnym kroku.

1. W usłudze Automatyzacja Azure, wybierz **zarządzania aktualizacjami**. Utwórz nowe wdrożenie wykonanie kroków opisanych w [tworzenia wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz **zaimportowane grupy** na **typu** listy rozwijanej wybierz odpowiednie Kolekcja programu Configuration Manager. Należy pamiętać o następujących kwestiach:. Jeśli okno obsługi jest zdefiniowany w wybranej kolekcji urządzeń programu Configuration Manager, elementy członkowskie kolekcji przestrzegać go zamiast **czas trwania** ustawień zdefiniowanych w zaplanowane wdrożenie.
    b. Składowe kolekcji docelowej muszą mieć połączenie z Internetem (bezpośrednio, za pośrednictwem serwera proxy albo za pośrednictwem bramy pakietu OMS).

Po zakończeniu wdrażania aktualizacji za pomocą usługi Automatyzacja Azure, komputerach docelowych, które są elementami członkowskimi grupy wybranego komputera zainstaluje aktualizacje zgodnie z harmonogramem ze swojej lokalnej pamięci podręcznej. Możesz [wyświetlić stan wdrożenia aktualizacji](automation-tutorial-update-management.md#view-results-of-an-update-deployment), aby monitorować wyniki swojego wdrożenia.

### <a name="manage-software-updates-from-azure-automation"></a>Zarządzanie aktualizacjami oprogramowania z usługi Automatyzacja Azure

Do zarządzania aktualizacjami maszyn wirtualnych z systemem Windows Server, które są klientami programu Configuration Manager, należy skonfigurować zasady klienta, aby wyłączyć funkcję zarządzania aktualizacjami oprogramowania dla wszystkich klientów zarządzanych przez to rozwiązanie. Domyślnie ustawienia klienta dotyczą wszystkich urządzeń w hierarchii. Aby uzyskać więcej informacji na temat tego ustawienia, zasad i sposobu ich konfigurowania, przejrzyj artykuł [Jak skonfigurować ustawienia klienta w programie System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Po wykonaniu tej zmiany konfiguracji, należy utworzyć nowe wdrożenie wykonanie kroków opisanych w [tworzenia wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz **zaimportowane grupy** na **typu** listy rozwijanej wybierz odpowiednią kolekcję programu Configuration Manager.

## <a name="next-steps"></a>Kolejne kroki
