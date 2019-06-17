---
title: Tworzenie konfiguracji DSC w usługi Azure Automation stanu Configuration (DSC) przy użyciu zasoby złożone
description: Dowiedz się, jak tworzyć konfiguracje za pomocą zasoby złożone w usługi Azure Automation stanu Configuration (DSC)
keywords: PowerShell dsc, konfiguracja żądanego stanu, konfiguracji dsc programu powershell, azure, zasoby złożone
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64588829cec964e52dcb44465869e0090f36f9f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61303965"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Tworzenie konfiguracji DSC w usługi Azure Automation stanu Configuration (DSC) przy użyciu zasoby złożone

Gdy zasób musi być zarządzane za pomocą więcej niż jednego żądanego stanu konfiguracji configuration (DSC), jest użycie najlepszej ścieżki [zasoby złożone](/powershell/dsc/authoringresourcecomposite). Złożone zasobów jest Konfiguracja zagnieżdżone i sparametryzowane używany jako zasób DSC w innej konfiguracji. Umożliwia to tworzenie złożonych konfiguracji zezwalając bazowego zasoby złożone (sparametryzowanych konfiguracji) zarządzane oddzielnie i wbudowane.

Usługa Azure Automation umożliwia [importu i kompilacji zasoby złożone](automation-dsc-compile.md#composite-resources). Po zasoby złożone zostały zaimportowane do konta usługi Automation, jesteś w stanie używać **konfiguracji Compose** środowiska w **Konfiguracja stanu (DSC)** strony.

## <a name="composing-a-configuration-from-composite-resources"></a>Tworzenie konfiguracji z zasoby złożone

Zanim będzie można przypisać konfiguracji z zasoby złożone w witrynie Azure portal, należy go utworzyć. Można to zrobić przy użyciu **konfiguracji Compose** na **Konfiguracja stanu (DSC)** stronie podczas albo **konfiguracje** lub **Compiled konfiguracje** karty.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** wybierz opcję **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij albo **konfiguracje** lub **skompilowany konfiguracje** , a następnie kliknij **tworzenia konfiguracji**  w menu u góry strony.
1. Na **podstawy** kroku, podaj nową nazwę konfiguracji (wymagane) i kliknij w dowolnym miejscu w wierszu wszystkie złożonego zasoby, które chcesz uwzględnić w nowej konfiguracji, a następnie kliknij przycisk **dalej** lub kliknij przycisk **Kod źródłowy** kroku. Dla następujących kroków, wybraliśmy **PSExecutionPolicy** i **RenameAndDomainJoin** zasoby złożone.
   ![Zrzut ekranu przedstawiający kroku podstawowe informacje na stronie konfiguracji compose](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **Kod źródłowy** kroku przedstawiono, jak wygląda złożone konfiguracji wybrane zasoby złożone. Możesz zobaczyć scalanie wszystkie parametry i jak są one przekazywane do złożonych zasobu. Po zakończeniu przeglądania nowego kodu źródłowego, kliknij przycisk **dalej** lub kliknij przycisk **parametry** kroku.
   ![Zrzut ekranu przedstawiający kroku kodu źródłowego na stronie konfiguracji compose](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na **parametry** kroku parametru, który ma każdy zasób złożonego jest widoczna, dzięki czemu można podać. Jeśli parametr ma opis, jest on wyświetlany obok pola parametru. Jeśli pole zostanie **PSCredential** parametr typu, na liście rozwijanej, aby skonfigurować, zawiera listę **poświadczeń** obiektów w bieżącym kontem usługi Automation. A **+ Dodaj poświadczenie** opcja jest również być dostępne. Po ich podaniu wszystkich wymaganych parametrów kliknij **Zapisz i Kompiluj**.
   ![Zrzut ekranu przedstawiający kroku parametrów na stronie konfiguracji compose](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po zapisaniu nowej konfiguracji jest przesyłany w kompilacji. Podobnie jak wszelkie zaimportowanej konfiguracji można wyświetlić stan zadania kompilacji. Aby uzyskać więcej informacji, zobacz [wyświetlanie zadanie kompilacji](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po pomyślnym zakończeniu kompilacji nowej konfiguracji pojawia się w **skompilowany konfiguracje** kartę. Gdy jest ona widoczna na tej karcie, może ona zostać przypisana do węzła zarządzanych przy użyciu kroków w [ponowne przypisywanie węzła do innego węzła konfiguracji](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dodawanie węzłów, zobacz [dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)
