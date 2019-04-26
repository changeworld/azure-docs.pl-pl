---
title: Tworzenie laboratorium w usłudze Azure DevTest Labs | Microsoft Docs
description: Tworzenie laboratorium w usłudze Azure DevTest Labs na potrzeby maszyn wirtualnych
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: c54b97bdf69908f32015631a9e527c6e289d1d2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202452"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Tworzenie laboratorium w usłudze Azure DevTest Labs
Azure DevTest Labs to infrastruktura, która obejmuje wiele różnych zasobów (np. maszyn wirtualnych) i umożliwia lepsze zarządzanie tymi zasobami przez określenie ograniczeń i przydziałów. W tym artykule szczegółowo omówiono proces tworzenia laboratorium przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć laboratorium, potrzebne są:

* Subskrypcja platformy Azure. Aby zapoznać się z opcjami zakupu platformy Azure, zobacz [Jak kupić system Azure](https://azure.microsoft.com/pricing/purchase-options/) lub [Bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/). Aby utworzyć laboratorium, trzeba być właścicielem subskrypcji.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Procedura tworzenia laboratorium w usłudze Azure DevTest Labs
Następujące kroki ilustrują tworzenie laboratorium w usłudze Azure DevTest Labs przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Z menu głównego po lewej stronie wybierz pozycję **Wszystkie usługi** (w górnej części listy). Wybierz pozycję * (w formie gwiazdek) obok pozycji **DevTest Labs** w **DEVOPS** sekcji. Ta akcja spowoduje dodanie **DevTest Labs** do menu nawigacji po lewej stronie, aby łatwo go dostępu podczas kolejnego. 

    ![Wszystkie usługi — Wybieranie usługi DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Teraz wybierz **DevTest Labs** w menu nawigacji po lewej stronie. Wybierz **Dodaj** na pasku narzędzi. 
   
    ![Dodawanie laboratorium](./media/devtest-lab-create-lab/add-lab-button.png)
1. Na **tworzenie laboratorium DevTest Lab** wykonaj następujące czynności: 
    1. Wprowadź **nazwa** dla laboratorium.
    2. W pozycji **Subskrypcja** wybierz subskrypcję do skojarzenia z laboratorium.
    3. Wprowadź **Nazwa grupy zasobów** dla laboratorium. 
    4. Wybierz **lokalizacji** do przechowywania laboratorium.
    4. Wybierz pozycję **Automatyczne zamykanie**, aby włączyć opcję i określić parametry automatycznego zamykania wszystkich maszyn wirtualnych laboratorium. Funkcja automatycznego zamykania służy głównie do osiągnięcia oszczędności. Pozwala określić, kiedy ma dojść do automatycznego zamknięcia maszyny wirtualnej. Ustawienia automatycznego zamykania można zmienić po utworzeniu laboratorium, wykonując czynności opisane w artykule [Manage all policies for a lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown) (Zarządzanie wszystkimi zasadami dla laboratorium w usłudze Azure DevTest Labs).
    1. Wprowadź informacje w polach **NAZWA** i **WARTOŚĆ** w obszarze **Tagi**, jeśli chcesz utworzyć niestandardowe tagowanie, które będzie dodawane do każdego zasobu utworzonego w laboratorium. Tagi są przydatne do porządkowania zasobów laboratorium według kategorii i zarządzania nimi. Aby uzyskać więcej informacji na temat tagów, w tym na temat sposobu dodawania tagów po utworzeniu laboratorium, zobacz [Dodawanie tagów do laboratorium](devtest-lab-add-tag.md).
    6. Wybierz pozycję **Opcje automatyzacji**, aby uzyskać szablony usługi Azure Resource Manager dotyczące automatyzacji konfiguracji. 
    7. Wybierz pozycję **Utwórz**. Stan procesu tworzenia laboratorium można monitorować, obserwując obszar **Powiadomienia**. 
    
        ![Tworzenie sekcji laboratorium usługi DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Po zakończeniu wybierz **przejdź do zasobu** w powiadomieniu. Alternatywnie, Odśwież **DevTest Labs** strony, aby zobaczyć nowo utworzone laboratorium na liście laboratoriów.  Wybierz laboratorium na liście. Zostanie wyświetlona strona główna dla swojego laboratorium. 

        ![Strona główna laboratorium](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu laboratorium warto rozważyć poniższe kroki:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratorium](devtest-lab-set-lab-policy.md)
* [Tworzenie szablonu laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

