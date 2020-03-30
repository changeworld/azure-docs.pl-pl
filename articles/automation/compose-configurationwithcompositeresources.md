---
title: Tworzenie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure (DSC) przy użyciu zasobów złożonych
description: Dowiedz się, jak redagować konfiguracje przy użyciu zasobów złożonych w konfiguracji stanu automatyzacji usługi Azure (DSC)
keywords: powershell dsc, konfiguracja żądanego stanu, powershell dsc azure, zasoby złożone
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370666"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Tworzenie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure (DSC) przy użyciu zasobów złożonych

Gdy zasób musi być zarządzany za pomocą więcej niż jednej konfiguracji żądanego stanu (DSC), najlepszą ścieżką jest użycie [zasobów złożonych](/powershell/scripting/dsc/resources/authoringresourcecomposite). Zasób złożony jest zagnieżdżoną i sparametryzowaną konfiguracją używaną jako zasób DSC w ramach innej konfiguracji. Dzięki temu tworzenie złożonych konfiguracji, umożliwiając jednocześnie podstawowe zasoby złożone (sparametryzowane konfiguracje) być indywidualnie zarządzane i budowane.

Usługa Azure Automation umożliwia [importowanie i kompilowanie zasobów złożonych.](automation-dsc-compile.md)
Po zaimportowaniu zasobów złożonych do konta automatyzacji można użyć **środowiska konfiguracji redagowania** na stronie **Konfiguracja stanu (DSC).**

## <a name="composing-a-configuration-from-composite-resources"></a>Tworzenie konfiguracji z zasobów złożonych

Aby można było przypisać konfigurację wykonaną z zasobów złożonych w witrynie Azure portal, należy ją skomponować. Można to zrobić za pomocą **konfiguracji redagowania** na stronie **Konfiguracja stanu (DSC),** podczas gdy na **kartach Konfiguracje** lub **Skompilowane konfiguracje.**

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie **Konto automatyzacji** wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **Konfiguracje** lub **Skompilowane konfiguracje,** a następnie kliknij polecenie **Komponuj konfigurację** w menu u góry strony.
1. W kroku **Podstawy** podaj nową nazwę konfiguracji (wymagane) i kliknij dowolne miejsce w wierszu każdego zasobu złożonego, który chcesz uwzględnić w nowej konfiguracji, a następnie kliknij przycisk **Dalej** lub kliknij krok **Kod źródłowy.** W poniższych krokach wybraliśmy **PSExecutionPolicy** i **RenameAndDomainJoin** zasobów złożonych.
   ![Zrzut ekranu przedstawiający krok podstawowy strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **Kod źródłowy** pokazuje, jak wygląda skomponowana konfiguracja wybranych zasobów złożonych. Można zobaczyć scalanie wszystkich parametrów i jak są one przekazywane do zasobu złożonego. Po zakończeniu przeglądania nowego kodu źródłowego kliknij przycisk **Dalej** lub kliknij krok **Parametry.**
   ![Zrzut ekranu przedstawiający krok kodu źródłowego strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. W kroku **Parametry** parametr, który ma każdy zasób złożony jest narażony, dzięki czemu mogą być dostarczane. Jeśli parametr ma opis, jest on wyświetlany obok pola parametru. Jeśli pole jest parametrem typu **PSCredential,** lista rozwijana do skonfigurowania zawiera listę obiektów **poświadczeń** na bieżącym koncie automatyzacji. Dostępna jest również opcja **+ Dodaj poświadczenia.** Po podaniu wszystkich wymaganych parametrów kliknij przycisk **Zapisz i skompiluj**.
   ![Zrzut ekranu przedstawiający krok parametrów strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po zapisaniu nowej konfiguracji jest przesyłana do kompilacji. Stan zadania kompilacji można wyświetlać jak każdą zaimportowaną konfigurację. Aby uzyskać więcej informacji, zobacz [Wyświetlanie zadania kompilacji](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po pomyślnym zakończeniu kompilacji nowa konfiguracja pojawi się na karcie **Skompilowane konfiguracje.** Gdy jest widoczny na tej karcie, można go przypisać do węzła zarządzanego, wykonując czynności opisane w [obszarze Ponowne przypisywanie węzła do innej konfiguracji węzła.](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać wprowadzenie, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji usługi Azure](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dołączać węzły, zobacz [Maszyny dołączania do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md)
- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [Polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Ceny konfiguracji stanu automatyzacji platformy Azure](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md)
