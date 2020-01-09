---
title: Tworzenie konfiguracji DSC w Azure Automation konfiguracji stanu (DSC) przy użyciu zasobów złożonych
description: Dowiedz się, jak redagować konfiguracje przy użyciu zasobów złożonych w Azure Automation konfiguracji stanu (DSC)
keywords: PowerShell DSC, Konfiguracja żądanego stanu, środowisko PowerShell DSC Azure, zasoby złożone
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 2c6d9aa13c8c320dc46f6634f3a60e0c1730c710
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418733"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Tworzenie konfiguracji DSC w Azure Automation konfiguracji stanu (DSC) przy użyciu zasobów złożonych

Gdy zasób musi być zarządzany z więcej niż jedną konfiguracją konfiguracja konfiguracji stanu (DSC), najlepszą ścieżką jest użycie [zasobów złożonych](/powershell/scripting/dsc/resources/authoringresourcecomposite). Zasób złożony jest zagnieżdżoną i sparametryzowana konfiguracją używaną jako zasób DSC w innej konfiguracji. Pozwala to na tworzenie złożonych konfiguracji, jednocześnie umożliwiając zarządzanie i kompilowanie bazowych zasobów złożonych (Konfiguracja sparametryzowane).

Azure Automation umożliwia [Importowanie i kompilowanie zasobów złożonych](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
Gdy zasoby złożone zostały zaimportowane na konto usługi Automation, można użyć funkcji tworzenia **konfiguracji** na stronie **Konfiguracja stanu (DSC)** .

## <a name="composing-a-configuration-from-composite-resources"></a>Redagowanie konfiguracji z zasobów złożonych

Aby można było przypisać konfigurację z zasobów złożonych w Azure Portal, należy ją utworzyć. Można to zrobić, korzystając z **konfiguracji redagowania** na stronie **Konfiguracja stanu (DSC)** , a także na kartach **konfiguracje** lub **skompilowane konfiguracje** .

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie **konto usługi Automation** wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **konfiguracje** lub **skompilowane konfiguracje** , a następnie kliknij pozycję **Utwórz konfigurację** w menu w górnej części strony.
1. Na etapie **podstawowe** Podaj nową nazwę konfiguracji (wymagana) i kliknij w dowolnym miejscu w wierszu każdego zasobu złożonego, który ma zostać uwzględniony w nowej konfiguracji, a następnie kliknij przycisk **dalej** lub kliknij krok **kod źródłowy** . W przypadku następujących kroków wybieramy zasoby złożone **PSExecutionPolicy** i **RenameAndDomainJoin** .
   ![zrzut ekranu przedstawiający podstawowe kroki strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **kod źródłowy** pokazuje, jak wygląda pozłożona konfiguracja wybranych zasobów złożonych. Można zobaczyć scalanie wszystkich parametrów i sposób przekazywania ich do zasobu złożonego. Po zakończeniu przeglądania nowego kodu źródłowego kliknij przycisk **dalej** lub kliknij etap **parametrów** .
   ![zrzut ekranu przedstawiający krok kod źródłowy na stronie redagowanie konfiguracji](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. W kroku **Parametry** , parametr, który został uwidoczniony każdy zasób złożony, aby można było je udostępnić. Jeśli parametr ma opis, zostanie wyświetlony obok pola parametru. Jeśli pole jest parametrem typu **PSCredential** , lista rozwijana do skonfigurowania zawiera listę obiektów **poświadczeń** na bieżącym koncie usługi Automation. Dostępna jest również opcja **+ Add a Credential** . Po podaniu wszystkich wymaganych parametrów kliknij przycisk **Zapisz i skompiluj**.
   ![zrzut ekranu przedstawiający krok parametrów na stronie Konfiguracja redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po zapisaniu nowej konfiguracji zostanie ona przesłana do kompilacji. Stan zadania kompilacji można wyświetlić jak każda zaimportowana konfiguracja. Aby uzyskać więcej informacji, zobacz [Wyświetlanie zadania kompilacji](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po pomyślnym zakończeniu kompilacji Nowa konfiguracja zostanie wyświetlona na karcie **skompilowane konfiguracje** . Gdy jest on widoczny na tej karcie, można go przypisać do węzła zarządzanego przy użyciu kroków w temacie [ponowne przypisywanie węzła do innej konfiguracji węzła](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dołączyć węzły, zobacz sekcję dołączanie [maszyn w celu zarządzania według konfiguracji stanu Azure Automation](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md)
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
