---
title: Utwórz bilet pomocy technicznej lub etui dla serii StorSimple 8000
description: Dowiedz się, jak rejestrować żądanie pomocy technicznej i rozpocząć sesję pomocy technicznej na urządzeniu z serii StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254991"
---
# <a name="contact-microsoft-support"></a>Kontakt z pomocą techniczną firmy Microsoft

Menedżer urządzeń StorSimple udostępnia możliwość **rejestrowania nowego żądania pomocy technicznej** w bloku podsumowania usługi. Jeśli wystąpią jakiekolwiek problemy z rozwiązaniem StorSimple, możesz utworzyć żądanie usługi dla pomocy technicznej. W sesji online z inżynierem pomocy technicznej może być również konieczne rozpoczęcie sesji pomocy technicznej na urządzeniu StorSimple. W tym artykule otrzymasz:

* Jak utworzyć żądanie pomocy technicznej.
* Jak zarządzać cyklem życia żądania pomocy technicznej z poziomu portalu.
* Jak rozpocząć sesję pomocy technicznej w interfejsie programu Windows PowerShell urządzenia StorSimple.

Przed utworzeniem żądania pomocy technicznej należy zapoznać się z [łączami SLA obsługujących storsimple 8000 series i informacjami.](https://msdn.microsoft.com/library/mt433077.aspx)

## <a name="create-a-support-request"></a>Tworzenie żądania obsługi

W zależności od [planu pomocy technicznej](https://azure.microsoft.com/support/plans/)można utworzyć bilety pomocy technicznej dla problemu na urządzeniu StorSimple bezpośrednio z bloku podsumowania usługi StorSimple Device Manager. Wykonaj następujące kroki, aby utworzyć żądanie pomocy technicznej:

#### <a name="to-create-a-support-request"></a>Aby utworzyć żądanie pomocy technicznej

1. Przejdź do usługi Menedżer urządzeń StorSimple. W ustawieniach bloku podsumowania usługi przejdź do sekcji **POMOC + ROZWIĄZYWANIE PROBLEMÓW,** a następnie kliknij pozycję **Nowe żądanie pomocy technicznej**.
     
    ![Skontaktuj się z obsługą MS za pośrednictwem nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. W bloku **Nowe żądanie pomocy technicznej** wybierz pozycję **Podstawy**. W bloku **Podstawy** wykonaj następujące czynności:
   1. Z listy rozwijanej **Typ wydania** wybierz pozycję **Techniczne**.
   2. Bieżąca **subskrypcja,** typ **usługi** i **zasób** (usługa StorSimple Device Manager) są wybierane automatycznie. 
   3. Wybierz **plan pomocy technicznej** z listy rozwijanej, jeśli masz wiele planów skojarzonych z subskrypcją. Aby włączyć pomoc techniczną, potrzebny jest płatny plan pomocy technicznej.
   4. Kliknij przycisk **alej**.

       ![Skontaktuj się z obsługą MS za pośrednictwem nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. W bloku **Nowe żądanie pomocy technicznej** wybierz krok **2 Problem**. W bloku **Problem** wykonaj następujące czynności:
    
    1. Wybierz **ważność**.
    2. Określ, czy problem jest związany z urządzeniem, czy z usługą StorSimple Device Manager.
    3. Wybierz **kategorię** dla tego problemu i podaj więcej **szczegółów** dotyczących problemu.
    4. Podaj datę i godzinę rozpoczęcia problemu.
    5. W **obszarze Przekazywanie pliku**kliknij ikonę folderu, aby przejść do pakietu pomocy technicznej.
    6. Sprawdź **informacje diagnostyczne udostępniania**.
    7. Kliknij przycisk **alej**.

       ![Skontaktuj się z obsługą MS za pośrednictwem nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. W bloku **Nowe żądanie pomocy technicznej** kliknij krok **3 Informacje kontaktowe**. W bloku **Informacje kontaktowe** wykonaj następujące czynności:

   1. W **opcjach Kontakt**podaj preferowaną metodę kontaktu (telefon lub adres e-mail) i język. Czas odpowiedzi jest wybierany automatycznie na podstawie planu subskrypcji.
   2. W informacjach kontaktowych podaj swoje imię i nazwisko, adres e-mail, opcjonalny kontakt, kraj/region. Zaznacz pole wyboru **Zapisz zmiany kontaktu dla przyszłych żądań pomocy technicznej.**
   3. Kliknij przycisk **Utwórz**.
   
       ![Skontaktuj się z obsługą MS za pośrednictwem nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Pomoc techniczna firmy Microsoft będzie korzystać z tych informacji, aby uzyskać dalsze informacje, diagnozę i rozwiązanie problemu.
      Po przesłaniu wniosku, inżynier pomocy technicznej skontaktuje się z Tobą tak szybko, jak to możliwe, aby kontynuować twoją prośbę.

## <a name="manage-a-support-request"></a>Zarządzanie żądaniem pomocy technicznej

Po utworzeniu biletu pomocy technicznej możesz zarządzać jego cyklem życia z poziomu portalu.

#### <a name="to-manage-your-support-requests"></a>Aby zarządzać żądaniami pomocy technicznej

1. Aby przejść do strony pomocy i pomocy technicznej, przejdź do **sekcji Przeglądaj > Pomoc + pomoc + pomoc .**

    ![Zarządzanie żądaniami pomocy technicznej](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Tabelaryczny wykaz wszystkich żądań pomocy technicznej jest wyświetlany w **pomocy + support** bloku.

    ![Zarządzanie żądaniami pomocy technicznej](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Wybierz i kliknij żądanie pomocy technicznej. Można wyświetlić stan i szczegóły tego żądania. Kliknij **+ Nowa wiadomość,** jeśli chcesz wykonać następujące żądanie.

    ![Zarządzanie żądaniami pomocy technicznej](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Rozpoczynanie sesji pomocy technicznej w programie Windows PowerShell dla storsimple

Aby rozwiązać wszelkie problemy, które mogą wystąpić z urządzeniem StorSimple, należy nawiązać kontakt z zespołem pomocy technicznej firmy Microsoft. Pomoc techniczna firmy Microsoft może wymagać użycia sesji pomocy technicznej do zalogowania się do urządzenia.

Aby rozpocząć sesję pomocy technicznej, wykonaj następujące czynności:

#### <a name="to-start-a-support-session"></a>Aby rozpocząć sesję pomocy technicznej

1. Dostęp do urządzenia można uzyskać bezpośrednio za pomocą konsoli szeregowej lub za pośrednictwem sesji telnet z komputera zdalnego. Aby to zrobić, wykonaj czynności opisane w [aplikacji Użyj putty, aby połączyć się z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W otwartej sesji naciśnij klawisz **Enter,** aby uzyskać wiersz polecenia.
3. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnym dostępem**.
4. W wierszu polecenia wpisz następujące hasło:
   
    `Password1`
5. W wierszu polecenia wpisz następujące polecenie:
   
    `Enable-HcsSupportAccess`
6. Zaszyfrowany ciąg zostanie przedstawiony. Skopiuj ten ciąg do edytora tekstu, takiego jak Notatnik.
7. Zapisz ten ciąg i wyślij go w wiadomości e-mail do pomocy technicznej firmy Microsoft.

> [!IMPORTANT]
> Dostęp do pomocy technicznej `Disable-HcsSupportAccess`można wyłączyć, uruchamiając program . Urządzenie StorSimple będzie również próbować wyłączyć dostęp do pomocy technicznej 8 godzin po zainicjowaniu sesji. Jest najlepszym rozwiązaniem, aby zmienić poświadczenia urządzenia StorSimple po zainicjowaniu sesji pomocy technicznej.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [diagnozować i rozwiązywać problemy związane z urządzeniem z serii StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)
