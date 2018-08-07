---
title: Zarządzanie magazynem platformy Azure przy użyciu usługi Azure Automation
description: Naucz się, jak usługa Azure Automation można zarządzać usługą Azure Storage na dużą skalę.
services: storage, automation
author: jodoglevy
ms.service: storage
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.component: common
ms.openlocfilehash: 82ec929c8d3055187a83179432fc601baa191cc4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526372"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Zarządzanie magazynem platformy Azure przy użyciu usługi Azure Automation
Ten przewodnik Wprowadzenie do usługi Azure Automation i jak może służyć do uproszczenia zarządzania obiektów blob, tabel i kolejek usługi Azure Storage.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure dla uproszczenie zarządzania chmurą poprzez automatyzację procesów. Za pomocą usługi Azure Automation, długotrwałych, ręczne, podatne na błędy i często powtarzanych zadań może zostać zautomatyzowane, aby zwiększyć niezawodność i wydajność i skrócić czas do wartości dla swojej organizacji.

Usługa Azure Automation zapewnia aparat wykonywania przepływu pracy o wysokiej niezawodności i o wysokiej dostępności, który jest skalowany w celu spełnienia konkretnych potrzeb w miarę rozwoju organizacji. W usłudze Azure Automation procesów może być rozpoczęła się ręcznie, przez systemy innych firm 3 lub w zaplanowanych odstępach czasu tak, aby zadania się tak zdarzyć, dokładnie tak, gdy jest to potrzebne.

Niższe koszty operacyjne i Zwolnij część IT / pracownicy działu DevOps, aby skupić się na pracy, który dodaje firm wartość przez przeniesienie zadań zarządzania systemem chmury uruchamiana automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Jak usługa Azure Automation mogą pomóc zarządzać usługą Azure Storage?
Usługa Azure Storage można zarządzać w usłudze Azure Automation za pomocą poleceń cmdlet programu PowerShell, które są dostępne w [programu Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Usługa Azure Automation obejmuje tych poleceń cmdlet programu PowerShell magazynu gotowych, tak, aby przeprowadzić wszystkich obiektów blob, tabela i kolejka zadań zarządzania w ramach usługi. Można również skojarzyć tych poleceń cmdlet w usłudze Azure Automation za pomocą poleceń cmdlet dla innych usług platformy Azure do automatyzacji złożonych zadań obejmujących wiele usług platformy Azure i systemami firm 3.

[Galerii elementów runbook usługi Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) zawiera szereg produktu zespół i społeczności elementy runbook, aby rozpocząć automatyzację zarządzania usługi Azure Storage, innych usług platformy Azure i systemami firm 3. Galeria elementów runbook obejmują:

* [Usuwanie magazynu obiektów blob platformy Azure, które są niektóre dni stare przy użyciu usługi Automation](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Pobieranie obiektu Blob z usługi Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Utworzyć kopię zapasową wszystkich dysków w jednej maszyny wirtualnej platformy Azure lub dla wszystkich maszyn wirtualnych w usłudze w chmurze](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Azure Automation i jak może służyć do zarządzania obiektów blob, tabel i kolejek usługi Azure Storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Automation.

Zapoznaj się z samouczkiem usługi Azure Automation [Tworzenie lub importowanie elementu runbook w usłudze Azure Automation](../../automation/automation-creating-importing-runbook.md).

