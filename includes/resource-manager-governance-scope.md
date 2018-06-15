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
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
ms.locfileid: "29529961"
---
Przed utworzeniem jakichkolwiek elementów, umożliwia przegląd koncepcji zakresu. Platforma Azure udostępnia cztery poziomy zarządzanie: grup zarządzania, subskrypcji, grupy zasobów i zasobów. [Grupy zarządzania](../articles/billing/billing-enterprise-mgmt-group-overview.md) znajdują się w wersji zapoznawczej. Na poniższej ilustracji przedstawiono przykład tych warstw.

![Zakres](./media/resource-manager-governance-scope/scope-levels.png)

Zastosowanie ustawień zarządzania na każdym z tych poziomów zakresu. Wybrany poziom określa, jak bardzo ustawienie jest stosowane. Niższe poziomy dziedziczyć ustawień z wyższego poziomu. Po zastosowaniu do subskrypcji, to ustawienie jest stosowane do wszystkich grup zasobów i zasobów w ramach subskrypcji. Zastosuj ustawienia w grupie zasobów, że ustawienie stosowane w przypadku grupy zasobów i wszystkie jego zasoby. Jednak innej grupie zasobów nie ma tego ustawienia.

Zwykle warto stosować ustawienia krytyczne na wyższych poziomach i wymagań specyficznych dla projektu na niższych poziomach. Można na przykład upewnij się, że wszystkie zasoby w Twojej organizacji są wdrażane w pewnych regionach. To wymaganie można spełnić, należy zastosować zasady do subskrypcji, która określa dozwolonych lokalizacji. Inni użytkownicy w organizacji dodawania nowej grupy zasobów i zasoby, automatycznie są wymuszane dozwolonych lokalizacji. 
