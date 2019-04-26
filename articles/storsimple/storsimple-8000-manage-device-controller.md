---
title: Zarządzaj kontrolerami urządzenia serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zatrzymać, ponowne uruchomienie, zamknięta lub zresetować Twoje kontrolery urządzeń StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 5e461f340e1c58f64c6d645a1e47cfd811bc4de5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505998"
---
# <a name="manage-your-storsimple-device-controllers"></a>Zarządzanie przez kontrolery urządzeń StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano różne operacje, które mogą być wykonywane na kontrolerach urządzeń StorSimple. Kontrolerów w urządzeniu StorSimple są kontrolery nadmiarowe (peer) w konfiguracji aktywne pasywne. W danym momencie tylko jeden kontroler jest aktywny i trwa jego przetwarzanie wszystkich operacji dysku i sieci. Drugi kontroler jest w trybie pasywnym. W przypadku niepowodzenia aktywnego kontrolera kontrolera pasywnego automatycznie stanie się aktywny.

Ten samouczek zawiera instrukcje krok po kroku, aby zarządzać kontrolery urządzeń za pomocą:

* **Kontrolery** bloku dla urządzenia w usłudze Menedżer urządzeń StorSimple.
* Program Windows PowerShell dla usługi StorSimple.

Zaleca się, że zarządzać kontrolery urządzeń za pomocą usługi Menedżer urządzeń StorSimple. Jeśli akcję można wykonać tylko przy użyciu programu Windows PowerShell dla usługi StorSimple, samouczek sprawia, że je zapisać.

Po przeczytaniu tego samouczka, będzie mieć możliwość:

* Ponowne uruchamianie lub zamykanie kontrolera urządzenia StorSimple
* Wyłączyć urządzenie StorSimple
* Resetowanie urządzenia StorSimple do ustawień fabrycznych

## <a name="restart-or-shut-down-a-single-controller"></a>Ponowne uruchamianie lub zamykanie jednego kontrolera
Zamknięcie lub ponowne uruchomienie kontrolera nie jest wymagane jako część normalnego działania. Operacji zamknięcia kontrolera pojedynczego urządzenia są powszechne tylko w przypadkach, w których wymagana jest wymiana składnik sprzętowy urządzenia nie powiodło się. Ponowne uruchomienie kontrolera będzie należało również w sytuacji, w których wpływ na wydajność w nadmiernego wykorzystania pamięci lub nieprawidłowe działanie kontrolera. Również konieczne może być ponowne uruchomienie kontrolera, po zastąpienia pomyślne kontrolera, jeśli chcesz włączyć i zastąpione kontrolera testów.

Ponowne uruchamianie urządzenia nie jest znaczący wpływ na połączonych inicjatory, przy założeniu, że jest dostępny kontroler pasywny. Jeśli kontroler pasywny nie jest dostępna lub wyłączyć wyłączone, a następnie ponowne uruchomienie aktywny kontroler może spowodować zakłócenia w działaniu usługi i przestoje.

> [!IMPORTANT]
> * **Uruchamianie kontrolera powinno nigdy nie można w fizycznie usunąć, ponieważ spowodowałoby utratę nadmiarowości i zwiększone ryzyko przestoju.**
> * Poniższa procedura dotyczy tylko urządzenia fizycznego StorSimple. Aby dowiedzieć się, jak uruchomić, zatrzymać i ponownie uruchomić urządzenie StorSimple w chmurze, zobacz [pracy z urządzeniem w chmurze](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Można ponownie uruchomić lub zamknąć kontroler pojedynczego urządzenia w portalu Azure usługi Menedżer urządzeń StorSimple lub środowiska Windows PowerShell dla usługi StorSimple.

Aby zarządzać Twoje kontrolery urządzeń w witrynie Azure portal, wykonaj następujące czynności.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Aby ponownie uruchomić lub zamknąć kontroler w witrynie Azure portal
1. W usłudze Menedżer urządzeń StorSimple przejdź do **urządzeń**. Wybierz urządzenie z listy urządzeń. 

    ![Wybierz urządzenie](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Przejdź do **Ustawienia > kontrolery**.
   
    ![Sprawdź, czy kontrolery urządzeń StorSimple są w dobrej kondycji](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. W **kontrolerów** bloku, sprawdź, czy stan kontrolera na urządzeniu z systemem **dobra kondycja**. Wybierz kontroler, kliknij prawym przyciskiem myszy, a następnie wybierz **ponowne uruchomienie** lub **Zamknij**.

    ![Uruchom ponownie lub zamknij kontrolery urządzeń StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Zostanie utworzone zadanie, aby ponownie uruchomić lub zamknąć kontroler i zostanie wyświetlony dotyczy ostrzeżenia, jeśli istnieje. Aby monitorować, ponowne uruchomienie lub zamknięcie, przejdź do **usługi > dzienników aktywności** a następnie przeprowadź filtrowanie według parametrów specyficzne dla usługi. Jeśli kontroler został zamknięty, będzie potrzebne na naciśnięcie przycisku zasilania, aby włączyć na kontrolerze, aby włączyć tę funkcję.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Aby ponownie uruchomić lub zamknąć kontroler w programie Windows PowerShell dla usługi StorSimple
Wykonaj poniższe kroki, aby zamknięcie lub ponowne uruchomienie jednego kontrolera na urządzeniu StorSimple z poziomu programu Windows PowerShell dla usługi StorSimple.

1. Dostęp do urządzenia za pośrednictwem konsoli szeregowej lub sesji telnet z komputera zdalnego. Aby połączyć z kontrolera 0 i Controller 1, wykonaj kroki w [przy użyciu programu PuTTY, aby nawiązać połączenie z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
3. W komunikacie transparentu Zanotuj kontrolera nawiązano połączenie (kontrolera 0 lub 1 kontrolera) i czy jest ona aktywna lub kontrolerze pasywnym (wstrzymania).
   
   * Aby zamknąć jednego kontrolera, w wierszu polecenia wpisz:
     
       `Stop-HcsController`
     
       Zamyka kontrolera, która jest podłączona. Jeśli zatrzymasz aktywny kontroler, następnie urządzenia nie powiedzie się za pośrednictwem na kontrolerze pasywnym.

   * Aby ponownie uruchomić kontroler, w wierszu polecenia wpisz polecenie:
     
       `Restart-HcsController`
     
       Spowoduje to ponowne uruchomienie kontrolera, która jest podłączona. Jeśli ponownie aktywny kontroler nie za pośrednictwem kontrolera pasywnego przed ponownym uruchomieniem.

## <a name="shut-down-a-storsimple-device"></a>Wyłączyć urządzenie StorSimple

W tej sekcji wyjaśniono, jak zamknąć bieżących lub zakończonych niepowodzeniem urządzenia StorSimple z komputera zdalnego. Urządzenie jest wyłączone, po oba kontrolery urządzeń są zamykane. Zamknięcie urządzenia jest wykonywane, gdy urządzenie jest przenoszony fizycznie lub jest wykluczana z usługi.

> [!IMPORTANT]
> Przed zamknięciem urządzenia należy sprawdzić kondycję składników urządzenia. Przejdź do urządzenia, a następnie kliknij przycisk **Ustawienia > kondycja sprzętu**. W **stan i kondycja sprzętu** bloku, sprawdź, czy stan diody LED wszystkich składników jest zielony. Dobra urządzenie ma stan zielony. Jeśli urządzenie jest wyłączone w celu zastąpienia nieprawidłowo działający składnik, wyświetlona zostanie zakończone niepowodzeniem (czerwony) lub uszkodzenia (żółty) dla odpowiednich składników.


#### <a name="to-shut-down-a-storsimple-device"></a>Aby wyłączyć urządzenie StorSimple

1. Użyj [ponowne uruchamianie lub zamykanie kontrolera](#restart-or-shut-down-a-single-controller) procedurę, aby zidentyfikować i zamknij kontrolera pasywnego na urządzeniu. Tę operację mogą wykonać w witrynie Azure portal lub w programie Windows PowerShell dla usługi StorSimple.
2. Powtórz powyższy krok, aby zamknąć aktywny kontroler.
3. Należy teraz sprawdźmy płaszczyzny wstecz urządzenia. Po dwa kontrolery są całkowicie zamknięty, stan diod LED na obu kontrolerach powinien migające czerwony. Jeśli potrzebujesz całkowicie wyłączyć urządzenia w tej chwili Przerzucanie wyłączniki moc zasilania i chłodzenia modułów (PCMs) do pozycji wył. Należy wyłączyć się od urządzenia.

## <a name="reset-the-device-to-factory-default-settings"></a>Resetowanie urządzenia do domyślnych ustawień fabrycznych

> [!IMPORTANT]
> Jeśli zajdzie potrzeba zresetowania urządzenia do domyślnych ustawień fabrycznych, skontaktuj się z Microsoft Support. W procedurze opisanej poniżej powinna służyć tylko w połączeniu z Microsoft Support.

Ta procedura opisuje sposób zresetowania urządzenia z systemem Microsoft Azure StorSimple do domyślnych ustawień fabrycznych przy użyciu programu Windows PowerShell dla usługi StorSimple.
Zresetowanie urządzenia usuwa wszystkie dane i ustawienia z całego klastra, domyślnie.

Wykonaj poniższe kroki, aby zresetowania urządzenia z systemem Microsoft Azure StorSimple do domyślnych ustawień fabrycznych:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Aby zresetować urządzenie do domyślnych ustawień w programie Windows PowerShell dla usługi StorSimple
1. Dostęp do urządzenia za pomocą jego konsoli szeregowej. Sprawdź komunikat transparentu, aby upewnić się, że nawiązano **Active** kontrolera.
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
3. W wierszu polecenia wpisz następujące polecenie, aby zresetować cały klaster, usuwając wszystkie ustawienia danych, metadane i kontrolera:
   
    `Reset-HcsFactoryDefault`
   
    Aby zresetować zamiast jednego kontrolera, należy użyć [HcsFactoryDefault resetowania](https://technet.microsoft.com/library/dn688132.aspx) polecenia cmdlet z `-scope` parametru.)
   
    System zostanie ponownie uruchomiony wielokrotnie. Użytkownik zostanie powiadomiony, gdy resetowanie zostało pomyślnie zakończone. W zależności od modelu systemu może potrwać 45 do 60 minut dla urządzenia 8100 i 8600 zakończyć ten proces 60 – 90 minut.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Pytania i odpowiedzi na pytania dotyczące zarządzania kontrolery urządzeń
W tej sekcji, firma Microsoft utworzono podsumowanie niektóre często zadawane pytania dotyczące zarządzania kontrolery urządzeń StorSimple.

**PYTANIE** Co się stanie, jeśli oba kontrolery Moje urządzenia są w dobrej kondycji i wyłączyć na i I ponownie uruchomić lub zamknąć aktywny kontroler?

**ODPOWIEDŹ** Jeśli oba kontrolery na twoim urządzeniu są w dobrej kondycji i wyłączyć, pojawi się monit o potwierdzenie. Można wybrać opcję:

* **Ponownie aktywny kontroler** — otrzymasz powiadomienie, że ponowne uruchomienie aktywny kontroler leży urządzenia do trybu failover kontrolera pasywnego. Ponowne uruchomienie kontrolera.
* **Zamknij aktywny kontroler** — otrzymasz powiadomienie, że zamykanie aktywny kontroler powoduje przestoje. Należy również naciśnij przycisk zasilania na urządzeniu, aby włączyć na kontrolerze.

**PYTANIE** Co się stanie, jeśli kontrolera pasywnego na Moje urządzenie jest niedostępne lub wyłączyć wyłączone i I ponownie uruchomić lub zamknąć aktywny kontroler?

**ODPOWIEDŹ** Jeśli kontrolera pasywnego na urządzeniu z systemem niedostępny lub wyłączyć wyłączone i chcesz:

* **Ponownie aktywny kontroler** — otrzymasz powiadomienie, że kontynuowanie tej operacji spowoduje tymczasowe przerw w działaniu usługi i pojawi się monit o potwierdzenie.
* **Zamknij aktywny kontroler** — otrzymasz powiadomienie, że kontynuowanie tej operacji skutkuje przestojów. Należy również naciśnij przycisk zasilania na jednej lub obu kontrolerów, aby włączyć urządzenie. Zostanie wyświetlony monit o potwierdzenie.

**PYTANIE** Gdy jest zamknięcie lub ponowne uruchomienie kontrolera nie powiedzie się kontynuować?

**ODPOWIEDŹ** Ponowne uruchamianie lub zamykanie kontroler może się niepowodzeniem, jeśli:

* Aktualizacja urządzenia jest w toku.
* Ponowne uruchomienie kontrolera jest już w toku.
* Zamknij kontroler jest już w toku.

**PYTANIE** Jak można ustalenie przypadku kontrolera został ponownie uruchomiony lub zamknięty?

**ODPOWIEDŹ** Można sprawdzić stan kontrolera w bloku kontrolera. Stan kontrolera będą wskazywać, czy kontroler jest w trakcie ponownego uruchamiania lub zamykania. Ponadto **alerty** bloku zawierać alerty informacyjne, jeśli kontroler zostanie ponownie uruchomiony lub zamknąć. Operacji zamknięcia i ponownego uruchomienia kontrolera są także rejestrowane w dziennikach aktywności. Aby uzyskać więcej informacji na temat dzienników aktywności, przejdź do [wyświetlanie dzienników aktywności](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**PYTANIE** Czy istnieje żadnego wpływu na operacje We/Wy w wyniku praca awaryjna kontrolera działanie?

**ODPOWIEDŹ** Połączenia TCP między inicjatory i aktywny kontroler zostanie zresetowany w wyniku praca awaryjna kontrolera, ale będzie można ponownie ustanowić podczas operacji przyjęto założenie, kontrolera pasywnego. W trakcie tej operacji może być Wstrzymaj tymczasowych (mniej niż 30 sekund), w działaniu operacji We/Wy między inicjatory i urządzenia.

**PYTANIE** Jak zwrócić Mój kontroler do obsługi po ukończeniu zamknij i usunąć?

**ODPOWIEDŹ** Aby przywrócić usługę kontrolera, należy wstawić jej do obudowy zgodnie z opisem w [zastąpić modułu kontrolera na urządzeniu StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Kolejne kroki
* Jeśli napotkasz jakiekolwiek problemy przy użyciu usługi StorSimple kontrolerach urządzeń, których nie można rozwiązać, korzystając z procedur wymienionych w tym samouczku [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej o korzystaniu z usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

