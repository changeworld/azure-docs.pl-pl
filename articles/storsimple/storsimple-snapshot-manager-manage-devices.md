---
title: Zarządzanie urządzeniami z StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania przystawki MMC programu StorSimple Snapshot Manager do łączenia i zarządzanie urządzeniami StorSimple.
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
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876810"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Użyj programu StorSimple Snapshot Manager do łączenia i zarządzanie urządzeniami StorSimple
## <a name="overview"></a>Omówienie
Korzystając z węzłów w StorSimple Snapshot Manager **zakres** okienko, aby zweryfikować importowanych danych urządzenia StorSimple i Odśwież urządzeń połączonych magazynów. Ponadto po kliknięciu **urządzeń** węzła, można wyświetlić listę podłączonego urządzenia i odpowiednie informacje o stanie w **wyniki** okienka.

![Połączonych urządzeń](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Rysunek 1: Podłączonego urządzenia StorSimple Snapshot Manager** 

W zależności od Twojego **widoku** wybrane opcje **wyniki** w okienku zostaną wyświetlone następujące informacje dotyczące każdego urządzenia. (Aby uzyskać więcej informacji o konfigurowaniu widok, przejdź do [menu Widok](storsimple-use-snapshot-manager.md#view-menu).

| Kolumny wyników | Opis |
|:--- |:--- |
| Nazwa |Nazwa urządzenia, zgodnie z konfiguracją w klasycznym portalu Azure |
| Model |Numer modelu urządzenia |
| Wersja |Wersja oprogramowania zainstalowanego na urządzeniu |
| Stan |Określa, czy urządzenie jest dostępne |
| Od ostatniej synchronizacji upłynęła |Data i godzina, kiedy urządzenie ostatniej synchronizacji |
| Numer seryjny |Numer seryjny urządzenia |

Kliknięcie prawym przyciskiem myszy **urządzeń** w węźle **zakres** okienka, możesz wybrać z następujących czynności:

* Dodaj lub Zastąp urządzenia
* Podłącz urządzenie i sprawdź importów
* Odśwież połączonych urządzeń

Jeśli klikniesz przycisk **urządzeń** węzła, a następnie kliknij prawym przyciskiem myszy urządzenie, nazwa w **wyniki** okienka, możesz wybrać z następujących czynności:

* Uwierzytelniania urządzenia
* Wyświetl szczegóły urządzenia
* Odśwież urządzenia
* Usuwanie konfiguracji urządzenia
* Zmień hasło urządzenia

> [!NOTE]
> Wszystkie te działania są także dostępne w **akcje** okienka.


W tym samouczku wyjaśniono, jak za pomocą StorSimple Snapshot Manager można połączyć i zarządzanie urządzeniami oraz wykonywać następujące zadania:

* Dodaj lub Zastąp urządzenia
* Podłącz urządzenie i sprawdź importów
* Odśwież połączonych urządzeń
* Uwierzytelniania urządzenia
* Wyświetl szczegóły urządzenia
* Odśwież poszczególnych urządzeń
* Usuwanie konfiguracji urządzenia
* Zmień hasło urządzenia wygasły
* Zastąp urządzenia nie powiodło się

> [!NOTE]
> Aby uzyskać ogólne informacje o interfejsie StorSimple Snapshot Manager, przejdź do [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Dodaj lub Zastąp urządzenia
Użyj poniższej procedury do dodania lub zamiany urządzenia StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Do dodania lub zamiany urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **urządzeń** węzeł, a następnie kliknij przycisk **Skonfiguruj urządzenie**. **Skonfiguruj urządzenie** zostanie wyświetlone okno dialogowe.
   
    ![Konfigurowanie urządzenia StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. W **urządzenia** listy rozwijanej wybierz adres IP urządzenia lub urządzenia wirtualnego. 
4. W **hasło** tekstu wpisz hasło programu StorSimple Snapshot Manager utworzone dla urządzenia w klasycznym portalu Azure. Kliknij przycisk **OK**. Wyszukuje StorSimple Snapshot Manager dla urządzeń, który został zidentyfikowany. 
   
   * Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie.
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij przycisk **OK** zamknąć komunikat o błędzie, a następnie kliknij przycisk **anulować** zamknąć **Skonfiguruj urządzenie** okno dialogowe.

## <a name="connect-a-device-and-verify-imports"></a>Podłącz urządzenie i sprawdź importów
Poniższa procedura umożliwia łączenie urządzenia StorSimple i sprawdź, czy jakichkolwiek istniejących grup woluminów, które administrator skojarzył kopie zapasowe są importowane.

#### <a name="to-connect-a-device-and-verify-imports"></a>Podłącz urządzenie i sprawdź importów
1. Podłącz urządzenie do programu StorSimple Snapshot Manager, postępuj zgodnie z instrukcjami w Dodaj lub Zastąp urządzenia. Gdy łączy się urządzenie, StorSimple Snapshot Manager działa w następujący sposób:
   
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. 
   
   * Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie. Po wybraniu urządzenia zostanie wyświetlony w **wyniki** okienka, a w polu stanu wskazuje, czy urządzenie jest **dostępne**. StorSimple Snapshot Manager importuje wszystkie grupy woluminu skonfigurowany dla urządzenia, pod warunkiem, że grupy wolumin ma skojarzone kopii zapasowych. Zasady tworzenia kopii zapasowej nie zostały zaimportowane. Wolumin grup, które nie mają skojarzonych kopii zapasowych nie zostaną zaimportowane.
2. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
3. Kliknij prawym przyciskiem myszy górny węzeł w **zakres** okienka, a następnie kliknij przycisk **Przełącz importów wyświetlanie**.
   
    ![Wybierz opcję Przełącz wyświetlanie importów](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. **Przełącz importów wyświetlanie** zostanie wyświetlone okno dialogowe, wyświetlanie stanu grupy importowanych woluminu i kopii zapasowych. Kliknij przycisk **OK**.

Po grup woluminu i kopii zapasowych zostaną pomyślnie zaimportowane, można użyć programu StorSimple Snapshot Manager do zarządzania nimi, tak jak będzie zarządzać grupami woluminu i kopii zapasowych, które zostały utworzone i skonfigurowane z StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Odśwież połączonych urządzeń
Użyj poniższej procedury do synchronizacji podłączonych urządzeń StorSimple z StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Aby odświeżyć połączonych urządzeń
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **urządzeń**, a następnie kliknij przycisk **Odśwież urządzeń**. Synchronizuje połączonych urządzeń, z StorSimple Snapshot Manager tak, aby wyświetlić grupy woluminu i kopii zapasowych, w tym wszelkie ostatnio dodane. 
   
    ![Odśwież urządzenia StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

**Odśwież urządzeń** działanie powoduje pobranie wszystkich nowych grup wolumin i wszystkie skojarzone kopii zapasowych z połączonych urządzeń. W przeciwieństwie do **ponownego skanowania woluminów** umożliwiające **woluminów** węzła, **Odśwież urządzenia** nie powoduje przywrócenia kopii zapasowej rejestru.

## <a name="authenticate-a-device"></a>Uwierzytelniania urządzenia
Użyj poniższej procedury do uwierzytelniania urządzenia StorSimple z StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Do uwierzytelniania urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **urządzeń**.
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **Uwierzytelnij**.
4. **Uwierzytelnij** zostanie wyświetlone okno dialogowe. Wpisz hasło urządzenia, a następnie kliknij przycisk **OK**.
   
    ![Uwierzytelnianie — okno dialogowe](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Wyświetl szczegóły urządzenia
Aby wyświetlić szczegóły urządzenia StorSimple i, jeśli to konieczne, należy ponownie zsynchronizować urządzenie z StorSimple Snapshot Manager, należy użyć poniższej procedury.

#### <a name="to-view-and-resynchronize-device-details"></a>Aby wyświetlić i ponownie zsynchronizować szczegóły urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **urządzeń**.
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **szczegóły**.

4 **szczegóły urządzenia** zostanie wyświetlone okno dialogowe. To pole zawiera nazwę, modelu, wersji, numer seryjny, stan, obiektów docelowych iSCSI kwalifikowana nazwa (IQN) i ostatniej synchronizacji daty i godziny.

* Kliknij przycisk **ponownej synchronizacji** do synchronizowania urządzenia.
* Kliknij przycisk **OK** lub **anulować** aby zamknąć okno dialogowe.
  
  ![Szczegóły urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Odśwież poszczególnych urządzeń
Aby ponownie zsynchronizować poszczególne urządzenia StorSimple z StorSimple Snapshot Manager, należy użyć poniższej procedury.

#### <a name="to-refresh-a-device"></a>Aby odświeżyć urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager. 
2. W **zakres** okienku, kliknij przycisk **urządzeń**. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **Odśwież urządzenia**. Urządzenia do synchronizacji z StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Usuwanie konfiguracji urządzenia
Użyj poniższej procedury można usunąć z programu StorSimple Snapshot Manager poszczególnych konfiguracji urządzenia StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Aby usunąć konfigurację urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **urządzeń**. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij przycisk **usunąć**. 
4. Zostanie wyświetlony następujący komunikat. Kliknij przycisk **tak** Aby usunąć konfigurację, lub kliknij przycisk **nr** Aby anulować usunięcie.
   
    ![Usuwanie konfiguracji urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Zmień hasło urządzenia wygasły
Wprowadź hasło do uwierzytelniania urządzenia StorSimple z StorSimple Snapshot Manager. Możesz skonfigurować to hasło, korzystając z interfejsu programu Windows PowerShell do konfigurowania urządzenia. Jednak można wygaśnięcia hasła. W takim przypadku można użyć klasycznego portalu Azure do zmiany hasła. Następnie ponieważ urządzenie zostało skonfigurowane w StorSimple Snapshot Manager przed wygaśnięciem hasła, należy ponownego uwierzytelnienia urządzenia StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Aby zmienić hasła
1. W klasycznym portalu Azure należy uruchomić usługę Menedżer StorSimple.
2. Kliknij przycisk **urządzeń** > **Konfiguruj** dla urządzenia.
3. Przewiń w dół do sekcji StorSimple Snapshot Manager. Wprowadź hasło składające się z 14-15 znaków. Upewnij się, że hasło zawiera wielkie litery, małe litery, liczbowego i znaki specjalne.
4. Wprowadź ponownie hasło, aby je potwierdzić.
5. Kliknij przycisk **Zapisz** w dolnej części strony.

#### <a name="to-re-authenticate-the-device"></a>Ponownego uwierzytelnienia urządzenia
1. Uruchom StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **urządzeń**. Zostanie wyświetlona lista skonfigurowanych urządzeń w **wyniki** okienka.
3. Wybierz urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij **Uwierzytelnij**.
4. W **Uwierzytelnij** okna, wprowadź nowe hasło.
5. Wybierz urządzenie, kliknij prawym przyciskiem myszy i wybierz **urządzenia odświeżania**. Urządzenia do synchronizacji z StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Zastąp urządzenia nie powiodło się
Jeśli urządzenie StorSimple kończy się niepowodzeniem i jest zastąpiony przez urządzenie stan wstrzymania (failover), wykonaj następujące kroki nawiązać nowe urządzenie i wyświetlić skojarzonych kopii zapasowych.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Aby nawiązać połączenie nowe urządzenie po trybu failover
1. Zmień konfigurację połączenia iSCSI, aby nowe urządzenie. Aby uzyskać instrukcje, przejdź do "krok 7: instalacji, zainicjować i formatowanie woluminu" w [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Nowe urządzenie StorSimple ma ten sam adres IP jak stary, można połączyć starą konfigurację.


1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz **usługi zarządzania StorSimple Microsoft**.
   4. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Zatrzymaj usługę**.
2. Usuń informacje o konfiguracji związanych ze starym urządzeniem:
   
   1. W Eksploratorze plików przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Usuń pliki w folderze BACatalog.
3. Uruchom ponownie usługę zarządzania Microsoft StorSimple:
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz **usługi zarządzania StorSimple Microsoft**.
   3. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Uruchom ponownie usługę**.
4. Uruchom StorSimple Snapshot Manager.
5. Aby skonfigurować nowe urządzenie StorSimple, wykonaj czynności opisane w kroku 2: łączenie urządzenia StorSimple w [wdrażanie StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Kliknij prawym przyciskiem myszy węzeł najwyższego poziomu w **zakres** okienka (StorSimple Snapshot Manager w tym przykładzie), a następnie kliknij przycisk **Przełącz importów wyświetlanie**. 
7. Komunikat jest wyświetlany, gdy grup importowanych woluminu i kopii zapasowych są widoczne w StorSimple Snapshot Manager. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązania StorSimple przy użyciu programu StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [StorSimple Snapshot Manager umożliwia wyświetlanie i zarządzanie woluminami](storsimple-snapshot-manager-manage-volumes.md).

