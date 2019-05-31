---
title: Tworzenie usługi Azure Service łańcucha bloków w witrynie Azure portal
description: Usługa Azure blockchain zobaczyliśmy, aby utworzyć składową konsorcjum.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 5cb236dc38ae41b202004b7e4806d8129378cfdb
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417498"
---
# <a name="quickstart-create-an-azure-blockchain-service-using-the-azure-portal"></a>Szybki start: Tworzenie usługi Azure Service łańcucha bloków w witrynie Azure portal

Usługa łańcuch bloków to platforma łańcucha bloków wykonywania logiki biznesowej w inteligentne kontraktu. Ten przewodnik Szybki Start pokazano, jak rozpocząć pracę od utworzenia zarządzanej rejestr przy użyciu witryny Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-managed-ledger"></a>Tworzenie zarządzanego rejestru

Usługa Azure usługa łańcuch bloków jest tworzona ze zdefiniowanym zestawem zasobów obliczeniowych i przestrzeni dyskowej.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz **łańcucha bloków** > **usługi platformy Azure Blockchain**.
1. Ukończ szablonu.

    ![Tworzenie usługi](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Element członkowski łańcucha bloków | Wybierz unikatową nazwę, która identyfikuje Azure Blockchain Service elementów członkowskich. Nazwa elementu członkowskiego łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi być od 2 do 20 znaków.
    Subskrypcja | Wybierz subskrypcję platformy Azure, którego chcesz użyć dla usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.
    Region | Lokalizacja musi być taka sama dla wszystkich elementów członkowskich konsorcjum.
    Element członkowski hasło do konta | Element członkowski hasło do konta jest używany do szyfrowania prywatnego klucza konta Ethereum, które są tworzone dla elementów członkowskich. Do zarządzania konsorcjum używasz konta i hasło do konta członkowskiego.
    Nazwa konsorcjum | Wprowadź unikatową nazwę dla nowego konsorcjum. Jeśli przyłączasz konsorcjum za pomocą zaproszenia, wartość jest konsorcjum są utworzone.
    Opis | Opis konsorcjum.
    Protocol |  (Wersja zapoznawcza) obsługuje protokół kworum.
    Cennik | Konfiguracja węzła dla nowej usługi. Wybierz opcję **Standardowa**. 2 węzłów modułu sprawdzania poprawności i węzła 1 transakcji są ustawienia domyślne.
    Transakcja węzeł hasła | Hasło dla elementu członkowskiego domyślnego transakcji węzła. Podaj hasło dla uwierzytelniania podstawowego, łącząc się z łańcucha bloków elementu członkowskiego domyślnego transakcji węzła publiczny punkt końcowy.

1. Wybierz **Utwórz** do udostępniania usługi. Aprowizowanie trwa około 10 minut.
1. Wybierz **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania.
1. Po wdrożeniu przejdź do elementu członkowskiego z łańcucha bloków.

Wybierz **Przegląd**, można wyświetlić podstawowe informacje dotyczące usługi, w tym RootContract adres i elementów członkowskich.

![Omówienie elementu członkowskiego łańcucha bloków](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Do kolejnego przewodnika Szybki Start lub samouczkiem, można użyć elementu członkowskiego, który został utworzony. Gdy nie jest już potrzebny, możesz usunąć zasoby, usuwając `myResourceGroup` grupę zasobów utworzoną przez usługę Azure Service łańcucha bloków.

Aby usunąć grupę zasobów:

1. W witrynie Azure portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usunięcie, wpisując nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Umożliwia MetaMask łącz się i wdrażaj inteligentne kontraktu](connect-metamask.md)