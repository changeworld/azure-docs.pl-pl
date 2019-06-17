---
title: Konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania wielościeżkowego wejścia/wyjścia (MPIO) dla urządzenia StorSimple połączony z hostem z systemem Windows Server 2012 R2.
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
ms.openlocfilehash: eda134257edb851eea076459b44e02fc59028f46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363401"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple

W tym samouczku opisano kroki należy wykonać, aby zainstalować i korzystać z funkcji wielościeżkowego wejścia/wyjścia (MPIO) na hoście z systemem Windows Server 2012 R2 i połączony z urządzeniem fizycznym StorSimple. Wskazówki zawarte w tym artykule dotyczy serii StorSimple 8000 tylko w przypadku urządzeń fizycznych. Wielościeżkowego wejścia/wyjścia nie jest obecnie obsługiwane na urządzenie StorSimple w chmurze.

Firma Microsoft opracowała obsługę funkcji wielościeżkowego wejścia/wyjścia (MPIO) w systemie Windows Server w celu ułatwienia tworzenia konfiguracji sieci iSCSI wysoko dostępnej i odpornej na uszkodzenia. Wielościeżkowe wejście/wyjście używa nadmiarowych składników ścieżki fizycznej — kart, kabli i przełączników — do tworzenia ścieżek logicznych między serwerem a urządzeniem magazynującym. W przypadku awarii składnika, powodując ścieżkę logiczną zakończyć się niepowodzeniem, uszkodzenie ścieżki logika wielościeżkowa używa ścieżki alternatywnej dla operacji We/Wy, dzięki czemu aplikacje mogą nadal uzyskiwać dostęp do swoich danych. Ponadto w zależności od konfiguracji, wielościeżkowego wejścia/wyjścia można również zwiększyć wydajność przez ponowne równoważenie obciążenia we wszystkich tych ścieżek. Aby uzyskać więcej informacji, zobacz [Omówienie wielościeżkowego wejścia/wyjścia](https://technet.microsoft.com/library/cc725907.aspx "wielościeżkowe wejście/wyjście — omówienie i funkcje").

Dla wysokiej dostępności rozwiązania StorSimple należy skonfigurować wielościeżkowe wejście/wyjście na urządzeniu StorSimple. Po zainstalowaniu wielościeżkowego wejścia/wyjścia na serwerach hostów z systemem Windows Server 2012 R2 serwery następnie może tolerować łącza, sieci lub interfejsu.

## <a name="mpio-configuration-summary"></a>Podsumowanie konfiguracji wielościeżkowego wejścia/wyjścia

Wielościeżkowe wejście/wyjście to opcjonalna funkcja w systemie Windows Server i nie jest instalowany domyślnie. Powinno być instalowane jako funkcja za pomocą Menedżera serwera.

Wykonaj następujące kroki, aby skonfigurować wielościeżkowe wejście/wyjście na urządzeniu StorSimple:

* Krok 1: Zainstaluj wielościeżkowego wejścia/wyjścia na hoście systemu Windows Server
* Krok 2: Konfigurowanie wielościeżkowego wejścia/wyjścia dla woluminów StorSimple
* Krok 3: Woluminy StorSimple instalacji na hoście
* Krok 4: Konfigurowanie wielościeżkowego wejścia/wyjścia w celu zapewnienia wysokiej dostępności i równoważenia obciążenia

W poniższych sekcjach omówiono każdego z powyższych kroków.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Zainstaluj wielościeżkowego wejścia/wyjścia na hoście systemu Windows Server

Aby zainstalować tę funkcję na hoście z systemem Windows Server, wykonaj poniższą procedurę.

#### <a name="to-install-mpio-on-the-host"></a>Aby zainstalować wielościeżkowego wejścia/wyjścia na hoście

1. Otwórz Menedżera serwera na hoście z systemem Windows Server. Domyślnie Menedżer serwera rozpoczyna się, gdy członek grupy Administratorzy zaloguje się na komputerze z systemem Windows Server 2012 R2 lub Windows Server 2012. Jeśli Menedżer serwera nie jest już otwarty, kliknij przycisk **Start > Menedżer serwera**.
   
   ![Menedżera serwera](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kliknij przycisk **Menedżera serwera > pulpit nawigacyjny > Dodaj role i funkcje**. Spowoduje to uruchomienie **Dodaj role i funkcje** kreatora.
   
   ![Dodaj role i funkcje kreatora 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. W **Dodaj role i funkcje** kreatora, wykonaj następujące czynności:
   
   1. Na **przed rozpoczęciem** kliknij **dalej**.
   2. Na **Wybieranie typu instalacji** strona, zaakceptuj domyślne ustawienie **opartej na rolach lub oparta na funkcjach** instalacji. Kliknij przycisk **Dalej**.
   
       ![Dodaj role i funkcje kreatora 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na **serwer docelowy wybierz** wybierz **wybierz serwer z puli serwerów**. Serwer hosta powinny zostać wykryte automatycznie. Kliknij przycisk **Dalej**.
   4. Na **Wybieranie ról serwera** kliknij **dalej**.
   5. Na **wybierz funkcje** wybierz **wielościeżkowego We/Wy**i kliknij przycisk **dalej**.
   
       ![Dodaj role i funkcje kreatora 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na **Potwierdź wybrane opcje instalacji** strony, potwierdź wybór, a następnie wybierz pozycję **automatycznego ponownego uruchomienia serwera docelowego, jeśli jest to wymagane**, jak pokazano poniżej. Kliknij pozycję **Zainstaluj**.
   
       ![Dodaj role i funkcje kreatora 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Otrzymasz powiadomienie po zakończeniu instalacji. Kliknij przycisk **Zamknij**, aby zamknąć kreatora.
   
       ![Dodaj role i funkcje kreatora 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurowanie wielościeżkowego wejścia/wyjścia dla woluminów StorSimple

Wielościeżkowe wejście/wyjście musi być skonfigurowany w celu zidentyfikowania woluminów StorSimple. Aby skonfigurować wielościeżkowe wejście/wyjście, rozpoznawał woluminów StorSimple, wykonaj następujące czynności.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia dla woluminów StorSimple

1. Otwórz **Konfiguracja wielościeżkowego wejścia/wyjścia**. Kliknij przycisk **Menedżera serwera > pulpit nawigacyjny > Narzędzia > wielościeżkowego wejścia/wyjścia**.
2. W **Właściwości wielościeżkowego wejścia/wyjścia** okno dialogowe, wybierz opcję **wykrywanie wielu ścieżek** kartę.
3. Wybierz **obsługę urządzeń iSCSI**, a następnie kliknij przycisk **Dodaj**.  
   ![Właściwości wielościeżkowego wejścia/wyjścia odnajdywanie wielu ścieżek](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Uruchom ponownie serwer po wyświetleniu monitu.
5. W **Właściwości wielościeżkowego wejścia/wyjścia** okno dialogowe, kliknij przycisk **urządzeń wielościeżkowego wejścia/wyjścia** kartę. Kliknij pozycję **Add** (Dodaj).
    </br>![Urządzeń wielościeżkowego wejścia/wyjścia Właściwości wielościeżkowego wejścia/wyjścia](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. W **Dodaj Obsługa wielościeżkowego wejścia/wyjścia** dialogowego **identyfikator urządzenia sprzętowego**, wprowadź numer seryjny urządzenia z systemem. Aby uzyskać numer seryjny urządzenia, dostęp do usługi Menedżer urządzeń StorSimple. Przejdź do **urządzeń > pulpit nawigacyjny**. Numer seryjny urządzenia jest wyświetlany w prawej **Quick Glance** okienku pulpitu nawigacyjnego z urządzenia.
    </br>
    ![Dodanie obsługi wielościeżkowego wejścia/wyjścia](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Uruchom ponownie serwer po wyświetleniu monitu.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: Woluminy StorSimple instalacji na hoście

Po skonfigurowaniu wielościeżkowego wejścia/wyjścia w systemie Windows Server mogą być instalowane woluminy utworzone na urządzeniu StorSimple, a następnie korzystać z zalet wielościeżkowego wejścia/wyjścia w celu zapewnienia nadmiarowości. Aby zainstalować wolumin, wykonaj następujące kroki.

#### <a name="to-mount-volumes-on-the-host"></a>Aby zainstalować woluminy na hoście

1. Otwórz **właściwości inicjatora iSCSI** okno na hoście systemu Windows Server. Kliknij przycisk **Menedżera serwera > pulpit nawigacyjny > Narzędzia > inicjatora iSCSI**.
2. W **właściwości inicjatora iSCSI** okno dialogowe, kliknij kartę odnajdywania, a następnie kliknij przycisk **odnajdowanie portalu obiektu docelowego**.
3. W **odnajdowanie portalu obiektu docelowego** okna dialogowego pole, wykonaj następujące czynności:
   
   1. Wprowadź adres IP, portu, danych urządzenia StorSimple (na przykład, wprowadź interfejs DATA 0).
   2. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe.
     
      > [!IMPORTANT]
      > **Jeśli używasz sieci prywatnej dla połączenia iSCSI, wprowadź adres IP, portu danych, który jest podłączony do sieci prywatnej.**
    
4. Powtórz kroki 2 i 3 dla drugiego interfejsu sieciowego (na przykład dane 1) na urządzeniu. Należy pamiętać, że te interfejsy powinno być włączone dla interfejsu iSCSI. Aby uzyskać więcej informacji, zobacz [zmodyfikować interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Wybierz **cele** karcie **właściwości inicjatora iSCSI** okno dialogowe. Powinien zostać wyświetlony urządzenia StorSimple docelowa nazwa IQN w obszarze **wykryte obiekty docelowe**.

   ![Karta elementy docelowe właściwości inicjatora iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kliknij przycisk **Connect** ustanowienie sesji iSCSI na urządzeniu StorSimple. A **nawiązywanie połączenia z docelowym** pojawi się okno dialogowe.
7. W **nawiązywanie połączenia z docelowym** okno dialogowe, wybierz opcję **Włącz wielościeżkowe** pole wyboru. Kliknij przycisk **zaawansowane**.
8. W **Zaawansowane ustawienia** okna dialogowego pole, wykonaj następujące czynności:
   
   1. Na **karty lokalnej** listy rozwijanej wybierz **inicjatora iSCSI firmy Microsoft**.
   2. Na **adres IP inicjatora** listy rozwijanej wybierz adres IP hosta.
   3. Na **portalu obiektu docelowego** IP listę rozwijaną, wybierz adres IP interfejsu urządzenia na liście.
   4. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe.
9. Kliknij pozycję **Właściwości**. W **właściwości** okno dialogowe, kliknij przycisk **Dodaj sesję**.
10. W **nawiązywanie połączenia z docelowym** okno dialogowe, wybierz opcję **Włącz wielościeżkowe** pole wyboru. Kliknij przycisk **zaawansowane**.
11. W **Zaawansowane ustawienia** okno dialogowe:

    1. Na **karty lokalnej** listy rozwijanej, wybierz opcję inicjatora iSCSI firmy Microsoft.
    2. Na **adres IP inicjatora** listę rozwijaną, wybierz adres IP odpowiadający hosta na liście. W takim przypadku w pojedynczym interfejsem sieciowym na hoście łączysz dwa interfejsy sieci na urządzeniu. W związku z tym ten interfejs jest taka sama jak ta podana dla pierwszej sesji.
    3. Na **IP portalu docelowego** listy rozwijanej, wybierz adres IP drugiego interfejsu danych, włączone na urządzeniu.
    4. Kliknij przycisk **OK** aby powrócić do okna dialogowego właściwości inicjatora iSCSI. Drugi sesji zostały dodane do obiektu docelowego.
12. Otwórz **Zarządzanie komputerem** , przechodząc do **Menedżera serwera > pulpit nawigacyjny > Zarządzanie komputerem**. W okienku po lewej stronie kliknij **magazynu > Zarządzanie dyskami**. Wolumin utworzony na urządzeniu StorSimple, które będą widoczne dla tego hosta zostanie wyświetlona w obszarze **przystawki Zarządzanie dyskami** jako nowe dyski.
13. Zainicjuj dysk, a następnie utwórz nowy wolumin. W trakcie format Wybierz blok o rozmiarze 64 KB.
    
    ![Zarządzanie dyskami](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. W obszarze **przystawki Zarządzanie dyskami**, kliknij prawym przyciskiem myszy **dysku** i wybierz **właściwości**.
15. W modelu usługi StorSimple ### **właściwości urządzenia dysku wielościeżkowe** okno dialogowe, kliknij przycisk **wielościeżkowego wejścia/wyjścia** kartę.
    
    ![StorSimple 8100 Multi-Path Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. W **Nazwa DSM** kliknij **szczegóły** i sprawdź, czy parametry są ustawione na parametrów domyślnych. Parametry domyślne są następujące:
    
    * Sprawdź ścieżkę, okres = 30
    * Liczba ponownych prób = 3
    * PDO Usuń okres = 20
    * Interwał ponawiania prób = 1
    * Sprawdź ścieżkę włączone = niezaznaczone.

> [!NOTE]
> **Nie należy modyfikować domyślnych parametrów.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurowanie wielościeżkowego wejścia/wyjścia w celu zapewnienia wysokiej dostępności i równoważenia obciążenia

Wielościeżkowe opartego o wysokiej dostępności i równoważenia obciążenia, wiele sesji należy dodać ręcznie zadeklarować różnych ścieżek dostępne. Na przykład, jeśli host ma dwa interfejsy podłączone do sieci iSCSI i urządzenie ma dwa interfejsy podłączone do sieci iSCSI, a następnie należy czterech sesji, skonfigurowany z odpowiednią ścieżką permutacji (tylko dwie sesje będzie wymagane, jeśli każdy interfejs danych i interfejs hosta jest w innej podsieci IP i nie podlega routingowi).

**Zaleca się, że masz co najmniej 8 aktywne sesje równoległe między urządzeniem i hosta aplikacji.** Można to osiągnąć przez włączenie 4 interfejsów sieciowych w systemie Windows Server. Użyj sieci fizycznej lub wirtualnej interfejsów przy użyciu technologii wirtualizacji sieci na poziomie sprzętu lub systemu operacyjnego na hoście z systemem Windows Server. Z dwoma interfejsami sieciowymi na urządzeniu ta konfiguracja mogłoby spowodować 8 aktywnych sesji. Taka konfiguracja pozwala zoptymalizować przepływność urządzenia i chmury.

> [!IMPORTANT]
> **Zaleca się, że użytkownik nie należy mieszać 1 GbE i interfejsów sieciowych 10 GbE. Jeśli używasz dwa interfejsy sieciowe, dotyczą obu interfejsów powinien być identyczny typu.**

Poniższa procedura opisuje sposób dodawania sesji, gdy urządzenie StorSimple o dwa interfejsy sieci jest podłączony do hosta z dwoma interfejsami sieciowymi. Dzięki temu tylko 4 sesji. Zastosuj tę samą procedurę z urządzeniem StorSimple przy użyciu dwa interfejsy sieciowe podłączone do hosta z czterech interfejsami sieciowymi. Należy skonfigurować 8 zamiast sesje 4 opisane w tym miejscu.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia w celu zapewnienia wysokiej dostępności i równoważenia obciążenia

1. Odnajdywanie elementu docelowego: w **właściwości inicjatora iSCSI** dialogowym **odnajdywania** kliknij pozycję **odnajdź Portal**.
2. W **nawiązywanie połączenia z docelowym** okna dialogowego wprowadź adres IP jednego z interfejsów sieciowych urządzenia.
3. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe.
4. W **właściwości inicjatora iSCSI** okno dialogowe, wybierz opcję **cele** kartę, zaznacz odnalezionych docelowego, a następnie kliknij **Connect**. **Nawiązywanie połączenia z docelowym** pojawi się okno dialogowe.
5. W **nawiązywanie połączenia z docelowym** okno dialogowe:
   
   1. Pozostaw domyślnie wybrane ustawienie obiektu docelowego dla **dodać to połączenie** do listy ulubionych elementów docelowych. To sprawia, że urządzenie automatycznej próbie ponownego uruchomienia połączenie, za każdym razem, gdy ten komputer zostanie uruchomiony ponownie.
   2. Wybierz **Włącz wielościeżkowe** pole wyboru.
   3. Kliknij przycisk **zaawansowane**.
6. W **Zaawansowane ustawienia** okno dialogowe:
   
   1. Na **karty lokalnej** listy rozwijanej wybierz **inicjatora iSCSI firmy Microsoft**.
   2. Na **adres IP inicjatora** listy rozwijanej wybierz adres IP odpowiadający interfejsowi pierwszy na hoście (interfejsu iSCSI).
   3. Na **IP portalu docelowego** listy rozwijanej, wybierz adres IP pierwszego interfejsu danych, włączone na urządzeniu.
   4. Kliknij przycisk **OK** aby powrócić do okna dialogowego właściwości inicjatora iSCSI.
7. Kliknij przycisk **właściwości**, a następnie w **właściwości** okno dialogowe, kliknij przycisk **Dodaj sesję**.
8. W **nawiązywanie połączenia z docelowym** okno dialogowe, wybierz opcję **Włącz wielościeżkowe** pole wyboru, a następnie kliknij przycisk **zaawansowane**.
9. W **Zaawansowane ustawienia** okno dialogowe:
   
   1. Na **karty lokalnej** listy rozwijanej wybierz **inicjatora iSCSI firmy Microsoft**.
   2. Na **adres IP inicjatora** listy rozwijanej wybierz adres IP odpowiadający drugi interfejs iSCSI na hoście.
   3. Na **IP portalu docelowego** listy rozwijanej, wybierz adres IP drugiego interfejsu danych, włączone na urządzeniu.
   4. Kliknij przycisk **OK** aby powrócić do **właściwości inicjatora iSCSI** okno dialogowe. Drugi sesji zostały dodane do obiektu docelowego.
10. Powtórz kroki 8 – 10, aby dodać dodatkowe sesji (ścieżek) do obiektu docelowego. Mając dwa interfejsy na hoście i dwa na urządzeniu możesz dodać daje w sumie czterech sesji.
11. Po dodaniu żądanej sesji (ścieżek) w **właściwości inicjatora iSCSI** okna dialogowego pole, wybierz element docelowy i kliknij przycisk **właściwości**. Na karcie sesje **właściwości** okno dialogowe, Uwaga czterech sesji identyfikatorów, które odpowiadają permutacji możliwej ścieżki. Aby anulować sesję, zaznacz pole wyboru obok identyfikatora sesji, a następnie kliknij **rozłączenia**.
12. Zaznacz, aby wyświetlić urządzenia przedstawione w sesjach **urządzeń** kartę. Aby skonfigurować zasady wielościeżkowego wejścia/wyjścia dla wybranego urządzenia, kliknij przycisk **wielościeżkowego wejścia/wyjścia**. **Szczegóły urządzenia** pojawi się okno dialogowe. Na **wielościeżkowego wejścia/wyjścia** karty, możesz wybrać odpowiednie **zasady równoważenia obciążenia** ustawienia. Można również wyświetlić **Active** lub **wstrzymania** typ ścieżki.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [do modyfikowania konfiguracji urządzenia StorSimple przy użyciu usługi Menedżer urządzeń StorSimple](storsimple-8000-modify-device-config.md).

