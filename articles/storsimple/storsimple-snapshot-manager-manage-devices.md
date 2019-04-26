---
title: Zarządzanie urządzeniami za pomocą Menedżera migawek StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób używania przystawki MMC przystawki StorSimple Snapshot Manager, aby nawiązać połączenie i zarządzanie urządzeniami StorSimple.
services: storsimple
documentationcenter: ''
author: SharS
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 51632b8b68640814fc113a94925b6d6deaca4c5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482562"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Aby nawiązać połączenie i zarządzanie urządzeniami StorSimple za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Węzły można użyć w programie StorSimple Snapshot Manager **zakres** okienko w celu sprawdzenia importowanych danych urządzenia StorSimple i Odśwież urządzenia magazynujące połączone. Ponadto po kliknięciu **urządzeń** węzła, można wyświetlić listę połączonych urządzeń i odpowiednie informacje o stanie w **wyniki** okienka.

![Podłączone urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Rysunek 1: Podłączone urządzenie StorSimple Snapshot Manager** 

W zależności od Twojego **widoku** wybory, **wyniki** okienku są wyświetlane następujące informacje na temat każdego urządzenia. (Aby uzyskać więcej informacji o konfigurowaniu widok, przejdź do [menu Widok](storsimple-use-snapshot-manager.md#view-menu).

| Kolumny wyników | Opis |
|:--- |:--- |
| Name (Nazwa) |Nazwa urządzenia, zgodnie z konfiguracją w klasycznej witrynie Azure portal |
| Modelowanie |Numer modelu urządzenia |
| Wersja |Wersja oprogramowania zainstalowanego na urządzeniu |
| Stan |Czy urządzenie jest dostępne |
| Ostatnia synchronizacja |Data i godzina, kiedy ostatniej synchronizacji urządzenia |
| Numer seryjny |Numer seryjny urządzenia |

Kliknięcie prawym przyciskiem myszy **urządzeń** w węźle **zakres** okienku możesz wybrać spośród następujących czynności:

* Dodawanie lub zastępowanie urządzenia
* Podłącz urządzenie i sprawdź Importy
* Odśwież połączone urządzenia

Jeśli klikniesz **urządzeń** nazw węzła i klikając prawym przyciskiem myszy urządzenie w **wyniki** okienku możesz wybrać spośród następujących czynności:

* Uwierzytelnienia urządzenia
* Wyświetlanie szczegółów urządzenia
* Odśwież urządzenie
* Usuwanie konfiguracji urządzenia
* Zmień hasło urządzenia

> [!NOTE]
> Wszystkie te działania są także dostępne w **akcje** okienka.


W tym samouczku wyjaśniono, jak za pomocą przystawki StorSimple Snapshot Manager połączenia i zarządzać urządzeniami i wykonywać następujące zadania:

* Dodawanie lub zastępowanie urządzenia
* Podłącz urządzenie i sprawdź Importy
* Odśwież połączone urządzenia
* Uwierzytelnienia urządzenia
* Wyświetlanie szczegółów urządzenia
* Odśwież poszczególnych urządzeń
* Usuwanie konfiguracji urządzenia
* Zmień hasło urządzenia wygasłe
* Zamień urządzenia nie powiodło się

> [!NOTE]
> Aby uzyskać ogólne informacje o interfejsie programu StorSimple Snapshot Manager, przejdź do [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Dodawanie lub zastępowanie urządzenia
Użyj poniższej procedury do dodania lub zamiany na urządzeniu StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Do dodania lub zamiany urządzenia
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **urządzeń** węzłem, a następnie kliknij przycisk **skonfigurować urządzenie**. **Skonfigurować urządzenie** pojawi się okno dialogowe.
   
    ![Konfigurowanie urządzenia StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. W **urządzenia** listy rozwijanej wybierz adres IP urządzenia lub urządzenia wirtualnego. 
4. W **hasło** tekstu wpisz hasło programu StorSimple Snapshot Manager utworzone dla urządzenia w klasycznej witrynie Azure portal. Kliknij przycisk **OK**. Wyszukuje programu StorSimple Snapshot Manager dla urządzenia, który został zidentyfikowany. 
   
   * Jeśli urządzenie jest dostępne, przystawki StorSimple Snapshot Manager dodaje połączenie.
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, przystawki StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij przycisk **OK** Zamknij komunikat o błędzie, a następnie kliknij przycisk **anulować** zamknąć **skonfigurować urządzenie** okno dialogowe.

## <a name="connect-a-device-and-verify-imports"></a>Podłącz urządzenie i sprawdź Importy
Użyj poniższej procedury do podłączenia urządzenia StorSimple i sprawdź, czy żadnych istniejących grup woluminu, które mają skojarzone kopie zapasowe są importowane.

#### <a name="to-connect-a-device-and-verify-imports"></a>Podłącz urządzenie i sprawdź Importy
1. Podłącz urządzenie do programu StorSimple Snapshot Manager, postępuj zgodnie z instrukcjami w Dodaj lub Zastąp urządzenia. Podczas łączenia się z urządzeniem, przystawki StorSimple Snapshot Manager działa w następujący sposób:
   
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, przystawki StorSimple Snapshot Manager zwraca komunikat o błędzie. 
   
   * Jeśli urządzenie jest dostępne, przystawki StorSimple Snapshot Manager dodaje połączenie. Po wybraniu urządzenia, pojawia się w **wyniki** okienku, a w polu stanu wskazuje, czy urządzenie jest **dostępne**. Przystawki StorSimple Snapshot Manager importuje wszystkie grupy woluminu skonfigurowany na tym urządzeniu, pod warunkiem, że grupy woluminu są skojarzone kopie zapasowe. Zasady tworzenia kopii zapasowych nie są importowane. Grupami woluminów, które nie mają skojarzonych kopii zapasowych nie są importowane.
2. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
3. Kliknij prawym przyciskiem myszy najwyższy węzeł w **zakres** okienku, a następnie kliknij przycisk **Przełącz wyświetlanie Importy**.
   
    ![Przełącz zaznacz Importy wyświetlanie](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. **Przełącz wyświetlanie Importy** pojawi się okno dialogowe, wyświetlanie stanu grupy importowane woluminu i kopii zapasowych. Kliknij przycisk **OK**.

Po grupami woluminów i kopii zapasowych zostaną pomyślnie zaimportowane, można użyć przystawki StorSimple Snapshot Manager do zarządzania nimi, tak samo, jak będą zarządzać grupami woluminów i kopii zapasowych, które zostały utworzone i skonfigurowane za pomocą przystawki StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Odśwież połączone urządzenia
Poniższa procedura umożliwia synchronizację połączonych urządzeń StorSimple z programu StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Aby odświeżyć połączonych urządzeń
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **urządzeń**, a następnie kliknij przycisk **Odśwież urządzeń**. Synchronizuje połączonych urządzeń, za pomocą przystawki StorSimple Snapshot Manager tak, aby wyświetlić grupami woluminów i kopii zapasowych, w tym wszystkie ostatnio dodane elementy. 
   
    ![Odśwież urządzenia StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

**Odśwież urządzeń** działanie powoduje pobranie wszystkich nowych grup woluminu i wszelkich skojarzonych kopii zapasowych z połączonych urządzeń. W odróżnieniu od **Skanuj ponownie woluminy** akcji dotyczących **woluminów** węzła, **Odśwież urządzeń** nie powoduje przywrócenia kopii zapasowej rejestru.

## <a name="authenticate-a-device"></a>Uwierzytelnienia urządzenia
Użyj poniższej procedury do uwierzytelniania urządzenia StorSimple przy użyciu programu StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Do uwierzytelniania urządzenia
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij **urządzeń**.
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **Uwierzytelnij**.
4. **Uwierzytelnij** pojawi się okno dialogowe. Wpisz hasło urządzenia, a następnie kliknij przycisk **OK**.
   
    ![Okno dialogowe uwierzytelniania](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Wyświetlanie szczegółów urządzenia
Użyj poniższej procedury, aby wyświetlić szczegóły dotyczące urządzenia StorSimple i, jeśli to konieczne, należy ponownie zsynchronizować urządzenia za pomocą przystawki StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Aby wyświetlić i wykonaj ponowną synchronizację szczegóły urządzenia
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij **urządzeń**.
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **szczegóły**.

4 **szczegóły urządzenia** pojawi się okno dialogowe. To pole zawiera nazwę, modelu, wersji, numer seryjny, stan, obiektów docelowych iSCSI kwalifikowana nazwa (IQN) i ostatniej synchronizacji daty i godziny.

* Kliknij przycisk **ponowna synchronizacja** do synchronizowania urządzenia.
* Kliknij przycisk **OK** lub **anulować** aby zamknąć okno dialogowe.
  
  ![Szczegóły urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Odśwież poszczególnych urządzeń
Użyj poniższej procedury do ponownej synchronizacji poszczególnych urządzeń StorSimple z programu StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Aby odświeżyć urządzenia
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager. 
2. W **zakres** okienku kliknij **urządzeń**. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **Odśwież urządzenie**. Synchronizacja urządzenia za pomocą przystawki StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Usuwanie konfiguracji urządzenia
Użyj poniższej procedury można usunąć poszczególnych konfiguracji urządzenia StorSimple z programu StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Aby usunąć konfigurację urządzenia
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij **urządzeń**. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **Usuń**. 
4. Zostanie wyświetlony następujący komunikat. Kliknij przycisk **tak** Aby usunąć konfigurację, lub kliknij przycisk **nie** Aby anulować usunięcie.
   
    ![Usuwanie konfiguracji urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Zmień hasło urządzenia wygasłe
Wprowadź hasło do uwierzytelniania urządzenia StorSimple przy użyciu programu StorSimple Snapshot Manager. To hasło należy skonfigurować, korzystając z interfejsu programu Windows PowerShell do konfigurowania urządzenia. Jednak można unieważnić hasła. W takim przypadku można użyć klasycznego portalu Azure, aby zmienić hasło. Następnie ponieważ urządzenie zostało skonfigurowane w programie StorSimple Snapshot Manager przed wygaśnięciem hasła, użytkownik musi zostać ponownie uwierzytelniony urządzeń programu StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Aby zmienić hasła
1. W klasycznym portalu Azure Uruchom usługę Menedżer StorSimple.
2. Kliknij przycisk **urządzeń** > **Konfiguruj** dla tego urządzenia.
3. Przewiń w dół do sekcji przystawki StorSimple Snapshot Manager. Wprowadź hasło składające się z 14 do 15 znaków. Upewnij się, że hasło zawiera wielkie litery, małe litery, cyfry i znaki specjalne.
4. Wprowadź ponownie hasło w celu potwierdzenia.
5. Kliknij przycisk **Zapisz** w dolnej części strony.

#### <a name="to-re-authenticate-the-device"></a>Konieczność ponownego uwierzytelnienia urządzenia
1. Uruchom Menedżera migawek StorSimple.
2. W **zakres** okienku kliknij **urządzeń**. Zostanie wyświetlona lista skonfigurowanych urządzeń w **wyniki** okienka.
3. Wybierz urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij **Uwierzytelnij**.
4. W **Uwierzytelnij** okna, wprowadź nowe hasło.
5. Wybierz urządzenie, kliknij prawym przyciskiem myszy i wybierz **urządzenia odświeżania**. Synchronizacja urządzenia za pomocą przystawki StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Zamień urządzenia nie powiodło się
Jeśli na urządzeniu StorSimple nie powiedzie się i są zastępowane przez urządzenie (tryb failover) w stanie wstrzymania, skorzystaj z poniższych wskazówek w celu nawiązywania nowego urządzenia i wyświetlania skojarzonych kopii zapasowych.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Aby nawiązać nowe urządzenie, po włączeniu trybu failover
1. Zmień konfigurację połączenia iSCSI, aby nowe urządzenie. Aby uzyskać instrukcje, przejdź do "krok 7: Instalowanie, inicjowanie i formatowanie woluminu"w [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Nowe urządzenie StorSimple ma ten sam adres IP, jak stary, może być możliwe nawiązanie połączenia starej konfiguracji.


1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz **Microsoft StorSimple Management Service**.
   4. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Zatrzymaj usługę**.
2. Usuń informacje o konfiguracji związane ze starym urządzeniem:
   
   1. W Eksploratorze plików przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Usuń pliki w folderze BACatalog.
3. Uruchom ponownie usługę zarządzania Microsoft StorSimple:
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz **Microsoft StorSimple Management Service**.
   3. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Uruchom ponownie usługę**.
4. Uruchom Menedżera migawek StorSimple.
5. Aby skonfigurować nowe urządzenie StorSimple, wykonaj kroki opisane w kroku 2: Podłącz urządzenie StorSimple w [wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Kliknij prawym przyciskiem myszy węzeł najwyższego poziomu w **zakres** okienku (programu StorSimple Snapshot Manager w przykładzie), a następnie kliknij przycisk **Przełącz wyświetlanie Importy**. 
7. Pojawi się komunikat, gdy grupy importowane woluminu i kopii zapasowych są widoczne w programie StorSimple Snapshot Manager. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [Menedżera migawek StorSimple umożliwia wyświetlanie i zarządzanie woluminami](storsimple-snapshot-manager-manage-volumes.md).

