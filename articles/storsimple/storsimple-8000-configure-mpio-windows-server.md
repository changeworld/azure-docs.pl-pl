---
title: Konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania wielościeżkowego wejścia/wyjścia (MPIO) dla Twojego urządzenia StorSimple podłączonego do hosta z systemem Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 4f2b094604f486d283574f4669fcad6f72bd4431
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple

W tym samouczku opisano kroki należy wykonać, aby zainstalować i używać funkcji wielościeżkowego wejścia/wyjścia (MPIO) na hoście z systemem Windows Server 2012 R2 i podłączony do urządzenia fizycznego StorSimple. Wskazówki zawarte w tym artykule dotyczą z serii StorSimple 8000 tylko urządzenia fizycznego. Wielościeżkowe wejście/wyjście nie jest obecnie obsługiwane na urządzeniu z StorSimple w chmurze.

Firma Microsoft opracowała obsługę funkcji wielościeżkowego wejścia/wyjścia (MPIO) w systemie Windows Server w celu ułatwienia tworzenia iSCSI o wysokiej dostępności, odpornej na uszkodzenia konfiguracji sieci. Wielościeżkowe wejście/wyjście używa nadmiarowych składników ścieżki fizycznej — kart, kabli i przełączników — do tworzenia ścieżek logicznych między serwerem a urządzeniem magazynującym. W przypadku awarii składnika, Ścieżka logiczna się niepowodzeniem, przyczyną logika wielościeżkowa używa ścieżki alternatywnej do operacji We/Wy, dzięki czemu aplikacje mogą nadal uzyskiwać dostępu do danych. Ponadto w zależności od konfiguracji wielościeżkowego wejścia/wyjścia mogą także podnieść wydajność przez ponowne równoważenie obciążenia we wszystkich tych ścieżek. Aby uzyskać więcej informacji, zobacz [Omówienie wielościeżkowego wejścia/wyjścia](https://technet.microsoft.com/library/cc725907.aspx "wielościeżkowego wejścia/wyjścia Przegląd oraz funkcje").

Aby wysokiej dostępności rozwiązania StorSimple należy skonfigurować wielościeżkowego wejścia/wyjścia na urządzeniu StorSimple. Podczas instalowania wielościeżkowego wejścia/wyjścia na serwerach hosta z systemem Windows Server 2012 R2, serwery następnie może tolerować łącza, sieci lub interfejsu.

## <a name="mpio-configuration-summary"></a>Podsumowanie konfiguracji wielościeżkowego wejścia/wyjścia

Wielościeżkowego wejścia/wyjścia jest opcjonalna funkcja w systemie Windows Server i nie jest instalowany domyślnie. Powinno być instalowane jako funkcja za pomocą Menedżera serwera.

Wykonaj następujące kroki, aby skonfigurować wielościeżkowego wejścia/wyjścia na urządzeniu StorSimple:

* Krok 1: Instalacja wielościeżkowego We/Wy na hoście systemu Windows Server
* Krok 2: Konfigurowanie wielościeżkowego wejścia/wyjścia dla woluminów StorSimple
* Krok 3: Woluminy StorSimple instalacji na hoście
* Krok 4: Konfigurowanie wielościeżkowego wejścia/wyjścia wysokiej dostępności i równoważenia obciążenia

W poniższych sekcjach omówiono każdego z powyższych kroków.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Instalacja wielościeżkowego We/Wy na hoście systemu Windows Server

Do zainstalowania tej funkcji na hoście z systemem Windows Server, wykonaj poniższą procedurę.

#### <a name="to-install-mpio-on-the-host"></a>Aby zainstalować wielościeżkowego We/Wy na hoście

1. Otwórz Menedżera serwera na hoście z systemem Windows Server. Domyślnie Menedżer serwera rozpoczyna się, gdy członek grupy Administratorzy loguje się do komputera z systemem Windows Server 2012 R2 lub Windows Server 2012. Jeśli Menedżer serwera nie jest jeszcze otwarty, kliknij przycisk **Start > Menedżera serwera**.
   
   ![Menedżer serwera](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kliknij przycisk **Menedżera serwera > pulpit nawigacyjny > Dodaj role i funkcje**. Spowoduje to uruchomienie **Dodaj role i funkcje** kreatora.
   
   ![Dodawanie ról i funkcji — Kreator 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. W **Dodaj role i funkcje** kreatora, wykonaj następujące czynności:
   
   1. Na **przed rozpoczęciem** kliknij przycisk **dalej**.
   2. Na **Wybieranie typu instalacji** Zaakceptuj domyślne ustawienie **rolach lub oparta na funkcjach** instalacji. Kliknij przycisk **Dalej**.
   
       ![Dodawanie ról i funkcji — Kreator 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na **serwera docelowego wybierz** wybierz pozycję **wybierz serwer z puli serwerów**. Serwer hosta powinien być wykrywane automatycznie. Kliknij przycisk **Dalej**.
   4. Na **Wybieranie ról serwera** kliknij przycisk **dalej**.
   5. Na **wybierz funkcje** wybierz **wielościeżkowego We/Wy**i kliknij przycisk **dalej**.
   
       ![Dodawanie ról i funkcji — Kreator 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na **Potwierdź wybrane opcje instalacji** , potwierdź wybór, a następnie wybierz **automatycznie ponownie uruchom serwer docelowy, jeśli jest to wymagane**, jak pokazano poniżej. Kliknij pozycję **Zainstaluj**.
   
       ![Dodawanie ról i funkcji — Kreator 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Użytkownik jest powiadamiany o zakończeniu instalacji. Kliknij przycisk **Zamknij**, aby zamknąć kreatora.
   
       ![Dodawanie ról i funkcji — Kreator 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurowanie wielościeżkowego wejścia/wyjścia dla woluminów StorSimple

Wielościeżkowego wejścia/wyjścia musi być skonfigurowany, aby zidentyfikować woluminów StorSimple. Aby skonfigurować, aby rozpoznać woluminów StorSimple, wykonaj następujące kroki.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Aby skonfigurować wielościeżkowego wejścia/wyjścia dla woluminów StorSimple

1. Otwórz **Konfiguracja MPIO**. Kliknij przycisk **Menedżera serwera > pulpit nawigacyjny > Narzędzia > wielościeżkowego wejścia/wyjścia**.
2. W **Właściwości wielościeżkowego wejścia/wyjścia** okno dialogowe, wybierz opcję **wykrywanie obsługi wielu ścieżek** kartę.
3. Wybierz **dodać obsługę urządzeń iSCSI**, a następnie kliknij przycisk **Dodaj**.  
   ![Właściwości wielościeżkowego wejścia/wyjścia odnajdywanie wielu ścieżek](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Uruchom ponownie serwer po wyświetleniu monitu.
5. W **Właściwości wielościeżkowego wejścia/wyjścia** okno dialogowe, kliknij przycisk **urządzeń wielościeżkowego wejścia/wyjścia** kartę. Kliknij przycisk **dodać**.
    </br>![Wielościeżkowe wejście/wyjście Właściwości wielościeżkowego We/Wy urządzenia](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. W **Dodawanie obsługi wielościeżkowego wejścia/wyjścia** okna dialogowego, w obszarze **identyfikator urządzenia sprzętowego**, wprowadź numer seryjny urządzenia. Aby uzyskać numer seryjny urządzenia, dostęp do usługi Menedżer StorSimple urządzenia. Przejdź do **urządzenia > pulpit nawigacyjny**. Numer seryjny urządzenia jest wyświetlany w prawej **szybki przegląd** okienku nawigacyjnym urządzenia.
    </br>
    ![Dodawanie obsługi wielościeżkowego wejścia/wyjścia](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Uruchom ponownie serwer po wyświetleniu monitu.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: Woluminy StorSimple instalacji na hoście

Po skonfigurowaniu wielościeżkowego wejścia/wyjścia w systemie Windows Server woluminy utworzone na urządzeniu StorSimple może być instalowany i następnie skorzystać z funkcji MPIO nadmiarowości. Aby zainstalować wolumin, wykonaj następujące kroki.

#### <a name="to-mount-volumes-on-the-host"></a>Aby zainstalować woluminy na hoście

1. Otwórz **właściwości inicjatora iSCSI** okno na hoście systemu Windows Server. Kliknij przycisk **Menedżera serwera > pulpit nawigacyjny > Narzędzia > inicjatora iSCSI**.
2. W **właściwości inicjatora iSCSI** okno dialogowe, kliknij kartę odnajdywania, a następnie kliknij przycisk **odnajdowanie portalu obiektu docelowego**.
3. W **odnajdowanie portalu obiektu docelowego** okna dialogowego wykonaj następujące czynności:
   
   1. Wprowadź adres IP, portu, danych urządzenia StorSimple (na przykład wprowadzić dane 0).
   2. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe.
     
     > [!IMPORTANT]
     > **Jeśli używasz sieci prywatnej dla połączenia iSCSI, wprowadź adres IP, portu danych, do którego jest podłączony do sieci prywatnej.**
    
4. Powtórz kroki 2 i 3 dla drugiego interfejsu sieciowego (na przykład 1 danych) na urządzeniu. Należy pamiętać, że te interfejsy powinno być włączone dla interfejsu iSCSI. Aby uzyskać więcej informacji, zobacz [zmodyfikować interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Wybierz **celów** karcie **właściwości inicjatora iSCSI** okno dialogowe. Powinny pojawić się urządzenia StorSimple target IQN w obszarze **wykryte obiekty docelowe**.

   ![Karta elementy docelowe właściwości inicjatora iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kliknij przycisk **Connect** ustanowienie sesji iSCSI przy użyciu swojego urządzenia StorSimple. A **nawiązywanie połączenia z docelowym** zostanie wyświetlone okno dialogowe.
7. W **nawiązywanie połączenia z docelowym** okno dialogowe, wybierz opcję **Włącz wielościeżkowe** pole wyboru. Kliknij przycisk **zaawansowane**.
8. W **Zaawansowane ustawienia** okna dialogowego wykonaj następujące czynności:
   
   1. Na **karty lokalnej** listy rozwijanej wybierz **inicjatora iSCSI firmy Microsoft**.
   2. Na **IP inicjatora** listy rozwijanej wybierz adres IP hosta.
   3. Na **portalu obiektu docelowego** IP listy rozwijanej wybierz adres IP interfejsu urządzenia.
   4. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe.
9. Kliknij pozycję **Właściwości**. W **właściwości** okno dialogowe, kliknij przycisk **dodać sesji**.
10. W **nawiązywanie połączenia z docelowym** okno dialogowe, wybierz opcję **Włącz wielościeżkowe** pole wyboru. Kliknij przycisk **zaawansowane**.
11. W **Zaawansowane ustawienia** okno dialogowe:

    1. Na **karty lokalnej** listy rozwijanej, wybierz inicjatora iSCSI firmy Microsoft.
    2. Na **IP inicjatora** listy rozwijanej wybierz adres IP odpowiadający hosta. W takim przypadku w jednym interfejsem sieciowym na hoście łączysz dwa interfejsy sieci na urządzeniu. W związku z tym ten interfejs jest taka sama jak ta podana dla pierwszej sesji.
    3. Na **docelowy adres IP portalu** listy rozwijanej, wybierz adres IP drugi interfejs danych, włączone na urządzeniu.
    4. Kliknij przycisk **OK** aby powrócić do okna dialogowego właściwości inicjatora iSCSI. Dodano drugiej sesji do obiektu docelowego.
12. Otwórz **Zarządzanie komputerem** przechodząc do **Menedżera serwera > pulpit nawigacyjny > Zarządzanie komputerem**. W okienku po lewej stronie kliknij **magazynu > Zarządzanie dyskami**. Wolumin utworzony na urządzeniu StorSimple, które są widoczne dla tego hosta zostanie wyświetlona w obszarze **Zarządzanie dyskami** jako nowe dyski.
13. Zainicjuj dysk, a następnie utwórz nowy wolumin. W trakcie format Wybierz blok o rozmiarze 64 KB.
    
    ![Zarządzanie dyskami](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. W obszarze **Zarządzanie dyskami**, kliknij prawym przyciskiem myszy **dysku** i wybierz **właściwości**.
15. W modelu StorSimple ### **właściwości urządzenia dysku wielościeżkowe** okno dialogowe, kliknij przycisk **wielościeżkowego wejścia/wyjścia** kartę.
    
    ![StorSimple 8100 DeviceProp wielościeżkowe dysku.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. W **Nazwa DSM** kliknij **szczegóły** i upewnij się, że parametry są ustawione na parametrów domyślnych. Parametry domyślne są:
    
    * Ścieżka upewnij się, okres = 30
    * Liczba ponownych prób = 3
    * PDO Usuń okres = 20
    * Interwał ponawiania prób = 1
    * Sprawdź ścieżkę włączone = niezaznaczone.

> [!NOTE]
> **Nie należy modyfikować domyślnych parametrów.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurowanie wielościeżkowego wejścia/wyjścia wysokiej dostępności i równoważenia obciążenia

Wielościeżkowe podstawie wysokiej dostępności i równoważenia obciążenia, wiele sesji należy dodać ręcznie do deklarowania różne ścieżki dostępne. Na przykład, jeśli host ma dwa interfejsy podłączone do sieci iSCSI i urządzenie ma dwa interfejsy podłączone do sieci iSCSI, a następnie należy cztery sesji skonfigurowane z odpowiednią ścieżką permutacji (tylko dwóch sesji będzie wymagane, jeśli każdy interfejs danych i interfejs hosta jest w innej podsieci IP i nie podlega routingowi).

**Zaleca się, że masz co najmniej 8 aktywne sesje równoległych między urządzeniem i hosta aplikacji.** Można to osiągnąć przez włączenie 4 interfejsów sieciowych w systemie Windows Server. Użyj sieci fizycznej lub wirtualnej interfejsów za pomocą technologii wirtualizacji sieci na poziomie sprzętu lub systemu operacyjnego na hoście z systemem Windows Server. Z dwoma interfejsami sieciowymi na urządzeniu ta konfiguracja spowoduje 8 aktywnych sesji. Taka konfiguracja pozwala zoptymalizować przepływność urządzenia i w chmurze.

> [!IMPORTANT]
> **Zaleca się, że niemieszanie 1 GbE i interfejsów sieciowych usługi 10 GbE. Jeśli używasz dwa interfejsy sieci dotyczą obu interfejsów powinna być typem identyczne.**

Poniższa procedura zawiera opis sposobu dodawania sesji, gdy urządzenie StorSimple z dwoma interfejsami sieciowymi jest podłączone do hosta z dwoma interfejsami sieciowymi. Dzięki temu tylko 4 sesji. Zastosuj tę samą procedurę z urządzeniem StorSimple z dwóch interfejsów sieciowych podłączonych do hosta z czterech interfejsami sieciowymi. Należy skonfigurować 8 zamiast 4 sesje opisane w tym miejscu.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Aby skonfigurować wielościeżkowe wejście/wyjście wysoką dostępność i równoważenie obciążenia

1. Odnajdź elementu docelowego: w **właściwości inicjatora iSCSI** na okna dialogowego **odnajdywania** , kliknij pozycję **odnajdź Portal**.
2. W **nawiązywanie połączenia z docelowym** okna dialogowego wprowadź adres IP jednego z interfejsów sieciowych urządzenia.
3. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe.
4. W **właściwości inicjatora iSCSI** okno dialogowe, wybierz opcję **celów** , zaznacz odnalezionych obiektu docelowego, a następnie kliknij **Connect**. **Nawiązywanie połączenia z docelowym** zostanie wyświetlone okno dialogowe.
5. W **nawiązywanie połączenia z docelowym** okno dialogowe:
   
   1. Pozostaw domyślnie wybrane ustawienie docelowej dla **dodania tego połączenia** do listy ulubionych elementów docelowych. Dzięki temu urządzenie automatycznie podejmować próby ponownego uruchomienia połączenia, za każdym razem, gdy ten komputer zostanie uruchomiony ponownie.
   2. Wybierz **Włącz wielościeżkowe** pole wyboru.
   3. Kliknij przycisk **zaawansowane**.
6. W **Zaawansowane ustawienia** okno dialogowe:
   
   1. Na **karty lokalnej** listy rozwijanej wybierz **inicjatora iSCSI firmy Microsoft**.
   2. Na **IP inicjatora** listy rozwijanej wybierz adres IP odpowiadający pierwszy interfejs na hoście (interfejsu iSCSI).
   3. Na **docelowy adres IP portalu** listy rozwijanej, wybierz adres IP dla pierwszego interfejsu danych, włączone na urządzeniu.
   4. Kliknij przycisk **OK** aby powrócić do okna dialogowego właściwości inicjatora iSCSI.
7. Kliknij przycisk **właściwości**i w **właściwości** okno dialogowe, kliknij przycisk **dodać sesji**.
8. W **nawiązywanie połączenia z docelowym** okno dialogowe, wybierz opcję **Włącz wielościeżkowe** pole wyboru, a następnie kliknij przycisk **zaawansowane**.
9. W **Zaawansowane ustawienia** okno dialogowe:
   
   1. Na **karty lokalnej** listy rozwijanej wybierz **inicjatora iSCSI firmy Microsoft**.
   2. Na **IP inicjatora** listy rozwijanej wybierz adres IP odpowiadający drugi interfejs iSCSI na hoście.
   3. Na **docelowy adres IP portalu** listy rozwijanej, wybierz adres IP drugi interfejs danych, włączone na urządzeniu.
   4. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe. Drugi sesji zostało dodane do obiektu docelowego.
10. Powtórz kroki 8-10, aby dodać dodatkowych sesji (ścieżki) do obiektu docelowego. Dwa interfejsy na hoście i dwa na urządzeniu można dodawać łącznie cztery sesji.
11. Po dodaniu żądanego sesji (ścieżki) w **właściwości inicjatora iSCSI** okno dialogowe, wybierz obiekt docelowy, a kliknij przycisk **właściwości**. Na karcie sesje **właściwości** okno dialogowe, Uwaga cztery sesji identyfikatorów, które odpowiadają permutacji możliwych ścieżek. Aby anulować sesję, zaznacz pole wyboru obok identyfikator sesji, a następnie kliknij przycisk **rozłączenia**.
12. Aby wyświetlić urządzenia przedstawione w sesji, wybierz **urządzeń** kartę. Aby skonfigurować zasady wielościeżkowego wejścia/wyjścia dla wybranego urządzenia, kliknij przycisk **wielościeżkowego wejścia/wyjścia**. **Szczegóły urządzenia** zostanie wyświetlone okno dialogowe. Na **wielościeżkowego wejścia/wyjścia** karcie można wybrać odpowiednie **zasady równoważenia obciążenia** ustawienia. Można również wyświetlić **Active** lub **wstrzymania** typu ścieżki.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do modyfikowania konfiguracji urządzenia StorSimple](storsimple-8000-modify-device-config.md).

