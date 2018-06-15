---
title: Zarządzanie przy użyciu automatyzacji Azure usługi w chmurze Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat jak usługa Automatyzacja Azure może służyć do zarządzania usługami w chmurze platformy Azure na dużą skalę.
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
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842937"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Zarządzania usługami w chmurze Azure przy użyciu usługi Automatyzacja Azure
W tym przewodniku przedstawiono usługi Automatyzacja Azure oraz jak on używany w celu uproszczenia zarządzania usług w chmurze Azure.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Automatyzacja Azure](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure, dla uproszczenia zarządzania chmurą za pomocą automatyzacji procesu. Przy użyciu usługi Automatyzacja Azure, można zautomatyzować długotrwałe, ręcznych, podatnych i często powtarzanych zadań, aby zwiększyć niezawodność, wydajność i wartość czasu dla Twojej organizacji.

Automatyzacja Azure umożliwia aparatowi wykonywania przepływów pracy wysoce niezawodne i wysokiej dostępności, która może obsłużyć do własnych potrzeb miarę rozwoju organizacji. W automatyzacji Azure procesów może być rozpoczęte ręcznie, przez systemy 3rd firmy lub w zaplanowanych odstępach czasu tak, aby zadania stanie dokładnie w razie potrzeby.

Obniżyć koszty operacyjne i zwolnić IT / personel DevOps skupić się na pracy, który dodaje biznesowych wartość przez przeniesienie zadań zarządzania chmury do automatycznego uruchamiania przez usługi Automatyzacja Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak usługi Automatyzacja Azure ułatwia zarządzanie usług w chmurze Azure?
Usługi w chmurze platformy Azure można zarządzać w automatyzacji Azure za pomocą poleceń cmdlet programu PowerShell, które są dostępne w [narzędzia programu Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Usługi Automatyzacja Azure ma te chmury usługi poleceń cmdlet programu PowerShell dostępne fabrycznej, dzięki czemu można wykonywać wszystkie zadania zarządzania usługi chmury w ramach usługi. Można również skojarzyć te polecenia cmdlet usługi Automatyzacja Azure, za pomocą poleceń cmdlet dla innych usług Azure, aby zautomatyzować złożone zadania 3 systemów firm i usług Azure.

Niektóre przykładowe zastosowania usługi Automatyzacja Azure do zarządzania usługami w chmurze Azure obejmują:

* [Ciągłej wdrożenia usługi w chmurze przy każdej aktualizacji cscfg lub cspkg w magazynie obiektów Blob platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Ponowne uruchomienie wystąpienia usługi w chmurze równolegle domeny uaktualnienia pojedynczo](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Automatyzacja Azure i jak może służyć do zarządzania usługami w chmurze platformy Azure, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat automatyzacji Azure.

* [Omówienie usługi Automatyzacja Azure](../automation/automation-intro.md)
* [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa uczenia usługi Automatyzacja Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
