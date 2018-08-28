---
title: Za pomocą aplikacji w aplikacji Azure Blockchain Workbench
description: Jak używać aplikacji umów w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e17a9275792e3a7fdbea6e3b95e596eaa15f4359
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105815"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Za pomocą aplikacji w aplikacji Azure Blockchain Workbench

Blockchain Workbench umożliwia tworzenie i podejmować działania dotyczące zamówień. Można również wyświetlić szczegóły, takie jak stan i historię transakcji kontraktu.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrażanie aplikacji Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [wdrażania aplikacji Azure Blockchain Workbench](blockchain-workbench-deploy.md) szczegółowe informacje dotyczące wdrażania
* Aplikacja wdrożonej łańcucha bloków w aplikacji Blockchain Workbench. Zobacz [tworzenie aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench](blockchain-workbench-create-app.md)

[Otwieranie aplikacji Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) w przeglądarce.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Musisz zalogować się jako członek Blockchain Workbench. Jeśli nie istnieją żadne aplikacje na liście, jesteś członkiem Blockchain Workbench, ale nie należy do żadnej aplikacji. Administrator aplikacji Blockchain Workbench można przypisać członków do aplikacji.

## <a name="create-new-contract"></a>Tworzenie nowego kontraktu 

Aby utworzyć nową umowę, musisz być członkiem określony jako kontrakt **inicjatora**. Uzyskać Definiowanie ról aplikacji i inicjatorów dla kontraktu, zobacz [przepływów pracy w konfiguracji — Przegląd](blockchain-workbench-configuration-overview.md#workflows). Aby uzyskać informacje dotyczące przypisywania członków do ról aplikacji, zobacz [Dodaj członka do aplikacji](blockchain-workbench-manage-users.md#add-member-to-application).

1. W sekcji aplikacji Blockchain Workbench wybierz Kafelek aplikacji, który zawiera kontrakt, który chcesz utworzyć. Wyświetli się lista aktywnych umów.

2. Aby utworzyć nową umowę, zaznacz **nowego kontraktu**.

    ![Przycisk Nowa Umowa](media/blockchain-workbench-use/contract-list.png)

3. **Nowego kontraktu** zostanie wyświetlone okienko. Określ wartości parametrów początkowych. Wybierz pozycję **Utwórz**.

    ![Nowe okienko kontraktu](media/blockchain-workbench-use/new-contract.png)

    Nowo utworzony kontrakt jest wyświetlany na liście z aktywnych umów.

    ![Lista aktywnych umów](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Podejmowanie akcji na kontraktu

W zależności od stanu zamówienia jest, elementów członkowskich może potrwać akcji, do którego nastąpi przejście do następnego stanu zamówienia. Akcje są definiowane jako [przejścia](blockchain-workbench-configuration-overview.md#transitions) w ramach [stanu](blockchain-workbench-configuration-overview.md#states). Należących do dozwolonych aplikacji lub wystąpienia roli przejścia elementów członkowskich może potrwać akcji. 

1. W sekcji aplikacji Blockchain Workbench wybierz Kafelek aplikacji, który zawiera kontrakt podjęcie działań.
2. Zaznacz kontrakt na liście. Szczegółowe informacje o umowie dotyczącej są wyświetlane w różnych sekcjach. 

    ![Szczegóły kontraktu](media/blockchain-workbench-use/contract-details.png)

    | Sekcja  | Opis  |
    |---------|---------|
    | Stan | Wyświetla bieżący postęp w etapach kontraktu |
    | Szczegóły | Bieżące wartości umowy |
    | Akcja | Szczegółowe informacje o ostatnią czynność. |
    | Działanie | Historia transakcji kontraktu |
    
3. W **akcji** zaznacz **reakcję**.

4. Szczegółowe informacje o bieżącym stanie zamówienia są wyświetlane w okienku. Wybierz akcję, którą chcesz wykonać listy rozwijanej. 

    ![Wybierz akcję](media/blockchain-workbench-use/choose-action.png)

5. Wybierz **reakcję** można zainicjować akcji.
6. Jeśli parametry są wymagane dla akcji, należy określić wartości dla akcji.

    ![Wykonaj akcję](media/blockchain-workbench-use/take-action.png)

7. Wybierz **reakcję** do wykonania akcji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jak rozwiązywać problemy z aplikacji Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)
