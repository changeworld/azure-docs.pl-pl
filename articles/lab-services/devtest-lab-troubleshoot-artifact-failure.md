---
title: Diagnozowanie błędów artefaktów na maszynie wirtualnej platformy Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problemy z awariami artefaktów w usłudze Azure DevTest Labs.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ebc64215683989ce07f4dd88dc352ecaefe184cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnozowanie błędów artefaktów w laboratorium 
Po utworzeniu artefaktu, można sprawdzić czy zakończyło się pomyślnie lub nie powiodło się. Dzienniki artefaktów w usłudze Azure DevTest Labs zapewniają informacje, które można użyć do diagnozowania awarii artefaktu. Masz kilka opcji wyświetlania informacji dziennika artefaktu maszyny wirtualnej systemu Windows:

* W portalu Azure
* W maszynie Wirtualnej

> [!NOTE]
> W celu zapewnienia błędy są poprawnie zidentyfikowane i wyjaśniono, jest ważne, czy artefaktu ma Struktura. Informacje dotyczące sposobu tworzenia poprawnie artefaktu, zobacz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md). Aby zapoznać się przykładem prawidłowo strukturalnych artefaktu, zapoznaj się [Test typy parametrów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefaktu.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Rozwiązywanie problemów z błędami artefaktów przy użyciu portalu Azure

1. W portalu Azure, na liście zasobów wybierz laboratorium.
2. Wybierz maszynę Wirtualną systemu Windows, która obejmuje artefaktu, który chcesz zbadać.
3. W lewym panelu w obszarze **ogólne**, wybierz pozycję **artefakty**. Zostanie wyświetlona lista artefakty skojarzone z tej maszyny Wirtualnej. Nazwa artefaktu i stan artefaktu są oznaczone.

   ![Stan artefaktów](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Wybierz artefakt pokazujący **stanu**. Otwiera artefaktu. Zostanie wyświetlony komunikat rozszerzenia, zawierający szczegółowe informacje o awarii artefaktu.

   ![Komunikat o błędzie artefaktów](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Rozwiązywanie problemów z błędami artefaktów z na maszynie wirtualnej

1. Zaloguj się do maszyny Wirtualnej, który zawiera do diagnozowania artefaktu.
2. Przejdź do C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, gdzie *1.9* jest numer wersji Azure niestandardowe rozszerzenie skryptu.

   ![Plik stanu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otwórz **stan** pliku.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne
* [Dołącz Maszynę wirtualną do istniejącej domeny usługi Active Directory przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Dodaj repozytorium Git do laboratorium](devtest-lab-add-artifact-repo.md).

