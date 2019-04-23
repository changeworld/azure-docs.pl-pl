---
title: Tworzenie lub modyfikowanie laboratoriów automatycznie przy użyciu szablonów usługi Azure Resource Manager przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą szablonów usługi Azure Resource Manager przy użyciu programu PowerShell Tworzenie lub modyfikowanie laboratoriów automatycznie w laboratorium DevTest lab
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149754"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Tworzenie lub modyfikowanie laboratoriów automatycznie przy użyciu szablonów usługi Azure Resource Manager i programu PowerShell

DevTest Labs oferuje wiele szablonów usługi Azure Resource Manager i skryptów programu PowerShell, które pomagają w szybkim i automatyczne tworzenie nowych labs lub modyfikowanie laboratoriów istniejących i następnie wdrażania tych zasobów.

W tym artykule opisano proces użycia tych szablonów i skrypty, aby zautomatyzować tworzenie, modyfikowanie i wdrożenia laboratoriów. W tym artykule także dowiesz się, gdzie można znaleźć więcej informacji o tym, jak wykonywać niektóre typowe zadania w usłudze DevTest Labs przy użyciu programu PowerShell.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: Zbieranie Szablony skryptów
Można znaleźć wstępnie utworzonych [szablonów usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) i [skryptów programu PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) u naszego publicznego [repozytorium GitHub](https://github.com/Azure/azure-devtestlab). Używać ich jako —, lub je dostosować do własnych potrzeb i przechowywać je w ramach własnego [prywatnym repozytorium Git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Zmodyfikowanie szablonu usługi Azure Resource Manager
Możesz wykonać kroki opisane w temacie [Tworzenie pierwszego szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) Jeśli nigdy nie utworzono szablonu przed.

Ponadto [najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferuje wiele wytycznych i sugestie ułatwiające tworzenie szablonów usługi Azure Resource Manager, które jest niezawodne i łatwe w użyciu. Zazwyczaj będzie używane odmiany jednej z metod lub podanych przykładów i zmodyfikuj swój szablon do własnych potrzeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: Wdrażanie zasobów za pomocą programu PowerShell
Po dostosowaniu swoje szablony i skrypty, wykonaj kroki, które są niezbędne do [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Artykuł zawiera ogólne informacje o używaniu programu Azure PowerShell przy użyciu szablonów usługi Azure Resource Manager do wdrażania zasobów na platformie Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Typowe zadania, które można wykonywać w usłudze DevTest labs przy użyciu programu PowerShell
Istnieje wiele innych typowych zadań, które można zautomatyzować przy użyciu programu PowerShell. W kolejnych sekcjach dokumentacji opisano kroki wymagane do wykonywania tych zadań.

* [Tworzenie obrazu niestandardowego z pliku VHD za pomocą programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Przekazywanie pliku VHD do laboratorium konta magazynu przy użyciu programu PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Dodawanie użytkownika zewnętrznego do laboratorium przy użyciu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Tworzenie roli niestandardowej laboratorium przy użyciu programu PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak utworzyć [prywatnego repozytorium Git](devtest-lab-add-artifact-repo.md) której będzie przechowywać swoje niestandardowe szablony lub skryptów.
* Zapoznaj się z [szablony usługi Azure Resource Manager z galerii szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
