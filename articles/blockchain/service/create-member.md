---
title: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków — Azure Portal
description: Utwórz członka usługi Azure łańcucha bloków dla konsorcjum łańcucha bloków przy użyciu Azure Portal.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/18/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9b08bf030d66c058ef1ec7c57f1fb2517046c522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285141"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Szybki Start: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu Azure Portal

W tym przewodniku szybki start wdrożono nowego członka łańcucha bloków i konsorcjum w usłudze Azure łańcucha bloków przy użyciu Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Tworzenie węzłów członkowskich łańcucha bloków

Utwórz element członkowski łańcucha bloków, w którym jest uruchamiany protokół finansów kworum w nowym lub istniejącym konsorcjum.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz pozycję **łańcucha bloków** > **usługę Azure łańcucha bloków (wersja zapoznawcza)** .

    ![Utwórz usługę](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Utwórz nową nazwę grupy zasobów lub wybierz istniejącą z subskrypcji.
    Region | Wybierz region, w którym ma zostać utworzony element członkowski. Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji.
    Protokół | Obecnie usługa Azure łańcucha bloków w wersji zapoznawczej obsługuje protokół kworum.
    Konsorcjum | W przypadku nowego konsorcjum wprowadź unikatową nazwę. W przypadku dołączania konsorcjum przy użyciu zaproszenia wybierz konsorcjum, do którego chcesz dołączyć.
    Nazwa | Wybierz unikatową nazwę elementu członkowskiego usługi Azure łańcucha bloków. Nazwa elementu członkowskiego łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi mieć długość od 2 do 20 znaków.
    Hasło konta elementu członkowskiego | Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego.
    Opis | Opis konsorcjum.
    Cennik | Konfiguracja węzła i koszt nowej usługi. Wybierz łącze **Zmień** , aby wybrać między warstwami **standardowa** i **podstawowa** .
    Hasło węzła | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków.

1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność ustawień. Wybierz pozycję **Utwórz** , aby zainicjować obsługę administracyjną usługi. Inicjowanie obsługi trwa około 10 minut.
1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania.
1. Po wdrożeniu przejdź do elementu członkowskiego łańcucha bloków.

Wybierz pozycję **Przegląd**, aby wyświetlić podstawowe informacje o usłudze, w tym adres RootContract i konto elementu członkowskiego.

![Przegląd składowej łańcucha bloków](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz użyć elementu członkowskiego utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, można je usunąć, usuwając grupę zasobów `myResourceGroup` utworzoną dla przewodnika Szybki Start.

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono członka usługi Azure łańcucha bloków i nowego konsorcjum. Skorzystaj z następnego przewodnika Szybki Start, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum w celu dołączenia do konsorcjum na platformie Azure łańcucha bloków.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z siecią Azure łańcucha bloków Service Consortium](connect-vscode.md)
