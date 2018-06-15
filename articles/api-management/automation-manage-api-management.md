---
title: Zarządzanie Azure API Management przy użyciu usługi Automatyzacja Azure
description: Więcej informacji na temat sposobu usługi Automatyzacja Azure może służyć do zarządzania usługi Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 10b483c70f7b5a3d767815306d8a690b1b9a5faf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30161850"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Zarządzanie Azure API Management przy użyciu usługi Automatyzacja Azure
W tym przewodniku przedstawiono usługi Automatyzacja Azure i jak on używany w celu uproszczenia zarządzania usługi Azure API Management.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Automatyzacja Azure](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure, dla uproszczenia zarządzania chmurą za pomocą automatyzacji procesu. Przy użyciu automatyzacji Azure, ręczne, powtarzane, długotrwałe i podatne na błędy zadań można zautomatyzować, aby zwiększyć niezawodność, wydajność i wartość czasu dla Twojej organizacji.

Automatyzacja Azure umożliwia aparatowi wykonywania przepływów pracy wysoce niezawodne, wysokiej dostępności, która może obsłużyć do własnych potrzeb. W automatyzacji Azure procesów może być rozpoczęte ręcznie, przez systemy 3rd firmy lub w zaplanowanych odstępach czasu tak, aby zadania stanie dokładnie w razie potrzeby.

Obniżenie kosztów operacyjnych i zwolnić IT i pracownicy DevOps skupić się na pracy dodającego wartość biznesową przez przeniesienie zadań zarządzania chmury do automatycznego uruchamiania przez usługi Automatyzacja Azure.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Jak usługi Automatyzacja Azure ułatwia zarządzanie Azure API Management?
Zarządzanie interfejsami API można zarządzać w automatyzacji Azure za pomocą [polecenia cmdlet programu Windows PowerShell dla interfejsu API usługi Azure API Management](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/?view=azurermps-5.5.0#api_management/). W ramach usługi Automatyzacja Azure można napisać skrypty przepływu pracy programu PowerShell do wykonywania wielu zadań interfejsu API zarządzania przy użyciu polecenia cmdlet. Można również skojarzyć te polecenia cmdlet usługi Automatyzacja Azure, za pomocą poleceń cmdlet dla innych usług Azure, aby zautomatyzować złożone zadania 3 systemów firm i usług Azure.

Oto kilka przykładów użycia interfejsu API zarządzania przy użyciu automatyzacji:

* [Zarządzanie interfejsami API Azure — używanie programu PowerShell na potrzeby tworzenia kopii zapasowych i przywracania](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Automatyzacja Azure i jak może służyć do zarządzania usługi Azure API Management, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz automatyzacji Azure [Wprowadzenie — samouczek](../automation/automation-first-runbook-graphical.md).

