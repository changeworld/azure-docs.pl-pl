---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73491445"
---
1. Znajdź utworzoną wirtualną sieć WAN. Na stronie Wirtualna sieć WAN w sekcji **Łączność** wybierz pozycję **Koncentratory**.
2. Na stronie Koncentratory wybierz pozycję **+Nowe centrum,** aby otworzyć stronę **Tworzenie centrum wirtualnego.**
3. Na karcie **Tworzenie** sieciowej strony **centrum wirtualnego** uzupełnij następujące pola:

   ![Podstawy](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Podstawy")

    **Szczegóły projektu**

   * Region (wcześniej określany jako Lokalizacja)
   * Nazwa
   * Centrum prywatnej przestrzeni adresowej. Minimalna przestrzeń adresowa jest /24, aby utworzyć koncentrator, co oznacza, że wszystko w zakresie od /25 do /32 spowoduje błąd podczas tworzenia.
4. Wybierz **kartę ExpressRoute**.

5. Na karcie **Usługa ExpressRoute** uzupełnij następujące pola:

   ![Expressroute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Wybierz **pozycję Tak,** aby utworzyć bramę **usługi ExpressRoute.**
   * Z listy rozwijanej wybierz wartość **jednostek skalowania bramy.**
6. Wybierz **pozycję Recenzja + Utwórz,** aby sprawdzić poprawność.
7. Wybierz **pozycję Utwórz,** aby utworzyć koncentrator. Po 30 minutach **odśwież,** aby wyświetlić koncentrator na stronie **Koncentratory.** Wybierz **pozycję Przejdź do zasobu,** aby przejść do zasobu.