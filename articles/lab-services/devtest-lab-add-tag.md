---
title: Dodawanie tagów do laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać tag do laboratorium w laboratorium usługi Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302801"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Dodawanie tagów do laboratorium w laboratorium usługi Azure DevTest Labs

Można utworzyć tagi niestandardowe i zastosować je do zasobów DevTest Labs, aby logicznie kategoryzować zasoby. Później można szybko i łatwo zobaczyć wszystkie zasoby w ramach subskrypcji, które mają ten tag. Tagi są przydatne, gdy trzeba zorganizować zasoby do rozliczeń lub zarządzania.

Zasoby obsługiwane przez tagi obejmują

* Maszyny wirtualne obliczania
* Karty interfejsów sieciowych
* Adresy IP
* Moduły równoważenia obciążenia
* Konta magazynu
* Dyski zarządzane

Znaczniki można zastosować podczas [tworzenia laboratorium,](devtest-lab-create-lab.md) a później zarządzać nimi za pomocą tagów bloku w konfiguracji i ustawieniach.

Każdy tag składa się z pary**wartości** **nazwy.**/ Na przykład można utworzyć znacznik o nazwie *costcenter* o wartości *34543*. Tag taki jak ten może pomóc później zidentyfikować zasoby laboratorium, które są rozliczane w tym określonym obszarze organizacji. Możesz wybrać nazwy i wartości, które mają sens dla sposobu organizowania subskrypcji.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Kroki zarządzania znacznikami w istniejącym laboratorium

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.** Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **Wszystkie zasoby**.
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz dodać znaczniki lub zarządzać nimi.
1. W obszarze **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.

    ![Przycisk Konfiguracja i zasady](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **MANAGE**wybierz pozycję **Znaczniki**.
1. Aby utworzyć nowy znacznik dla tego laboratorium, wprowadź parę**Wartość** **nazwy**/i wybierz pozycję **Zapisz**. Można również wybrać istniejący tag z listy, aby wyświetlić zasoby skojarzone z tym tagiem lub zarządzać nimi.

    ![Zarządzanie tagami](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Tagi utworzone na poziomie laboratorium przepływu przez wszystkie zasoby rozliczane, które laboratorium obraca się w ramach subskrypcji. Na przykład znaczniki na poziomie laboratorium przepływu do podstawowych maszyn wirtualnych obliczeniowych maszyn wirtualnych laboratorium.Można użyć tagów w kontekście zarządzania kosztami. Znaczniki na poziomie laboratorium są wyświetlane w filtrze znaczników do zarządzania kosztami.

## <a name="understanding-limitations-to-tags"></a>Opis ograniczeń tagów

Tagi mają następujące ograniczenia:

* Każdy zasób lub grupa zasobów może mieć co najwyżej 15 par nazwa/wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, z których każdy może mieć 15 par nazwa/wartość tagu.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

[Użyj tagów do organizowania zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) zapewnia więcej szczegółów na temat używania tagów na platformie Azure, w tym jak zarządzać tagami przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
* Ograniczenia i konwencje można stosować w całej subskrypcji przy użyciu niestandardowych zasad. Zasady, które można zdefiniować może wymagać, aby wszystkie zasoby mają wartość dla określonego tagu. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad i harmonogramów](devtest-lab-set-lab-policy.md).
* Zapoznaj się z [galerią szablonów szybkiego startu usługi DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
