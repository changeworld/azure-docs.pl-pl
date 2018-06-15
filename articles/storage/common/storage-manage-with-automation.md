---
title: Zarządzanie magazynem Azure za pomocą usługi Automatyzacja Azure
description: Więcej informacji na temat jak usługa Automatyzacja Azure może służyć do zarządzania magazynem Azure na dużą skalę.
services: storage, automation
documentationcenter: ''
author: jodoglevy
manager: eamono
editor: ''
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873800"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Zarządzanie magazynem Azure za pomocą usługi Automatyzacja Azure
W tym przewodniku przedstawiono usługi Automatyzacja Azure oraz jak on używany w celu uproszczenia zarządzania obiektów blob magazynu Azure, tabel i kolejek.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Automatyzacja Azure](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure, dla uproszczenia zarządzania chmurą za pomocą automatyzacji procesu. Przy użyciu usługi Automatyzacja Azure, można zautomatyzować, aby zwiększyć niezawodność i wydajność długotrwałe, ręcznych, podatnych i często powtarzanych zadań i skrócić czas wartości dla Twojej organizacji.

Automatyzacja Azure umożliwia aparatowi wykonywania przepływów pracy wysoce niezawodne i wysokiej dostępności, która może obsłużyć do własnych potrzeb miarę rozwoju organizacji. W automatyzacji Azure procesów może być rozpoczęte ręcznie, przez systemy 3rd firmy lub w zaplanowanych odstępach czasu tak, aby zadania stanie dokładnie w razie potrzeby.

Obniżyć koszty operacyjne i zwolnić IT / personel DevOps skupić się na pracy, który dodaje biznesowych wartość przez przeniesienie zadań zarządzania chmury do automatycznego uruchamiania przez usługi Automatyzacja Azure.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Jak usługi Automatyzacja Azure ułatwia zarządzanie magazynem Azure?
Magazyn Azure można zarządzać w automatyzacji Azure za pomocą poleceń cmdlet programu PowerShell, które są dostępne w [programu Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Usługi Automatyzacja Azure ma tych poleceń cmdlet programu PowerShell magazynu fabrycznej, umożliwiając wykonanie wszystkich obiektów blob, tabel i kolejki zadań zarządzania w ramach usługi. Można również skojarzyć te polecenia cmdlet usługi Automatyzacja Azure, za pomocą poleceń cmdlet dla innych usług Azure, aby zautomatyzować złożone zadania 3 systemów firm i usług Azure.

[Galerię elementów runbook usługi Automatyzacja Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) zawiera szereg produktu zespołu i społeczność elementy runbook, aby rozpocząć automatyzacji zarządzania usługi Azure Storage, innych usług platformy Azure i systemów firm 3. Galeria elementów runbook obejmują:

* [Usuwanie obiektów blob magazynu Azure, które są niektórych dni starego przy użyciu automatyzacji usługi](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Pobieranie obiektu Blob z usługi Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Wykonaj kopię zapasową wszystkich dysków dla jednej maszyny Wirtualnej platformy Azure lub dla wszystkich maszyn wirtualnych w usłudze w chmurze](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Automatyzacja Azure i jak może służyć do zarządzania obiektów blob, tabel i kolejek usługi Azure Storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat automatyzacji Azure.

Zobacz samouczek usługi Automatyzacja Azure [Tworzenie lub importowanie elementu runbook automatyzacji Azure](../../automation/automation-creating-importing-runbook.md).

