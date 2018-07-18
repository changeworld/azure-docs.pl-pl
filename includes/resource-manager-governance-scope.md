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
ms.openlocfilehash: 4f5dbbd1da8a221fb5f8b9641e016829987db538
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740611"
---
Przed utworzeniem jakichkolwiek elementów zapoznajmy się z pojęciem zakresu. Platforma Azure oferuje cztery poziomy zarządzania: grupy zarządzania, subskrypcję, grupy zasobów i zasób. [Grupy zarządzania](../articles/billing/billing-enterprise-mgmt-group-overview.md) są w wersji zapoznawczej. Na poniższej ilustracji przedstawiono takie przykładowe warstwy.

![Zakres](./media/resource-manager-governance-scope/scope-levels.png)

Ustawienia zarządzania są stosowane na dowolnych z tych poziomów zakresu. Zasięg zastosowania ustawienia jest określany na podstawie wybranego poziomu. Niższe poziomy dziedziczą ustawienia z wyższych poziomów. Po zastosowaniu ustawienia do subskrypcji to ustawienie jest stosowane do wszystkich grup zasobów i zasobów w ramach subskrypcji. Kiedy ustawienie jest stosowane do grupy zasobów, jest ono stosowane do grupy zasobów i wszystkich zasobów, które się w niej znajdują. Takie ustawienie nie ma jednak wpływu na inną grupę zasobów.

Krytyczne ustawienia zwykle warto stosować na wyższych poziomach, a wymagania specyficzne dla projektu na niższych poziomach. Może na przykład zajść potrzeba upewnienia się, że wszystkie zasoby w organizacji zostaną wdrożone w określonych regionach. Aby sprostać temu wymaganiu, należy do subskrypcji zastosować zasady, które określą dozwolone lokalizacje. Kiedy inni użytkownicy w organizacji będą dodawać nowe grupy zasobów i zasoby, dozwolone lokalizacje będą automatycznie wymuszane. 
