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
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150295"
---
1. W portalu w obszarze **Wszystkie zasoby** kliknij pozycję **+ Dodaj**.
2. W **wszystko** strony pola wyszukiwania, typ **bramy sieci lokalnej**, a następnie kliknij przycisk, aby powrócić do listy zasobów. Kliknij pozycję **Brama sieci lokalnej**, aby otworzyć stronę, a następnie kliknij pozycję **Utwórz**, aby otworzyć stronę **Utwórz bramę sieci lokalnej**.

   ![utwórz bramę sieci lokalnej](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Na stronie **Utwórz bramę sieci lokalnej** określ wartości dla bramy sieci lokalnej.

   - **Nazwa:** Określ nazwę obiektu bramy sieci lokalnej. Jeśli to możliwe, użyj frazy, które są intuicyjne, takie jak **ClassicVNetLocal** lub **TestVNet1Local**. Ułatwia to określenie bramy sieci lokalnej w portalu.
   - **Adres IP:** Określ prawidłowy publiczny **adresu IP** dla urządzenia sieci VPN lub bramy sieci wirtualnej, do którego chcesz się połączyć.

     * **Jeśli sieć lokalna reprezentuje lokalizację lokalną:** Określ publiczny adres IP urządzenia sieci VPN, który chcesz się połączyć. Nie może ono znajdować się za translatorem adresów sieciowych i musi być dostępne za pomocą usługi Azure.
     * **Jeśli sieć lokalna reprezentuje inną sieć wirtualną:** Określ adres publiczny adres IP, który został przypisany do bramy sieci wirtualnej dla tej sieci wirtualnej.
     * **Jeśli nie masz jeszcze adresu IP:** Można tworzą prawidłowy zastępczego adresu IP, a następnie możesz wrócić i zmodyfikować to ustawienie, przed nawiązaniem połączenia.
   - **Przestrzeń adresowa** odwołuje się do zakresów adresów sieci, które reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi się łączysz.
   - **Skonfiguruj ustawienia protokołu BGP:** Należy używać tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
   - **Subskrypcja:** Sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
   - **Grupa zasobów:** Wybierz grupę zasobów, do którego chcesz używać. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
   - **Lokalizacja:** Wybierz lokalizację, w której ten obiekt zostanie utworzony w. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.

4. Kliknij przycisk **Utwórz**, aby utworzyć bramę sieci lokalnej.
