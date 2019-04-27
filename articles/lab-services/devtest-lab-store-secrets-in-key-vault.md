---
title: Store wpisów tajnych w magazynie kluczy w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywać klucze tajne w usłudze Azure Key Vault i używać ich podczas tworzenia maszyny Wirtualnej, formuły, lub środowiska.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 17469d3602935715d570a496e12b6680269ff465
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622946"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Store wpisów tajnych w magazynie kluczy w usłudze Azure DevTest Labs
Może być konieczne wprowadzenie złożony klucz tajny, korzystając z usługi Azure DevTest Labs: hasło dla maszyny Wirtualnej Windows publiczny klucz SSH dla maszyny Wirtualnej systemu Linux lub osobistego tokenu dostępu do sklonowania repozytorium Git za pomocą artefaktu. Klucze tajne są zwykle długie i mają losowo wybranych znaków. W związku z tym dodawane może być trudne i nie można użyć, zwłaszcza, jeśli używasz tego samego klucza tajnego wiele razy.

Aby rozwiązać ten problem, a także przechowywać klucze tajne w bezpiecznym miejscu, usługa DevTest Labs obsługuje przechowywania wpisów tajnych w [usługi Azure key vault](../key-vault/key-vault-overview.md). Gdy użytkownik zapisze wpisu tajnego po raz pierwszy, usługa DevTest Labs automatycznie tworzy magazynu kluczy w tej samej grupie zasobów zawierającej laboratorium, a następnie zapisuje klucz tajny w usłudze key vault. DevTest Labs tworzy oddzielny magazyn kluczy dla każdego użytkownika. 

## <a name="save-a-secret-in-azure-key-vault"></a>Zapisz klucz tajny w usłudze Azure Key Vault
Aby zapisać klucz tajny w usłudze Azure Key Vault, wykonaj następujące czynności:

1. Wybierz **Moje wpisy tajne** w menu po lewej stronie.
2. Wprowadź **nazwa** dla klucza tajnego. Zobaczysz tę nazwę w liście rozwijanej podczas tworzenia maszyny Wirtualnej, formuły, lub w środowisku. 
3. Wprowadź klucz tajny jako **wartość**.

    ![Klucz tajny Store](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Użyj klucza tajnego z usługi Azure Key Vault
Gdy zachodzi potrzeba wprowadź klucz tajny, aby utworzyć Maszynę wirtualną, formuły, lub w środowisku, można ręcznie wprowadzić klucz tajny lub wybierz zapisane wpisu tajnego z magazynu kluczy. Aby używać hasła przechowywane w magazynie kluczy, wykonaj następujące czynności:

1. Wybierz **używać hasła zapisane**. 
2. Wybierz klucz tajny z listy rozwijanej dla **wybierz wpis tajny**. 

    ![Użyj klucza tajnego w maszynie Wirtualnej](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Użyj klucza tajnego w szablonie usługi Azure Resource Manager
Należy określić nazwę wpisu tajnego w szablonie usługi Azure Resource Manager, który jest używany do tworzenia maszyny Wirtualnej, jak pokazano w poniższym przykładzie:

![Użyj klucza tajnego w środowisku lub formuły](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Kolejne kroki

- [Utwórz Maszynę wirtualną przy użyciu klucza tajnego](devtest-lab-add-vm.md) 
- [Utwórz formułę określającą przy użyciu klucza tajnego](devtest-lab-manage-formulas.md)
- [Utwórz środowisko z użyciem klucza tajnego](devtest-lab-create-environment-from-arm.md)
