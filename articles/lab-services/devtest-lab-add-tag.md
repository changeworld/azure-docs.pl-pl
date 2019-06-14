---
title: Dodawanie tagów do laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać tag do laboratorium Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e4d9aeb527461cc7292235fef1de0abdfa4242bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311372"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Dodawanie tagów do laboratorium Azure DevTest Labs

Można tworzyć niestandardowe tagi i stosować je do zasobów usługi DevTest Labs logicznie kategoryzowanie zasobów. Później można szybko i łatwo zobaczyć wszystkich zasobów w ramach subskrypcji, które mają ten tag. Tagi są przydatne, gdy trzeba zorganizować zasoby w celach rozliczeniowych lub zarządzania.

Zasoby, które są obsługiwane przez tagi zawierają

* Obliczeniowe maszyny wirtualne
* Karty interfejsów sieciowych
* Adresy IP
* Moduły równoważenia obciążenia
* Konta magazynu
* Dyski zarządzane

Można zastosować tagi, gdy użytkownik [tworzenie laboratorium](devtest-lab-create-lab.md) i później zarządzać nimi za pomocą bloku tagów w sekcji Konfiguracja i ustawienia.

Każdy tag składa się z **nazwa**/**wartość** pary. Na przykład, może utworzyć tag o nazwie *costcenter* wartością *34543*. Tag, np. to może pomóc później zidentyfikować zasoby laboratorium, które są płatne z tym obszarem określonych w Twojej organizacji. Uzyskaj wybranie nazwy i wartości, które mają sens dla sposobu organizowania subskrypcji.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Kroki, które umożliwią Zarządzanie tagami w istniejącego laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby zaznacz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy. Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**.
1. Zaznacz na liście laboratoriów laboratorium, w której chcesz dodać lub zarządzania nimi.
1. W laboratorium **Przegląd** wybierz opcję **konfiguracji i zasad**.

    ![Przycisk konfiguracji i zasad](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **ZARZĄDZAJ**, wybierz opcję **tagi**.
1. Aby utworzyć nowy tag, w tym środowisku laboratoryjnym, wprowadź **nazwa**/**wartość** pair, a następnie wybierz pozycję **Zapisz**. Możesz również wybrać istniejący znacznik z listy, aby wyświetlić lub zarządzać zasobów skojarzonych z tym tagiem.

    ![Zarządzaj tagami](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Opis ograniczeń do tagów

Tagi mają następujące ograniczenia:

* Każdy zasób lub grupa zasobów może mieć co najwyżej 15 par nazwa/wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, z których każdy może mieć 15 par nazwa/wartość tagu.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

[Organizowanie zasobów platformy Azure za pomocą tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) zapewnia większą szczegółowe informacje o platformie Azure, w tym sposobu zarządzania nimi przy użyciu programu PowerShell lub wiersza polecenia platformy Azure przy użyciu tagów.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
* Za pomocą zasad niestandardowych, można zastosować ograniczenia i konwencje w ramach subskrypcji. Zasady, które należy zdefiniować może wymagać, że wszystkie zasoby mają wartości dla określonego tagu. Aby uzyskać więcej informacji, zobacz [konfigurować zasady i harmonogramy](devtest-lab-set-lab-policy.md).
* Zapoznaj się z [galerii szablonów DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
