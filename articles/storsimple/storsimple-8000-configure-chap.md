---
title: Konfigurowanie protokołu CHAP dla urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362857"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurowanie protokołu CHAP dla urządzenia StorSimple

W tym samouczku opisano sposób konfigurowania protokołu CHAP dla urządzenia StorSimple. Procedura szczegółowo opisane w tym artykule dotyczy urządzeń z serii StorSimple 8000.

Protokół CHAP oznacza Challenge Handshake Authentication Protocol. Jest schematu uwierzytelniania używany przez serwery do weryfikacji tożsamości klientów zdalnych. Weryfikacja opiera się na udostępnionym hasła lub klucza tajnego. Protokół CHAP, może być jednym ze sposobów (jednokierunkowe) lub wzajemne (dwukierunkowe). Jednym ze sposobów protokołu CHAP jest, gdy inicjator jest uwierzytelniany element docelowy. W wzajemnego lub wstecznego protokołu CHAP inicjator jest uwierzytelniany element docelowy, a następnie inicjator uwierzytelnia obiektu docelowego. Inicjator może być realizowane bez uwierzytelniania docelowego. Jednak docelowym może być realizowane tylko wtedy, gdy inicjator uwierzytelniania jest również implementowana.

Najlepszym rozwiązaniem zaleca się używanie protokołu CHAP w celu ulepszania zabezpieczeń iSCSI.

> [!NOTE]
> Należy pamiętać, że protokół IPSEC nie jest obecnie obsługiwane na urządzeniach StorSimple.

Ustawienia protokołu CHAP na urządzeniu StorSimple można skonfigurować w następujący sposób:

* Jedno- lub jednokierunkowe uwierzytelnianie
* Dwukierunkowe lub uwierzytelniania wzajemnego lub do tyłu

W każdym z tych przypadków portal dla urządzeń i oprogramowania inicjatora iSCSI server musi zostać skonfigurowany. Szczegółowe instrukcje zostały podane dla tej konfiguracji są opisane w następującego samouczka.

## <a name="unidirectional-or-one-way-authentication"></a>Jedno- lub jednokierunkowe uwierzytelnianie

W przypadku uwierzytelniania jednokierunkowe inicjator jest uwierzytelniany element docelowy. To uwierzytelnianie wymaga, skonfigurować ustawienia inicjatora protokołu CHAP na urządzeniu StorSimple i oprogramowaniem iSCSI Initiator na hoście. Szczegółowe procedury dotyczące hostów Windows i urządzeń StorSimple, na których są opisane w dalszej części.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Aby skonfigurować urządzenie do uwierzytelniania jednokierunkowe

1. W witrynie Azure portal przejdź do usługi Menedżer urządzeń StorSimple. Kliknij przycisk **urządzeń** wybierz i kliknij przycisk chcesz Konfigurowanie protokołu CHAP dla urządzenia. Przejdź do **ustawienia urządzenia > zabezpieczeń**. W **ustawienia zabezpieczeń** bloku kliknij **protokołu CHAP**.
   
    ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. W **protokołu CHAP** bloku, a następnie w **inicjatora protokołu CHAP** sekcji:
   
   1. Podaj nazwę użytkownika dla usługi inicjatora protokołu CHAP.
   2. Podaj hasło dla użytkownika inicjatora protokołu CHAP.
      
      > [!IMPORTANT]
      > Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaków. Hasło CHAP musi mieć długość od 12 do 16 znaków. Dłuższe nazwy użytkownika ani hasła powoduje niepowodzenie uwierzytelniania na hoście Windows.
   
   3. Potwierdź hasło.

       ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat potwierdzenia. Kliknij przycisk **OK** , aby zapisać zmiany.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Aby skonfigurować jednokierunkowe uwierzytelnianie na serwerze hosta Windows
1. Uruchom inicjator iSCSI na serwerze hosta Windows.
2. W **właściwości inicjatora iSCSI** okna, wykonaj następujące czynności:
   
   1. Kliknij przycisk **odnajdywania** kartę.
      
       ![Właściwości inicjatora iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Kliknij przycisk **odnajdź Portal**.
3. W **odnajdowanie portalu obiektu docelowego** okno dialogowe:
   
   1. Określ adres IP urządzenia.
   2. Kliknij przycisk **zaawansowane**.
      
       ![Odnajdowanie portalu obiektu docelowego](./media/storsimple-configure-chap/IC740945.png)
4. W **Zaawansowane ustawienia** okno dialogowe:
   
   1. Wybierz **Włącz protokół CHAP zalogować** pole wyboru.
   2. W **nazwa** pola, podaj nazwę użytkownika, który został określony dla inicjatora protokołu CHAP w witrynie Azure portal.
   3. W **docelowego wpisu tajnego** pola, podaj hasło, który został określony dla inicjatora protokołu CHAP w witrynie Azure portal.
   4. Kliknij przycisk **OK**.
      
       ![Zaawansowane ustawienia ogólne](./media/storsimple-configure-chap/IC740946.png)
5. Na **cele** karcie **właściwości inicjatora iSCSI** oknie stanu urządzenia powinny się wyświetlać jako **połączono**. Jeśli używasz urządzenia StorSimple 1200 każdy wolumin jest instalowany jako obiektu docelowego iSCSI. W związku z tym kroki 3 i 4 należy powtórzyć dla każdego woluminu.
   
    ![Woluminów zainstalowanych jako oddzielne elementy docelowe](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Jeśli zmienisz nazwę interfejsu iSCSI, Nowa nazwa jest używana dla nowych sesji iSCSI. Nowe ustawienia nie są używane dla istniejącej sesji do momentu wylogowania i zaloguj ponownie.

Aby uzyskać więcej informacji na temat konfigurowania protokołu CHAP na serwerze hosta Windows, przejdź do [dodatkowych kwestii dotyczących](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Dwukierunkowe lub wzajemnego uwierzytelniania

W przypadku uwierzytelniania dwukierunkowe inicjator jest uwierzytelniany element docelowy, a następnie inicjator uwierzytelnia obiektu docelowego. Ta procedura wymaga od użytkownika skonfigurować ustawienia inicjatora protokołu CHAP, ustawienia odwrotnego protokołu CHAP na urządzeniu i oprogramowaniem iSCSI Initiator na hoście. W poniższych procedurach opisano kroki konfigurowania wzajemnego uwierzytelniania na urządzeniu i na hoście Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Aby skonfigurować urządzenie do wzajemnego uwierzytelniania

1. W witrynie Azure portal przejdź do usługi Menedżer urządzeń StorSimple. Kliknij przycisk **urządzeń** wybierz i kliknij przycisk chcesz Konfigurowanie protokołu CHAP dla urządzenia. Przejdź do **ustawienia urządzenia > zabezpieczeń**. W **ustawienia zabezpieczeń** bloku kliknij **protokołu CHAP**.
   
    ![Obiektu docelowego protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Przewiń w dół na tej stronie, a następnie w **element docelowy protokołu CHAP** sekcji:
   
   1. Podaj **nazwy użytkownika** dla Twojego urządzenia.
   2. Podaj **hasło odwrotnego protokołu CHAP** dla Twojego urządzenia.
   3. Potwierdź hasło.
3. W **inicjatora protokołu CHAP** sekcji:
   
   1. Podaj **nazwa_użytkownika** dla Twojego urządzenia.
   2. Podaj **hasło** dla Twojego urządzenia.
   3. Potwierdź hasło.

       ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat potwierdzenia. Kliknij przycisk **OK** , aby zapisać zmiany.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Aby skonfigurować uwierzytelnianie dwukierunkowe na serwerze hosta Windows

1. Uruchom inicjator iSCSI na serwerze hosta Windows.
2. W **właściwości inicjatora iSCSI** okna, kliknij przycisk **konfiguracji** kartę.
3. Kliknij przycisk **protokołu CHAP**.
4. W **wzajemnego tajny protokołu CHAP inicjatora iSCSI** okno dialogowe:
   
   1. Typ **hasło odwrotnego protokołu CHAP** skonfigurowanego w witrynie Azure portal.
   2. Kliknij przycisk **OK**.
      
       ![wzajemne tajny protokołu CHAP inicjatora iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Kliknij przycisk **cele** kartę.
6. Kliknij przycisk **Connect** przycisku. 
7. W **połączyć z docelowym** okno dialogowe, kliknij przycisk **zaawansowane**.
8. W **zaawansowane właściwości** okno dialogowe:
   
   1. Wybierz **Włącz protokół CHAP zalogować** pole wyboru.
   2. W **nazwa** pola, podaj nazwę użytkownika, który został określony dla inicjatora protokołu CHAP w witrynie Azure portal.
   3. W **docelowego wpisu tajnego** pola, podaj hasło, który został określony dla inicjatora protokołu CHAP w witrynie Azure portal.
   4. Wybierz **Przeprowadź uwierzytelnianie wzajemne** pole wyboru.
      
       ![Ustawienia zaawansowane uwierzytelnianie wzajemne](./media/storsimple-configure-chap/IC740950.png)
   5. Kliknij przycisk **OK** w celu ukończenia konfiguracji protokołu CHAP

Aby uzyskać więcej informacji na temat konfigurowania protokołu CHAP na serwerze hosta Windows, przejdź do [dodatkowych kwestii dotyczących](#additional-considerations).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

**Szybkie połączenie** funkcja nie obsługuje połączenia, które mają włączone protokołu CHAP. Po włączeniu uwierzytelniania CHAP, upewnij się, że używasz **Connect** przycisku, który jest dostępny na **cele** kartę, aby nawiązać połączenie z obiektu docelowego.

![Połączenia z obiektem docelowym](./media/storsimple-configure-chap/IC740947.png)

W **nawiązywanie połączenia z docelowym** okno dialogowe, które są prezentowane, wybierz **dodać to połączenie do listy ulubionych elementów docelowych** pole wyboru. Zaznacz to pole wyboru gwarantuje, że za każdym razem, gdy komputer jest uruchamiany ponownie, zostanie podjęta próba Aby przywrócić połączenie do ulubionych obiektów docelowych iSCSI.

## <a name="errors-during-configuration"></a>Błędy podczas konfiguracji

Jeśli Twoja konfiguracja protokołu CHAP jest nieprawidłowe, a następnie użytkownik prawdopodobnie zobacz **niepowodzenie uwierzytelniania** komunikat o błędzie.

## <a name="verification-of-chap-configuration"></a>Weryfikacja konfiguracji protokołu CHAP

Aby sprawdzić, czy protokołu CHAP jest używany, wykonując następujące kroki.

#### <a name="to-verify-your-chap-configuration"></a>Aby sprawdzić konfigurację protokołu CHAP
1. Kliknij przycisk **ulubione elementy docelowe**.
2. Wybierz element docelowy, dla której włączono uwierzytelnianie.
3. Kliknij przycisk **szczegóły**.
   
    ![Inicjator właściwości ulubionych obiektów docelowych iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. W **ulubionych szczegóły elementu docelowego** okna dialogowego Zanotuj wpisu w **uwierzytelniania** pola. Jeśli konfiguracja zakończyło się pomyślnie, powinna być widoczna nazwa **protokołu CHAP**.
   
    ![Szczegóły elementu docelowego ulubionych](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

