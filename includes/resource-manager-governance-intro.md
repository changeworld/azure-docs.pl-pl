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
ms.openlocfilehash: 0b3af1bd7b3ab48074432d9e39552d72c6664b98
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124735"
---
Wdrażanie zasobów na platformie Azure jest procesem niezwykle elastycznym. Można zdecydować, jakiego rodzaju zasoby będą wdrażane, gdzie się one znajdują oraz jak należy je skonfigurować. Ta elastyczność może jednak spowodować, że dostępnych będzie więcej opcji, niż powinno być dozwolonych w organizacji. Podczas planowania wdrażania zasobów na platformie Azure mogą wyniknąć następujące kwestie:

* Jak mogę spełnić wymagania prawne dotyczące niezależności danych obowiązujące w niektórych krajach?
* Jak mogę kontrolować koszty?
* Jak mogę zagwarantować, że nikt nieodwracalnie nie zmodyfikuje krytycznego systemu?
* Jak mogę śledzić koszty zasobów i odpowiednio je rozliczać?

W tym artykule znajdują się odpowiedzi na te pytania. W szczególności opisano wykonywanie następujących czynności:

> [!div class="checklist"]
> * Przypisywanie użytkowników do ról i przypisywanie ról do zakresu, dzięki czemu użytkownicy mają uprawnienia do wykonywania tylko i wyłącznie oczekiwanych akcji.
> * Stosowanie zasad, które określają konwencje dla zasobów w ramach subskrypcji.
> * Blokowanie zasobów, które są krytyczne dla systemu.
> * Oznaczanie zasobów tagami, dzięki czemu można je śledzić według wartości, które są istotne dla organizacji.

W tym artykule skoncentrowano się na zadaniach, które należy wykonać w celu zaimplementowania ładu. Aby uzyskać bardziej szczegółowe omówienie pojęć, zobacz [Governance in Azure](../articles/security/governance-in-azure.md) (Ład na platformie Azure). 
