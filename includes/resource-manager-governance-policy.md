---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740594"
---
[Zasady platformy Azure](/azure/azure-policy/) ułatwiają zapewnienie, że wszystkie zasoby w subskrypcji spełniają standardy firmy. Zasady umożliwiają obniżenie kosztów przez ograniczenie opcji wdrażania do tych typów zasobów i jednostek SKU, które zostały zatwierdzone. Dla zasobów są definiowane akcje i reguły, które są automatycznie wymuszane podczas wdrażania. Można na przykład kontrolować typy wdrażanych zasobów. Można też ograniczyć zatwierdzone lokalizacje dla zasobów. Niektóre zasady uniemożliwiają wykonanie akcji, a inne służą do konfigurowania inspekcji akcji.

Zasady są uzupełnieniem kontroli dostępu opartej na rolach (RBAC). Funkcja RBAC koncentruje się na dostępie użytkowników. Jest to system z domyślnym odmawianiem i wyraźnym zezwalaniem. Zasady skupiają się na właściwościach zasobów podczas ich wdrażania i po wdrożeniu. Jest to system z domyślnym zezwalaniem i wyraźnym zabranianiem.

W kontekście zasad funkcjonują dwa pojęcia, które należy zrozumieć, *definicje zasad* i *przypisania zasad*. Definicja zasad zawiera opis warunków zarządzania, które mają być wymuszane. Przypisanie zasad wprowadza definicję zasad do akcji w określonym zakresie.

![Przypisywanie zasad](./media/resource-manager-governance-policy/policy-concepts.png)

Platforma Azure udostępnia kilka wbudowanych definicji zasad, których można używać bez żadnych modyfikacji. Wystarczy przekazać wartości parametrów, aby określić wartości, które są dozwolone w Twoim zakresie. Jeśli wbudowane definicje zasad nie spełniają wymagań, można [utworzyć niestandardowe definicje zasad](../articles/azure-policy/create-manage-policy.md).
