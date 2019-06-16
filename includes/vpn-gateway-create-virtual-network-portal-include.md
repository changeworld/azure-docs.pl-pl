---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d35da4f1eaed91411c015ed7665944d886f9d79c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170090"
---
Aby utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonaj poniższe kroki. Użyj [przykładowych wartości](#values), jeśli wykonujesz te kroki w ramach samouczka. Jeśli nie wykonujesz tych kroków w ramach samouczka, koniecznie zastąp te wartości własnymi. Aby uzyskać więcej informacji dotyczących pracy z sieciami wirtualnymi, zobacz temat [Omówienie sieci wirtualnych](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN istnieje powielony zakres adresów, ruch nie będzie przekierowywany w oczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

1. Przejdź w przeglądarce do witryny [Azure Portal](http://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób**. W polu **Szukaj w witrynie Marketplace** wpisz „Sieć wirtualna”. Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.
3. Z listy **Wybierz model wdrożenia** w dolnej części strony Sieć wirtualna wybierz pozycję **Menedżer zasobów**, a następnie kliknij przycisk **Utwórz**. Spowoduje to otwarcie strony „Tworzenie sieci wirtualnej”.

   ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "Strona Tworzenie sieci wirtualnej")
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe.

   - **Nazwa**: Wprowadź nazwę sieci wirtualnej. W tym przykładzie jest używana sieć VNet1.
   - **Przestrzeń adresowa**: Wprowadź przestrzeń adresową. Jeśli masz wiele przestrzeni adresowych do dodania, dodaj pierwszą przestrzeń adresową. Możesz dodać dodatkowe przestrzenie adresowe później, po utworzeniu sieci wirtualnej. Upewnij się, że określona przestrzeń adresowa nie nakłada się na przestrzeń adresową dla Twojej lokalizacji lokalnej.
   - **Subskrypcja**: Sprawdź, czy wyświetlana jest prawidłowa subskrypcja. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów**: Wybierz istniejącą grupę zasobów lub Utwórz nową, wpisując nazwę nowej grupy zasobów. Jeśli tworzysz nową grupę, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   - **Lokalizacja**: Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.
   - **Podsieć**: Dodaj nazwę pierwszej podsieci i zakres adresów podsieci. Możesz dodać dodatkowe podsieci i podsieć bramy później, po utworzeniu tej sieci wirtualnej. 

5. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz, aby Twoją sieć wirtualną można było łatwo znaleźć na pulpicie nawigacyjnym, a następnie kliknij przycisk **Utwórz**. Po kliknięciu przycisku **Utwórz** na pulpicie nawigacyjnym zostanie umieszczony kafelek, który będzie odzwierciedlać postęp Twojej sieci wirtualnej. Wygląd kafelka zmienia się w trakcie tworzenia sieci wirtualnej.
