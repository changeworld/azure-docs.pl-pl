---
title: Omówienie konfiguracji stanu usługi Azure Automation
description: Omówienie programu Azure automatyzacji stanu Configuration (DSC), jego warunki i znane problemy
keywords: PowerShell dsc, konfiguracja żądanego stanu, azure dsc programu powershell
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2d46803f81b369f8f24a6f0e3c7f32955503e4a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006065"
---
# <a name="azure-automation-state-configuration-overview"></a>Omówienie konfiguracji stanu usługi Azure Automation

Konfiguracja usługi Azure Automation stanu jest usługą platformy Azure, która pozwala na zapis, zarządzanie i skompilować PowerShell Desired State Configuration (DSC) [konfiguracje](/powershell/dsc/configurations), zaimportuj [zasoby DSC](/powershell/dsc/resources), i Przypisz konfiguracje do węzłów docelowych, wszystko to w chmurze.

## <a name="why-use-azure-automation-state-configuration"></a>Dlaczego warto korzystać z usługi Azure Automation stanu konfiguracji

Konfiguracja stanu usługi Azure Automation oferuje następujące korzyści za pośrednictwem za pomocą DSC spoza platformy Azure.

### <a name="built-in-pull-server"></a>Serwerze ściągania wbudowane

Konfiguracja stanu usługi Azure Automation zapewnia DSC serwera ściągania, podobnie jak [Windows funkcji DSC usługi](/powershell/dsc/pullserver) tak, aby węzły docelowe automatycznie otrzymywać konfiguracje, są zgodne z żądanego stanu i raportować o ich zgodność. Serwerze ściągania wbudowane w usłudze Azure Automation eliminuje potrzebę konfiguracji i utrzymania własnym serwerze ściągania. Usługa Azure Automation mogą określać docelową maszyn wirtualnych lub fizycznych Windows lub Linux, w chmurze lub lokalnie.

### <a name="management-of-all-your-dsc-artifacts"></a>Zarządzanie Twoimi artefaktami DSC

Konfiguracja stanu usługi Azure Automation oferuje tę samą warstwę zarządzania [PowerShell Desired State Configuration](/powershell/dsc/overview) usługa Azure Automation zapewnia skryptów programu PowerShell.

W witrynie Azure portal lub za pomocą programu PowerShell można zarządzać wszystkie usługi DSC konfiguracji, zasobów i węzły docelowe.

![Zrzut ekranu przedstawiający blok usługi Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importuj dane raportowania w usłudze Log Analytics

Węzły, które są zarządzane za pomocą usługi Azure Automation stan konfiguracji Wyślij szczegółowe dane raportowania stanu na serwerze ściągania wbudowanych. Można skonfigurować konfiguracji stan automatyzacji platformy Azure, aby wysyłać te dane do swojego obszaru roboczego usługi Log Analytics. Aby dowiedzieć się, jak wysyłać dane stanu konfiguracji stanu do obszaru roboczego usługi Log Analytics, zobacz [do przodu Azure Automation konfiguracji stanu raportowania danych do dziennika nalytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Wideo z wprowadzeniem

Wolisz obejrzeć film niż przeczytać artykuł? Poniższy klip wideo z maja 2015 r. podczas konfiguracji stan automatyzacji platformy Azure została po raz pierwszy zapoznaj się z.

> [!NOTE]
> Gdy pojęć i cyklu życia omówione w tym filmie są poprawne, Konfiguracja stanu automatyzacji Azure rozwinęła ponieważ zarejestrowano ten film wideo. Ona jest teraz ogólnie dostępna, jest znacznie bardziej rozległe interfejsu użytkownika w witrynie Azure portal i obsługuje wiele dodatkowych możliwości.

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dodawanie węzłów, zobacz [dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)