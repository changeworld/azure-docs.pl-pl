---
title: Zarządzanie kontrolerami urządzenia serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zatrzymać, uruchom ponownie, zamknij lub zresetować kontrolerów urządzenia StorSimple.
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
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875116"
---
# <a name="manage-your-storsimple-device-controllers"></a>Zarządzanie kontrolerów urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano różne operacje, które można wykonać na kontrolerach urządzenia StorSimple. Kontrolery w urządzeniu StorSimple są kontrolery nadmiarowy (peer) w konfiguracji aktywny / pasywny. W danym momencie tylko jeden kontroler jest aktywna i przetwarza wszystkie operacje dysku i sieci. Inny kontroler jest w trybie pasywnym. W przypadku niepowodzenia aktywnego kontrolera pasywnym kontrolera automatycznie staje się aktywny.

Ten samouczek zawiera instrukcje krok po kroku, aby zarządzać kontrolery urządzeń przy użyciu:

* **Kontrolery** bloku dla urządzenia w usłudze Menedżer StorSimple urządzenia.
* Program Windows PowerShell dla StorSimple.

Zaleca się zarządzanie nimi kontrolerów urządzeń za pomocą usługi Menedżera urządzeń StorSimple. Jeśli akcję można wykonać tylko przy użyciu programu Windows PowerShell dla urządzenia StorSimple, samouczka sprawia, że je zapisać.

Po przeczytaniu tego samouczka, będą mieć możliwość:

* Ponowne uruchamianie lub zamykanie kontrolera urządzenia StorSimple
* Wyłącz urządzenia StorSimple
* Resetowanie do ustawień fabrycznych urządzenia StorSimple

## <a name="restart-or-shut-down-a-single-controller"></a>Ponowne uruchamianie lub zamykanie jednego kontrolera
Zamknięcie lub ponowne uruchomienie kontrolera nie jest wymagana w ramach normalnego działania. Operacji zamknięcia na kontrolerze jednego urządzenia są często używane tylko w przypadkach, w których wymagana jest wymiana składnik sprzętowy urządzenia nie powiodło się. Ponowne uruchomienie kontrolera może być konieczne również w sytuacji, w których wpływ na wydajność w nadmiernego wykorzystania pamięci lub nieprawidłowo kontrolera. Może również musisz ponownie uruchomić kontrolera po wymianie pomyślne kontrolera, jeśli chcesz włączyć i zastąpionego kontroler testów.

Ponowne uruchamianie urządzenia nie jest znaczący wpływ na inicjatory połączone, przy założeniu, że jest dostępny kontroler pasywnych. Jeśli kontroler pasywnej nie jest dostępna lub włączenia wyłączone, a następnie ponowne uruchomienie kontrolera active może spowodować przerwy w świadczeniu usługi i przestoje.

> [!IMPORTANT]
> * **Działającego kontrolera powinien nigdy nie zostać usunięty, ponieważ skutkowałoby to utratą nadmiarowości i zwiększone ryzyko przestoju.**
> * Poniższa procedura ma zastosowanie tylko do urządzenia fizycznego StorSimple. Aby dowiedzieć się, jak uruchamianie, zatrzymywanie i ponowne uruchomienie urządzenia StorSimple w chmurze, zobacz [pracować z urządzenia chmury](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Można ponownie uruchomić lub zamknąć kontrolera jednego urządzenia za pośrednictwem portalu Azure usługi Menedżer StorSimple urządzenia lub środowiska Windows PowerShell dla urządzenia StorSimple.

Aby zarządzać kontrolerów urządzenia z portalu Azure, wykonaj następujące kroki.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Aby ponownie uruchomić lub zamknąć kontrolera w portalu Azure
1. W usłudze Menedżer StorSimple urządzeń, przejdź do **urządzeń**. Wybierz urządzenie z listy urządzeń. 

    ![Wybierz urządzenie](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Przejdź do **Ustawienia > kontrolerów**.
   
    ![Sprawdź, czy kontrolery urządzeń StorSimple są w dobrej kondycji](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. W **kontrolerów** bloku, sprawdź, czy stan kontrolerów na urządzeniu jest **dobra kondycja**. Wybierz kontroler, kliknij prawym przyciskiem myszy, a następnie wybierz **Uruchom ponownie** lub **zamknąć**.

    ![Uruchom ponownie lub zamknąć kontrolery urządzeń StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Zadanie jest tworzone na ponowne uruchamianie lub zamykanie kontrolera i prezentowany dotyczy ostrzeżenia, jeśli istnieją. Aby monitorować ponownego uruchomienia lub zamknięcia, przejdź do **usługi > Dzienniki aktywności** , a następnie Filtruj według parametry specyficzne dla usługi. Jeśli kontroler został zamknięty, będzie konieczne na naciśnięcie przycisku zasilania, aby włączyć na kontrolerze, aby włączyć tę funkcję.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Aby ponownie uruchomić lub zamknąć kontrolera w programie Windows PowerShell dla urządzenia StorSimple
Wykonaj poniższe kroki, aby zamknąć lub ponownie uruchomić pojedynczy kontroler na urządzeniu StorSimple z programu Windows PowerShell dla urządzenia StorSimple.

1. Dostęp do urządzenia za pośrednictwem konsoli szeregowej lub sesji telnet z komputera zdalnego. Aby połączyć się kontrolera 0 lub 1 kontrolera, postępuj zgodnie z instrukcjami [przy użyciu programu PuTTY można nawiązać połączenia z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
3. W komunikacie transparentu Zanotuj kontrolera są podłączone do (kontrolera 0 lub 1 kontrolera) i czy jest ona aktywnych lub pasywnych kontrolera (wstrzymania).
   
   * Można zamknąć jednego kontrolera, w wierszu polecenia wpisz:
     
       `Stop-HcsController`
     
       Zamyka kontrolera, który jest połączony. Zatrzymanie aktywnym kontrolerze, następnie urządzenia awaryjnej pasywnym kontrolera.

   * Aby ponownie uruchomić kontrolera, w wierszu polecenia wpisz:
     
       `Restart-HcsController`
     
       Spowoduje to ponowne uruchomienie kontrolera, który jest połączony. Po ponownym uruchomieniu kontrolera active, nie za pośrednictwem pasywnym kontrolera przed ponownym uruchomieniem.

## <a name="shut-down-a-storsimple-device"></a>Wyłącz urządzenia StorSimple

W tej sekcji wyjaśniono, jak zamknąć uruchomionej lub urządzenie StorSimple nie powiodło się z komputerem zdalnym. Urządzenie jest wyłączone, po kontrolerów urządzeń są zamknięte. Zamknięcia urządzenia odbywa się, gdy urządzenie jest przenoszony fizycznie lub pochodzi z usługi.

> [!IMPORTANT]
> Przed zamknięciem urządzenia, Sprawdź kondycję składniki urządzenia. Przejdź do urządzenia, a następnie kliknij przycisk **Ustawienia > kondycji sprzętu**. W **sprzętu i stan kondycji** bloku, sprawdź, czy stan LED wszystkich składników jest zielony. Dobra urządzenie ma stan zielony. Jeśli urządzenie jest wyłączone w celu zastąpienia nieprawidłowo działający składnik, pojawi się nie powiodło się (czerwony) lub obniżeniem statusu (żółty) dla odpowiednich składników.


#### <a name="to-shut-down-a-storsimple-device"></a>Aby wyłączyć urządzenia StorSimple

1. Użyj [ponowne uruchamianie lub zamykanie kontrolera](#restart-or-shut-down-a-single-controller) procedurę, aby zidentyfikować i zamknij pasywnym kontroler na urządzeniu. Tę operację można wykonać w portalu Azure lub w programie Windows PowerShell dla urządzenia StorSimple.
2. Powtórz powyższy krok można zamknąć active kontrolera.
3. Teraz musisz sprawdzić na płaszczyźnie wstecz urządzenia. Po dwóch kontrolerów są całkowicie zamknięty, stan LED na obu kontrolerów powinny migający czerwony. Jeśli chcesz całkowicie wyłączyć urządzenia w tej chwili Przerzuć przełączniki zasilania na zasilania i chłodzenia modułów (PCMs) do położenia OFF. Wyłączyć to urządzenie.

## <a name="reset-the-device-to-factory-default-settings"></a>Zresetowanie urządzenia do domyślnych ustawień fabrycznych

> [!IMPORTANT]
> Jeśli potrzebujesz zresetować urządzenie do domyślnych ustawień fabrycznych, skontaktuj się z Microsoft Support. W procedurze opisanej poniżej należy używać tylko w połączeniu z Microsoft Support.

Ta procedura opisuje sposób zresetowania urządzenia Microsoft Azure StorSimple domyślne ustawienia fabryczne przy użyciu programu Windows PowerShell dla urządzenia StorSimple.
Zresetowanie urządzenia usuwa wszystkie dane i ustawienia z całego klastra domyślnie.

Wykonaj poniższe kroki, aby zresetować do domyślnych ustawień fabrycznych urządzenia Microsoft Azure StorSimple:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Aby zresetować urządzenie do domyślnych ustawień w programie Windows PowerShell dla urządzenia StorSimple
1. Dostęp do urządzenia za pomocą jego konsoli szeregowej. Sprawdź komunikat transparentu, aby upewnić się, że masz połączenie **Active** kontrolera.
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
3. W wierszu polecenia wpisz następujące polecenie, aby zresetować całego klastra, usuwając wszystkie ustawienia danych, metadane i kontrolera:
   
    `Reset-HcsFactoryDefault`
   
    Aby zamiast tego należy zresetować jednego kontrolera, należy użyć [HcsFactoryDefault resetowania](http://technet.microsoft.com/library/dn688132.aspx) polecenia cmdlet z `-scope` parametru.)
   
    System zostanie ponownie uruchomiony wiele razy. Podczas resetowania zostało zakończone pomyślnie, otrzymasz powiadomienie. 45 do 60 minut na urządzeniu 8100 i 60 90 minut 8600 zakończyć ten proces może potrwać w zależności od modelu systemu.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Pytania i odpowiedzi dotyczące zarządzania kontrolerami urządzenia
W tej sekcji możemy utworzono podsumowanie niektóre często zadawane pytania dotyczące zarządzania kontrolery urządzeń StorSimple.

**PYTANIE** Co się stanie w przypadku obu kontrolerów w urządzeniu są w dobrej kondycji i został włączony na i I ponowne uruchamianie lub zamykanie aktywnym kontrolerze?

**ODPOWIEDŹ** Jeśli kontrolery na urządzeniu są w dobrej kondycji i został włączony, zostanie wyświetlony monit o potwierdzenie. Można wybrać opcję:

* **Uruchom ponownie kontroler active** — zostanie wyświetlona informacja, że ponowne uruchomienie kontrolera usługi active spowodowane urządzenia do trybu failover pasywnym kontrolera. Ponowne uruchomienie kontrolera.
* **Zamknij aktywny kontroler** — zostanie wyświetlona informacja, że zamykanie active kontrolera powoduje Przestój. Należy również naciśnij przycisk zasilania na urządzeniu, aby włączyć na kontrolerze.

**PYTANIE** Co się stanie, jeśli pasywnym kontrolera w urządzeniu jest niedostępny lub włączenia wyłączone i I ponowne uruchamianie lub zamykanie aktywnym kontrolerze?

**ODPOWIEDŹ** Jeśli kontroler pasywnym na urządzeniu jest niedostępna albo jest wyłączona funkcja wyłączona i użytkownik wybierze:

* **Uruchom ponownie kontroler active** — otrzymasz powiadomienie, że kontynuowanie tej operacji spowoduje tymczasowe przerw w działaniu usługi, i zostanie wyświetlony monit o potwierdzenie.
* **Zamknij aktywny kontroler** — otrzymasz powiadomienie, że kontynuowanie tej operacji skutkuje przestoju. Należy również naciśnij przycisk zasilania na jeden lub oba kontrolerów, aby włączyć w urządzeniu. Zostanie wyświetlony monit o potwierdzenie.

**PYTANIE** Gdy nie kontrolera ponownego uruchomienia lub zamknięcia nie powiedzie się postęp?

**ODPOWIEDŹ** Ponowne uruchamianie lub zamykanie kontroler może się niepowodzeniem, jeśli:

* Trwa aktualizacja urządzenia.
* Ponowne uruchomienie kontrolera jest już w toku.
* Zamknięcie kontrolera jest już w toku.

**PYTANIE** Jak może Ci zorientować się, jeśli kontroler został uruchomiony ponownie lub zamknąć?

**ODPOWIEDŹ** Można sprawdzić stan kontrolera w bloku kontrolera. Stan kontrolera wskazać, czy kontroler jest w trakcie ponownego uruchamiania lub zamykania. Ponadto **alerty** bloku zawierać alert informacyjny, jeśli kontroler zostanie ponownie uruchomiony lub zamknięty. Operacji zamknięcia i ponownego uruchomienia kontrolera są także rejestrowane w dziennikach acitivity. Aby uzyskać więcej informacji o dziennikach acitivity, przejdź do [wyświetlać dzienniki aktywności](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**PYTANIE** Istnieje już wpływu na we/wy wyniku kontrolera trybu failover?

**ODPOWIEDŹ** Połączenia TCP między inicjatorów i aktywnym kontrolerze zostanie zresetowany w wyniku kontrolera pracy awaryjnej, ale będzie można ponownie ustanowić podczas operacji zakłada pasywnym kontrolera. W trakcie tej operacji może być tymczasowe (mniej niż 30 sekund) wstrzymaniu operacje wejścia/wyjścia między inicjatorów i urządzenia.

**PYTANIE** Jak zwraca Moje kontrolera do obsługi po został zamknięty i usunięty?

**ODPOWIEDŹ** Aby przywrócić kontrolera usługi, należy wstawić go do obudowy zgodnie z opisem w [zastąpić modułu kontroler na urządzeniu StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Następne kroki
* Jeśli wystąpią problemy z kontrolerów urządzenia StorSimple, których nie można rozpoznać przy użyciu procedur zamieszczonych w tym samouczku [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej o korzystaniu z usługi Menedżera urządzeń StorSimple, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

