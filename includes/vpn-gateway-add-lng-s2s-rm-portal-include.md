---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0270fa207b782d2d89f2408a380b766f58fa05cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
1. W portalu w obszarze **Wszystkie zasoby** kliknij pozycję **+ Dodaj**. 
2. W polu wyszukiwania na stronie **Wszystko** wpisz wartość pola **Brama sieci lokalnej**, a następnie kliknij przycisk wyszukiwania. Spowoduje to zwrócenie listy. Kliknij pozycję **Brama sieci lokalnej**, aby otworzyć stronę, a następnie kliknij pozycję **Utwórz**, aby otworzyć stronę **Utwórz bramę sieci lokalnej**.

  ![utwórz bramę sieci lokalnej](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. Na stronie **Utwórz bramę sieci lokalnej** określ wartości dla bramy sieci lokalnej.

  - **Nazwa:** określ nazwę obiektu bramy sieci lokalnej.
  - **Adres IP:** jest to publiczny adres IP urządzenia sieci VPN, z którym ma nawiązać połączenie platforma Azure. Określ prawidłowy publiczny adres IP. Adres IP nie może znajdować się za translatorem adresów sieciowych i musi być dostępny za pomocą usługi Azure. Jeśli w danej chwili nie masz adresu IP, możesz użyć wartości widocznych na zrzucie ekranu, ale konieczny będzie powrót i zamiana zastępczego adresu IP na publiczny adres IP urządzenia sieci VPN. W przeciwnym razie usługa Azure nie będzie mogła nawiązać połączenia.
  - **Przestrzeń adresowa** odwołuje się do zakresów adresów sieci, które reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz się łączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. *Użyj w tym miejscu własnych wartości, a nie wartości widocznych na zrzucie ekranu*.
  - **Skonfiguruj ustawienia protokołu BGP:** użyj tej opcji tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
  - **Subskrypcja:** sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
  - **Grupa zasobów** wybierz grupę zasobów, która ma być używana. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
  - **Lokalizacja:** wybierz lokalizację, w której ten obiekt zostanie utworzony. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.

4. Po określeniu wartości kliknij przycisk **Utwórz** u dołu strony, aby utworzyć bramę sieci lokalnej.