---
title: Tworzenie biletu pomocy technicznej lub w przypadku serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rejestrować żądania pomocy technicznej i rozpocząć sesję pomocy technicznej na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: e0df86e40d0dc1092ad7ff04f01bbc3e5e3d1c4e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743056"
---
# <a name="contact-microsoft-support"></a>Kontakt z pomocą techniczną firmy Microsoft

Menedżer urządzeń StorSimple oferuje możliwość **dziennika nowe żądanie pomocy technicznej** wewnątrz bloku podsumowania usługi. Jeśli wystąpią problemy z rozwiązania StorSimple można utworzyć żądania obsługi technicznej. W ramach sesji online ze specjalistą pomocy technicznej może również należy rozpocząć sesję pomocy technicznej na urządzeniu StorSimple. W tym artykule przedstawiono:

* Jak utworzyć żądanie pomocy technicznej.
* Jak zarządzać cykl żądania obsługi z w portalu.
* Jak rozpocząć sesję pomocy technicznej w interfejsie programu Windows PowerShell urządzenia StorSimple.

Przegląd [StorSimple 8000 serii Obsługa SLA oraz informacje](https://msdn.microsoft.com/library/mt433077.aspx) przed utworzeniem żądania pomocy technicznej.

## <a name="create-a-support-request"></a>Utwórz żądanie obsługi

W zależności od Twojego [plan pomocy technicznej](https://azure.microsoft.com/support/plans/), można tworzyć bilety pomocy technicznej problemu, na urządzeniu StorSimple bezpośrednio z bloku podsumowania usługi Menedżer StorSimple urządzenia. Wykonaj poniższe kroki, aby utworzyć żądanie pomocy technicznej:

#### <a name="to-create-a-support-request"></a>Aby utworzyć żądanie obsługi

1. Przejdź do usługi Menedżer urządzeń StorSimple. W bloku Podsumowanie ustawień usługi, przejdź do **pomocy technicznej i rozwiązywania problemów** sekcji, a następnie kliknij przycisk **nowy obsługuje żądania**.
     
    ![MS skontaktuj się z pomocą techniczną przez nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. W **nowy obsługuje żądania** bloku, wybierz opcję **podstawy**. W **podstawy** blok, wykonaj następujące czynności:
   1. Z **wydawania typu** listy rozwijanej wybierz **techniczne**.
   2. Bieżący **subskrypcji**, **usługi** typu i **zasobów** (usługa Menedżera urządzeń StorSimple) są automatycznie wybrana. 
   3. Wybierz **plan pomocy technicznej** z listy rozwijanej, jeśli masz wiele planów skojarzonych z Twoją subskrypcją. Należy plan płatnej pomocy technicznej, aby włączyć pomocy technicznej.
   4. Kliknij przycisk **Dalej**.

       ![MS skontaktuj się z pomocą techniczną przez nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. W **nowy obsługuje żądania** bloku, wybierz opcję **krok 2 Problem**. W **Problem** blok, wykonaj następujące czynności:
    
    1. Wybierz **ważność**.
    2. Określ, czy problem jest związana z urządzenia lub usługi Menedżer StorSimple urządzenia.
    3. Wybierz **kategorii** w tym problemie i podaj więcej **szczegóły** o problemie.
    4. Podaj datę i godzinę dla problemu.
    5. W **przekazywania pliku**, kliknij ikonę folderu, aby przejść do pakietu programu obsługi.
    6. Sprawdź **udostępnianie informacji diagnostycznych**.
    7. Kliknij przycisk **Dalej**.

       ![MS skontaktuj się z pomocą techniczną przez nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. W **nowy obsługuje żądania** bloku, kliknij przycisk **informacji skontaktuj się z kroku 3**. W **informacje kontaktowe** blok, wykonaj następujące czynności:

    1. W **opcje kontaktu**, podaj preferowaną metodę kontaktu (telefonu lub poczty e-mail) i języka. Czas odpowiedzi jest automatycznie wybierany w oparciu o plan subskrypcji.
    2. Informacje kontaktowe podać nazwę, poczty e-mail, opcjonalne kontaktu, kraju. Wybierz **Zapisz zmiany dotyczące kontaktu dla przyszłych żądań obsługi** pole wyboru.
    3. Kliknij przycisk **Utwórz**.
   
        ![MS skontaktuj się z pomocą techniczną przez nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Microsoft Support użyje tych informacji do dotrzeć do Ciebie, aby uzyskać więcej informacji, diagnozowanie i rozwiązywanie.
Gdy prześlesz żądanie z pracownikiem pomocy technicznej skontaktuje się z Tobą tak szybko, jak to możliwe, aby przejść do żądania.

## <a name="manage-a-support-request"></a>Zarządzanie żądania obsługi

Po utworzeniu biletu pomocy technicznej możesz zarządzać jego cyklem życia z poziomu portalu.

#### <a name="to-manage-your-support-requests"></a>Do zarządzania własnych żądań pomocy technicznej

1. Aby przejść do strony pomocy i obsługi technicznej, przejdź do **Przeglądaj > Pomoc i obsługa techniczna**.

    ![Zarządzaj żądaniami obsługi](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Tabelaryczny spis wszystkich żądań pomocy technicznej jest wyświetlany w **Pomoc i obsługa techniczna** bloku.

    ![Zarządzaj żądaniami obsługi](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Wybierz, a następnie kliknij przycisk żądania pomocy technicznej. Można wyświetlić stan i szczegóły dla tego żądania. Kliknij przycisk **+ nowy komunikat** aby kolejnych czynności związanych z tym żądaniem.

    ![Zarządzaj żądaniami obsługi](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Rozpocznij sesję pomocy technicznej w programie Windows PowerShell dla urządzenia StorSimple

Aby rozwiązać problemy, które mogą pojawić się z urządzeniem StorSimple, konieczne będzie współpracować z zespołu Support firmy Microsoft. Microsoft Support konieczne może być zastosowanie sesji pomocy technicznej do logowania się do urządzenia.

Wykonaj poniższe kroki, aby rozpocząć sesję pomocy technicznej:

#### <a name="to-start-a-support-session"></a>Aby rozpocząć sesję pomocy technicznej

1. Dostęp do urządzenia, za pomocą konsoli szeregowej lub za pośrednictwem sesji telnet z komputera zdalnego. Aby to zrobić, wykonaj kroki opisane w [przy użyciu programu PuTTY można nawiązać połączenia z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W sesji, który zostanie otwarty, naciśnij klawisz **Enter** klawisz, aby wyświetlić wiersz polecenia.
3. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
4. W wierszu polecenia wpisz następujące hasło:
   
    `Password1`
5. W wierszu polecenia wpisz następujące polecenie:
   
    `Enable-HcsSupportAccess`
6. Do użytkownika zostanie wyświetlone zaszyfrowanego ciągu. Skopiuj ten ciąg do edytora tekstu, takiego jak Notatnik.
7. Zapisz ten ciąg i wysłać go w wiadomości e-mail do firmy Microsoft Support.

> [!IMPORTANT]
> Dostęp do pomocy technicznej można wyłączyć, uruchamiając `Disable-HcsSupportAccess`. Urządzenia StorSimple spróbuje również wyłączyć dostęp do pomocy technicznej 8 godzin po sesji zostało zainicjowane. Jest najlepszym rozwiązaniem jest zmiana poświadczeń urządzenia StorSimple po zainicjowaniu sesji pomocy technicznej.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zdiagnozować i rozwiązać problemy związane z serii StorSimple 8000 urządzenia](storsimple-8000-troubleshoot-deployment.md)
