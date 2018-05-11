---
title: Konfigurowanie protokołu CHAP dla urządzenia z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skonfigurować protokół uwierzytelniania typu Challenge Handshake (CHAP) na urządzeniu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: c1a558769ffaa52ed2e996a2b537a5ea409101bd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurowanie protokołu CHAP dla urządzenia StorSimple

W tym samouczku przedstawiono sposób konfigurowania protokołu CHAP dla urządzenia StorSimple. Procedury szczegółowo opisane w tym artykule dotyczą urządzeń z serii StorSimple 8000.

Protokół uwierzytelniania typu Challenge Handshake oznacza protokołu CHAP. Jest schemat uwierzytelniania używany przez serwery do weryfikacji tożsamości klientów zdalnych. Weryfikacja jest oparta na wspólne hasło lub klucz tajny. Protokół CHAP, może być jedną z metod (jednokierunkowe) lub wzajemne (dwukierunkowe). Jednym ze sposobów CHAP jest podczas obiekt docelowy uwierzytelniania inicjatora. W wzajemnego lub wstecznego protokołu CHAP, element docelowy umożliwiają uwierzytelnienie inicjatora a następnie inicjatora obiektu docelowego. Inicjator może być realizowane bez uwierzytelnienia docelowego. Jednak docelowym może być realizowane tylko wtedy, gdy uwierzytelnianie inicjatora również jest zaimplementowana.

Jako najlepsze rozwiązanie zaleca się używać protokołu CHAP w celu zwiększenia bezpieczeństwa iSCSI.

> [!NOTE]
> Należy pamiętać, że protokół IPSEC nie jest obecnie obsługiwane urządzenia StorSimple.

Ustawienia protokołu CHAP na urządzeniu StorSimple można skonfigurować w następujący sposób:

* Uwierzytelnianie jednokierunkowe lub jednokierunkowe
* Dwukierunkowy lub uwierzytelniania wzajemnego lub wstecznego

W każdym z tych przypadków portal dla urządzenia i oprogramowania inicjatora iSCSI serwera musi być skonfigurowana. Szczegółowe informacje na temat tej konfiguracji zostały opisane w poniższych samouczka.

## <a name="unidirectional-or-one-way-authentication"></a>Uwierzytelnianie jednokierunkowe lub jednokierunkowe

W przypadku uwierzytelniania jednokierunkowe inicjator jest uwierzytelniany element docelowy. To uwierzytelnianie wymaga skonfigurowania ustawień inicjatora protokołu CHAP na urządzeniu StorSimple i oprogramowaniem iSCSI Initiator na hoście. Szczegółowe procedury dotyczące hosta systemu Windows i urządzeń StorSimple, na których zostały opisane dalej.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Aby móc skonfigurować urządzenie do uwierzytelniania jednokierunkowe

1. W portalu Azure przejdź do usługi Menedżer StorSimple urządzenia. Kliknij przycisk **urządzeń** i wybierz i kliknij przycisk chcesz Konfigurowanie protokołu CHAP dla urządzenia. Przejdź do **ustawienia urządzenia > zabezpieczeń**. W **ustawienia zabezpieczeń** bloku, kliknij przycisk **protokołu CHAP**.
   
    ![Inicjatora protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. W **protokołu CHAP** bloku, a następnie w **inicjatora protokołu CHAP** sekcji:
   
   1. Podaj nazwę użytkownika dla użytkownika inicjatora protokołu CHAP.
   2. Podaj hasło dla użytkownika inicjatora protokołu CHAP.
      
    > [!IMPORTANT]
    > Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaków. Hasło protokołu CHAP musi należeć do zakresu od 12 do 16 znaków. Dłużej nazwa użytkownika lub hasło powoduje niepowodzenie uwierzytelniania na hoście systemu Windows.
   
   3. Potwierdź hasło.

       ![Inicjatora protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat potwierdzenia. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Aby skonfigurować uwierzytelnianie jednokierunkowe na serwerze hosta systemu Windows
1. Na serwerze hosta z systemem Windows uruchom inicjatora iSCSI.
2. W **właściwości inicjatora iSCSI** okna, wykonaj następujące czynności:
   
   1. Kliknij przycisk **odnajdywania** kartę.
      
       ![Właściwości inicjatora iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Kliknij przycisk **odnajdź Portal**.
3. W **odnajdowanie portalu obiektu docelowego** okno dialogowe:
   
   1. Określ adres IP urządzenia.
   2. Kliknij przycisk **zaawansowane**.
      
       ![Odnajdowanie portalu obiektu docelowego](./media/storsimple-configure-chap/IC740945.png)
4. W **Zaawansowane ustawienia** okno dialogowe:
   
   1. Wybierz **Włącz protokół CHAP logowania** pole wyboru.
   2. W **nazwa** pola, podaj nazwę użytkownika, który określony dla inicjatora protokołu CHAP w portalu Azure.
   3. W **klucz tajny obiektu docelowego** pola, podaj hasło określone dla inicjatora protokołu CHAP w portalu Azure.
   4. Kliknij przycisk **OK**.
      
       ![Zaawansowane ustawienia ogólne](./media/storsimple-configure-chap/IC740946.png)
5. Na **celów** karcie **właściwości inicjatora iSCSI** okna, stan urządzenia powinny się wyświetlać jako **połączony**. Jeśli używasz urządzenia StorSimple 1200 każdy wolumin jest zainstalowany jako obiektu docelowego iSCSI. W związku z tym kroki 3 i 4 należy powtórzyć dla każdego woluminu.
   
    ![Woluminów zainstalowanych jako osobne cele](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Jeśli zmienisz nazwę iSCSI Nowa nazwa jest używana dla nowej sesji iSCSI. Nowe ustawienia nie są ponownie używane istniejące sesje aż do wylogowywania i logowania.

Aby uzyskać więcej informacji o konfigurowaniu protokołu CHAP na serwerze hosta systemu Windows, przejdź do [uwagi dodatkowe](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Dwukierunkowy lub wzajemnego uwierzytelniania

W przypadku uwierzytelniania dwukierunkowego docelowy umożliwiają uwierzytelnienie inicjatora a następnie inicjatora obiektu docelowego. Ta procedura wymaga od użytkownika skonfigurować ustawienia inicjatora protokołu CHAP, wstecznego protokołu CHAP ustawienia na urządzeniu i oprogramowaniem iSCSI Initiator na hoście. W poniższych procedurach opisano kroki, aby skonfigurować uwierzytelnianie wzajemne, na urządzeniu i na hoście systemu Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Aby móc skonfigurować urządzenie do wzajemnego uwierzytelniania

1. W portalu Azure przejdź do usługi Menedżer StorSimple urządzenia. Kliknij przycisk **urządzeń** i wybierz i kliknij przycisk chcesz Konfigurowanie protokołu CHAP dla urządzenia. Przejdź do **ustawienia urządzenia > zabezpieczeń**. W **ustawienia zabezpieczeń** bloku, kliknij przycisk **protokołu CHAP**.
   
    ![Obiektu docelowego protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Przewiń w dół na tej stronie, a następnie w **docelowego protokołu CHAP** sekcji:
   
   1. Podaj **nazwy użytkownika** dla danego urządzenia.
   2. Podaj **wstecznego protokołu CHAP hasła** dla danego urządzenia.
   3. Potwierdź hasło.
3. W **inicjatora protokołu CHAP** sekcji:
   
   1. Podaj **nazwy użytkownika** dla danego urządzenia.
   2. Podaj **hasło** dla danego urządzenia.
   3. Potwierdź hasło.

       ![Inicjatora protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat potwierdzenia. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Aby skonfigurować uwierzytelnianie dwukierunkowe na serwerze hosta systemu Windows

1. Na serwerze hosta z systemem Windows uruchom inicjatora iSCSI.
2. W **właściwości inicjatora iSCSI** okna, kliknij przycisk **konfiguracji** kartę.
3. Kliknij przycisk **protokołu CHAP**.
4. W **wzajemnego protokołu CHAP klucz tajny inicjatora iSCSI** okno dialogowe:
   
   1. Typ **wstecznego protokołu CHAP hasła** skonfigurowanego w portalu Azure.
   2. Kliknij przycisk **OK**.
      
       ![wzajemne tajny protokołu CHAP inicjatora iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Kliknij przycisk **cele** kartę.
6. Kliknij przycisk **Connect** przycisku. 
7. W **połączyć z docelowym** okno dialogowe, kliknij przycisk **zaawansowane**.
8. W **właściwości zaawansowane** okno dialogowe:
   
   1. Wybierz **Włącz protokół CHAP logowania** pole wyboru.
   2. W **nazwa** pola, podaj nazwę użytkownika, który określony dla inicjatora protokołu CHAP w portalu Azure.
   3. W **klucz tajny obiektu docelowego** pola, podaj hasło określone dla inicjatora protokołu CHAP w portalu Azure.
   4. Wybierz **wykonaj wzajemnego uwierzytelniania** pole wyboru.
      
       ![Ustawienia zaawansowane wzajemnego uwierzytelniania.](./media/storsimple-configure-chap/IC740950.png)
   5. Kliknij przycisk **OK** w celu ukończenia konfiguracji protokołu CHAP

Aby uzyskać więcej informacji o konfigurowaniu protokołu CHAP na serwerze hosta systemu Windows, przejdź do [uwagi dodatkowe](#additional-considerations).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

**Szybkie połączenie** funkcja nie obsługuje połączeń, które mają włączony protokół CHAP. Po włączeniu protokołu CHAP, upewnij się, że używasz **Connect** przycisku, który jest dostępny na **cele** kartę, aby połączyć się z obiektem docelowym.

![Łączenie do obiektu docelowego](./media/storsimple-configure-chap/IC740947.png)

W **nawiązywanie połączenia z docelowym** okno dialogowe jest wyświetlone, wybierz **Dodaj to połączenie do listy ulubionych elementów docelowych** pole wyboru. Wybranie tej opcji gwarantuje, że przy każdym ponownym uruchomieniu komputera próby odzyskania połączenia do ulubionych obiektów docelowych iSCSI.

## <a name="errors-during-configuration"></a>Błędy podczas konfiguracji

Jeśli konfiguracja protokołu CHAP jest niepoprawny, a następnie może powodować zobacz **niepowodzenie uwierzytelniania** komunikat o błędzie.

## <a name="verification-of-chap-configuration"></a>Weryfikacja konfiguracji protokołu CHAP

Aby sprawdzić, czy CHAP jest używany przez wykonanie następujących kroków.

#### <a name="to-verify-your-chap-configuration"></a>Aby sprawdzić konfigurację protokołu CHAP
1. Kliknij przycisk **Ulubione obiekty docelowe**.
2. Wybierz cel, dla której jest włączone uwierzytelnianie.
3. Kliknij przycisk **szczegóły**.
   
    ![Inicjator właściwości ulubionych obiektów docelowych iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. W **ulubionych szczegóły docelowej** okna dialogowego Zanotuj wpis w **uwierzytelniania** pola. Jeśli konfiguracja zakończyło się pomyślnie, powinien powiedzieć **protokołu CHAP**.
   
    ![Szczegóły ulubiony obiekt docelowy](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

