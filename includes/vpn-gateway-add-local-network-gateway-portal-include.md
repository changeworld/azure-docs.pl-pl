---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6c59513c2c84b5f280e8200411b53848768eaf29
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970842"
---
1. W portalu kliknij pozycję **+Utwórz zasób**.
2. W polu wyszukiwania wpisz wartość **Brama sieci lokalnej**, a następnie naciśnij klawisz **Enter**, aby wyszukać. Spowoduje to zwrócenie listy wyników. Kliknij pozycję **Brama sieci lokalnej**, a następnie kliknij przycisk **Utwórz**, aby otworzyć stronę **Utwórz bramę sieci lokalnej**.

   ![Tworzenie bramy sieci lokalnej](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Tworzenie bramy sieci lokalnej")

3. Na stronie **Utwórz bramę sieci lokalnej** określ wartości dla bramy sieci lokalnej.

   - **Nazwa:** określ nazwę obiektu bramy sieci lokalnej.
   - **Adres IP:** jest to publiczny adres IP urządzenia sieci VPN, z którym ma nawiązać połączenie platforma Azure. Określ prawidłowy publiczny adres IP. Jeśli w danej chwili nie masz adresu IP, możesz użyć wartości widocznych w przykładzie, ale konieczny będzie powrót i zamiana zastępczego adresu IP na publiczny adres IP urządzenia sieci VPN. W przeciwnym razie usługa Azure nie będzie mogła nawiązać połączenia.
   - **Przestrzeń adresowa** odwołuje się do zakresów adresów sieci, które reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz się łączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. *Jeśli chcesz nawiązać połączenie ze swoją lokacją lokalną, podaj w tym miejscu własne wartości, a nie wartości pokazane w przykładzie*.
   - **Skonfiguruj ustawienia protokołu BGP:** użyj tej opcji tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
   - **Subskrypcja:** sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
   - **Grupa zasobów** wybierz grupę zasobów, która ma być używana. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
   - **Lokalizacja:** Lokalizacja jest taka sama jak **region** w innych ustawieniach. Wybierz lokalizację, w której zostanie utworzony ten obiekt. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.

4. Po określeniu wartości kliknij przycisk **Utwórz** u dołu strony, aby utworzyć bramę sieci lokalnej.
