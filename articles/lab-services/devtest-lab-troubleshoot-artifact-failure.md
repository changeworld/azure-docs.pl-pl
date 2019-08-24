---
title: Diagnozowanie błędów artefaktów na maszynie wirtualnej Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak rozwiązywać błędy artefaktów w Azure DevTest Labs.
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
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 8b7a72c03d41d54aa80505e781b6f6d32cd2a2c0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991363"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnozowanie błędów artefaktów w laboratorium 
Po utworzeniu artefaktu możesz sprawdzić, czy zakończyło się powodzeniem, czy niepowodzeniem. Dzienniki artefaktów w Azure DevTest Labs zawierają informacje, których można użyć do diagnozowania błędu artefaktu. Istnieje kilka opcji wyświetlania informacji o dzienniku artefaktów dla maszyny wirtualnej z systemem Windows:

* W witrynie Azure Portal
* Na maszynie wirtualnej

> [!NOTE]
> Aby upewnić się, że błędy są poprawnie zidentyfikowane i wyjaśnione, ważne jest, aby artefakt miał prawidłową strukturę. Aby uzyskać informacje na temat poprawnego konstruowania artefaktu, zobacz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md). Aby zobaczyć przykład prawidłowo uporządkowanej artefaktu, sprawdź artefakty [typów parametrów testu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Rozwiązywanie problemów z błędami artefaktów przy użyciu Azure Portal

1. W Azure Portal na liście zasobów wybierz swoje laboratorium.
2. Wybierz maszynę wirtualną z systemem Windows, która zawiera artefakt, który chcesz zbadać.
3. W lewym panelu w obszarze **Ogólne**wybierz pozycję **artefakty**. Zostanie wyświetlona lista artefaktów skojarzonych z tą maszyną wirtualną. Nazwa artefaktu i stan artefaktu są wskazane.

   ![Stan artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Wybierz artefakt pokazujący **stanu**. Artefakt zostanie otwarty. Zostanie wyświetlony komunikat o rozszerzeniu, który zawiera szczegółowe informacje o błędzie artefaktu.

   ![Komunikat o błędzie artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Rozwiązywanie problemów z błędami artefaktów z poziomu maszyny wirtualnej

1. Zaloguj się do maszyny wirtualnej zawierającej artefakt, który chcesz zdiagnozować.
2. Przejdź do C:\Packages\Plugins\Microsoft.COMPUTE.CustomScriptExtension\\*1,9*\Status, gdzie *1,9* to numer wersji rozszerzenia niestandardowego skryptu platformy Azure.

   ![Plik stanu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otwórz plik **stanu** .

Aby uzyskać instrukcje dotyczące znajdowania plików dziennika na maszynie wirtualnej z **systemem Linux** , zobacz następujący artykuł: [Korzystanie z rozszerzenia niestandardowego skryptu platformy Azure w wersji 2 z maszynami wirtualnymi z systemem Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Powiązane wpisy w blogu
* [Przyłączanie maszyny wirtualnej do istniejącej domeny Active Directory przy użyciu szablonu Menedżer zasobów w DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium git do laboratorium](devtest-lab-add-artifact-repo.md).

