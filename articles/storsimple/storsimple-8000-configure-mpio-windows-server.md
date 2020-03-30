---
title: Konfigurowanie mpio dla urządzenia StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania wielościeżkowego wejścia/odpowiedzi (MPIO) dla urządzenia StorSimple podłączonego do hosta z systemem Windows Server 2012 R2.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60363401"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurowanie wielościeżkowych we/wy dla urządzenia StorSimple

W tym samouczku opisano kroki, które należy wykonać, aby zainstalować i używać funkcji Wielościeżkowych operacji wejścia/wykonania (MPIO) na hoście z systemem Windows Server 2012 R2 i połączonym z urządzeniem fizycznym StorSimple. Wskazówki zawarte w tym artykule dotyczą tylko urządzeń fizycznych serii StorSimple 8000. Mpio nie jest obecnie obsługiwane w urządzeniu StorSimple Cloud Appliance.

Firma Microsoft zbudowała obsługę funkcji Wielościeżkowych operacji wejścia/wy (MPIO) w systemie Windows Server, aby pomóc w tworzeniu wysoce dostępnych, odpornych na uszkodzenia konfiguracji sieci iSCSI. Mpio używa nadmiarowych składników ścieżki fizycznej — adapterów, kabli i przełączników — do tworzenia ścieżek logicznych między serwerem a urządzeniem pamięci masowej. Jeśli występuje błąd składnika, powodujący niepowodzenie ścieżki logicznej, logika wielościeżkowa używa alternatywnej ścieżki dla we/wy, dzięki czemu aplikacje mogą nadal uzyskiwać dostęp do swoich danych. Ponadto w zależności od konfiguracji mpio można również zwiększyć wydajność poprzez ponowne zrównoważenie obciążenia we wszystkich tych ścieżkach. Aby uzyskać więcej informacji, zobacz [omówienie mpio](https://technet.microsoft.com/library/cc725907.aspx "Omówienie i funkcje mpio").

Aby uzyskać wysoką dostępność rozwiązania StorSimple, mpio należy skonfigurować na urządzeniu StorSimple. Po zainstalowaniu mpio na serwerach hosta z systemem Windows Server 2012 R2, serwery mogą następnie tolerować awarię łącza, sieci lub interfejsu.

## <a name="mpio-configuration-summary"></a>Podsumowanie konfiguracji mpio

Mpio jest opcjonalną funkcją w systemie Windows Server i nie jest instalowany domyślnie. Powinno być instalowane jako funkcja za pomocą Menedżera serwera.

Wykonaj następujące kroki, aby skonfigurować mpio na urządzeniu StorSimple:

* Krok 1: Instalowanie mpio na hoście systemu Windows Server
* Krok 2: Konfigurowanie mpio dla woluminów StorSimple
* Krok 3: Mount StorSimple woluminów na hoście
* Krok 4: Konfigurowanie mpio dla wysokiej dostępności i równoważenia obciążenia

Każdy z powyższych kroków jest omówiony w poniższych sekcjach.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Instalowanie mpio na hoście systemu Windows Server

Aby zainstalować tę funkcję na hoście systemu Windows Server, wykonaj poniższą procedurę.

#### <a name="to-install-mpio-on-the-host"></a>Aby zainstalować mpio na hoście

1. Otwórz Menedżera serwera na hoście systemu Windows Server. Menedżer serwera jest domyślnie uruchamiany, gdy członek grupy Administratorzy loguje się do komputera z systemem Windows Server 2012 R2 lub Windows Server 2012. Jeśli Menedżer serwera nie jest jeszcze otwarty, kliknij przycisk **Uruchom > Menedżer serwera**.
   
   ![Menedżer serwera](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kliknij **pozycję > Pulpit nawigacyjny Menedżera serwera > Dodaj role i funkcje**. Spowoduje to uruchomienie kreatora **Dodawanie ról i funkcji.**
   
   ![Kreator dodawania ról i funkcji 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. W Kreatorze **Dodawania ról i funkcji** wykonaj następujące czynności:
   
   1. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
   2. Na stronie **Wybierz typ instalacji** zaakceptuj ustawienie domyślne instalacji **opartej na rolach lub w oparciu o funkcje.** Kliknij przycisk **alej**.
   
       ![Kreator dodawania ról i funkcji 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na stronie **Wybierz serwer docelowy** wybierz pozycję **Wybierz serwer z puli serwerów**. Serwer hosta powinien zostać wykryty automatycznie. Kliknij przycisk **alej**.
   4. Na stronie **Wybieranie ról serwera** kliknij przycisk **Dalej**.
   5. Na stronie **Wybierz obiekty** wybierz pozycję **Wielościeżkowe we/wy**i kliknij przycisk **Dalej**.
   
       ![Kreator dodawania ról i funkcji 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na stronie **Potwierdź wybór instalacji,** potwierdź wybór, a następnie wybierz **automatycznie uruchom ponownie serwer docelowy, jeśli jest to wymagane,** jak pokazano poniżej. Kliknij **pozycję Zainstaluj**.
   
       ![Kreator dodawania ról i funkcji 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Użytkownik zostanie powiadomiony po zakończeniu instalacji. Kliknij przycisk **Zamknij**, aby zamknąć kreatora.
   
       ![Kreator dodawania ról i funkcji 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurowanie mpio dla woluminów StorSimple

Mpio musi być skonfigurowany do identyfikowania woluminów StorSimple. Aby skonfigurować mpio do rozpoznawania woluminów StorSimple, wykonaj następujące kroki.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Aby skonfigurować mpio dla woluminów StorSimple

1. Otwórz **konfigurację MPIO**. Kliknij pozycję **> > Narzędzia > > mpio Menedżera serwera**.
2. W oknie dialogowym **Właściwości mpio** wybierz kartę **Odkryj wiele ścieżek.**
3. Wybierz **pozycję Dodaj obsługę urządzeń iSCSI,** a następnie kliknij przycisk **Dodaj**.  
   ![Właściwości MPIO odnajdywanie wielu ścieżek](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Uruchom ponownie serwer po wyświetleniu monitu.
5. W oknie dialogowym **Właściwości mpio** kliknij kartę **Urządzenia MPIO.** Kliknij pozycję **Dodaj**.
    </br>![Właściwości MPIO Urządzenia MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. W oknie dialogowym **Dodawanie obsługi mpio** w obszarze **Identyfikator sprzętu urządzenia**wprowadź numer seryjny urządzenia. Aby uzyskać numer seryjny urządzenia, przejdź do usługi StorSimple Device Manager. Przejdź do **pulpitu**> urządzenia . Numer seryjny urządzenia jest wyświetlany w prawym okienku **Szybki podgląd** pulpitu nawigacyjnego urządzenia.
    </br>
    ![Dodaj obsługę mpio](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Uruchom ponownie serwer po wyświetleniu monitu.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: Mount StorSimple woluminów na hoście

Po skonfigurowaniu mpio w systemie Windows Server można zamontować woluminy utworzone na urządzeniu StorSimple, a następnie skorzystać z funkcji MPIO w celu zapewnienia nadmiarowości. Aby zainstalować wolumin, wykonaj następujące czynności.

#### <a name="to-mount-volumes-on-the-host"></a>Aby zainstalować woluminy na hoście

1. Otwórz okno **Właściwości inicjatora iSCSI** na hoście systemu Windows Server. Kliknij **pozycję > > Narzędzia > > iSCSI — menedżer serwera**.
2. W oknie dialogowym **Właściwości inicjatora iSCSI** kliknij kartę Odnajdowanie, a następnie kliknij pozycję **Odnajduj portal docelowy**.
3. W oknie dialogowym **Odnajdowanie portalu docelowego** wykonaj następujące czynności:
   
   1. Wprowadź adres IP portu DATA urządzenia StorSimple (na przykład wprowadź DANE 0).
   2. Kliknij **przycisk OK,** aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI.**
     
      > [!IMPORTANT]
      > **Jeśli używasz sieci prywatnej dla połączeń iSCSI, wprowadź adres IP portu DATA podłączonego do sieci prywatnej.**
    
4. Powtórz kroki 2-3 dla drugiego interfejsu sieciowego (na przykład DATA 1) w urządzeniu. Należy pamiętać, że te interfejsy powinny być włączone dla interfejsu iSCSI. Aby uzyskać więcej informacji, zobacz [Modyfikowanie interfejsów sieciowych](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Wybierz kartę **Cele** w oknie dialogowym **Właściwości inicjatora iSCSI.** W obszarze **Wykryte obiekty docelowe**urządzenia StorSimple powinny zostać wyświetlone funkcja docelowa IQN urządzenia StorSimple.

   ![Karta Obiekty docelowe inicjatora iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kliknij **przycisk Połącz,** aby ustanowić sesję iSCSI za pomocą urządzenia StorSimple. Zostanie wyświetlone okno dialogowe **Połącz z obiektem** docelowym.
7. W oknie dialogowym **Łączenie z obiektem docelowym** zaznacz pole wyboru **Włącz wiele ścieżek.** Kliknij pozycję **Zaawansowane**.
8. W oknie dialogowym **Ustawienia zaawansowane** wykonaj następujące czynności:
   
   1. Z listy rozwijanej **Karta lokalna** wybierz pozycję **Inicjator iSCSI firmy Microsoft**.
   2. Na liście rozwijanej **Inicjator IP** wybierz adres IP hosta.
   3. Na liście rozwijanej IP **portalu docelowego** wybierz adres IP interfejsu urządzenia.
   4. Kliknij **przycisk OK,** aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI.**
9. Kliknij pozycję **Właściwości**. W oknie dialogowym **Właściwości** kliknij pozycję **Dodaj sesję**.
10. W oknie dialogowym **Łączenie z obiektem docelowym** zaznacz pole wyboru **Włącz wiele ścieżek.** Kliknij pozycję **Zaawansowane**.
11. W oknie dialogowym **Ustawienia zaawansowane:**

    1. Z listy rozwijanej **Karta lokalna** wybierz pozycję Inicjator iSCSI firmy Microsoft.
    2. Na liście rozwijanej **Inicjator IP** wybierz adres IP odpowiadający hostowi. W takim przypadku łączysz dwa interfejsy sieciowe na urządzeniu z jednym interfejsem sieciowym na hoście. W związku z tym ten interfejs jest taki sam, jak przewidziane dla pierwszej sesji.
    3. Na liście rozwijanej **IP portalu docelowego** wybierz adres IP drugiego interfejsu danych włączonego na urządzeniu.
    4. Kliknij **przycisk OK,** aby powrócić do okna dialogowego Właściwości inicjatora iSCSI. Dodano drugą sesję do obiektu docelowego.
12. Otwórz **zarządzanie komputerem,** przechodząc do **> pulpitu nawigacyjnego Menedżera serwera > zarządzania komputerem**. W lewym okienku kliknij pozycję **Magazyn > Zarządzanie dyskami**. Wolumin utworzony na urządzeniu StorSimple, który jest widoczny dla tego hosta, jest wyświetlany w obszarze **Zarządzanie dyskami** jako nowe dyski.
13. Zaikwfować dysk i utworzyć nowy wolumin. Podczas procesu formatowania wybierz rozmiar bloku o rozmiarze 64 KB.
    
    ![Zarządzanie dyskami](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. W obszarze **Zarządzanie dyskami**kliknij prawym przyciskiem myszy **pozycję Dysk** i wybierz polecenie **Właściwości**.
15. W oknie dialogowym StorSimple Model #### **Właściwości urządzenia dysku wieloprzebiegowego** kliknij kartę **MPIO.**
    
    ![StorSimple 8100 Multi-Path Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. W sekcji **Nazwa DSM** kliknij pozycję **Szczegóły** i sprawdź, czy parametry są ustawione na parametry domyślne. Domyślne parametry to:
    
    * Okres weryfikacji ścieżki = 30
    * Liczba ponownych prób = 3
    * Okres usuwania chNP = 20
    * Interwał ponawiania = 1
    * Weryfikacja ścieżki włączona = niezaznaczone.

> [!NOTE]
> **Nie należy modyfikować parametrów domyślnych.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurowanie mpio dla wysokiej dostępności i równoważenia obciążenia

W przypadku wysokiej dostępności i równoważenia obciążenia opartego na wielu ścieżkach wiele sesji musi zostać dodanych ręcznie, aby zadeklarować różne dostępne ścieżki. Na przykład, jeśli host ma dwa interfejsy podłączone do sieci iSCSI, a urządzenie ma dwa interfejsy podłączone do sieci iSCSI, potrzebne są cztery sesje skonfigurowane z prawidłową permutacje ścieżki (tylko dwie sesje będą wymagane, jeśli każdy interfejs DATA i interfejs hosta znajduje się w innej podsieci IP i nie jest rutowalny).

**Zaleca się, że masz co najmniej 8 aktywnych sesji równoległych między urządzeniem a hostem aplikacji.** Można to osiągnąć, włączając 4 interfejsy sieciowe w systemie Windows Server. Używaj fizycznych interfejsów sieciowych lub interfejsów wirtualnych za pośrednictwem technologii wirtualizacji sieci na poziomie sprzętu lub systemu operacyjnego na hoście systemu Windows Server. W dwóch interfejsach sieciowych na urządzeniu ta konfiguracja spowoduje 8 aktywnych sesji. Ta konfiguracja pomaga zoptymalizować przepustowość urządzenia i chmury.

> [!IMPORTANT]
> **Zaleca się, aby nie mieszać interfejsów sieciowych 1 GbE i 10 GbE. Jeśli używasz dwóch interfejsów sieciowych, oba interfejsy powinny być tego samego typu.**

W poniższej procedurze opisano sposób dodawania sesji, gdy urządzenie StorSimple z dwoma interfejsami sieciowymi jest połączone z hostem z dwoma interfejsami sieciowymi. Daje to tylko 4 sesje. Tej samej procedury należy stosować urządzenie StorSimple z dwoma interfejsami sieciowymi połączonymi z hostem z czterema interfejsami sieciowymi. Musisz skonfigurować 8 zamiast 4 sesji opisanych tutaj.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Aby skonfigurować mpio dla wysokiej dostępności i równoważenia obciążenia

1. Odnajdowanie obiektu docelowego: w oknie dialogowym **Właściwości inicjatora iSCSI** na karcie **Odnajdowanie** kliknij pozycję **Odnajduj portal**.
2. W oknie dialogowym **Łączenie z obiektem docelowym** wprowadź adres IP jednego z interfejsów sieciowych urządzenia.
3. Kliknij **przycisk OK,** aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI.**
4. W oknie dialogowym **Właściwości inicjatora iSCSI** wybierz kartę **Cele,** wyróżnij odnaleziony cel, a następnie kliknij przycisk **Połącz**. Zostanie wyświetlone okno dialogowe **Połącz z obiektem** docelowym.
5. W oknie dialogowym **Łączenie z obiektem docelowym:**
   
   1. Pozostaw domyślne wybrane ustawienie docelowe dla **Dodaj to połączenie** do listy ulubionych obiektów docelowych. Powoduje to, że urządzenie automatycznie próbuje ponownie uruchomić połączenie za każdym razem, gdy ten komputer zostanie ponownie uruchomiony.
   2. Zaznacz pole wyboru **Włącz wiele ścieżek.**
   3. Kliknij pozycję **Zaawansowane**.
6. W oknie dialogowym **Ustawienia zaawansowane:**
   
   1. Z listy rozwijanej **Karta lokalna** wybierz pozycję **Inicjator iSCSI firmy Microsoft**.
   2. Na liście rozwijanej **Inicjator IP** wybierz adres IP odpowiadający pierwszemu interfejsowi na hoście (interfejsowi iSCSI).
   3. Na liście rozwijanej **IP portalu docelowego** wybierz adres IP dla pierwszego interfejsu danych włączonego na urządzeniu.
   4. Kliknij **przycisk OK,** aby powrócić do okna dialogowego Właściwości inicjatora iSCSI.
7. Kliknij pozycję **Właściwości**, a następnie w oknie dialogowym **Właściwości** kliknij pozycję **Dodaj sesję**.
8. W oknie dialogowym **Łączenie z obiektem docelowym** zaznacz pole wyboru **Włącz wiele ścieżek,** a następnie kliknij pozycję **Zaawansowane**.
9. W oknie dialogowym **Ustawienia zaawansowane:**
   
   1. Z listy rozwijanej **Karta lokalna** wybierz pozycję **Inicjator iSCSI firmy Microsoft**.
   2. Na liście rozwijanej **Inicjator IP** wybierz adres IP odpowiadający drugiemu interfejsowi iSCSI na hoście.
   3. Na liście rozwijanej **IP portalu docelowego** wybierz adres IP drugiego interfejsu danych włączonego na urządzeniu.
   4. Kliknij **przycisk OK,** aby powrócić do okna dialogowego **Właściwości inicjatora iSCSI.** Teraz dodano drugą sesję do obiektu docelowego.
10. Powtórz kroki 8-10, aby dodać dodatkowe sesje (ścieżki) do obiektu docelowego. Za pomocą dwóch interfejsów na hoście i dwóch na urządzeniu można dodać łącznie cztery sesje.
11. Po dodaniu żądanych sesji (ścieżek) w oknie dialogowym **Właściwości inicjatora iSCSI** zaznacz obiekt **docelowy**i kliknij przycisk Właściwości . Na karcie Sesje w oknie dialogowym **Właściwości** zanotuj cztery identyfikatory sesji, które odpowiadają możliwym permutacji ścieżki. Aby anulować sesję, zaznacz pole wyboru obok identyfikatora sesji, a następnie kliknij przycisk **Rozłącz**.
12. Aby wyświetlić urządzenia prezentowane w sesjach, wybierz kartę **Urządzenia.** Aby skonfigurować zasady mpio dla wybranego urządzenia, kliknij przycisk **MPIO**. Zostanie **wyświetlone** okno dialogowe Szczegóły urządzenia. Na karcie **MPIO** można wybrać odpowiednie ustawienia **zasad równoważenia obciążenia.** Można również wyświetlić typ ścieżki **aktywnej** lub **wstrzymania.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [korzystaniu z usługi StorSimple Device Manager w celu zmodyfikowania konfiguracji urządzenia StorSimple](storsimple-8000-modify-device-config.md).

