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
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
ms.locfileid: "29528343"
---
Podczas wdrażania zasobów na platformie Azure, masz bardzo elastyczne, podczas podejmowania decyzji o jakich typów zasobów, aby wdrożyć, gdzie znajdują się oraz jak je skonfigurować. Jednak tego elastyczność może otworzyć więcej opcji niż chcesz zezwolić w firmie. Podczas planowania, wdrażania zasobów na platformie Azure, może być zastanawiasz się:

* Sposób spełnia wymogi prawne dla suwerenności danych w niektórych krajach?
* Metody kontrolowania kosztów?
* Jak upewnij, że ktoś nie mogą przypadkowo zmieniają krytyczne systemu
* Jak śledzenie kosztów zasobów i dokładnie naliczać opłaty?

W tym artykule opisano te pytania. W szczególności należy:

> [!div class="checklist"]
* Przypisywanie użytkowników do ról i przypisz role do zakresu, dlatego użytkownicy mają uprawnienia do wykonywania akcji oczekiwany, ale nie więcej akcji.
* Zastosuj zasady, które określają konwencje dotyczące zasobów w ramach subskrypcji.
* Blokowanie zasobów, które są niezbędne do systemu.
* Tag zasobów, co pozwala na śledzenie ich przez wartości odpowiednich dla danej organizacji.

Ten artykuł skupia się na zadaniach, które należy wykonać, aby wdrożyć ładu. Szersze omówienie pojęć, zobacz [ładu na platformie Azure](../articles/security/governance-in-azure.md). 
