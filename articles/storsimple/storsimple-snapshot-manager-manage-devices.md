---
title: Zarządzanie urządzeniami za pomocą Menedżera migawek StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania przystawki Programu MMC Menedżera migawek StorSimple do łączenia urządzeń StorSimple i zarządzania nimi.
services: storsimple
documentationcenter: ''
author: twooley
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: a037ddc5a43b288349b17dc7d954991a2c634aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254692"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Użyj Menedżera migawek StorSimple do łączenia urządzeń StorSimple i zarządzania nimi
## <a name="overview"></a>Omówienie
Za pomocą węzłów w okienku StorSimple Snapshot Manager **Scope Scope** można zweryfikować zaimportowane dane urządzenia StorSimple i odświeżyć podłączone urządzenia magazynowe. Ponadto po kliknięciu **węzła Urządzenia** w okienku **Wyniki** można wyświetlić listę podłączonych urządzeń i odpowiednie informacje o stanie.

![Połączone urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Rysunek 1: Urządzenie podłączone do Menedżera migawek StorSimple** 

W zależności od wybranych opcji **Widok** w okienku **Wyniki** są wyświetlane następujące informacje o każdym urządzeniu. (Aby uzyskać więcej informacji na temat konfigurowania widoku, przejdź do [menu Widok](storsimple-use-snapshot-manager.md#view-menu).

| Kolumna Wyniki | Opis |
|:--- |:--- |
| Nazwa |Nazwa urządzenia skonfigurowana w klasycznej witrynie Azure portal |
| Model |Numer modelu urządzenia |
| Wersja |Wersja oprogramowania zainstalowanego na urządzeniu |
| Stan |Czy urządzenie jest dostępne |
| Ostatnio zsynchronizowany |Data i godzina ostatniej synchronizacji urządzenia |
| Nr seryjny |Numer seryjny urządzenia |

Po kliknięciu prawym przyciskiem myszy **węzła Urządzenia** w okienku **Zakres** można wybrać jedną z następujących akcji:

* Dodawanie lub zamienianie urządzenia
* Podłączanie urządzenia i weryfikowanie importu
* Odświeżanie podłączonych urządzeń

Po kliknięciu **węzła Urządzenia,** a następnie kliknięciu prawym przyciskiem myszy nazwy urządzenia w okienku **Wyniki,** możesz wybrać jedną z następujących akcji:

* Uwierzytelnianie urządzenia
* Wyświetlanie szczegółów urządzenia
* Odświeżanie urządzenia
* Usuwanie konfiguracji urządzenia
* Zmienianie hasła urządzenia

> [!NOTE]
> Wszystkie te akcje są również dostępne w okienku **Akcje.**


W tym samouczku wyjaśniono, jak używać Menedżera migawek StorSimple do łączenia urządzeń i zarządzania nimi oraz wykonywania następujących zadań:

* Dodawanie lub zamienianie urządzenia
* Podłączanie urządzenia i weryfikowanie importu
* Odświeżanie podłączonych urządzeń
* Uwierzytelnianie urządzenia
* Wyświetlanie szczegółów urządzenia
* Odświeżanie pojedynczego urządzenia
* Usuwanie konfiguracji urządzenia
* Zmienianie wygasłego hasła urządzenia
* Zastępowanie nieudanego urządzenia

> [!NOTE]
> Aby uzyskać ogólne informacje dotyczące korzystania z interfejsu Menedżera migawek StorSimple, przejdź do [interfejsu użytkownika StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Dodawanie lub zamienianie urządzenia
Poniższa procedura służy do dodawania lub zastępowania urządzenia StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Aby dodać lub wymienić urządzenie
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy węzeł **Urządzenia,** a następnie kliknij polecenie **Konfiguruj urządzenie**. Zostanie wyświetlone okno dialogowe **Konfigurowanie urządzenia.**
   
    ![Konfigurowanie urządzenia StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. W polu rozwijającym **Urządzenie** wybierz adres IP urządzenia lub urządzenia wirtualnego. 
4. W polu **tekstowym Hasło** wpisz hasło Menedżera migawek StorSimple utworzone dla urządzenia w klasycznym portalu azure. Kliknij przycisk **OK**. StorSimple Snapshot Manager wyszukuje urządzenie, które zostało zidentyfikowane. 
   
   * Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie.
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij **przycisk OK,** aby zamknąć komunikat o błędzie, a następnie kliknij przycisk **Anuluj,** aby zamknąć okno dialogowe **Konfigurowanie urządzenia.**

## <a name="connect-a-device-and-verify-imports"></a>Podłączanie urządzenia i weryfikowanie importu
Poniższa procedura służy do łączenia urządzenia StorSimple i sprawdzania, czy są importowane istniejące grupy woluminów, które mają skojarzone kopie zapasowe.

#### <a name="to-connect-a-device-and-verify-imports"></a>Aby podłączyć urządzenie i zweryfikować import
1. Aby podłączyć urządzenie do Menedżera migawek StorSimple, postępuj zgodnie z instrukcjami w grze Dodaj lub wymień urządzenie. Gdy łączy się z urządzeniem, StorSimple Snapshot Manager odpowiada w następujący sposób:
   
   * Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. 
   
   * Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie. Po wybraniu urządzenia pojawi się ono w okienku **Wyniki,** a pole stanu wskazuje, że urządzenie jest **dostępne**. StorSimple Snapshot Manager importuje wszystkie grupy woluminów skonfigurowane dla urządzenia, pod warunkiem, że grupy woluminów mają skojarzone kopie zapasowe. Zasady tworzenia kopii zapasowych nie są importowane. Grupy woluminów, które nie mają skojarzonych kopii zapasowych, nie są importowane.
2. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
3. Kliknij prawym przyciskiem myszy górny węzeł w okienku **Zakres,** a następnie kliknij polecenie **Przełącz wyświetlanie importów**.
   
    ![Wybierz pozycję Przełącz wyświetlanie importów](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Zostanie wyświetlone okno dialogowe **Przełączanie wyświetlania importu** z wyświetlaniem stanu grup importowanych woluminów i kopii zapasowych. Kliknij przycisk **OK**.

Po pomyślnym zaimportowaniu grup woluminów i kopii zapasowych można zarządzać nimi za pomocą Menedżera migawek StorSimple, podobnie jak zarządzanie grupami woluminów i kopiami zapasowymi utworzonymi i skonfigurowanym za pomocą Menedżera migawek StorSimple. 

## <a name="refresh-connected-devices"></a>Odświeżanie podłączonych urządzeń
Poniższa procedura służy do synchronizowania podłączonych urządzeń StorSimple z Menedżerem migawek StorSimple.

#### <a name="to-refresh-connected-devices"></a>Aby odświeżyć podłączone urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy pozycję **Urządzenia**, a następnie kliknij polecenie **Odśwież urządzenia**. Spowoduje to zsynchronizowanie podłączonych urządzeń z Menedżerem migawek StorSimple, dzięki czemu można wyświetlać grupy woluminów i kopie zapasowe, w tym wszelkie najnowsze dodatki. 
   
    ![Odświeżanie urządzeń StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Akcja **Odśwież urządzenia** pobiera wszystkie nowe grupy woluminów i wszelkie skojarzone kopie zapasowe z podłączonych urządzeń. W przeciwieństwie do akcji **Rescan woluminów** dostępnych dla **węzła Woluminy,** **urządzenia odświeżania** nie przywraca rejestru kopii zapasowej.

## <a name="authenticate-a-device"></a>Uwierzytelnianie urządzenia
Poniższa procedura służy do uwierzytelniania urządzenia StorSimple za pomocą Menedżera migawek StorSimple.

#### <a name="to-authenticate-a-device"></a>Aby uwierzytelnić urządzenie
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij pozycję **Urządzenia**.
3. W okienku **Wyniki** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij polecenie **Uwierzytelnij**.
4. Zostanie wyświetlone okno dialogowe **Uwierzytelnij.** Wpisz hasło urządzenia, a następnie kliknij przycisk **OK**.
   
    ![Okno dialogowe Uwierzytelnij](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Wyświetlanie szczegółów urządzenia
Poniższa procedura służy do wyświetlania szczegółów urządzenia StorSimple i, jeśli to konieczne, ponownie zsynchronizować urządzenie za pomocą StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Aby wyświetlić i ponownie zsynchronizować szczegóły urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij pozycję **Urządzenia**.
3. W okienku **Wyniki** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij polecenie **Szczegóły**.

4. Zostanie wyświetlone okno dialogowe **Szczegóły urządzenia.** To pole zawiera nazwę, model, wersję, numer seryjny, stan, docelową nazwę kwalifikowaną iSCSI (IQN) oraz datę i godzinę ostatniej synchronizacji.

* Kliknij **przycisk Ponowniesynkowanie,** aby zsynchronizować urządzenie.
* Kliknij **przycisk OK** lub **Anuluj,** aby zamknąć okno dialogowe.
  
  ![Szczegóły urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Odświeżanie pojedynczego urządzenia
Poniższa procedura służy do ponownej synchronizacji pojedynczego urządzenia StorSimple za pomocą Menedżera migawek StorSimple.

#### <a name="to-refresh-a-device"></a>Aby odświeżyć urządzenie
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple. 
2. W okienku **Zakres** kliknij pozycję **Urządzenia**. 
3. W okienku **Wyniki** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij polecenie **Odśwież urządzenie**. Spowoduje to zsynchronizowanie urządzenia z Menedżerem migawek StorSimple.

## <a name="delete-a-device-configuration"></a>Usuwanie konfiguracji urządzenia
Poniższa procedura służy do usuwania indywidualnej konfiguracji urządzenia StorSimple z Menedżera migawek StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Aby usunąć konfigurację urządzenia
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij pozycję **Urządzenia**. 
3. W okienku **Wyniki** kliknij prawym przyciskiem myszy nazwę urządzenia, a następnie kliknij polecenie **Usuń**. 
4. Zostanie wyświetlony następujący komunikat. Kliknij **przycisk Tak,** aby usunąć konfigurację, lub kliknij przycisk **Nie,** aby anulować usunięcie.
   
    ![Usuwanie konfiguracji urządzenia](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Zmienianie wygasłego hasła urządzenia
Aby uwierzytelnić urządzenie StorSimple, należy wprowadzić hasło za pomocą Menedżera migawek StorSimple. To hasło można skonfigurować podczas konfigurowania urządzenia za pomocą interfejsu programu Windows PowerShell. Jednak hasło może wygasnąć. W takim przypadku można użyć klasycznego portalu platformy Azure, aby zmienić hasło. Następnie, ponieważ urządzenie zostało skonfigurowane w StorSimple Snapshot Manager przed wygaśnięciem hasła, należy ponownie uwierzytelnić urządzenie w StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Aby zmienić wygasłe hasło
1. W klasycznym portalu platformy Azure uruchom usługę StorSimple Manager.
2. Kliknij **pozycję Urządzenia** > **Konfiguruj** dla urządzenia.
3. Przewiń w dół do sekcji Menedżer migawek StorSimple. Wprowadź hasło o masie od 14 do 15 znaków. Upewnij się, że hasło zawiera kombinację wielkich, małych liter, znaków liczbowych i specjalnych.
4. Wprowadź ponownie hasło, aby to potwierdzić.
5. Kliknij przycisk **Zapisz** w dolnej części strony.

#### <a name="to-re-authenticate-the-device"></a>Aby ponownie uwierzytelnić urządzenie
1. Uruchom Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij pozycję **Urządzenia**. W okienku **Wyniki** zostanie wyświetlona lista skonfigurowanych urządzeń.
3. Wybierz urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Uwierzytelnij**.
4. W oknie **Uwierzytelnij** wprowadź nowe hasło.
5. Wybierz urządzenie, kliknij prawym przyciskiem myszy i wybierz polecenie **Odśwież urządzenie**. Spowoduje to zsynchronizowanie urządzenia z Menedżerem migawek StorSimple.

## <a name="replace-a-failed-device"></a>Zastępowanie nieudanego urządzenia
Jeśli urządzenie StorSimple ulegnie awarii i zostanie zastąpione przez urządzenie rezerwowe (trybu failover), wykonaj następujące kroki, aby połączyć się z nowym urządzeniem i wyświetlić skojarzone kopie zapasowe.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Aby połączyć się z nowym urządzeniem po przełączeniu awaryjnym
1. Ponownie skonfiguruj połączenie iSCSI z nowym urządzeniem. Aby uzyskać instrukcje, przejdź do "Krok 7: Zainstaluj, zaakwializuj i sformatować wolumin" w [obszarze Wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Jeśli nowe urządzenie StorSimple ma ten sam adres IP co stary, można połączyć starą konfigurację.


1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   3. W oknie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   4. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję **Zatrzymaj usługę**.
2. Usuń informacje o konfiguracji związane ze starym urządzeniem:
   
   1. W Eksploratorze plików przejdź do folderu C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Usuń pliki z folderu BACatalog.
3. Uruchom ponownie usługę zarządzania microsoft storsimple:
   
   1. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   2. W oknie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   3. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję Uruchom ponownie **usługę**.
4. Uruchom Menedżera migawek StorSimple.
5. Aby skonfigurować nowe urządzenie StorSimple, wykonaj kroki opisane w kroku 2: Podłącz urządzenie StorSimple w [Menedżerze migawek Deploy StorSimple](storsimple-snapshot-manager-deployment.md).
6. Kliknij prawym przyciskiem myszy węzeł najwyższego poziomu w okienku **Zakres** (StorSimple Snapshot Manager w przykładzie), a następnie kliknij polecenie **Przełącz wyświetlanie importów**. 
7. Komunikat pojawia się, gdy importowane grupy woluminów i kopie zapasowe są widoczne w Menedżerze migawek StorSimple. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązaniem StorSimple, za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się, jak [używać Menedżera migawek StorSimple do wyświetlania woluminów i zarządzania nimi.](storsimple-snapshot-manager-manage-volumes.md)

