---
title: Zarządzanie kontrolerami urządzeń storsimple serii 8000 | Dokumenty firmy Microsoft
description: Dowiedz się, jak zatrzymać, ponownie uruchomić, wyłączyć lub zresetować kontrolery urządzeń StorSimple.
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
ms.openlocfilehash: ce49dcaa06288ba9e7a4d232338c727064d59685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267770"
---
# <a name="manage-your-storsimple-device-controllers"></a>Zarządzanie kontrolerami urządzeń StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano różne operacje, które można wykonać na kontrolerach urządzeń StorSimple. Kontrolery w urządzeniu StorSimple są nadmiarowe (równorzędne) kontrolery w konfiguracji aktywny-pasywny. W danym momencie tylko jeden kontroler jest aktywny i przetwarza wszystkie operacje dysku i sieci. Drugi kontroler jest w trybie pasywnym. Jeśli aktywny kontroler ulegnie awarii, kontroler pasywny automatycznie staje się aktywny.

Ten samouczek zawiera instrukcje krok po kroku do zarządzania kontrolerami urządzeń przy użyciu:

* **Kontrolery** bloku dla urządzenia w storsimple device manager usługi.
* Windows PowerShell for StorSimple.

Zaleca się zarządzanie kontrolerami urządzeń za pośrednictwem usługi StorSimple Device Manager. Jeśli akcję można wykonać tylko przy użyciu programu Windows PowerShell dla StorSimple, samouczek zanotowuje ją.

Po przeczytaniu tego samouczka, będzie można:

* Ponowne uruchamianie lub zamykanie kontrolera urządzeń StorSimple
* Zamykanie urządzenia StorSimple
* Resetowanie ustawień fabrycznych urządzenia StorSimple

## <a name="restart-or-shut-down-a-single-controller"></a>Ponowne uruchamianie lub zamykanie pojedynczego kontrolera
Ponowne uruchomienie lub zamknięcie kontrolera nie jest wymagane w ramach normalnej pracy systemu. Operacje zamykania kontrolera pojedynczego urządzenia są typowe tylko w przypadkach, gdy nieudany składnik sprzętowy urządzenia wymaga wymiany. Ponowne uruchomienie kontrolera może być również wymagane w sytuacji, w której na wydajność ma wpływ nadmierne użycie pamięci lub nieprawidłowo działający kontroler. Może być również konieczne ponowne uruchomienie kontrolera po pomyślnym wymianie kontrolera, jeśli chcesz włączyć i przetestować wymieniony kontroler.

Ponowne uruchomienie urządzenia nie przerywa pracy podłączonych inicjatorów przy założeniu, że kontroler pasywny jest dostępny. Jeśli kontroler pasywny jest niedostępny lub wyłączony, ponowne uruchomienie aktywnego kontrolera może spowodować zakłócenia usługi i przestoje.

> [!IMPORTANT]
> * **Uruchomiony kontroler nigdy nie powinien być fizycznie usunięty, ponieważ spowodowałoby to utratę nadmiarowości i zwiększone ryzyko przestoju.**
> * Poniższa procedura dotyczy tylko urządzenia fizycznego StorSimple. Aby uzyskać informacje dotyczące uruchamiania, zatrzymywania i ponownego uruchamiania urządzenia StorSimple Cloud Appliance, zobacz [Praca z urządzeniem w chmurze](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Można ponownie uruchomić lub zamknąć kontroler pojedynczego urządzenia za pośrednictwem portalu Azure usługi StorSimple Device Manager lub programu Windows PowerShell for StorSimple.

Aby zarządzać kontrolerami urządzeń z witryny Azure portal, wykonaj następujące kroki.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Aby ponownie uruchomić lub zamknąć kontroler w witrynie Azure portal
1. W usłudze StorSimple Device Manager przejdź do **witryny Urządzenia**. Wybierz urządzenie z listy urządzeń. 

    ![Wybierz urządzenie](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Przejdź do **ustawień > kontrolerów**.
   
    ![Sprawdź, czy kontrolery urządzeń StorSimple są w dobrej kondycji](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. W bloku **Kontrolery** sprawdź, czy stan obu kontrolerów w urządzeniu jest **w dobrej kondycji**. Wybierz kontroler, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Uruchom ponownie** lub **Zamknij**.

    ![Wybieranie ponownego uruchamiania lub zamykania kontrolerów urządzeń StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Zadanie jest tworzone w celu ponownego uruchomienia lub zamknięcia kontrolera i są prezentowane odpowiednie ostrzeżenia, jeśli istnieje. Aby monitorować ponowne uruchomienie lub zamknięcie, przejdź do **dzienników aktywności usługi >,** a następnie filtruj według parametrów specyficznych dla usługi. Jeśli kontroler został wyłączony, należy nacisnąć przycisk zasilania, aby włączyć kontroler, aby go włączyć.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Aby ponownie uruchomić lub zamknąć kontroler w programie Windows PowerShell for StorSimple
Wykonaj następujące kroki, aby zamknąć lub ponownie uruchomić pojedynczy kontroler na urządzeniu StorSimple z programu Windows PowerShell for StorSimple.

1. Dostęp do urządzenia za pośrednictwem konsoli szeregowej lub sesji telnet z komputera zdalnego. Aby połączyć się z kontrolerem 0 lub kontrolerem 1, wykonaj czynności opisane w [aplikacji Użyj puTTY, aby połączyć się z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**.
3. W komunikacie banerowym zanotuj kontroler, z którym masz połączenie (Kontroler 0 lub Kontroler 1) i czy jest to kontroler aktywny, czy pasywny (rezerwowy).
   
   * Aby zamknąć pojedynczy kontroler, w wierszu polecenia wpisz:
     
       `Stop-HcsController`
     
       Spowoduje to wyłączenie kontrolera, z którego masz połączenie. Jeśli zatrzymasz aktywny kontroler, urządzenie przejdzie w tryb fail on the passive controller.

   * Aby ponownie uruchomić kontroler, w wierszu polecenia wpisz:
     
       `Restart-HcsController`
     
       Spowoduje to ponowne uruchomienie kontrolera, z którym nawiązano połączenie. Po ponownym uruchomieniu aktywnego kontrolera, to w trybie fail over do kontrolera pasywnego przed ponownym uruchomieniem.

## <a name="shut-down-a-storsimple-device"></a>Zamykanie urządzenia StorSimple

W tej sekcji wyjaśniono, jak zamknąć uruchomione lub nieudane urządzenie StorSimple z komputera zdalnego. Urządzenie jest wyłączone po wyłączeniu obu kontrolerów urządzeń. Zamknięcie urządzenia odbywa się, gdy urządzenie jest fizycznie przenoszone lub jest wyjęte z usługi.

> [!IMPORTANT]
> Przed wyłączeniem urządzenia sprawdź stan komponentów urządzenia. Przejdź do urządzenia, a następnie kliknij pozycję **Ustawienia > kondycja sprzętu**. W bloku **Kondycja stanu i sprzętu** sprawdź, czy stan diody LED wszystkich komponentów jest zielony. Tylko zdrowe urządzenie ma zielony stan. Jeśli urządzenie jest wyłączone w celu zastąpienia nieprawidłowo działającego komponentu, dla danego składnika zostanie wyświetlony uszkodzony (czerwony) lub obniżony (żółty) stan.


#### <a name="to-shut-down-a-storsimple-device"></a>Aby wyłączyć urządzenie StorSimple

1. Użyj [procedury ponownego uruchomienia lub wyłącz kontrolera,](#restart-or-shut-down-a-single-controller) aby zidentyfikować i wyłączyć kontroler pasywny na urządzeniu. Tę operację można wykonać w witrynie Azure portal lub w programie Windows PowerShell for StorSimple.
2. Powtórz powyższy krok, aby wyłączyć aktywny kontroler.
3. Teraz musisz spojrzeć na tylną płaszczyznę urządzenia. Po całkowitym wyłączeniu obu kontrolerów diody LED stanu obu kontrolerów powinny migać na czerwono. Jeśli w tym momencie musisz całkowicie wyłączyć urządzenie, przerzuć przełączniki zasilania na moduły zasilania i chłodzenia (PCM) na pozycję OFF. Powinno to wyłączyć urządzenie.

## <a name="reset-the-device-to-factory-default-settings"></a>Resetowanie urządzenia do domyślnych ustawień fabrycznych

> [!IMPORTANT]
> Jeśli chcesz zresetować urządzenie do ustawień fabrycznych, skontaktuj się z pomocą techniczną firmy Microsoft. Opisana poniżej procedura powinna być używana tylko w połączeniu z pomocą techniczną firmy Microsoft.

W tej procedurze opisano sposób resetowania urządzenia Microsoft Azure StorSimple do ustawień domyślnych ustawień fabrycznych przy użyciu programu Windows PowerShell for StorSimple.
Resetowanie urządzenia domyślnie powoduje usunięcie wszystkich danych i ustawień z całego klastra.

Wykonaj następujące czynności, aby zresetować urządzenie Usługi Microsoft Azure StorSimple do ustawień domyślnych ustawień fabrycznych:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Aby zresetować urządzenie do ustawień domyślnych w programie Windows PowerShell for StorSimple
1. Dostęp do urządzenia za pośrednictwem konsoli szeregowej. Sprawdź komunikat banera, aby upewnić się, że masz połączenie z **kontrolerem Active.**
2. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**.
3. W wierszu polecenia wpisz następujące polecenie, aby zresetować cały klaster, usuwając wszystkie ustawienia danych, metadanych i kontrolera:
   
    `Reset-HcsFactoryDefault`
   
    Zamiast tego należy zresetować pojedynczy kontroler, należy użyć polecenia cmdlet `-scope` [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) z parametrem.)
   
    System uruchomi się ponownie wiele razy. Zostaniesz powiadomiony po pomyślnym zakończeniu resetowania. W zależności od modelu systemu, może upłynąć 45-60 minut dla urządzenia 8100 i 60-90 minut dla 8600, aby zakończyć ten proces.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Pytania i odpowiedzi dotyczące zarządzania kontrolerami urządzeń
W tej sekcji podsumowaliśmy niektóre z często zadawanych pytań dotyczących zarządzania kontrolerami urządzeń StorSimple.

**PYTANIE** Co się stanie, jeśli oba kontrolery na moim urządzeniu są w dobrej kondycji i włączone, a ponowne uruchomienie lub wyłączenie aktywnego kontrolera zostanie ponownie uruchomione?

**A.** Jeśli oba kontrolery urządzenia są w dobrej kondycji i włączone, zostanie wyświetlony monit o potwierdzenie. Możesz wybrać:

* **Uruchom ponownie aktywny kontroler** — zostaniesz powiadomiony, że ponowne uruchomienie aktywnego kontrolera spowodowało, że urządzenie działało awaryjnie do kontrolera pasywnego. Kontroler zostanie uruchomiony ponownie.
* **Zamknij aktywny kontroler** — zostanie powiadomiony, że zamknięcie aktywnego kontrolera powoduje przestoje. Aby włączyć kontroler, należy również nacisnąć przycisk zasilania urządzenia.

**PYTANIE** Co się stanie, jeśli kontroler pasywny na moim urządzeniu jest niedostępny lub wyłączony, a ponowne uruchomienie lub wyłączenie aktywnego kontrolera zostanie ponownie uruchomiony?

**A.** Jeśli kontroler pasywny w urządzeniu jest niedostępny lub wyłączony, a ty wybierzesz:

* **Uruchom ponownie aktywny kontroler** — zostanie wyświetlone powiadomienie, że kontynuowanie operacji spowoduje tymczasowe zakłócenie usługi i zostanie wyświetlony monit o potwierdzenie.
* **Zamknij aktywny kontroler** — zostaniesz powiadomiony, że kontynuowanie operacji powoduje przestoje. Aby włączyć urządzenie, należy również nacisnąć przycisk zasilania na jednym lub obu kontrolerach. Zostanie wyświetlony monit o potwierdzenie.

**PYTANIE** Kiedy ponowne uruchomienie lub zamknięcie kontrolera nie może się powiększyć?

**A.** Ponowne uruchomienie lub zamknięcie kontrolera może zakończyć się niepowodzeniem, jeśli:

* Trwa aktualizacja urządzenia.
* Ponowne uruchomienie kontrolera jest już w toku.
* Zamknięcie kontrolera jest już w toku.

**PYTANIE** Jak można dowiedzieć się, czy kontroler został ponownie uruchomiony lub zamknięty?

**A.** Można sprawdzić stan kontrolera na bloku kontrolera. Stan kontrolera wskazuje, czy kontroler jest w trakcie ponownego uruchamiania lub zamykania. Ponadto **alerty** bloku zawierają alert informacyjny, jeśli kontroler jest ponownie uruchomiony lub zamknięty. Operacje ponownego uruchamiania i zamykania kontrolera są również rejestrowane w dziennikach aktywności. Aby uzyskać więcej informacji na temat dzienników aktywności, przejdź do [strony Wyświetlanie dzienników aktywności](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**PYTANIE** Czy w wyniku pracy awaryjnej kontrolera występuje jakikolwiek wpływ na we/wy?

**A.** Połączenia TCP między inicjatorami a aktywnym kontrolerem zostaną zresetowane w wyniku pracy awaryjnej kontrolera, ale zostaną przywrócone, gdy kontroler pasywny przyjmie operację. W trakcie tej operacji może wystąpić tymczasowa (mniej niż 30 sekund) przerwa w działaniu we/wy między inicjatorami a urządzeniem.

**PYTANIE** Jak przywrócić kontroler do serwisu po jego zamknięciu i usunięciu?

**A.** Aby przywrócić kontroler do serwisu, należy go włożyć do obudowy zgodnie z [opisem w zastąp moduł kontrolera na urządzeniu StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Następne kroki
* Jeśli wystąpią jakiekolwiek problemy z kontrolerami urządzeń StorSimple, których nie można rozwiązać, korzystając z procedur wymienionych w tym samouczku, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej na temat korzystania z usługi StorSimple Device Manager, przejdź do [usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

