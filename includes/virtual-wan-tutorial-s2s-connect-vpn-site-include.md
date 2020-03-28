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
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75450834"
---
1. Wybierz **pozycję Połącz witryny sieci VPN,** aby otworzyć stronę **Połącz witryny.**

    ![Połączyć](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "nawiązywania połączenia")

   Wypełnij następujące pola:

   * Wprowadź klucz wstępny. Jeśli nie wprowadzisz klucza, platforma Azure automatycznie zarób go za Ciebie.
   * Wybierz ustawienia Protokołu i Protokołu IPsec. Patrz [domyślne/niestandardowe szczegóły IPSec] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Wybierz odpowiednią opcję dla **Propagacji trasy domyślnej**. Opcja **Włącz** umożliwia koncentratorowi wirtualnemu propagowanie wyuczonej domyślnej trasy do tego połączenia. Ta flaga umożliwia domyślną propagację trasy do połączenia tylko wtedy, gdy domyślna trasa została już nauczona przez koncentrator wirtualnej sieci WAN w wyniku wdrożenia zapory w centrum lub jeśli inna połączona lokacja wymusiła tunelowanie włączone. Trasa domyślna nie pochodzi z centrum wirtualnej sieci WAN.

2. Wybierz przycisk **Połącz**.
3. W ciągu kilku minut witryna wyświetli stan połączenia i łączności.

   ![Stan](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Stan połączenia:** Jest to stan zasobu platformy Azure dla połączenia, które łączy witrynę sieci VPN z bramą sieci VPN usługi Azure Hub. Gdy ta operacja płaszczyzny sterowania zakończy się pomyślnie, brama sieci VPN platformy Azure i lokalne urządzenie sieci VPN przystąpią do ustanawiania łączności.

   **Stan łączności:** Jest to rzeczywisty stan łączności (ścieżki danych) między bramą sieci VPN platformy Azure w centrum i witrynie sieci VPN. Może pokazać dowolny z następujących stanów:

    * **Nieznany:** Ten stan jest zazwyczaj widoczny, jeśli systemy wewnętrznej bazy danych pracują nad przejściem do innego stanu.
    * **Łączenie:** Brama sieci VPN platformy Azure próbuje skontaktować się z rzeczywistą lokalną witryną sieci VPN.
    * **Połączone:** Łączność jest nawiązywać między bramą sieci VPN platformy Azure a lokalną lokacją sieci VPN.
    * **Rozłączony:** Ten stan jest widoczny, jeśli z jakiegokolwiek powodu (lokalnie lub na platformie Azure) połączenie zostało rozłączone.
4. W witrynie sieci VPN w centrum można dodatkowo wykonać następujące czynności: 

   * Edytuj lub usuń połączenie sieci VPN.
   * Usuń witrynę w witrynie Azure portal.
   * Pobierz konfigurację specyficzne dla gałęzi, aby uzyskać szczegółowe informacje na temat strony platformy Azure przy użyciu menu kontekstu (...) obok witryny. Jeśli chcesz pobrać konfigurację wszystkich połączonych witryn w centrum, wybierz **pobierz konfigurację VPN w** górnym menu.
