---
title: Zarządzanie usługą Azure API Management przy użyciu usługi Azure Automation
description: Dowiedz się, jak usługa Azure Automation może służyć do zarządzania usługą Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072064"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Zarządzanie usługą Azure API Management przy użyciu usługi Azure Automation
W tym przewodniku przedstawiono usługę Azure Automation i sposób jej użycia w celu uproszczenia zarządzania usługą Azure API Management.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure ułatwiająca zarządzanie chmurą za pomocą automatyzacji procesów. Korzystanie z usługi Azure Automation, ręczne, powtarzające się, długotrwałe i podatne na błędy zadania można zautomatyzować, aby zwiększyć niezawodność, wydajność i czas na wartość dla twojej organizacji.

Usługa Azure Automation zapewnia wysoce niezawodny, wysoce dostępny aparat wykonywania przepływu pracy, który jest skalowany zgodnie z Twoimi potrzebami. W usłudze Azure Automation procesy można uruchamiać ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania są wykonywane dokładnie wtedy, gdy są potrzebne.

Zmniejsz obciążenie operacyjne i uwolnij pracowników działu IT i DevOps, aby skupić się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>W jaki sposób usługa Azure Automation może pomóc w zarządzaniu usługą Azure API Management?
Zarządzaniem interfejsami API można zarządzać w usłudze Azure Automation przy użyciu [poleceń cmdlet programu Windows PowerShell dla interfejsu API usługi Azure API Management](https://docs.microsoft.com/powershell/module/az.apimanagement). W ramach usługi Azure Automation można pisać skrypty przepływu pracy programu PowerShell w celu wykonywania wielu zadań zarządzania interfejsami API przy użyciu poleceń cmdlet. Te polecenia cmdlet w usłudze Azure Automation można również sparować z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w usługach platformy Azure i systemach innych firm.

Oto kilka przykładów używania usługi API Management w programie Powershell:

* [Przykłady programu Azure PowerShell dla usługi API Management](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy usługi Azure Automation i jak można go używać do zarządzania usługą Azure API Management, skorzystaj z tych łączy, aby dowiedzieć się więcej.

* Zobacz [samouczek wprowadzenie usługi](../automation/automation-first-runbook-graphical.md)Azure Automation .

