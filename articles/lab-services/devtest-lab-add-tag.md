---
title: Dodawanie tagów do laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać tag do laboratorium w usłudze Azure DevTest Labs
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
ms.openlocfilehash: 3d9a5b3c0ae0b6058d3e8ccf8cdb340bd1200edc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Dodawanie tagów do laboratorium w usłudze Azure DevTest Labs

Można tworzyć niestandardowe znaczniki i zastosować je do zasobów DevTest Labs do klasyfikowania logicznie zasobów. Później można szybko i łatwo zobaczyć wszystkie zasoby w ramach subskrypcji, które mają ten tag. Tagi są przydatne, gdy trzeba organizowania zasobów do zarządzania lub rozliczeń.

Zasoby, które są obsługiwane przez tagów uwzględnić

* Obliczenia bazy danych maszyn wirtualnych
* Karty interfejsów sieciowych
* Adresy IP
* Moduły równoważenia obciążenia
* Konta magazynu
* Dyski zarządzane

Możesz zastosować tagi, gdy użytkownik [tworzenie laboratorium](devtest-lab-create-lab.md) i później zarządzać nimi za pomocą bloku tagów w sekcji Konfiguracja i ustawienia.

Każdy znacznik składa się z **nazwa**/**wartość** pary. Na przykład można utworzyć tag o nazwie *costcenter* , który ma wartość *34543*. Tag takie, jak to może pomóc później zidentyfikuj zasoby laboratorium, które są rozliczeniowy w do tej określonej części organizacji. Pobierz wybrać nazwy i wartości odpowiednich dla sposobu organizowania subskrypcji.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Kroki, aby zarządzać znaczników w istniejących laboratorium

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy. Laboratorium może być jest wyświetlana na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**.
1. Z listy labs wybierz laboratorium, w której chcesz dodać lub zarządzać tagów.  
1. W laboratorium **omówienie** wybierz opcję **konfiguracji i zasadach**.  

    ![Przycisk zasad i konfiguracji](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **ZARZĄDZAJ**, wybierz pozycję **tagi**.
1. Do utworzenia nowego tagu dla tego laboratorium, wprowadź **nazwa**/**wartość** Sparuj, a następnie wybierz **zapisać**. Można również wybrać istniejący znacznik z listy, aby wyświetlić lub zarządzać zasoby skojarzone z tym znacznikiem.

    ![Zarządzaj tagami](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Opis ograniczeń do tagów

Tagi mają następujące ograniczenia:

* Każdy zasób lub grupa zasobów może mieć co najwyżej 15 par nazwa/wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, z których każdy może mieć 15 par nazwa/wartość tagu. 
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

[Używaj tagów do organizowania zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) zapewnia większą szczegółowe informacje o platformie Azure, łącznie ze sposobem zarządzania tagów za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure przy użyciu tagów.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
* Za pomocą niestandardowych zasad można stosować ograniczenia i konwencje w Twojej subskrypcji. Zasady, które należy zdefiniować może wymagać, że wszystkie zasoby mają wartość określony tag. Aby uzyskać więcej informacji, zobacz [ustawić zasady i harmonogramy](devtest-lab-set-lab-policy.md).
* Eksploruj [galerię szablonów DevTest Labs Azure Resource Manager — Szybki Start](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
