---
title: Wdrażanie usługi StorSimple Device Manager na platformie Azure | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak utworzyć i usunąć usługę StorSimple Device Manager w witrynie Azure portal i opisano sposób zarządzania kluczem rejestracji usługi.
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
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 1e75acc03209fdd7e613801c9152f24aaecfa6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267783"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Wdrażanie usługi StorSimple Device Manager dla urządzeń z serii StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Usługa StorSimple Device Manager działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Po utworzeniu usługi można jej używać do zarządzania wszystkimi urządzeniami podłączonymi do usługi StorSimple Device Manager z jednej centralnej lokalizacji, minimalizując w ten sposób obciążenie administracyjne.

W tym samouczku opisano kroki wymagane do utworzenia, usunięcia, migracji usługi i zarządzania kluczem rejestracji usługi. Informacje zawarte w tym artykule mają zastosowanie tylko do urządzeń z serii StorSimple 8000. Aby uzyskać więcej informacji na temat tablic wirtualnych StorSimple, przejdź do [wdrażania usługi StorSimple Device Manager dla tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Witryna Azure portal obsługuje urządzenia z aktualizacją 5.0 lub nowszą. Jeśli urządzenie nie jest aktualne, należy natychmiast zainstalować aktualizację 5. Aby uzyskać więcej informacji, przejdź do [instalacji aktualizacji 5](storsimple-8000-install-update-5.md). 
> - Jeśli używasz urządzenia StorSimple Cloud Appliance (8010/8020), nie można zaktualizować urządzenia w chmurze. Użyj najnowszej wersji oprogramowania, aby utworzyć nowe urządzenie w chmurze z aktualizacją 5.0, a następnie przejść w stan fail over do nowego urządzenia w chmurze utworzone. 
> - Na wszystkich urządzeniach z aktualizacją 4.0 lub wcześniejszą będą dostępne zmniejszone funkcje zarządzania. 

## <a name="create-a-service"></a>Tworzenie usługi
Aby utworzyć usługę StorSimple Device Manager, musisz mieć:

* Subskrypcja z umową Enterprise Agreement
* Aktywne konto magazynu platformy Microsoft Azure
* Informacje rozliczeniowe używane do zarządzania dostępem

Dozwolone są tylko subskrypcje z umową Enterprise Agreement. Podczas tworzenia usługi można również wygenerować domyślne konto magazynu.

Jedna usługa może zarządzać wieloma urządzeniami. Jednak urządzenie nie może obejmować wiele usług. Duże przedsiębiorstwo może mieć wiele wystąpień usługi do pracy z różnymi subskrypcjami, organizacjami, a nawet lokalizacjami wdrażania. 

> [!NOTE]
> Do zarządzania urządzeniami z serii StorSimple 8000 i macierzami wirtualnymi StorSimple potrzebne są oddzielne wystąpienia usługi StorSimple Device Manager.

Wykonaj następujące kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Dla każdej usługi StorSimple Device Manager istnieją następujące atrybuty:

* **Nazwa** — nazwa, która została przypisana do usługi StorSimple Device Manager podczas jej tworzenia. **Nie można zmienić nazwy usługi po utworzeniu usługi. Dotyczy to również innych jednostek, takich jak urządzenia, woluminy, kontenery woluminów i zasady tworzenia kopii zapasowych, których nie można zmienić w witrynie Azure portal.**
* **Status** — stan usługi, która może być **aktywna,** **Tworzenie**lub **Online**.
* **Lokalizacja** — położenie geograficzne, w którym urządzenie StorSimple zostanie wdrożone.
* **Subskrypcja** — subskrypcja rozliczeniowa skojarzona z usługą.

## <a name="delete-a-service"></a>Usuwanie usługi

Przed usunięciem usługi upewnij się, że żadne podłączone urządzenia jej nie używają. Jeśli usługa jest używana, należy dezaktywować podłączone urządzenia. Operacja dezaktywacji spowoduje zerwanie połączenia między urządzeniem a usługą, ale zachowa dane urządzenia w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć operacji. Każde urządzenie, które korzystało z usługi, musi zostać zresetowane do ustawień fabrycznych, zanim będzie można z niej korzystać z innej usługi. W tym scenariuszu dane lokalne na urządzeniu, a także konfiguracji, zostanie utracone.

Wykonaj następujące kroki, aby usunąć usługę.

### <a name="to-delete-a-service"></a>Aby usunąć usługę

1. Wyszukaj usługę, którą chcesz usunąć. Kliknij ikonę **Zasoby,** a następnie wprowadź odpowiednie terminy do wyszukania. W wynikach wyszukiwania kliknij usługę, którą chcesz usunąć.

    ![Wyszukaj usługę do usunięcia](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Spowoduje to przejście do bloku usługi StorSimple Device Manager. Kliknij **pozycję Usuń**.

    ![Usuń usługę](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kliknij **przycisk Tak** w powiadomieniu o potwierdzeniu. Usunięcie usługi może potrwać kilka minut.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi

Po pomyślnym utworzeniu usługi należy zarejestrować urządzenie StorSimple w usłudze. Aby zarejestrować swoje pierwsze urządzenie StorSimple, potrzebny jest klucz rejestracji usługi. Aby zarejestrować dodatkowe urządzenia w istniejącej usłudze StorSimple, potrzebny jest zarówno klucz rejestracji, jak i klucz szyfrowania danych usługi (który jest generowany na pierwszym urządzeniu podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [StorSimple security](storsimple-8000-security.md). Klucz rejestracji można uzyskać, uzyskując dostęp do **kluczy** w bloku Menedżera urządzeń StorSimple.

Wykonaj następujące kroki, aby uzyskać klucz rejestracji usługi.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Klucz rejestracyjny usługi należy przechowywać w bezpiecznym miejscu. Ten klucz, a także klucz szyfrowania danych usługi, będą potrzebować tego klucza, aby zarejestrować dodatkowe urządzenia w tej usłudze. Po uzyskaniu klucza rejestracji usługi należy skonfigurować urządzenie za pomocą interfejsu programu Windows PowerShell for StorSimple.

Aby uzyskać szczegółowe informacje na temat używania tego klucza rejestracji, zobacz [Krok 3: Konfigurowanie i rejestrowanie urządzenia za pośrednictwem programu Windows PowerShell for StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Ponowne generowanie klucza rejestracji usługi
Należy ponownie wygenerować klucz rejestracji usługi, jeśli wymagane jest wykonanie rotacji kluczy lub jeśli lista administratorów usługi uległa zmianie. Podczas ponownego generowania klucza, nowy klucz jest używany tylko do rejestrowania kolejnych urządzeń. Ten proces nie ma wpływu na urządzenia, które zostały już zarejestrowane.

Wykonaj następujące kroki, aby ponownie wygenerować klucz rejestracji usługi.

### <a name="to-regenerate-the-service-registration-key"></a>Aby ponownie wygenerować klucz rejestracji usługi
1. W **bloku StorSimple Device Manager** przejdź do **klawiszy** **zarządzania &gt; ** .
    
    ![Blok Klucze](./media/storsimple-8000-manage-service/regenregkey2.png)

2. W bloku **Klawisze** kliknij pozycję **Regeneruj**.

    ![Kliknij przycisk wygeneruj ponownie](./media/storsimple-8000-manage-service/regenregkey3.png)
3. W **bloku klucz rejestracji usługi regeneracji** przejrzyj akcję wymaganą podczas ponownego generowania kluczy. Wszystkie kolejne urządzenia, które są zarejestrowane w tej usłudze, używają nowego klucza rejestracji. Kliknij przycisk **Wygeneruj,** aby potwierdzić. Użytkownik zostanie powiadomiony po zakończeniu regeneracji.

    ![Potwierdź regenerację](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Pojawi się nowy klucz rejestracji usługi.

5. Skopiuj ten klucz i zapisz go do rejestracji nowych urządzeń za pomocą tej usługi.



## <a name="change-the-service-data-encryption-key"></a>Zmienianie klucza szyfrowania danych usługi
Klucze szyfrowania danych usługi są używane do szyfrowania poufnych danych klienta, takich jak poświadczenia konta magazynu, które są wysyłane z usługi StorSimple Manager do urządzenia StorSimple. Należy okresowo zmieniać te klucze, jeśli organizacja IT ma zasady rotacji kluczy na urządzeniach magazynujących. Proces zmiany klucza może się nieznacznie różnić w zależności od tego, czy istnieje jedno urządzenie, czy wiele urządzeń zarządzanych przez usługę StorSimple Manager. Aby uzyskać więcej informacji, przejdź do [pliku StorSimple security and data protection](storsimple-8000-security.md).

Zmiana klucza szyfrowania danych usługi jest procesem 3-etapowym:

1. Korzystając ze skryptów programu Windows PowerShell dla usługi Azure Resource Manager, autoryzuj urządzenie do zmiany klucza szyfrowania danych usługi.
2. Za pomocą programu Windows PowerShell for StorSimple zainicjuj zmianę klucza szyfrowania danych usługi.
3. Jeśli masz więcej niż jedno urządzenie StorSimple, zaktualizuj klucz szyfrowania danych usługi na innych urządzeniach.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1: Użyj skryptu programu Windows PowerShell, aby autoryzować urządzenie do zmiany klucza szyfrowania danych usługi
Zazwyczaj administrator urządzenia zażąda, aby administrator usługi autoryzował urządzenie do zmiany kluczy szyfrowania danych usługi. Administrator usługi następnie upoważni urządzenie do zmiany klucza.

Ten krok jest wykonywany przy użyciu skryptu opartego na usłudze Azure Resource Manager. Administrator usługi może wybrać urządzenie, które kwalifikuje się do autoryzacji. Urządzenie jest następnie autoryzowane do uruchomienia procesu zmiany klucza szyfrowania danych usługi. 

Aby uzyskać więcej informacji na temat korzystania ze skryptu, przejdź do [autorytore-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Jakie urządzenia mogą być autoryzowane do zmiany kluczy szyfrowania danych usługi?
Urządzenie musi spełniać następujące kryteria, aby móc autoryzować do inicjowania zmian klucza szyfrowania danych usługi:

* Urządzenie musi być w trybie online, aby kwalifikować się do autoryzacji zmiany klucza szyfrowania danych usługi.
* Możesz ponownie autoryzować to samo urządzenie po 30 minutach, jeśli zmiana klucza nie została zainicjowana.
* Można autoryzować inne urządzenie, pod warunkiem, że zmiana klucza nie została zainicjowana przez wcześniej autoryzowane urządzenie. Po autoryzacji nowego urządzenia stare urządzenie nie może zainicjować zmiany.
* Nie można autoryzować urządzenia, gdy trwa narzucie klucza szyfrowania danych usługi.
* Możesz autoryzować urządzenie, gdy niektóre urządzenia zarejestrowane w usłudze przetoczyły szyfrowanie, podczas gdy inne nie. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Za pomocą programu Windows PowerShell for StorSimple zainicjowanie zmiany klucza szyfrowania danych usługi
Ten krok jest wykonywany w interfejsie programu Windows PowerShell for StorSimple na autoryzowanym urządzeniu StorSimple.

> [!NOTE]
> Żadne operacje nie można wykonać w witrynie Azure portal usługi StorSimple Manager, dopóki nie zostanie zakończone przerzucanie klucza.


Jeśli używasz konsoli szeregowej urządzenia do łączenia się z interfejsem programu Windows PowerShell, wykonaj następujące kroki.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Aby zainicjować zmianę klucza szyfrowania danych usługi
1. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz polecenie:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po pomyślnym zakończeniu polecenia cmdlet otrzymasz nowy klucz szyfrowania danych usługi. Skopiuj i zapisz ten klucz do użycia w kroku 3 tego procesu. Ten klucz będzie używany do aktualizacji wszystkich pozostałych urządzeń zarejestrowanych w usłudze StorSimple Manager.
   
   > [!NOTE]
   > Ten proces musi zostać zainicjowany w ciągu czterech godzin od autoryzowania urządzenia StorSimple.
   > 
   > 
   
   Ten nowy klucz jest następnie wysyłany do usługi, aby zostać wypchnięty do wszystkich urządzeń, które są zarejestrowane w usłudze. Alert pojawi się na pulpicie nawigacyjnym usługi. Usługa wyłączy wszystkie operacje na zarejestrowanych urządzeniach, a administrator urządzenia będzie musiał zaktualizować klucz szyfrowania danych usługi na innych urządzeniach. Jednak we/wy (hosty wysyłające dane do chmury) nie zostaną zakłócone.
   
   Jeśli masz jedno urządzenie zarejestrowane w usłudze, proces narzucenia został zakończony i możesz pominąć następny krok. Jeśli masz wiele urządzeń zarejestrowanych w usłudze, przejdź do kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Aktualizowanie klucza szyfrowania danych usługi na innych urządzeniach StorSimple
Te kroki muszą być wykonywane w interfejsie programu Windows PowerShell urządzenia StorSimple, jeśli masz wiele urządzeń zarejestrowanych w usłudze StorSimple Manager. Klucz uzyskany w kroku 2 musi służyć do aktualizacji wszystkich pozostałych storsimple urządzenia zarejestrowane w storsimple usługi Menedżera.

Wykonaj następujące czynności, aby zaktualizować szyfrowanie danych usługi na urządzeniu.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aby zaktualizować klucz szyfrowania danych usługi na urządzeniach fizycznych
1. Użyj programu Windows PowerShell for StorSimple, aby połączyć się z konsolą. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Podaj klucz szyfrowania danych usługi uzyskany w [kroku 2: Za pomocą programu Windows PowerShell for StorSimple należy zainicjować zmianę klucza szyfrowania danych usługi](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aby zaktualizować klucz szyfrowania danych usługi na wszystkich urządzeniach w chmurze 8010/8020
1. Pobierz i skonfiguruj skrypt programu PowerShell [Update-CloudApplianceServiceEncryptionKey.ps1.](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) 
2. Otwórz program PowerShell i w wierszu polecenia wpisz:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ten skrypt zapewni, że klucz szyfrowania danych usługi jest ustawiony na wszystkich urządzeniach w chmurze 8010/8020 w menedżerze urządzeń.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Obsługiwane operacje na urządzeniach z wersjami przed aktualizacją 5.0
W witrynie Azure portal obsługiwane są tylko urządzenia StorSimple z aktualizacją 5.0 lub nowszą. Urządzenia, na których są uruchomione starsze wersje, mają ograniczoną obsługę. Po migracji do witryny Azure portal, użyj poniższej tabeli, aby zrozumieć, które operacje są obsługiwane na urządzeniach z systemem wersje przed aktualizacją 5.0.

| Operacja                                                                                                                       | Obsługiwane      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Rejestrowanie urządzenia                                                                                                               | Tak            |
| Konfigurowanie ustawień urządzenia, takich jak ogólne, sieciowe i zabezpieczające                                                                | Tak            |
| Skanowanie, pobieranie i instalowanie aktualizacji                                                                                             | Tak            |
| Dezaktywacja urządzenia                                                                                                               | Tak            |
| Usuwanie urządzenia                                                                                                                   | Tak            |
| Tworzenie, modyfikowanie i usuwanie kontenera woluminu                                                                                   | Nie             |
| Tworzenie, modyfikowanie i usuwanie woluminu                                                                                             | Nie             |
| Tworzenie, modyfikowanie i usuwanie zasad tworzenia kopii zapasowych                                                                                      | Nie             |
| Wykonywanie ręcznej kopii zapasowej                                                                                                            | Nie             |
| Wykonywanie zaplanowanej kopii zapasowej                                                                                                         | Nie dotyczy |
| Przywracanie z zestawu kopii zapasowych                                                                                                        | Nie             |
| Klonowanie na urządzeniu z aktualizacją 3.0 lub nowszą <br> Urządzenie źródłowe jest uruchomione w wersji przed aktualizacją 3.0.                                | Tak            |
| Klonowanie na urządzeniu z uruchomionymi wersjami przed aktualizacją 3.0                                                                          | Nie             |
| Przełącz w stan failover jako urządzenie źródłowe <br> (z urządzenia z systemem wersji przed aktualizacją 3.0 do urządzenia z aktualizacją 3.0 lub nowszą)                                                               | Tak            |
| Przewijowanie awaryjne jako urządzenie docelowe <br> (do urządzenia z systemem wersji oprogramowania przed aktualizacją 3.0)                                                                                   | Nie             |
| Wyczyść alert                                                                                                                  | Tak            |
| Wyświetlanie zasad tworzenia kopii zapasowych, katalogu kopii zapasowych, woluminów, kontenerów woluminów, wykresów monitorowania, zadań i alertów utworzonych w klasycznym portalu | Tak            |
| Włączanie i wyłączanie kontrolerów urządzeń                                                                                              | Tak            |


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [procesie wdrażania StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Dowiedz się więcej o [zarządzaniu kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej o tym, jak [administrować urządzeniem StorSimple za pomocą usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).
