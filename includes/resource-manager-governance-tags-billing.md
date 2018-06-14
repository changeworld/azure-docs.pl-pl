---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 7843410043b726526380b2a916d96f414a2decda
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
ms.locfileid: "29532324"
---
Po zastosowaniu tagów do zasobów, można wyświetlić kosztów zasobów z tych tagów. Trwa podczas analizy kosztów wyświetlić najnowsze użycie, więc może nie być wyświetlana koszty jeszcze. Gdy koszty są dostępne, można wyświetlić kosztów zasobów między grupami zasobów w ramach subskrypcji. Użytkownicy muszą mieć [subskrypcji poziom dostępu do informacji dotyczących rozliczeń](../articles/billing/billing-manage-access.md) koszty.

Aby wyświetlić koszty według znaczników w portalu, wybierz subskrypcję, a następnie wybierz **analizy kosztów**.

![Analiza kosztów](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Następnie filtrować według wartości tagu i wybierz **Zastosuj**.

![Koszt widok znaczników](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Można również użyć [interfejsów API usługi Azure rozliczeń](../articles/billing/billing-usage-rate-card-overview.md) można programowo wyświetlać kosztów.
