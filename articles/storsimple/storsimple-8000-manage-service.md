---
title: Wdrażanie usługi Menedżer StorSimple urządzenie na platformie Azure | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak tworzyć i usuwać usługi Menedżer StorSimple urządzenia w portalu Azure i opisano sposób zarządzania klucz rejestracji usługi.
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
ms.openlocfilehash: d6010b7ff03689588251a9649eecb412bf9f3a8d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012747"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Wdrażanie usługi Menedżer StorSimple urządzenia dla urządzeń z serii StorSimple 8000

## <a name="overview"></a>Przegląd

Usługa Menedżera urządzeń StorSimple działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Po utworzeniu usługi musisz służy do zarządzania wszystkie urządzenia, które są połączone z usługą Menedżera urządzeń StorSimple z jednej centralnej lokalizacji, minimalizując w ten sposób nakładu prac administracyjnych.

W tym samouczku opisano kroki wymagane do tworzenia, usuwania, migracji usługi i zarządzanie klucz rejestracji usługi. Informacje zawarte w tym artykule ma zastosowanie tylko do urządzeń z serii StorSimple 8000. Aby uzyskać więcej informacji dotyczących tablic wirtualnego StorSimple, przejdź do [wdrożyć usługę Menedżer StorSimple urządzenia dla macierzy wirtualnego StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure portal obsługuje urządzenia z systemem aktualizacji w wersji 5.0 lub nowszej. Jeśli urządzenie nie jest aktualny, natychmiast zainstalować aktualizacji 5. Aby uzyskać więcej informacji, przejdź do [zainstalować aktualizacji 5](storsimple-8000-install-update-5.md). 
> - Jeśli używasz urządzenia StorSimple w chmurze (8010/8020), nie można zaktualizować urządzenia chmury. Najnowszą wersję oprogramowania umożliwia utworzenie nowego urządzenia chmury z aktualizacji w wersji 5.0, a następnie przełączyć nowy urządzenia chmury utworzony. 
> - Wszystkie urządzenia z uruchomioną aktualizacją 4.0 lub wcześniejszym może wystąpić [funkcjonalność zarządzania ograniczona](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-5.0). 

## <a name="create-a-service"></a>Tworzenie usługi
Aby utworzyć usługę Menedżer urządzeń StorSimple, wymagane są:

* Subskrypcja z umową Enterprise
* Aktywne konto magazynu Microsoft Azure
* Informacji dotyczących rozliczeń, która służy do zarządzania dostępem

Dozwolone są tylko subskrypcje z umową Enterprise. Można także wygenerować domyślne konto magazynu podczas tworzenia usługi.

Jednej usługi można zarządzać wieloma urządzeniami. Jednak urządzenia nie może obejmować wiele usług. Duże przedsiębiorstwa mogą być wiele wystąpień usługi do pracy z różnych subskrypcji, organizacji lub nawet miejsc wdrożenia. 

> [!NOTE]
> Należy oddzielnego wystąpienia usługi Menedżer urządzenia StorSimple do zarządzania urządzeń z serii StorSimple 8000 i tablice wirtualne StorSimple.

Wykonaj poniższe kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Dla każdej usługi Menedżera urządzeń StorSimple istnieją następujące atrybuty:

* **Nazwa** — nazwę, która została przypisana w usłudze Menedżer StorSimple urządzenia podczas jej tworzenia. **Nie można zmienić nazwy usługi, po utworzeniu usługi. Dotyczy to również dla innych jednostek, takich jak urządzenia, kontenery woluminów, woluminów i zasad tworzenia kopii zapasowych, które nie mogą zostać zmienione w portalu Azure.**
* **Stan** — stan usługi, która może być **Active**, **tworzenie**, lub **Online**.
* **Lokalizacja** — lokalizacji geograficznej, w którym zostanie wdrożone urządzenie StorSimple.
* **Subskrypcja** — rozliczeń subskrypcji, która jest skojarzona z usługą.

## <a name="delete-a-service"></a>Usuwanie usługi

Przed usunięciem usługi, upewnij się, że żadnych podłączonych urządzeń używają go. Jeśli usługa jest w użyciu, Dezaktywuj połączonych urządzeń. Operacja Dezaktywuj sever połączenia między urządzeniem i usługi, ale zachować dane urządzenie w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć operacji. Dowolne urządzenie, które zostało przy użyciu usługi musi zostaną zresetowane do ustawień fabrycznych, zanim będzie można go używać z innej usługi. W tym scenariuszu dane lokalne na urządzeniu, a także konfiguracji, zostaną utracone.

Wykonaj poniższe kroki, aby usunąć usługę.

### <a name="to-delete-a-service"></a>Aby usunąć usługę

1. Wyszukiwanie usługi, którą chcesz usunąć. Kliknij przycisk **zasobów** ikonę, a następnie wprowadź odpowiednie terminy do wyszukania. W wynikach wyszukiwania kliknij usługi, którą chcesz usunąć.

    ![Usługa wyszukiwania do usunięcia](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Powoduje to przejście do bloku usługi Menedżer StorSimple urządzenia. Kliknij polecenie **Usuń**.

    ![Usuwanie usługi](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kliknij przycisk **tak** powiadomienia o potwierdzenie. Może upłynąć kilka minut, aż usługi do usunięcia.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi

Po pomyślnym utworzeniu usługi musisz zarejestrować urządzenie StorSimple w usłudze. Aby zarejestrować pierwszego urządzenia StorSimple, konieczne będzie klucz rejestracji usługi. Aby zarejestrować dodatkowych urządzeń z istniejącej usługi StorSimple, należy zarówno klucz rejestracji i klucza szyfrowania danych usługi (który jest generowany na pierwszym urządzeniem podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [zabezpieczenia usługi StorSimple](storsimple-8000-security.md). Możesz pobrać klucz rejestracji po zalogowaniu się do **kluczy** w bloku Menedżera urządzeń StorSimple.

Wykonaj poniższe kroki, aby pobrać klucz rejestracji usługi.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Klucz rejestracji usługi należy przechowywać w bezpiecznym miejscu. Należy tego klucza, a także klucz szyfrowania danych usługi, aby zarejestrować dodatkowych urządzeń z tą usługą. Po uzyskaniu klucza rejestracji usługi, należy skonfigurować urządzenie za pomocą programu Windows PowerShell dla StorSimple interfejsu.

Aby uzyskać więcej informacji na temat sposobu użycia tego klucza rejestracji, zobacz [krok 3: Konfigurowanie i rejestrowanie urządzenia za pomocą środowiska Windows PowerShell dla StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Wygeneruj ponownie klucz rejestracji usługi
Musisz ponownie wygenerować klucz rejestracji usługi, jeśli jest wymagane do wykonania obrotu klucza lub zmiana listy administratorów usługi. Podczas ponownego generowania klucza, nowy klucz służy tylko do celów rejestracji kolejnych urządzeń. Przez ten proces nie dotyczy to urządzeń, które zostały już zarejestrowane.

Wykonaj poniższe kroki, aby wygenerować ponownie klucz rejestracji usługi.

### <a name="to-regenerate-the-service-registration-key"></a>Można ponownie wygenerować klucz rejestracji usługi
1. W **Menedżera urządzeń StorSimple** bloku, przejdź do **zarządzania &gt;**  **klucze**.
    
    ![Blok Klucze](./media/storsimple-8000-manage-service/regenregkey2.png)

2. W **klucze** bloku, kliknij przycisk **ponownie wygenerować**.

    ![Kliknij przycisk regenerate](./media/storsimple-8000-manage-service/regenregkey3.png)
3. W **klucz rejestracji usługi Regenerate** bloku, przeglądanie nagrań akcji wymagany, gdy klucze są generowane. Wszystkie kolejne urządzenia, które są zarejestrowane w usłudze tej usługi Użyj nowego klucza rejestracji. Kliknij przycisk **ponownie wygenerować** o potwierdzenie. Użytkownik jest powiadamiany po zakończeniu ponownego wygenerowania.

    ![Potwierdź regenerate](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Pojawi się nowy klucz rejestracji usługi.

5. Skopiuj ten klucz i zapisać go do rejestracji żadnych nowych urządzeń z tą usługą.



## <a name="change-the-service-data-encryption-key"></a>Zmiana klucza szyfrowania danych usługi
Klucze szyfrowania danych usługi są używane do szyfrowania danych poufnych klienta, takich jak poświadczeń konta magazynu, które są wysyłane z usługi Menedżer StorSimple na urządzeniu StorSimple. Należy zmienić te klucze okresowo, jeśli Twoja organizacja IT zasady rotacją kluczy w urządzeniach magazynujących. Proces zmiany klucza może być nieznacznie różnić w zależności od tego, czy istnieje jednego lub wielu urządzeniach zarządzanych przez usługę Menedżer StorSimple. Aby uzyskać więcej informacji, przejdź do [StorSimple zabezpieczeń i ochrony danych](storsimple-8000-security.md).

Zmiana klucza szyfrowania danych usługi jest procesem krok 3:

1. Za pomocą skryptów środowiska Windows PowerShell dla usługi Azure Resource Manager, autoryzacji urządzeń można zmienić klucza szyfrowania danych usługi.
2. Przy użyciu programu Windows PowerShell dla urządzenia StorSimple, inicjują zmiany klucza szyfrowania danych usługi.
3. Jeśli masz więcej niż jedno urządzenie StorSimple, należy zaktualizować klucz szyfrowania danych usługi na innych urządzeniach.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1: Użyj środowiska Windows PowerShell skryptu do autoryzacji urządzeń można zmienić klucza szyfrowania danych usługi
Zazwyczaj administratora urządzenia żądanie, że administrator usługi autoryzacji urządzeń, aby zmienić klucze szyfrowania danych usługi. Następnie autoryzacji urządzeń, aby zmienić wartość klucza administratora usługi.

Ten krok jest wykonywane przy użyciu usługi Azure Resource Manager, na podstawie skryptu. Administrator usługi można wybrać urządzenie, które kwalifikują się upoważnione. Urządzenie jest następnie autoryzację do uruchomienia procesu zmiany klucza szyfrowania danych usługi. 

Aby uzyskać więcej informacji o używaniu skryptu, przejdź do [ServiceEncryptionRollover.ps1 autoryzacji](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Aby zmienić klucze szyfrowania danych usługi może być upoważnionych urządzeń?
Zanim może być upoważnionych do zainicjowania zmiany klucza szyfrowania danych usługi, urządzenie musi spełniać następujące kryteria:

* Urządzenie musi być w trybie online na kwalifikować się do autoryzacji zmiany klucza szyfrowania danych usługi.
* Ponownie po 30 minutach można autoryzować tego samego urządzenia, jeśli nie została zainicjowana zmiany klucza.
* Inne urządzenie, można autoryzować pod warunkiem, że zmiany klucza nie została zainicjowana przez wcześniej upoważnionego urządzenia. Po autoryzacji nowe urządzenie starym urządzeniem nie można zainicjować zmianę.
* Nie można autoryzować urządzenia, gdy trwa Przerzucanie klucza szyfrowania danych usługi.
* W przypadku niektórych urządzeń zarejestrowanych w usłudze ma przerzuceniem szyfrowania, podczas gdy inne nie zawierają można autoryzować urządzenia. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Użyj środowiska Windows PowerShell dla urządzenia StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi
Ten krok jest wykonywane w programie Windows PowerShell dla interfejsu StorSimple na urządzeniu StorSimple na autoryzowanych.

> [!NOTE]
> Żadne operacje nie można wykonać w portalu Azure usługi Menedżer StorSimple do czasu ukończenia przerzucania klucza.


Jeśli używasz konsoli szeregowej urządzenia do nawiązania połączenia interfejsu programu Windows PowerShell, wykonaj następujące kroki.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Aby zainicjować zmiany klucza szyfrowania danych usługi
1. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po pomyślnym ukończeniu polecenia cmdlet otrzymasz nowy klucz szyfrowania danych usługi. Skopiuj i Zapisz ten klucz do użycia w kroku 3 tego procesu. Ten klucz będzie używany do zaktualizowania wszystkich pozostałych urządzeń zarejestrowanych w usłudze Menedżer StorSimple.
   
   > [!NOTE]
   > Ten proces musi zostać zainicjowany w ciągu czterech godzin autoryzowania urządzenia StorSimple.
   > 
   > 
   
   Ten nowy klucz jest następnie wysyłane do usługi, aby zostać przeniesiony do wszystkich urządzeń, które są zarejestrowane w usłudze. Na pulpicie nawigacyjnym usługi pojawi się alert. Usługa wyłączy wszystkie operacje na zarejestrowanych urządzeniach, a następnie należy zaktualizować klucz szyfrowania danych usługi na innych urządzeniach administratora urządzenia. Jednak operacje We/Wy (wysyłanie danych do chmury hostów) nie zostanie zakłócone.
   
   Jeśli masz jednego urządzenia zarejestrowane w usłudze przerzucania proces został już ukończony i można przejść następnego kroku. Jeśli masz wiele urządzeń zarejestrowanych w usłudze, przejdź do kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Zaktualizuj klucz szyfrowania danych usługi na innych urządzeń StorSimple
Te czynności należy wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple, jeśli masz wiele urządzeń zarejestrowanych w usłudze Menedżer StorSimple. Klucz, który został uzyskany w kroku 2 może posłużyć do zaktualizowania wszystkich pozostałych urządzenia StorSimple zarejestrowane w usłudze Menedżer StorSimple.

Wykonaj poniższe kroki, aby zaktualizować usługę szyfrowania danych na urządzeniu.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aby zaktualizować klucz szyfrowania danych usługi na fizycznych urządzeniach
1. Umożliwia łączenie z konsolą programu Windows PowerShell dla StorSimple. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Podaj klucz szyfrowania danych usługi, które zostały uzyskane w [krok 2: Użyj środowiska Windows PowerShell dla urządzenia StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aby zaktualizować klucz szyfrowania danych usługi na wszystkie urządzenia 8010/8020 chmury
1. Pobierania i instalacji [CloudApplianceServiceEncryptionKey.ps1 aktualizacji](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) skrypt programu PowerShell. 
2. Otwórz program PowerShell i wpisz w wierszu polecenia:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ten skrypt zostanie upewnij się, że ten klucz szyfrowania danych usługi jest ustawiony na wszystkie urządzenia 8010/8020 chmury w Menedżerze urządzeń.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Obsługiwane operacje na urządzenia z wersjami przed aktualizacji w wersji 5.0
W portalu Azure obsługiwane są tylko urządzenia StorSimple uruchomioną aktualizacją 5.0 lub nowszej. Obsługują ograniczoną liczbę urządzeń, które są uruchomione wcześniejsze wersje. Po przeprowadzeniu migracji do portalu Azure, skorzystaj z poniższej tabeli, aby zrozumieć, jakie operacje są obsługiwane na urządzeniach z systemem wersje starsze niż 5.0 aktualizacji.

| Operacja                                                                                                                       | Obsługiwane      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Rejestrowanie urządzenia                                                                                                               | Yes            |
| Konfigurowanie ustawień urządzenia, takie jak ogólne, sieci i zabezpieczeń                                                                | Yes            |
| Skanowanie, Pobierz i zainstaluj aktualizacje                                                                                             | Yes            |
| Dezaktywować urządzenie                                                                                                               | Yes            |
| Usuwanie urządzenia                                                                                                                   | Yes            |
| Tworzenie, modyfikowanie i usuwanie kontenera woluminów                                                                                   | Nie             |
| Tworzenie, modyfikowanie i usuwanie woluminu                                                                                             | Nie             |
| Tworzenie, modyfikowanie i usuwanie zasad tworzenia kopii zapasowej                                                                                      | Nie             |
| Wykonaj kopię zapasową ręczne                                                                                                            | Nie             |
| Wykonać zaplanowaną kopię zapasową                                                                                                         | Nie dotyczy |
| Przywrócenie z zestawu kopii zapasowych                                                                                                        | Nie             |
| Klonowanie na urządzeniu z systemem aktualizacji w wersji 3.0 lub nowszy <br> Urządzenia źródłowego jest uruchomiona wersja przed 3.0 aktualizacji.                                | Yes            |
| Klonowanie na urządzeniu z systemem wersji wcześniejszych niż aktualizacja 3.0                                                                          | Nie             |
| Trybu failover jako urządzenia źródłowego <br> (z urządzenia z uruchomioną wersją, przed 3.0 aktualizacji na urządzeniu z systemem Update 3.0 i nowsze)                                                               | Yes            |
| Trybu failover jako urządzenia docelowego <br> (na urządzenie z oprogramowaniem w wersji przed Update 3.0)                                                                                   | Nie             |
| Wyczyść alert                                                                                                                  | Yes            |
| Wyświetl zasady tworzenia kopii zapasowej, katalogu kopii zapasowej woluminów, kontenery woluminów, wykresy monitorowania, zadania i alerty tworzone w klasycznym portalu | Yes            |
| Włączanie i wyłączanie kontrolery urządzeń                                                                                              | Yes            |


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [procesu wdrażania StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Dowiedz się więcej o [Zarządzanie kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej o sposobie [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).
