---
title: Diagnozowanie błędów artefaktów na maszynie wirtualnej Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z artefaktów w usłudze Azure DevTest Labs.
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
ms.date: 04/17/2019
ms.author: spelluru
ms.openlocfilehash: 29af70a2713e7b4aebf611d8f2b547e38c6c5d3d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60623144"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnozowanie błędów artefaktów w środowisku laboratoryjnym 
Po utworzeniu artefakt, można sprawdzić, aby zobaczyć, czy zakończonych powodzeniem lub niepowodzeniem. Dzienniki artefaktów w usłudze Azure DevTest Labs zawierają informacje, które umożliwia diagnozowanie błędów artefaktów. Masz kilka opcji, podczas wyświetlania informacji dziennika artefaktu dla maszyny Wirtualnej z systemem Windows:

* W witrynie Azure Portal
* Na maszynie wirtualnej

> [!NOTE]
> Aby upewnić się, błędy zostały poprawnie zidentyfikowane i wyjaśniono, jest ważne, czy artefakt jest struktura. Aby uzyskać informacje o tym, jak utworzyć poprawnie artefakt, zobacz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md). Aby zobaczyć przykład artefaktu prawidłowo ze strukturą, zapoznaj się z [Test typy parametrów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefaktu.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Rozwiązywanie problemów z błędami artefaktów przy użyciu witryny Azure portal

1. W witrynie Azure portal, na liście zasobów wybierz środowiska laboratoryjnego.
2. Wybierz maszynę Wirtualną Windows, która obejmuje artefakt, który chcesz zbadać.
3. W lewym panelu w obszarze **ogólne**, wybierz opcję **artefaktów**. Zostanie wyświetlona lista artefakty skojarzone z daną maszynę Wirtualną. Nazwa artefaktu i stan artefaktów są wskazane.

   ![Stan artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Wybierz artefakt pokazujący **stanu**. Zostanie otwarty artefaktu. Wyświetlany jest komunikat rozszerzenia, który zawiera szczegółowe informacje o błędzie artefaktu.

   ![Komunikat o błędzie artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Rozwiązywanie problemów z błędami artefaktu z poziomu maszyny wirtualnej

1. Zaloguj się do maszyny Wirtualnej, która zawiera artefaktu, który chcesz zdiagnozować.
2. Przejdź do C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, gdzie *1.9* jest numerem wersji niestandardowe rozszerzenie skryptu platformy Azure.

   ![Plik stanu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otwórz **stan** pliku.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne
* [Dołączanie maszyny Wirtualnej do istniejącej domeny usługi Active Directory przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Dodawanie repozytorium Git do laboratorium](devtest-lab-add-artifact-repo.md).

