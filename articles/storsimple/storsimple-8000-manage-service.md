---
title: Wdrażanie usługi Menedżer urządzeń StorSimple na platformie Azure | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak tworzyć i usuwać usługi Menedżer urządzeń StorSimple w witrynie Azure portal, a w tym artykule opisano sposób zarządzania klucz rejestracji usługi.
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
ms.openlocfilehash: eb1fe69a7fb99949ac95291c33e76c1a32bf5439
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60506628"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Wdrażanie usługi Menedżer urządzeń StorSimple dla urządzeń z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Usługa Menedżer urządzeń StorSimple działa na platformie Microsoft Azure i nawiązanie połączenia z wieloma urządzeniami StorSimple. Po utworzeniu usługi, można użyć go do zarządzania wszystkie urządzenia, które są podłączone do usługi Menedżer urządzeń StorSimple z jednym, centralnym miejscu, minimalizując w ten sposób nakładu prac administracyjnych.

W tym samouczku opisano kroki wymagane do tworzenia, usuwania, migracji usługi i zarządzanie klucz rejestracji usługi. Informacje zawarte w tym artykule dotyczy tylko urządzeń z serii StorSimple 8000. Aby uzyskać więcej informacji na temat macierzach wirtualnych StorSimple, przejdź do [wdrażania usługi Menedżer urządzeń StorSimple dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure portal obsługuje urządzenia z systemem aktualizacji 5.0 lub nowszy. Jeśli urządzenie nie jest aktualny, natychmiast zainstalować aktualizacji Update 5. Aby uzyskać więcej informacji, przejdź do [instalowanie aktualizacji Update 5](storsimple-8000-install-update-5.md). 
> - Jeśli korzystasz z urządzenia StorSimple w chmurze (8010/8020), nie można zaktualizować urządzenie w chmurze. Użyj najnowszej wersji oprogramowania do tworzenia nowego urządzenia w chmurze z aktualizacją 5.0, a następnie przełączyć w tryb failover do nowego urządzenia w chmurze utworzone. 
> - Wszystkie urządzenia z aktualizacją Update 4.0 lub wcześniejszej doświadczy funkcje zarządzania ograniczonym. 

## <a name="create-a-service"></a>Tworzenie usługi
Aby utworzyć usługę Menedżer urządzeń StorSimple, musisz mieć:

* Subskrypcji z umową Enterprise Agreement
* Aktywne konto magazynu Microsoft Azure
* Rozliczeń informacje, które są używane do zarządzania dostępem

Dozwolone są tylko subskrypcji z umową Enterprise Agreement. Możesz również wygenerować domyślne konto magazynu podczas tworzenia usługi.

Pojedyncza usługa umożliwia zarządzanie wieloma urządzeniami. Jednak urządzenia nie mogą obejmować wiele usług. Duże przedsiębiorstwo może mieć wiele wystąpień usługi do pracy z różnymi subskrypcjami, organizacji lub nawet lokalizacji wdrożenia. 

> [!NOTE]
> Należy osobnych wystąpień usługi Menedżer urządzeń StorSimple do zarządzania urządzeń StorSimple serii 8000 oraz macierzy wirtualnych StorSimple.

Wykonaj poniższe kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Dla każdej usługi Menedżer urządzeń StorSimple istnieją następujące atrybuty:

* **Nazwa** — nazwy, który został przypisany do usługi Menedżer urządzeń StorSimple, gdy został on utworzony. **Nie można zmienić nazwę usługi, po utworzeniu usługi. Dotyczy to również dla innych jednostek, takich jak urządzenia, woluminy, kontenery woluminów i zasady tworzenia kopii zapasowych, które nie mogą zostać zmienione w witrynie Azure portal.**
* **Stan** — stan usługi, która może być **Active**, **tworzenie**, lub **Online**.
* **Lokalizacja** — lokalizację geograficzną, w którym zostanie wdrożone urządzenie StorSimple.
* **Subskrypcja** — rozliczeń subskrypcji, który jest skojarzony z usługą.

## <a name="delete-a-service"></a>Usuwanie usługi

Zanim usuniesz to usługa, upewnij się, że brak połączonych urządzeń jest używany. Jeśli usługa jest w użyciu, należy wyłączyć połączonych urządzeń. Operacja Dezaktywuj sever połączenia między urządzeniem a usługą, ale zachować dane urządzenia w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć operacji. Każde urządzenie, które zostało przy użyciu usługi musi zostaną zresetowane do ustawień fabrycznych, zanim będzie można używać z inną usługą. W tym scenariuszu lokalne dane na urządzeniu, a także konfigurację, zostaną utracone.

Wykonaj poniższe kroki, aby usunąć usługę.

### <a name="to-delete-a-service"></a>Aby usunąć usługi

1. Wyszukaj usługę, którą chcesz usunąć. Kliknij przycisk **zasobów** ikonę, a następnie wprowadź odpowiednie warunki wyszukiwania. W wynikach wyszukiwania kliknij usługę, którą chcesz usunąć.

    ![Usługa wyszukiwania do usunięcia](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Spowoduje to przejście do bloku usługi Menedżer urządzeń StorSimple. Kliknij przycisk **Usuń**.

    ![Usuń usługę](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kliknij przycisk **tak** w powiadomieniu o potwierdzenie. Może upłynąć kilka minut, aż usługi do usunięcia.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi

Po pomyślnym utworzeniu usługi musisz zarejestrować urządzenia StorSimple w usłudze. Aby zarejestrować pierwszego urządzenia StorSimple, potrzebujesz klucza rejestracji usługi. Celu rejestracji dodatkowych urządzeń za pomocą istniejącej usługi StorSimple, potrzebujesz klucza rejestracji i klucza szyfrowania danych usługi, (który jest generowany na pierwszym urządzeniu podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [zabezpieczenia usługi StorSimple](storsimple-8000-security.md). Możesz pobrać klucz rejestracji, uzyskując dostęp do **klucze** w bloku usługi Menedżer urządzeń StorSimple.

Wykonaj poniższe kroki, aby pobrać klucz rejestracji usługi.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Klucz rejestracji usługi należy przechowywać w bezpiecznym miejscu. Konieczne będzie ten klucz, a także klucz szyfrowania danych usługi, aby zarejestrować dodatkowych urządzeń z tą usługą. Po uzyskaniu klucza rejestracji usługi, należy skonfigurować urządzenie za pomocą programu Windows PowerShell dla usługi StorSimple interfejsu.

Aby uzyskać więcej informacji na temat sposobu użycia tego klucza rejestracji, zobacz [krok 3: Konfigurowanie i rejestrowanie urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Wygeneruj ponownie klucz rejestracji usługi
Musisz ponownie wygenerować klucz rejestracji usługi, jeśli jest wymagane do wykonania wymiany kluczy, lub jeśli zmieniono listy administratorów usługi. Podczas ponownego generowania klucza, nowy klucz jest używany tylko do celów rejestracji kolejnych urządzeń. Nie przez ten proces dotyczy to urządzeń, które zostały już zarejestrowane.

Wykonaj poniższe kroki, aby ponownie wygenerować klucz rejestracji usługi.

### <a name="to-regenerate-the-service-registration-key"></a>Aby ponownie wygenerować klucz rejestracji usługi
1. W **Menedżer urządzeń StorSimple** przejdź do bloku **zarządzania &gt;**  **klucze**.
    
    ![Blok Klucze](./media/storsimple-8000-manage-service/regenregkey2.png)

2. W **klucze** bloku kliknij **ponownie wygenerować**.

    ![Kliknij przycisk Wygeneruj ponownie klucz](./media/storsimple-8000-manage-service/regenregkey3.png)
3. W **klucz rejestracji usługi ponowne generowanie** bloku, przeglądanie akcji wymagany, gdy klucze są generowane. Wszystkie kolejne urządzenia, które zostały zarejestrowane przy użyciu tej usługi użyj nowy klucz rejestracji. Kliknij przycisk **ponownie wygenerować** o potwierdzenie. Otrzymasz powiadomienie po zakończeniu ponownego wygenerowania.

    ![Upewnij się, Wygeneruj ponownie klucz](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Pojawi się nowy klucz rejestracji usługi.

5. Skopiuj ten klucz i zapisać go do rejestrowania nowych urządzeń z tą usługą.



## <a name="change-the-service-data-encryption-key"></a>Zmień klucz szyfrowania danych usługi
Klucze szyfrowania danych usługi są używane do szyfrowania danych poufnych klienta, takich jak poświadczenia konta magazynu, które są wysyłane z usługi StorSimple Manager z urządzeniem StorSimple. Należy okresowo zmieniane tych kluczy, jeśli Twoja organizacja IT ma zasady wymiany kluczy w urządzeniach magazynujących. Proces zmiany klucza może być nieco inne w zależności od tego, czy istnieje jednego lub wielu urządzeń zarządzanych przez usługę StorSimple Manager. Aby uzyskać więcej informacji, przejdź do [StorSimple zabezpieczeń i ochrony danych](storsimple-8000-security.md).

Zmienianie klucza szyfrowania danych usługi to proces, krok 3:

1. Za pomocą skryptów środowiska Windows PowerShell dla usługi Azure Resource Manager, autoryzować urządzenia, aby zmienić klucz szyfrowania danych usługi.
2. Używanie programu Windows PowerShell dla usługi StorSimple, zainicjować zmiany klucza szyfrowania danych usługi.
3. Jeśli masz więcej niż jednego urządzenia StorSimple, należy zaktualizować klucz szyfrowania danych usługi na innych urządzeniach.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1: Użyj skryptu programu Windows PowerShell można autoryzować urządzenia, aby zmienić klucz szyfrowania danych usługi
Zazwyczaj administrator urządzenia będzie żądać, że administrator usługi autoryzacji urządzeń, aby zmienić klucze szyfrowania danych usługi. Administrator usługi będą następnie autoryzować urządzenia do zmiany klucza.

Ten krok jest wykonywane przy użyciu skryptu opartych na usłudze Azure Resource Manager. Administrator usługi może wybrać urządzenie, które jest uprawniona do upoważnić. Urządzenie jest następnie autoryzowana do uruchomienia proces zmiany klucza szyfrowania danych usługi. 

Aby uzyskać więcej informacji na temat za pomocą skryptu, przejdź do [ServiceEncryptionRollover.ps1 autoryzacji](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Urządzeń, które może być autoryzowane, aby zmienić klucze szyfrowania danych usługi?
Urządzenie musi spełniać następujące kryteria, zanim może być autoryzowane można zainicjować zmiany klucza szyfrowania danych usługi:

* Urządzenie musi być w trybie online, aby zakwalifikować się do autoryzacji zmian klucza szyfrowania danych usługi.
* Tym samym urządzeniu może autoryzować ponownie po 30 minutach, jeśli nie zainicjowano zmian klucza.
* Możesz autoryzować innego urządzenia, pod warunkiem, że zmiana klucza nie została zainicjowana przez autoryzowani wcześniej urządzenia. Po autoryzacji nowe urządzenie starym urządzeniem nie można zainicjować zmiany.
* Nie można autoryzować urządzenia, podczas przerzucania klucza szyfrowania danych usługi jest w toku.
* W przypadku niektórych urządzeń zarejestrowanych w usłudze mają przenoszone szyfrowania, podczas gdy inne osoby nie można autoryzować urządzenia. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Zmiany w programie Windows PowerShell dla usługi StorSimple zainicjować klucza szyfrowania danych usługi
Ten krok jest wykonywane w programie Windows PowerShell dla interfejsu usługi StorSimple na urządzeniu StorSimple autoryzowanych.

> [!NOTE]
> Nie można wykonywać operacji w witrynie Azure portal usługi Menedżer StorSimple do momentu ukończenia przerzucania klucza.


Jeśli używasz konsoli szeregowej urządzenia połączyć się z interfejsu programu Windows PowerShell, wykonaj następujące kroki.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Aby zainicjować zmiany klucza szyfrowania danych usługi
1. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz polecenie:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po pomyślnym ukończeniu polecenia cmdlet, zostanie wyświetlony nowy klucz szyfrowania danych usługi. Skopiuj i Zapisz ten klucz do użycia w kroku 3 tego procesu. Ten klucz będzie używany do zaktualizowania wszystkich pozostałych urządzeń zarejestrowanych za pomocą usługi StorSimple Manager.
   
   > [!NOTE]
   > Ten proces musi zostać zainicjowany w ciągu czterech godzin autoryzacji urządzenia StorSimple.
   > 
   > 
   
   Ten nowy klucz zostanie przesłany do usługi ma zostać wypchnięty na wszystkie urządzenia, które są zarejestrowane w usłudze. Alert pojawi się na pulpicie nawigacyjnym usługi. Usługa spowoduje wyłączenie wszystkich operacji na zarejestrowanych urządzeniach, a administrator urządzenia następnie należy zaktualizować klucz szyfrowania danych usługi na innych urządzeniach. Jednak operacje We/Wy (hosty wysyłające dane do chmury) nie zostanie przerwane.
   
   W przypadku pojedynczego urządzenia zarejestrowane w usłudze proces przerzucania został już ukończony i można pominąć następny krok. Jeśli masz wiele urządzeń zarejestrowanych w usłudze, przejdź do kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Zaktualizuj klucz szyfrowania danych usługi na innych urządzeniach StorSimple
Te kroki można wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple, jeśli masz wiele urządzeń zarejestrowanych w usłudze StorSimple Manager. Klucz, który został uzyskany w kroku 2 musi służyć do aktualizacji wszystkich pozostałych StorSimple urządzenia zarejestrowane przy użyciu usługi StorSimple Manager.

Wykonaj poniższe kroki, aby zaktualizować szyfrowania danych usługi na urządzeniu.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aby zaktualizować klucz szyfrowania danych usługi na urządzeniach fizycznych
1. Umożliwia łączenie z konsolą programu Windows PowerShell dla usługi StorSimple. Wybierz opcję 1, aby zalogować się z pełnym dostępem.
2. W wierszu polecenia wpisz polecenie:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Podaj klucz szyfrowania danych usługi uzyskany w [krok 2: Użyj programu Windows PowerShell dla usługi StorSimple można zainicjować zmiany klucza szyfrowania danych usługi](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aby zaktualizować klucz szyfrowania danych usługi na wszystkich urządzeń chmury 8010/8020
1. Pobierz i skonfiguruj [CloudApplianceServiceEncryptionKey.ps1 aktualizacji](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) skrypt programu PowerShell. 
2. Otwórz program PowerShell i wpisz w wierszu polecenia:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ten skrypt spowoduje upewnij się, że ten klucz szyfrowania danych usługi jest ustawiona na wszystkich urządzeń chmury 8010/8020 w Menedżerze urządzeń.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Obsługiwane operacje na urządzeniach z systemem w wersjach wcześniejszych niż 5.0 aktualizacji
W witrynie Azure portal są obsługiwane tylko na urządzeniach StorSimple z aktualizacją Update w wersji 5.0 lub nowszy. Urządzenia, które są uruchomione starsze wersje obsługują ograniczoną liczbę. Po przeprowadzeniu migracji do witryny Azure portal, należy użyć poniższej tabeli, aby zrozumieć, jakie operacje są obsługiwane na urządzeniach z systemem w wersjach wcześniejszych niż 5.0 aktualizacji.

| Operacja                                                                                                                       | Obsługiwane      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Rejestrowanie urządzenia                                                                                                               | Tak            |
| Konfigurowanie ustawień urządzenia, takich jak ogólne, sieci i zabezpieczeń                                                                | Tak            |
| Skanowanie, Pobierz i zainstaluj aktualizacje                                                                                             | Tak            |
| Dezaktywowanie urządzenia                                                                                                               | Yes            |
| Usuwanie urządzenia                                                                                                                   | Yes            |
| Tworzenie, modyfikowanie i usuwanie kontenera woluminów                                                                                   | Nie             |
| Tworzenie, modyfikowanie i usuwanie woluminu                                                                                             | Nie             |
| Tworzenie, modyfikowanie i usuwanie zasad kopii zapasowych                                                                                      | Nie             |
| Utwórz kopię zapasową ręczne                                                                                                            | Nie             |
| Wykonaj zaplanowane tworzenie kopii zapasowej                                                                                                         | Nie dotyczy |
| Przywracanie z kopii zapasowych                                                                                                        | Nie             |
| Sklonować na urządzeniu z systemem aktualizacji w wersji 3.0 lub nowszy <br> Urządzenie źródłowe jest uruchomiony w wersji wcześniejszej niż Update w wersji 3.0.                                | Tak            |
| Sklonować na urządzeniu z systemem w wersjach wcześniejszych niż Update w wersji 3.0                                                                          | Nie             |
| Tryb failover, jak urządzenie źródłowe <br> (z urządzenia z wersją przed Update w wersji 3.0 na urządzeniu z systemem Update w wersji 3.0 i nowszych)                                                               | Yes            |
| Pracy awaryjnej jako urządzenie docelowe <br> (na urządzeniu z systemem w wersji wcześniejszej niż 3.0 aktualizacji oprogramowania)                                                                                   | Nie             |
| Wyczyść alert                                                                                                                  | Yes            |
| Wyświetl zasady tworzenia kopii zapasowych, wykaz kopii zapasowych, woluminy, kontenery woluminów, wykresy monitorowania, zadania i alerty utworzone w portalu klasycznym | Yes            |
| Włączanie i wyłączanie kontrolery urządzeń                                                                                              | Yes            |


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [procesu wdrażania usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Dowiedz się więcej o [Zarządzanie kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej na temat [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
