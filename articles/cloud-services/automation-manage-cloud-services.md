---
title: Zarządzanie usługami w chmurze platformy Azure przy użyciu usługi Azure Automation | Dokumentacja firmy Microsoft
description: Naucz się, jak używać usługi Azure Automation do zarządzania usługami platformy Azure w chmurze na dużą skalę.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: b3660901c86dd644369e6d1913e825cbd5ea316b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623208"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Zarządzanie usług Azure Cloud Services przy użyciu usługi Azure Automation
Ten przewodnik Wprowadzenie do usługi Azure Automation i jak może służyć do uproszczenia zarządzania usługami Azure cloud services.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure dla uproszczenie zarządzania chmurą poprzez automatyzację procesów. Za pomocą usługi Azure Automation, długotrwałych, ręczne, podatne na błędy i często powtarzanych zadań można zautomatyzować zwiększyć niezawodność, wydajność i czasy wykonywania operacji dla Twojej organizacji.

Usługa Azure Automation zapewnia aparat wykonywania przepływu pracy o wysokiej niezawodności i o wysokiej dostępności, który jest skalowany w celu spełnienia konkretnych potrzeb w miarę rozwoju organizacji. W usłudze Azure Automation procesów może być rozpoczęła się ręcznie, przez systemy innych firm 3 lub w zaplanowanych odstępach czasu tak, aby zadania się tak zdarzyć, dokładnie tak, gdy jest to potrzebne.

Niższe koszty operacyjne i Zwolnij część IT / pracownicy działu DevOps, aby skupić się na pracy, który dodaje firm wartość przez przeniesienie zadań zarządzania systemem chmury uruchamiana automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak usługa Azure Automation ułatwiają zarządzanie usług Azure cloud services?
Usług Azure cloud services można zarządzać w usłudze Azure Automation za pomocą poleceń cmdlet programu PowerShell, które są dostępne w [narzędzia programu Azure PowerShell](/powershell/). Usługa Azure Automation obejmuje te chmury usługi poleceń cmdlet programu PowerShell dostępnych gotowych, tak, aby wykonywać wszystkie zadania zarządzania usługi chmury w ramach usługi. Można również skojarzyć tych poleceń cmdlet w usłudze Azure Automation za pomocą poleceń cmdlet dla innych usług platformy Azure do automatyzacji złożonych zadań obejmujących wiele usług platformy Azure i systemami firm 3.

Niektóre przykład użycia usługi Azure Automation do zarządzania usługi Azure Cloud Services obejmują:

* [Ciągłe wdrażanie usługi w chmurze przy każdej aktualizacji pliku cscfg lub cspkg w usłudze Azure Blob storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Ponowne uruchamianie wystąpień usługi w chmurze w sposób równoległy, jedną domenę uaktualnienia w czasie](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Azure Automation i jak może służyć do zarządzania usługami w chmurze platformy Azure, skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Automation.

* [Omówienie usługi Azure Automation](../automation/automation-intro.md)
* [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa uczenia się usługi Azure Automation](https://azure.microsoft.com/documentation/learning-paths/automation/)
