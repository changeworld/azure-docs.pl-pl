---
title: Przy użyciu aplikacji w Azure Blockchain Workbench
description: Sposób użycia aplikacji umów w Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b6de4f1df56d1ec80ed74c98f4e3a1db9d206612
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Przy użyciu aplikacji w Azure Blockchain Workbench

Blockchain Workbench służy do tworzenia i podjąć działania w umowach. Można również wyświetlić szczegółowe informacje, takie jak stan i historię transakcji kontraktu.

## <a name="prerequisites"></a>Wymagania wstępne

* Deployment Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [deployment Azure Blockchain Workbench](blockchain-workbench-deploy.md) szczegółowe informacje dotyczące wdrażania
* Aplikacja blockchain wdrożonej w Blockchain Workbench. Zobacz [tworzenie aplikacji blockchain w Azure Blockchain Workbench]()

[Otwórz Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) w przeglądarce.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Musisz zalogować się jako członek Blockchain Workbench. Jeśli nie żadne aplikacje na liście, są członkami Blockchain Workbench, ale nie jest członkiem wszystkich aplikacji. Administrator Blockchain Workbench można przypisać elementów członkowskich do aplikacji.

## <a name="create-new-contract"></a>Tworzenie nowego kontraktu 

Aby utworzyć nowy kontrakt, musisz być członkiem **AllowedInstanceRoles** roli. 

1. W sekcji aplikacji Blockchain Workbench wybierz Kafelek aplikacji, który zawiera kontrakt, który chcesz utworzyć. Wyświetli się lista aktywnych umów.

2. Aby utworzyć nową umowę, zaznacz **kontraktu**.

    ![Nowy przycisk kontraktu](media/blockchain-workbench-use/contract-list.png)

3. **Kontraktu** jest wyświetlany w okienku. Należy określić wartości parametrów początkowych. Wybierz pozycję **Utwórz**.

    ![Nowe okienko kontraktu](media/blockchain-workbench-use/new-contract.png)

    Nowo utworzony kontrakt jest wyświetlana na liście z innych umowach.

    ![Lista aktywnych umów](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Podejmij działanie kontraktu

1. W sekcji aplikacji Blockchain Workbench wybierz Kafelek aplikacji zawiera kontrakt podjęcie działań.

    ![Lista aplikacji](media/blockchain-workbench-use/apps-list.png)

2. Wybierz umowę na liście.

    ![Lista umów](media/blockchain-workbench-use/select-contract.png)

    Szczegóły umowy zostaną wyświetlone w różne sekcje. 

    ![Szczegóły kontraktu](media/blockchain-workbench-use/contract-details.png)

    | Sekcja  | Opis  |
    |---------|---------|
    | Stan | Wyświetla bieżący postęp w etapach kontraktu |
    | Szczegóły | Bieżące wartości kontraktu |
    | Akcja | Szczegółowe informacje o ostatnią czynność. |
    | Działanie | Historia transakcji kontraktu |
    
3. W **akcji** zaznacz **reakcję**.

4. Szczegółowe informacje o bieżącym stanie kontrakt są wyświetlane w okienku. Wybierz akcję, którą chcesz wykonać w listy rozwijanej. 

    ![Wykonaj akcję](media/blockchain-workbench-use/take-action.png)

5. Wybierz **Execute** podjęcie działań.

## <a name="next-steps"></a>Kolejne kroki

[Jak rozwiązywać problemy z Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)