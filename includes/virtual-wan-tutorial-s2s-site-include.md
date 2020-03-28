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
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488844"
---
1. Na stronie portalu dla wirtualnego wan w sekcji **Łączność** wybierz **pozycję Lokacje sieci VPN,** aby otworzyć stronę witryn sieci VPN.
2. Na stronie **Lokacje sieci VPN** kliknij pozycję **+Utwórz lokację**.

   ![Podstawy](./media/virtual-wan-tutorial-site-include/basics.png "Podstawy")
3. Na stronie **Tworzenie witryny sieci VPN** na karcie **Podstawy** uzupełnij następujące pola:

    * **Region** — wcześniej określany jako lokalizacja. Jest to lokalizacja, w której chcesz utworzyć ten zasób witryny.
    * **Nazwa** — nazwa, za pomocą której chcesz odwołać się do witryny lokalnej.
    * **Dostawca urządzenia** — nazwa dostawcy urządzenia sieci VPN (na przykład: Citrix, Cisco, Barracuda). Może to pomóc zespołowi platformy Azure lepiej zrozumieć środowisko, aby dodać dodatkowe możliwości optymalizacji w przyszłości lub pomóc w rozwiązywaniu problemów.
    * **Protokół Border Gateway Protocol** — włącz opcję oznacza, że wszystkie połączenia z lokacji będą włączone w ujmowania BGP. Ostatecznie skonfigurujesz informacje BGP dla każdego łącza z witryny sieci VPN w sekcji Łącza. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z konfigurowaniem protokołu BGP w sieci VPN bramy wirtualnej platformy Azure. Lokalny adres równorzędny BGP nie może być taki sam jak publiczny adres IP sieci VPN na urządzeniu lub przestrzeń adresowa sieci wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla równorzędnego adresu IP BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Jednak nie może to być adres APIPA (169.254.x.x). Określ ten adres w odpowiedniej witrynie sieci VPN reprezentującej lokalizację. Aby zapoznać się z wymaganiami wstępnymi protokołu BGP, zobacz [Informacje o Usłudze BGP z bramą sieci VPN platformy Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Zawsze możesz edytować połączenie sieci VPN, aby zaktualizować jego parametry BGP (Peering IP na łączu i AS #) po włączeniu ustawienia BGP lokacji SIECI VPN.
    * **Prywatna przestrzeń adresowa** — przestrzeń adresowa IP, która znajduje się w witrynie lokalnej. Ruch do tej przestrzeni adresowej jest kierowany do lokacji lokalnej. Jest to wymagane, gdy protokół BGP nie jest włączony dla witryny.
    * **Koncentratory** — koncentrator, z którego chcesz się połączyć. Lokację można połączyć tylko z centrami, które mają bramę sieci VPN. Jeśli nie widzisz koncentratora, najpierw utwórz bramę sieci VPN w tym centrum.
4. Wybierz **łącza,** aby dodać informacje o łączach fizycznych w gałęzi. Jeśli masz urządzenie CPE wirtualnego partnera wan, skontaktuj się z nimi, aby sprawdzić, czy te informacje są wymieniane z platformą Azure jako część przekazywania informacji o gałęzi skonfigurowanych z ich systemów.

   ![Linki](./media/virtual-wan-tutorial-site-include/links.png "Linki")

    * **Nazwa łącza** — nazwa, którą chcesz podać dla łącza fizycznego w witrynie sieci VPN. Przykład: mylink1.
    * **Nazwa dostawcy** — nazwa łącza fizycznego w witrynie sieci VPN. Przykład: ATT, Verizon.
    * **Szybkość** — jest to szybkość urządzenia SIECI VPN w lokalizacji oddziału. Przykład: 50, co oznacza, że 50 Mb/s to szybkość urządzenia SIECI VPN w lokacji oddziału.
    * **Adres IP** — publiczny adres IP urządzenia wstępnego za pomocą tego łącza. Opcjonalnie można podać prywatny adres IP lokalnego urządzenia sieci VPN, które znajduje się za usługą ExpressRoute.
5. Za pomocą tego pola wyboru można usunąć lub dodać dodatkowe łącza. Obsługiwane są cztery łącza na witrynę VPN. Na przykład jeśli w lokalizacji oddziału znajduje się czterech usługodawców internetowych, można utworzyć cztery łącza. po jednym na każdego usługodawcę w usługodawcę i podać informacje dla każdego łącza.
6. Po zakończeniu wypełniania pól wybierz **pozycję Przejrzyj + utwórz,** aby zweryfikować i utworzyć witrynę.
7. Wyświetl stan na stronie witryn sieci VPN. Witryna przejdzie do **Połączenia potrzebne,** ponieważ lokacja nie została jeszcze połączona z koncentratorem.