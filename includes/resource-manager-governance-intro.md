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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740606"
---
Wdrażanie zasobów na platformie Azure jest procesem niezwykle elastycznym. Można zdecydować, jakiego rodzaju zasoby będą wdrażane, gdzie się one znajdują oraz jak należy je skonfigurować. Ta elastyczność może jednak spowodować, że dostępnych będzie więcej opcji, niż powinno być dozwolonych w organizacji. Podczas planowania wdrażania zasobów na platformie Azure mogą wyniknąć następujące kwestie:

* Jak mogę spełnić wymagania prawne dotyczące niezależności danych obowiązujące w niektórych krajach?
* Jak mogę kontrolować koszty?
* Jak mogę zagwarantować, że nikt nieodwracalnie nie zmodyfikuje krytycznego systemu?
* Jak mogę śledzić koszty zasobów i odpowiednio je rozliczać?

W tym artykule znajdują się odpowiedzi na te pytania. W szczególności opisano wykonywanie następujących czynności:

> [!div class="checklist"]
* Przypisywanie użytkowników do ról i przypisywanie ról do zakresu, dzięki czemu użytkownicy mają uprawnienia do wykonywania tylko i wyłącznie oczekiwanych akcji.
* Stosowanie zasad, które określają konwencje dla zasobów w ramach subskrypcji.
* Blokowanie zasobów, które są krytyczne dla systemu.
* Oznaczanie zasobów tagami, dzięki czemu można je śledzić według wartości, które są istotne dla organizacji.

W tym artykule skoncentrowano się na zadaniach, które należy wykonać w celu zaimplementowania ładu. Aby uzyskać bardziej szczegółowe omówienie pojęć, zobacz [Governance in Azure](../articles/security/governance-in-azure.md) (Ład na platformie Azure). 
