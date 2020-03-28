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
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488938"
---
1. Znajdź utworzoną wirtualną sieć WAN. Na stronie Wirtualna sieć WAN w sekcji **Łączność** wybierz pozycję **Koncentratory**.
2. Na stronie Koncentratory wybierz pozycję **+Nowe centrum,** aby otworzyć stronę **Tworzenie centrum wirtualnego.**

    ![Podstawy](./media/virtual-wan-tutorial-hub-include/basics.png "Podstawy")
3. Na karcie **Tworzenie** sieciowej strony **centrum wirtualnego** uzupełnij następujące pola:

    **Szczegóły projektu**

   * Region (wcześniej określany jako Lokalizacja)
   * Nazwa
   * Centrum prywatnej przestrzeni adresowej. Minimalna przestrzeń adresowa jest /24, aby utworzyć koncentrator, co oznacza, że wszystko w zakresie od /25 do /32 spowoduje błąd podczas tworzenia.
4. Wybierz **dalej: Lokacja na lokację**.

    ![Lokacja na miejscu](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Lokacja-lokacja")

5. Na karcie **Lokacja lokacja** wykonaj następujące pola:

   * Wybierz **pozycję Tak,** aby utworzyć sieć VPN typu lokacja lokacja.
   * Pole Numer AS nie można obecnie edytować w centrum wirtualnym.
   * Z listy rozwijanej wybierz wartość **jednostek skalowania bramy.** Jednostka skalowania umożliwia pobranie agregującej przepływności bramy sieci VPN tworzonej w centrum wirtualnym w celu nawiązania połączenia lokacji. Jeśli wybierzesz jednostkę skalowania 1 = 500 Mb/s, oznacza to, że zostaną utworzone dwa wystąpienia nadmiarowości, z których każde ma maksymalną przepustowość 500 Mb/s. Na przykład, jeśli masz pięć gałęzi, z których każda wykonuje 10 Mb/s w oddziale, będziesz potrzebować łącznej 50 Mb/s na końcu głowy. Planowanie zagregowanej pojemności bramy sieci VPN platformy Azure należy wykonać po dokonaniu oceny pojemności potrzebnej do obsługi liczby oddziałów do koncentratora.
6. Wybierz **pozycję Recenzja + Utwórz,** aby sprawdzić poprawność.
7. Wybierz **pozycję Utwórz,** aby utworzyć koncentrator. Po 30 minutach **odśwież,** aby wyświetlić koncentrator na stronie **Koncentratory.** Wybierz **pozycję Przejdź do zasobu,** aby przejść do zasobu.