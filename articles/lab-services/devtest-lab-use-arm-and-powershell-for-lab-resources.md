---
title: Tworzenie lub modyfikowanie labs automatycznie za pomocą szablonów usługi Azure Resource Manager przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać szablonów usługi Azure Resource Manager przy użyciu programu PowerShell do tworzenia lub modyfikowania labs automatycznie w laboratorium DevTest lab
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
ms.openlocfilehash: 7cf6e5569e9d47b2a279f39cf837a4c0558bf3c9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Tworzenie lub modyfikowanie labs automatycznie przy użyciu szablonów usługi Azure Resource Manager i programu PowerShell

DevTest Labs udostępnia wiele szablonów usługi Azure Resource Manager i skryptów programu PowerShell, które pomagają w szybkim i automatyczne tworzenie nowych labs lub zmodyfikować istniejącą labs oraz następnie wdrażania tych zasobów.

W tym artykule opisano informacje pomocne przy użyciu tych szablonów i skryptów można zautomatyzować tworzenie, modyfikowanie i wdrażania programu labs przez proces. Ten artykuł zawiera także gdzie można znaleźć więcej informacji na temat sposobu wykonywania niektórych typowych zadań w usłudze DevTest Labs przy użyciu programu PowerShell.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: Zbieranie z szablonów i skryptów
Można znaleźć wstępnie wprowadzone [szablonów usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) i [skryptów programu PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) na naszego publicznego [repozytorium Github](https://github.com/Azure/azure-devtestlab). Używane jako-, lub dostosować je do potrzeb i zapisanie ich w ramach własnego [prywatne repozytorium Git](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Modyfikowanie szablonu usługi Azure Resource Manager
Możesz wykonać kroki opisane w temacie [Tworzenie pierwszego szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) Jeśli nigdy nie utworzono szablonu przed.

Ponadto [najlepszych rozwiązań dotyczących tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferuje wiele wskazówki i sugestie ułatwiające tworzenie szablonów usługi Azure Resource Manager, które są niezawodne i łatwe w użyciu. Zazwyczaj będzie używane odmianą jednej z metod lub przykłady podane i zmodyfikować szablon do własnych potrzeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: Wdrażanie zasobów przy użyciu programu PowerShell
Po dostosowaniu z szablonów i skrypty, wykonaj kroki niezbędne do [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Artykuł zawiera ogólne informacje o używaniu programu Azure PowerShell przy użyciu szablonów usługi Azure Resource Manager do wdrażania zasobów platformy Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Typowe zadania, które można wykonywać w usłudze DevTest labs przy użyciu programu PowerShell
Istnieje wielu innych typowych zadań, które można zautomatyzować za pomocą programu PowerShell. W poniższych sekcjach dokumentacji opisano kroki wymagane do wykonania tych zadań.

* [Tworzenie niestandardowego obrazu z pliku VHD za pomocą programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Przekaż plik wirtualnego dysku twardego na konto magazynu w laboratorium przy użyciu programu PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Dodaj użytkownika zewnętrznego w laboratorium przy użyciu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Tworzenie laboratorium niestandardowej roli zabezpieczeń przy użyciu programu PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak utworzyć [prywatne repozytorium Git](devtest-lab-add-artifact-repo.md) którym będą przechowywanie szablony niestandardowe lub skryptów.
* Eksploruj [szablonów usługi Azure Resource Manager z galerii szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates).
