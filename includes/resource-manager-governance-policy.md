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
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
ms.locfileid: "29529978"
---
[Zasady usługi Azure](/azure/azure-policy/) firmowej standardów pomocy należy upewnić się, wszystkie zasoby w subskrypcji. Zasady umożliwiają zmniejszenie kosztów przez ograniczenie opcje wdrażania tych typów zasobów i jednostki magazynowe, które zostały zatwierdzone. Definiowanie reguł i akcji dla zasobów, a te reguły są automatycznie wprowadzane podczas wdrażania. Na przykład można kontrolować typów zasobów, które zostały wdrożone. Lub, można ograniczyć lokalizacje zatwierdzone dla zasobów. Niektóre zasady dotyczące akcji odmowy, a niektóre zasady dotyczące — Konfiguracja inspekcji akcji.

Zasady są uzupełniające do kontroli dostępu opartej na rolach (RBAC). RBAC koncentruje się na dostęp użytkowników, i jest domyślnie odmowy jawne i zezwolić systemu. Zasady koncentruje się na właściwości zasobu, podczas i po wdrożeniu. Jego domyślny Zezwalaj jawne i odmawianie systemu.

Istnieją dwa pojęć z zasadami - *definicje zasad* i *przypisania zasad*. Definicję zasad w tym artykule opisano warunków zarządzania, które mają zostać wymuszone. Przypisanie zasad umieszcza definicję zasad w akcji dla określonego zakresu.

![Przypisać zasady](./media/resource-manager-governance-policy/policy-concepts.png)

Platforma Azure udostępnia kilka definicje zasad wbudowanych, których można używać bez żadnych modyfikacji. Należy podać wartości parametrów, aby określić wartości, które są dozwolone w zakresie Twoich obowiązków. Jeśli definicji wbudowanych zasad nie spełniają wymagań, możesz [utworzyć zasady niestandardowe definicje](../articles/azure-policy/create-manage-policy.md).
