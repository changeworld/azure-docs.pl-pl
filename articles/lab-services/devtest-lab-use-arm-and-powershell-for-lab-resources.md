---
title: Tworzenie lub modyfikowanie laboratoriów przy użyciu szablonów usługi Azure Resource Manager
description: Dowiedz się, jak automatycznie tworzyć lub modyfikować laboratoria w laboratorium devtest za pomocą szablonów usługi Azure Resource Manager w programie PowerShell
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 180081829387afeb7e63defe7a22378aa8d417d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170308"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Automatyczne tworzenie lub modyfikowanie laboratoriów przy użyciu szablonów usługi Azure Resource Manager i programu PowerShell

DevTest Labs udostępnia wiele szablonów usługi Azure Resource Manager i skryptów programu PowerShell, które mogą pomóc szybko i automatycznie utworzyć nowe laboratoria lub zmodyfikować istniejące laboratoria, a następnie wdrożyć te zasoby.

Ten artykuł pomaga przejść przez proces korzystania z tych szablonów i skryptów do automatyzacji tworzenia, modyfikowania i wdrażania laboratoriów. W tym artykule pokazano również, gdzie można znaleźć więcej informacji na temat sposobu korzystania z programu PowerShell do wykonywania niektórych typowych zadań w DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: Zbierz szablony i skrypty
Wstępnie utworzone [szablony usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) i [skrypty programu PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) można znaleźć w naszym publicznym [repozytorium GitHub.](https://github.com/Azure/azure-devtestlab) Używaj ich w stanie, w jakim jest, lub dostosuj je do swoich potrzeb i przechowuj je we własnym [prywatnym repozytorium Git.](devtest-lab-add-artifact-repo.md)

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Modyfikowanie szablonu usługi Azure Resource Manager
Możesz wykonać kroki opisane w [witrynie Utwórz swój pierwszy szablon usługi Azure Resource Manager,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) jeśli nigdy wcześniej nie utworzono szablonu.

Ponadto [najważniejsze wskazówki dotyczące tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferuje wiele wskazówek i sugestii ułatwiających tworzenie szablonów usługi Azure Resource Manager, które są niezawodne i łatwe w użyciu. Zazwyczaj użyjesz odmiany jednego z podejść lub przykładów podanych i zmodyfikujesz szablon dla twoich potrzeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: Wdrażanie zasobów za pomocą programu PowerShell
Po dostosowaniu szablonów i skryptów wykonaj kroki niezbędne do [wdrożenia zasobów za pomocą szablonów Usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Ten artykuł zawiera ogólne informacje dotyczące korzystania z szablonów usługi Azure PowerShell z usługą Azure Resource Manager w celu wdrożenia zasobów na platformie Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Typowe zadania, które można wykonywać w laboratoriach DevTest przy użyciu programu PowerShell
Istnieje wiele innych typowych zadań, które można zautomatyzować przy użyciu programu PowerShell. W poniższych sekcjach dokumentacji opisano kroki wymagane do wykonania tych zadań.

* [Tworzenie obrazu niestandardowego z pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Przekazywanie pliku VHD do konta magazynu laboratorium przy użyciu programu PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Dodawanie użytkownika zewnętrznego do laboratorium przy użyciu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Tworzenie roli niestandardowej laboratorium przy użyciu programu PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć [prywatne repozytorium Git,](devtest-lab-add-artifact-repo.md) w którym będziesz przechowywać dostosowane szablony lub skrypty.
* Zapoznaj się [z szablonami usługi Azure Resource Manager z galerii szablonów Szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates).
