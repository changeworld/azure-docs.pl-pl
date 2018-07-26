---
title: Omówienie DSC usługi Azure Automation
description: Omówienie programu Azure Automation Desired State Configuration (DSC), jego warunki i znane problemy
keywords: PowerShell dsc, konfiguracja żądanego stanu, azure dsc programu powershell
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6d49ffa59ed53c0a1966a4132fd5fe1689a13ce
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247353"
---
# <a name="azure-automation-dsc-overview"></a>Omówienie DSC usługi Azure Automation

Usługa Azure Automation DSC jest usługą platformy Azure, która pozwala na zapis, zarządzanie i skompilować PowerShell Desired State Configuration (DSC) [konfiguracje](https://msdn.microsoft.com/powershell/dsc/configurations), zaimportuj [zasoby DSC](https://msdn.microsoft.com/powershell/dsc/resources)i przypisz konfiguracje Aby węzły docelowe, wszystko to w chmurze.

## <a name="why-use-azure-automation-dsc"></a>Dlaczego warto korzystać z usługi Azure Automation DSC

Usługa Azure Automation DSC zapewnia kilka korzyści za pośrednictwem za pomocą DSC spoza platformy Azure.

### <a name="built-in-pull-server"></a>Serwerze ściągania wbudowane

Usługa Azure Automation zapewnia DSC serwera ściągania, podobnie jak [Windows funkcji DSC usługi](/powershell/dsc/pullserver) tak, aby węzły docelowe automatycznie otrzymywać konfiguracje, są zgodne z żądanego stanu i raportować o swojej zgodności.
Serwerze ściągania wbudowane w usłudze Azure Automation eliminuje potrzebę konfiguracji i utrzymania własnym serwerze ściągania.
Usługa Azure Automation mogą określać docelową maszyn wirtualnych lub fizycznych Windows lub Linux, w chmurze lub lokalnie.

### <a name="management-of-all-your-dsc-artifacts"></a>Zarządzanie Twoimi artefaktami DSC

Usługa Azure Automation DSC zapewnia tę samą warstwę zarządzania [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) usługa Azure Automation zapewnia skryptów programu PowerShell.

W witrynie Azure portal lub za pomocą programu PowerShell można zarządzać wszystkie usługi DSC konfiguracji, zasobów i węzły docelowe.

![Zrzut ekranu przedstawiający blok usługi Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importuj dane raportowania w usłudze Log Analytics

Węzły, które są zarządzane za pomocą usługi Azure Automation DSC wysyłać szczegółowe dane raportowania stanu serwera ściągania wbudowanych.
Można skonfigurować usługi Azure Automation DSC można wysyłać te dane do swojego obszaru roboczego usługi Log Analytics.
Aby dowiedzieć się, jak wysyłać dane o stanie DSC do obszaru roboczego usługi Log Analytics, zobacz [do przodu automatyzacji Azure DSC reporting danych do usługi Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Wideo z wprowadzeniem

Wolisz obejrzeć film niż przeczytać artykuł? Poniższy klip wideo z maja 2015 roku, kiedy usługi Azure Automation DSC zostało po raz pierwszy zapoznaj się z.

>[!NOTE]
>Gdy pojęć i cyklu życia omówione w tym filmie są poprawne, Azure Automation DSC rozwinęła ponieważ zarejestrowano ten film wideo.
>Ona jest teraz ogólnie dostępna, jest znacznie bardziej rozległe interfejsu użytkownika w witrynie Azure portal i obsługuje wiele dodatkowych możliwości.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak dołączyć węzły były zarządzane za pomocą usługi Azure Automation DSC, zobacz [dołączanie maszyn w celu zarządzania przez usługi Azure Automation DSC](automation-dsc-onboarding.md)
* Aby rozpocząć pracę, przy użyciu usługi Azure Automation DSC, zobacz [wprowadzenie do usługi Azure Automation DSC](automation-dsc-getting-started.md)
* Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w usłudze Azure Automation DSC](automation-dsc-compile.md)
* Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Automation DSC, można zobaczyć [poleceń cmdlet usługi Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
* Aby zapoznać się z przykładem użycia usługi Azure Automation DSC w potoku ciągłego wdrażania, zobacz [ciągłe wdrażanie w usłudze IaaS maszyn wirtualnych przy użyciu usługi Azure Automation DSC i Chocolatey](automation-dsc-cd-chocolatey.md)
