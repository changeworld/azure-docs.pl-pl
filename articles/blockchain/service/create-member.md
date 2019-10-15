---
title: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków przy użyciu Azure Portal
description: Użyj usługi Azure łańcucha bloków Service, aby utworzyć członka łańcucha bloków.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: c87f9a044d6655b2062f2c2657d3b465699e0dd2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329257"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Szybki Start: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu Azure Portal

Usługa Azure łańcucha bloków to platforma łańcucha bloków, w ramach której można wykonać logikę biznesową w ramach kontraktu inteligentnego. W tym przewodniku szybki start pokazano, jak rozpocząć pracę przez utworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Tworzenie węzłów członkowskich łańcucha bloków

Utwórz element członkowski łańcucha bloków, w którym jest uruchamiany protokół finansów kworum w nowym lub istniejącym konsorcjum.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz pozycję **łańcucha bloków** > **Azure łańcucha bloków Service**.
1. Ukończ szablon.

    ![Utwórz usługę](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Łańcucha bloków element członkowski | Wybierz unikatową nazwę identyfikującą członka usługi Azure łańcucha bloków Service łańcucha bloków. Nazwa elementu członkowskiego łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi mieć długość od 2 do 20 znaków.
    Subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.
    Region | Lokalizacja musi być taka sama dla wszystkich członków konsorcjum.
    Hasło konta elementu członkowskiego | Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego.
    Nazwa konsorcjum | W przypadku nowego konsorcjum wprowadź unikatową nazwę. W przypadku dołączania konsorcjum przez zaproszenie wartość jest konsorcjum, do którego jest przyłączany.
    Opis | Opis konsorcjum.
    Protocol (Protokół) |  Wersja zapoznawcza obsługuje protokół kworum.
    Cennik | Konfiguracja węzła dla nowej usługi. Wybierz pozycję **Standardowy**. 2 węzły modułu sprawdzania poprawności i 1 węzeł transakcji to ustawienia domyślne.
    Hasło węzła transakcji | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków.

1. Wybierz pozycję **Utwórz** , aby zainicjować obsługę administracyjną usługi. Inicjowanie obsługi trwa około 10 minut.
1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania.
1. Po wdrożeniu przejdź do elementu członkowskiego łańcucha bloków.

Wybierz pozycję **Przegląd**, aby wyświetlić podstawowe informacje o usłudze, w tym adres RootContract i konto elementu członkowskiego.

![Przegląd składowej łańcucha bloków](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz użyć elementu członkowskiego utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, można je usunąć, usuwając grupę zasobów `myResourceGroup` utworzoną przez usługę Azure łańcucha bloków.

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Łączenie i wdrażanie kontraktu inteligentnego przy użyciu klasy dbmask](connect-metamask.md)