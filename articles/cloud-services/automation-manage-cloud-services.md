---
title: Zarządzanie usługami w chmurze platformy Azure przy użyciu usługi Azure Automation | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Azure Automation może służyć do zarządzania usługami w chmurze platformy Azure na dużą skalę.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72439077"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Zarządzanie usługami w chmurze platformy Azure przy użyciu automatyzacji platformy Azure
W tym przewodniku przedstawiono usługę Azure Automation oraz sposób jej użycia w celu uproszczenia zarządzania usługami w chmurze platformy Azure.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure ułatwiająca zarządzanie chmurą za pomocą automatyzacji procesów. Korzystanie z usługi Azure Automation, długotrwałe, ręczne, podatne na błędy i często powtarzane zadania można zautomatyzować, aby zwiększyć niezawodność, wydajność i czas na wartość dla twojej organizacji.

Usługa Azure Automation zapewnia wysoce niezawodny i wysoce dostępny aparat wykonywania przepływu pracy, który jest skalowany w zależności od potrzeb w miarę rozwoju organizacji. W usłudze Azure Automation procesy można uruchamiać ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania są wykonywane dokładnie wtedy, gdy są potrzebne.

Zmniejsz obciążenie operacyjne i uwolnij pracowników DZIAŁU IT / DevOps, aby skupić się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>W jaki sposób usługa Azure Automation może pomóc w zarządzaniu usługami w chmurze platformy Azure?
Usługi w chmurze platformy Azure można zarządzać w usłudze Azure Automation przy użyciu poleceń cmdlet programu PowerShell dostępnych w [narzędziach programu Azure PowerShell.](/powershell/) Usługa Azure Automation ma te polecenia cmdlet usługi w chmurze Programu PowerShell dostępne po wyjęciu z pudełka, dzięki czemu można wykonywać wszystkie zadania zarządzania usługami w chmurze w ramach usługi. Te polecenia cmdlet w usłudze Azure Automation można również sparować z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w usługach platformy Azure i systemach innych firm.

Niektóre przykłady użycia usługi Azure Automation do zarządzania usługami w chmurze platformy Azure obejmują:

* [Ciągłe wdrażanie usługi w chmurze za każdym razem, gdy cscfg lub cspkg jest aktualizowane w magazynie obiektów Blob platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Równoległe uruchamianie wystąpień usługi w chmurze, jedna domena uaktualniająca naraz](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznaliście podstawy usługi Azure Automation i sposób, w jaki można jej używać do zarządzania usługami w chmurze platformy Azure, skorzystaj z tych łączy, aby dowiedzieć się więcej o usłudze Azure Automation.

* [Omówienie usługi Azure Automation](../automation/automation-intro.md)
* [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
