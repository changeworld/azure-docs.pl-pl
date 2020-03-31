---
title: Konfigurowanie protokołu CHAP dla urządzenia z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania protokołu CHAP (Challenge Handshake Authentication Protocol) na urządzeniu StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267965"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurowanie protokołu CHAP dla urządzenia StorSimple

W tym samouczku wyjaśniono, jak skonfigurować protokół CHAP dla urządzenia StorSimple. Procedura opisana w tym artykule dotyczy urządzeń z serii StorSimple 8000.

Chap oznacza protokół uwierzytelniania uzgadniania wyzwania. Jest to schemat uwierzytelniania używany przez serwery do sprawdzania tożsamości klientów zdalnych. Weryfikacja jest oparta na udostępnionym haśle lub kluczu tajnym. Protokół CHAP może być jednokierunkowy (jednokierunkowy) lub wzajemny (dwukierunkowy). Jednym ze sposobów protokołu CHAP jest, gdy obiekt docelowy uwierzytelnia inicjatora. W wzajemnym lub odwrotnym chap, obiekt docelowy uwierzytelnia inicjatora, a następnie inicjator uwierzytelnia obiektu docelowego. Uwierzytelnianie inicjatora można zaimplementować bez uwierzytelniania docelowego. Jednak uwierzytelnianie docelowe można zaimplementować tylko wtedy, gdy zaimplementowano również uwierzytelnianie inicjatora.

Najlepszym rozwiązaniem jest używanie protokołu CHAP w celu zwiększenia bezpieczeństwa iSCSI.

> [!NOTE]
> Należy pamiętać, że protokół IPSEC nie jest obecnie obsługiwany na urządzeniach StorSimple.

Ustawienia protokołu CHAP na urządzeniu StorSimple można skonfigurować w następujący sposób:

* Uwierzytelnianie jednokierunkowe lub jednokierunkowe
* Uwierzytelnianie dwukierunkowe lub wzajemne lub odwrotne

W każdym z tych przypadków należy skonfigurować portal urządzenia i oprogramowanie inicjatora iSCSI serwera. Szczegółowe kroki dla tej konfiguracji są opisane w poniższym samouczku.

## <a name="unidirectional-or-one-way-authentication"></a>Uwierzytelnianie jednokierunkowe lub jednokierunkowe

W uwierzytelnianiu jednokierunkowym obiekt docelowy uwierzytelnia inicjatora. To uwierzytelnianie wymaga skonfigurowania ustawień inicjatora protokołu CHAP na urządzeniu StorSimple i oprogramowania inicjatora iSCSI na hoście. Szczegółowe procedury dla urządzenia StorSimple i hosta systemu Windows są opisane w następnej kolejności.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Aby skonfigurować urządzenie do uwierzytelniania jednokierunkowego

1. W witrynie Azure portal przejdź do usługi StorSimple Device Manager. Kliknij **pozycję Urządzenia** i wybierz pozycję i kliknij urządzenie, dla którego chcesz skonfigurować protokół CHAP. Przejdź do **ustawień urządzenia > security**. W bloku **Ustawienia zabezpieczeń** kliknij przycisk **CHAP**.
   
    ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. W bloku **CHAP** i w sekcji **Inicjator protokołu CHAP:**
   
   1. Podaj nazwę użytkownika inicjatora protokołu CHAP.
   2. Podaj hasło inicjatora protokołu CHAP.
      
      > [!IMPORTANT]
      > Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaki. Hasło PROTOKOŁU CHAP musi zawierać od 12 do 16 znaków. Dłuższa nazwa użytkownika lub hasło powoduje niepowodzenie uwierzytelniania na hoście systemu Windows.
   
   3. Potwierdź hasło.

       ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kliknij przycisk **Zapisz**. Zostanie wyświetlony komunikat potwierdzający. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Aby skonfigurować uwierzytelnianie jednokierunkowe na serwerze hosta systemu Windows
1. Na serwerze hosta systemu Windows uruchom inicjatora iSCSI.
2. W oknie **Właściwości inicjatora iSCSI** wykonaj następujące czynności:
   
   1. Kliknij kartę **Odnajdowanie.**
      
       ![Właściwości inicjatora iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Kliknij **przycisk Odkryj portal**.
3. W oknie dialogowym **Odnajdowanie portalu docelowego:**
   
   1. Określ adres IP urządzenia.
   2. Kliknij pozycję **Zaawansowane**.
      
       ![Odkryj portal docelowy](./media/storsimple-configure-chap/IC740945.png)
4. W oknie dialogowym **Ustawienia zaawansowane:**
   
   1. Zaznacz pole wyboru **Włącz logowanie protokołu CHAP.**
   2. W polu **Nazwa** podaj nazwę użytkownika określoną dla inicjatora protokołu CHAP w witrynie Azure portal.
   3. W docelowym polu **tajnym** podaj hasło określone dla inicjatora protokołu CHAP w witrynie Azure portal.
   4. Kliknij przycisk **OK**.
      
       ![Ustawienia zaawansowane ogólne](./media/storsimple-configure-chap/IC740946.png)
5. Na karcie **Obiekty docelowe** w oknie **Właściwości inicjatora iSCSI** stan urządzenia powinien być wyświetlany jako **Połączony**. Jeśli używasz urządzenia StorSimple 1200, każdy wolumin jest montowany jako cel iSCSI. W związku z tym kroki 3-4 będą musiały być powtórzone dla każdego woluminu.
   
    ![Woluminy zamontowane jako oddzielne obiekty docelowe](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Jeśli zmienisz nazwę iSCSI, nowa nazwa będzie używana dla nowych sesji iSCSI. Nowe ustawienia nie są używane dla istniejących sesji, dopóki nie wylogujesz się i nie zalogujesz ponownie.

Aby uzyskać więcej informacji na temat konfigurowania protokołu CHAP na serwerze hosta systemu Windows, przejdź do [temat Dodatkowe zagadnienia](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Uwierzytelnianie dwukierunkowe lub wzajemne

W uwierzytelnianiu dwukierunkowym obiekt docelowy uwierzytelnia inicjatora, a następnie inicjator uwierzytelnia obiekt docelowy. Ta procedura wymaga, aby użytkownik skonfigurował ustawienia inicjatora protokołu CHAP, odwrotne ustawienia protokołu CHAP na urządzeniu i oprogramowanie inicjatora iSCSI na hoście. W poniższych procedurach opisano kroki konfigurowania wzajemnego uwierzytelniania na urządzeniu i na hoście systemu Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Aby skonfigurować urządzenie do uwierzytelniania wzajemnego

1. W witrynie Azure portal przejdź do usługi StorSimple Device Manager. Kliknij **pozycję Urządzenia** i wybierz pozycję i kliknij urządzenie, dla którego chcesz skonfigurować protokół CHAP. Przejdź do **ustawień urządzenia > security**. W bloku **Ustawienia zabezpieczeń** kliknij przycisk **CHAP**.
   
    ![Cel protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Przewiń w dół na tej stronie i w sekcji **Docelowa chap:**
   
   1. Podaj **odwrotną nazwę użytkownika CHAP** dla swojego urządzenia.
   2. Podaj **hasło odwrotnego protokołu CHAP** dla swojego urządzenia.
   3. Potwierdź hasło.
3. W sekcji **Inicjator protokołu CHAP:**
   
   1. Podaj **nazwę użytkownika** urządzenia.
   2. Podaj **hasło** do swojego urządzenia.
   3. Potwierdź hasło.

       ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kliknij przycisk **Zapisz**. Zostanie wyświetlony komunikat potwierdzający. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Aby skonfigurować uwierzytelnianie dwukierunkowe na serwerze hosta systemu Windows

1. Na serwerze hosta systemu Windows uruchom inicjatora iSCSI.
2. W oknie **Właściwości inicjatora iSCSI** kliknij kartę **Konfiguracja.**
3. Kliknij przycisk **CHAP**.
4. W oknie dialogowym **Inicjatora iSCSI — klucz tajny protokołu CHAP:**
   
   1. Wpisz **hasło odwrotnego protokołu CHAP** skonfigurowane w witrynie Azure portal.
   2. Kliknij przycisk **OK**.
      
       ![Inicjator iSCSI wzajemny klucz tajny CHAP](./media/storsimple-configure-chap/IC740949.png)
5. Kliknij kartę **Cele.**
6. Kliknij przycisk **Połącz.** 
7. W oknie dialogowym **Łączenie z obiektem docelowym** kliknij pozycję **Zaawansowane**.
8. W oknie dialogowym **Właściwości zaawansowane:**
   
   1. Zaznacz pole wyboru **Włącz logowanie protokołu CHAP.**
   2. W polu **Nazwa** podaj nazwę użytkownika określoną dla inicjatora protokołu CHAP w witrynie Azure portal.
   3. W docelowym polu **tajnym** podaj hasło określone dla inicjatora protokołu CHAP w witrynie Azure portal.
   4. Zaznacz pole wyboru **Wykonywanie uwierzytelniania wzajemnego.**
      
       ![Wzajemne uwierzytelnianie ustawień zaawansowanych](./media/storsimple-configure-chap/IC740950.png)
   5. Kliknij **przycisk OK,** aby ukończyć konfigurację protokołu CHAP

Aby uzyskać więcej informacji na temat konfigurowania protokołu CHAP na serwerze hosta systemu Windows, przejdź do [temat Dodatkowe zagadnienia](#additional-considerations).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Funkcja **Szybkie łączenie** nie obsługuje połączeń z włączonym rozcięciem CHAP. Gdy protokół CHAP jest włączony, upewnij się, że używasz przycisku **Połącz,** który jest dostępny na karcie **Obiekty docelowe,** aby połączyć się z obiektem docelowym.

![Połącz się z obiektem docelowym](./media/storsimple-configure-chap/IC740947.png)

W wyświetlonym oknie dialogowym **Łączenie z obiektem docelowym** zaznacz pole wyboru **Dodaj to połączenie do listy ulubionych obiektów docelowych.** To zaznaczenie gwarantuje, że za każdym razem, gdy komputer zostanie ponownie uruchomiony, podejmowana jest próba przywrócenia połączenia z ulubionymi celami iSCSI.

## <a name="errors-during-configuration"></a>Błędy podczas konfiguracji

Jeśli konfiguracja protokołu CHAP jest niepoprawna, prawdopodobnie zostanie wyświetlony komunikat o **błędzie błędu uwierzytelniania.**

## <a name="verification-of-chap-configuration"></a>Weryfikacja konfiguracji protokołu CHAP

Można sprawdzić, czy protokół CHAP jest używany, wykonując następujące kroki.

#### <a name="to-verify-your-chap-configuration"></a>Aby zweryfikować konfigurację protokołu CHAP
1. Kliknij **pozycję Ulubione cele**.
2. Wybierz obiekt docelowy, dla którego włączono uwierzytelnianie.
3. Kliknij pozycję **Szczegóły**.
   
    ![Inicjator iSCSI właściwości ulubionych obiektów docelowych](./media/storsimple-configure-chap/IC740951.png)
4. W oknie dialogowym **Ulubione szczegóły docelowe** zanotuj wpis w polu **Uwierzytelnianie.** Jeśli konfiguracja zakończyła się pomyślnie, należy powiedzieć **CHAP**.
   
    ![Ulubione szczegóły celu](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

