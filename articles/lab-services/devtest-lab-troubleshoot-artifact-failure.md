---
title: Diagnozowanie błędów artefaktów na maszynie wirtualnej usługi Azure DevTest Labs
description: DevTest Labs zawierają informacje, których można użyć do zdiagnozowania błędu artefaktu. W tym artykule pokazano, jak rozwiązywać problemy z awariami artefaktów.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760321"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnozowanie awarii artefaktów w laboratorium 
Po utworzeniu artefaktu można sprawdzić, czy powiódł się, czy nie powiodło się. Dzienniki artefaktów w laboratoriach deweloperów platformy Azure zawierają informacje, których można użyć do zdiagnozowania błędu artefaktu. Masz kilka opcji wyświetlania informacji dziennika artefaktów dla maszyny Wirtualnej systemu Windows:

* W witrynie Azure Portal
* Na maszynie wirtualnej

> [!NOTE]
> Aby upewnić się, że błędy są poprawnie identyfikowane i wyjaśnione, ważne jest, aby artefakt miał właściwą strukturę. Aby uzyskać informacje dotyczące prawidłowego konstruowania artefaktu, zobacz [Tworzenie artefaktów niestandardowych](devtest-lab-artifact-author.md). Aby zobaczyć przykład artefaktu o prawidłowej strukturze, zapoznaj się z artefaktem [typy parametrów testowych.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Rozwiązywanie problemów z awariami artefaktów przy użyciu witryny Azure portal

1. W witrynie Azure portal na liście zasobów wybierz laboratorium.
2. Wybierz maszynę wirtualną systemu Windows, która zawiera artefakt, który chcesz zbadać.
3. W lewym panelu w obszarze **OGÓLNE**wybierz pozycję **Artefakty**. Pojawi się lista artefaktów skojarzonych z tą maszyną wirtualną. Nazwa artefaktu i stan artefaktu są wskazane.

   ![Stan artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Wybierz artefakt, który pokazuje stan **Niepowodzenie.** Zostanie otwarty artefakt. Wyświetlany jest komunikat o rozszerzeniu zawierający szczegółowe informacje o awarii artefaktu.

   ![Komunikat o błędzie artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Rozwiązywanie problemów z awariami artefaktów z poziomu maszyny wirtualnej

1. Zaloguj się do maszyny Wirtualnej, która zawiera artefakt, który chcesz zdiagnozować.
2. Przejdź do folderu C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, gdzie *1.9* to numer wersji rozszerzenia skryptu niestandardowego platformy Azure.

   ![Plik statusu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otwórz plik **stanu.**

Aby uzyskać instrukcje dotyczące znajdowania plików dziennika na maszynie wirtualnej **z systemem Linux,** zobacz następujący artykuł: [Użyj rozszerzenia skryptu niestandardowego platformy Azure w wersji 2 z maszynami wirtualnymi systemu Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Podobne posty na blogu
* [Dołączanie maszyny Wirtualnej do istniejącej domeny usługi Active Directory przy użyciu szablonu Menedżera zasobów w laboratoriach DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium Git do laboratorium.](devtest-lab-add-artifact-repo.md)

