---
title: Jak zainstalować Linux główny serwer docelowy dla trybu failover z platformy Azure do środowiska lokalnego | Dokumentacja firmy Microsoft
description: Przed ponownej ochrony maszyny wirtualnej systemu Linux, należy Linux główny serwer docelowy. Dowiedz się, jak zainstalować jedną.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauravd
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 27b38eafd9ab5da11d66ede4d446a4952e30c534
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768599"
---
# <a name="install-a-linux-master-target-server"></a>Instalowanie serwera docelowego elementu głównego systemu Linux
Po przejścia w tryb failover maszyn wirtualnych na platformie Azure, możesz w trybie wstecz maszyn wirtualnych do lokacji lokalnej. Aby wykonaj powrót po awarii, musisz Włącz ponownie ochronę maszyny wirtualnej z platformy Azure do lokacji lokalnej. Ten proces wymaga lokalnego serwera docelowego elementu głównego, aby odbierać ruch. 

Chronione maszyny wirtualnej w przypadku maszyny wirtualnej systemu Windows, należy główny cel systemu Windows. W przypadku maszyny wirtualnej systemu Linux należy główny cel systemu Linux. Przeczytaj poniższe kroki, aby dowiedzieć się, jak utworzyć i zainstalować główny cel systemu Linux.

> [!IMPORTANT]
> Począwszy od wersji 9.10.0 główny serwer docelowy, najnowsze głównego serwera docelowego można zainstalować tylko na serwerze Ubuntu 16.04. Nowe instalacje nie są dozwolone w CentOS6.6 serwerów. Jednak można kontynuować stare serwerów główny cel został uaktualniony przy użyciu 9.10.0 wersji.

## <a name="overview"></a>Przegląd
Ten artykuł zawiera instrukcje dotyczące sposobu instalowania główny cel systemu Linux.

Opublikuj komentarze lub pytania na końcu tego artykułu lub na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wybrać hosta, na którym chcesz wdrożyć główny cel, ustal, jeśli powrót po awarii będzie istniejących na lokalnej maszynie wirtualnej lub nowej maszyny wirtualnej. 
    * Dla istniejącej maszyny wirtualnej hosta docelowego elementu głównego powinien mieć dostęp do magazynów danych maszyny wirtualnej.
    * Jeśli nie istnieje na lokalnej maszynie wirtualnej (w przypadku odzyskiwania do alternatywnej lokalizacji), powrotu po awarii maszyny wirtualnej zostanie utworzony na tym samym hoście jako głównego celu. Można wybrać dowolnego hosta ESXi, aby zainstalować główny cel.
* Główny cel powinna być w sieci, który może komunikować się z serwerem przetwarzania i serwer konfiguracji.
* Wersja docelowego elementu głównego musi być późniejsza niż w przypadku wersji serwera przetwarzania i serwer konfiguracji. Na przykład jeśli wersja serwera konfiguracji jest 9.4, wersja docelowego elementu głównego można 9.4 lub 9.3, ale nie 9.5.
* Główny cel może być tylko maszyny wirtualne VMware, a nie serwera fizycznego.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ustawianie rozmiaru wytyczne dotyczące tworzenia głównego serwera docelowego

Utwórz zgodnie z wytycznymi następujące zmiany rozmiaru docelowego elementu głównego:
- **Pamięć RAM**: 6 GB lub więcej
- **Rozmiar dysku systemu operacyjnego**: 100 GB lub więcej (zainstalować CentOS6.6)
- **Dodatkowy dysk rozmiar dysku przechowywania**: 1 TB
- **Rdzenie Procesora**: rdzenie 4 lub więcej

Obsługiwane są następujące jądra Ubuntu obsługiwane.


|Seria jądra  |Obsługuje maksymalnie  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Wdrażanie serwera docelowego elementu głównego

### <a name="install-ubuntu-16042-minimal"></a>Zainstaluj Ubuntu 16.04.2 minimalnego

Wziąć pod uwagę następujące kroki, aby zainstalować 64-bitowym systemie operacyjnym Ubuntu 16.04.2.

1.   Przejdź do [pobrać link](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), wybierz najbliższą anddownload dublowany ISO Ubuntu 16.04.2 minimalnego 64-bitowych.
Zachowaj ISO Ubuntu 16.04.2 minimalnego 64-bitowe w stacji dysków DVD i uruchomić system.

1.  Wybierz **angielski** jako preferowany język, a następnie wybierz **Enter**.
    
    ![Wybierz język](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)
1. Wybierz **zainstalować Ubuntu Server**, a następnie wybierz **Enter**.

    ![Wybierz opcję instalacji Ubuntu Server](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

1.  Wybierz **angielski** jako preferowany język, a następnie wybierz **Enter**.

    ![Wybierz preferowany język angielski](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

1. Wybierz odpowiednią opcję z **strefy czasowej** listy Opcje, a następnie wybierz **Enter**.

    ![Wybierz odpowiednią strefę czasową](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

1. Wybierz **nr** (opcja domyślna), a następnie wybierz **Enter**.

     ![Konfigurowanie klawiatury](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)
1. Wybierz **języka angielskiego (US)** jako kraju pochodzenia dla klawiatury, a następnie wybierz **Enter**.

1. Wybierz **języka angielskiego (US)** jako układu klawiatury, a następnie wybierz **Enter**.

1. Wprowadź nazwę hosta serwera w **Hostname** , a następnie wybierz **Kontynuuj**.

1. Aby utworzyć konto użytkownika, wprowadź nazwę użytkownika, a następnie wybierz **Kontynuuj**.

      ![Tworzenie konta użytkownika](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

1. Wprowadź hasło dla nowego konta użytkownika, a następnie wybierz **Kontynuuj**.

1.  Potwierdź hasło dla nowego użytkownika, a następnie wybierz **Kontynuuj**.

    ![Potwierdzenie hasła](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

1.  W następnej wybór szyfrowania katalogu macierzystego, wybierz **nr** (opcja domyślna), a następnie wybierz **Enter**.

1. Jeśli strefy czasowej, która jest wyświetlana jest poprawny, wybierz **tak** (opcja domyślna), a następnie wybierz **Enter**. Aby ponownie skonfigurować strefę czasową, wybierz **nr**.

1. Wybierz z opcji partycjonowania — metoda **z przewodnikiem - Użyj cały dysk**, a następnie wybierz **Enter**.

     ![Wybierz opcję partycjonowania — metoda](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

1.  Wybierz odpowiedni dysk z **wybierz dysk do partycji** opcje, a następnie wybierz **Enter**.

    ![Wybierz dysk](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

1.  Wybierz **tak** Aby zapisać zmiany na dysku, a następnie wybierz **Enter**.

1.  Przy wyborze Konfiguracja serwera proxy, zaznacz opcję domyślną, wybierz **Kontynuuj**, a następnie wybierz **Enter**.

     ![Wybierz opcję domyślną](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

1.  Wybierz **nie aktualizacje automatyczne** opcji w zaznaczeniu uaktualnień w systemie zarządzania, a następnie wybierz **Enter**.

     ![Wybierz sposób zarządzania uaktualnień](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

    > [!WARNING]
    > Azure Site Recovery głównego serwera docelowego wymaga bardzo określonej wersji Ubuntu, dlatego należy upewnić się, że jądra, które aktualizacje są wyłączone dla maszyny wirtualnej. Jeśli są one włączone regularne uaktualnienia spowodować głównego serwera docelowego do nieprawidłowego działania. Upewnij się, że wybrano **nie aktualizacje automatyczne** opcji.

1.  Wybierz opcje domyślne. Jeśli chcesz openSSH dla połączenia SSH, wybierz opcję **OpenSSH serwera** opcji, a następnie wybierz **Kontynuuj**.

    ![Wybierz oprogramowanie](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

1. Selction instalowania moduł ładujący rozruchu CHODNIKÓW, wybierz **tak**, a następnie wybierz **Enter**.

1. Wybierz odpowiednie urządzenie do instalacji moduł ładujący rozruchu (najlepiej **/dev/sda**), a następnie wybierz **Enter**.

1. Wybierz **Kontynuuj**, a następnie wybierz **Enter** do zakończenia instalacji.

    ![Zakończenie instalacji](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

1. Po zakończeniu instalacji, zaloguj się do maszyny Wirtualnej z nowymi poświadczeniami użytkownika. (Zobacz **kroku 10** Aby uzyskać więcej informacji.)

1. Wykonaj kroki opisane na poniższym zrzucie ekranu, aby ustawić hasło użytkownika głównego. Następnie zaloguj się jako użytkownik ROOT.

    ![Ustaw główny hasło użytkownika](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Skonfiguruj maszynę jako główny serwer docelowy

Aby uzyskać identyfikator dla każdego dysku twardego SCSI na maszynie wirtualnej systemu Linux **dysku. EnableUUID = TRUE** parametru musi być włączona. Aby włączyć ten parametr, należy wykonać następujące czynności:

1. Zamknij maszynę wirtualną.

2. Kliknij prawym przyciskiem myszy wpis dla maszyny wirtualnej w okienku po lewej stronie, a następnie wybierz **Edytuj ustawienia**.

3. Wybierz **opcje** kartę.

4. W okienku po lewej stronie wybierz **zaawansowane** > **ogólne**, a następnie wybierz **parametry konfiguracji** przycisk w prawej dolnej części ekranu.

    ![Karta Opcje](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    **Parametry konfiguracji** opcja jest niedostępna, gdy komputer jest uruchomiony. Aby uaktywnić na tej karcie, zamknij maszynę wirtualną.

5. Zobacz, czy wiersz z **dysku. EnableUUID** już istnieje.

    - Jeśli wartość istnieje i ma ustawioną wartość **False**, zmień wartość na **True**. (Wartości nie jest rozróżniana).

    - Jeśli wartość istnieje i ma ustawioną wartość **True**, wybierz pozycję **anulować**.

    - Jeśli wartość nie istnieje, wybierz **Dodaj wiersz**.

    - W kolumnie Nazwa Dodaj **dysku. EnableUUID**, a następnie ustaw wartość **TRUE**.

    ![Sprawdzanie Określa, czy na dysku. EnableUUID już istnieje.](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>Wyłącz uaktualnienia jądra

Azure Site Recovery główny serwer docelowy wymaga określonych wersji systemu Ubuntu, upewnij się, że uaktualnienia jądra są wyłączone dla maszyny wirtualnej. Jeśli uaktualnienia jądra są włączone, może spowodować głównego serwera docelowego do nieprawidłowego działania.

#### <a name="download-and-install-additional-packages"></a>Pobieranie i instalowanie dodatkowych pakietów

> [!NOTE]
> Upewnij się, że masz połączenie z Internetem na pobieranie i instalowanie dodatkowych pakietów. Jeśli nie masz połączenie z Internetem, musisz ręcznie te pakiety RPM Znajdź i zainstaluj je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Pobierz Instalator dla Instalatora

Jeśli urządzenie docelowe wzorca ma połączenie z Internetem, można użyć następujące kroki pobierania Instalatora. W przeciwnym razie należy skopiować Instalator z serwera przetwarzania, a następnie zainstaluj go.

#### <a name="download-the-master-target-installation-packages"></a>Pobierz pakiety instalacyjne docelowego elementu głównego

[Pobierz najnowsze bitów instalacji docelowego elementu głównego Linux](https://aka.ms/latestlinuxmobsvc).

Aby pobrać za pomocą systemu Linux, wpisz:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Upewnij się, można pobrać i Rozpakuj Instalatora w katalogu macierzystym. Jeśli plików do **/usr/Local**, instalacja zakończy się niepowodzeniem.


#### <a name="access-the-installer-from-the-process-server"></a>Dostęp do Instalatora z serwera przetwarzania

1. Na serwerze przetwarzania, przejdź do **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository C:\Program Files (x86)**.

2. Skopiuj plik Instalatora wymagane z serwera przetwarzania i zapisz go jako **latestlinuxmobsvc.tar.gz** w katalogu macierzystym.


### <a name="apply-custom-configuration-changes"></a>Zastosuj zmiany konfiguracji niestandardowej

Aby zastosować zmiany konfiguracji niestandardowej, należy użyć następujące kroki:


1. Uruchom następujące polecenie, aby untar dane binarne.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Zrzut ekranu przedstawiający polecenia do uruchomienia](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Uruchom następujące polecenie, aby udzielić zezwolenia.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Uruchom następujące polecenie, aby uruchomić skrypt.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Uruchom skrypt tylko raz na serwerze. Następnie należy wyłączyć serwer. Uruchom ponownie serwer, po dodaniu dysku, zgodnie z opisem w następnej sekcji.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Dodaj dysk przechowywania do docelowego elementu głównego systemu Linux maszyny wirtualnej

Wykonaj następujące kroki, aby utworzyć dysk przechowywania:

1. Dołącz nowy dysk 1 TB do maszyny wirtualnej systemu Linux główny cel, a następnie uruchomienie maszyny.

2. Użyj **wielościeżkowe -ll** polecenie, aby dowiedzieć się wielościeżkowe identyfikator dysku przechowywania.
    
     `multipath -ll`

        ![The multipath ID of the retention disk](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Sformatuj dysk, a następnie utworzyć systemu plików na nowym dysku.

    
    `mkfs.ext4 /dev/mapper/<Retention disk's multipath id>`
    
    ![Tworzenie system plików na dysku](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Po utworzeniu systemu plików, należy zainstalować dysk przechowywania.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```
    ![Instalowanie dysku przechowywania](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Utwórz **fstab** wejścia, należy zainstalować dysk przechowywania, przy każdym uruchomieniu systemu.
    
    `vi /etc/fstab`
    
    Wybierz **Wstaw** aby rozpocząć edytowanie pliku. Utwórz nowy wiersz, a następnie wstaw następujący tekst. Edytuj identyfikator wielościeżkowe dysku, na podstawie Identyfikatora wielościeżkowe wyróżnione z poprzednie polecenie.

     **/dev/mapowania/ <Retention disks multipath id> /mnt/rw ext4 przechowywania 0 0**

    Wybierz **Esc**, a następnie wpisz **: wq** (zapisać i zamknąć) aby zamknąć okno edytora.

### <a name="install-the-master-target"></a>Zainstaluj główny cel

> [!IMPORTANT]
> Wersja serwera docelowego elementu głównego musi być późniejsza niż w przypadku wersji serwera przetwarzania i serwer konfiguracji. Jeśli ten warunek nie jest spełniony, ponownej ochrony zakończy się pomyślnie, ale replikacja nie powiedzie się.


> [!NOTE]
> Przed zainstalowaniem głównego serwera docelowego sprawdź, czy **/etc/hosts** pliku na maszynie wirtualnej zawiera wpisy, które mapują lokalną nazwą hosta do adresów IP, które są skojarzone z wszystkich kart sieciowych.

1. Kopiuj hasło z **Recovery\private\connection.passphrase witryny Azure C:\ProgramData\Microsoft** na serwerze konfiguracji. Następnie zapisz go jako **passphrase.txt** w tym samym katalogu lokalnego, uruchamiając następujące polecenie:

    `echo <passphrase> >passphrase.txt`

    Przykład: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Zanotuj adres IP serwera konfiguracji. Uruchom następujące polecenie, aby zainstalować głównego serwera docelowego i Zarejestruj serwer z serwerem konfiguracji.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Przykład: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Poczekaj na zakończenie skryptu. Czy główny cel rejestruje pomyślnie, docelowego elementu głównego są dostępne w **infrastruktura usługi Site Recovery** strony portalu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Zainstaluj główny cel, używając instalacja interakcyjna

1. Uruchom następujące polecenie, aby zainstalować główny cel. W roli agenta, wybierz opcję **docelowego elementu głównego**.

    ```
    ./install
    ```

2. Wybierz domyślną lokalizację instalacji, a następnie wybierz **Enter** aby kontynuować.

    ![Wybieranie domyślnej lokalizacji instalacji główny serwer docelowy](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Po zakończeniu instalacji należy zarejestrować serwer konfiguracji przy użyciu wiersza polecenia.

1. Zanotuj adres IP serwera konfiguracji. Należy go w następnym kroku.

2. Uruchom następujące polecenie, aby zainstalować głównego serwera docelowego i Zarejestruj serwer z serwerem konfiguracji.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Przykład: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Poczekaj na zakończenie skryptu. Czy główny cel został pomyślnie zarejestrowany, docelowego elementu głównego są dostępne w **infrastruktura usługi Site Recovery** strony portalu.


### <a name="install-vmware-tools-on-the-master-target-server"></a>Zainstaluj narzędzia VMware na głównym serwerze docelowym

Musisz zainstalować narzędzia VMware w docelowym elemencie głównym, aby go odnaleźć w magazynie danych. Jeśli narzędzia nie są zainstalowane, ekranu ponownej ochrony nie ma na liście w magazynie danych. Po zakończeniu instalacji narzędzi VMware należy ponownie uruchomić.

### <a name="upgrade-the-master-target-server"></a>Uaktualnienia głównego serwera docelowego

Uruchom Instalatora. Automatycznie wykrywa, że agent jest zainstalowany w głównym celu. Aby przeprowadzić uaktualnienie, wybierz **Y**.  Po zakończeniu instalacji sprawdź wersję programu docelowego elementu głównego zainstalowane za pomocą następującego polecenia:

`cat /usr/local/.vx_version`


Zostanie wyświetlone **wersji** pola zwraca numer wersji głównego celu.

## <a name="common-issues"></a>Typowe problemy

* Upewnij się, że nie należy włączać narzędzia Storage vMotion na wszystkie składniki zarządzania, takie jak głównego celu. Jeśli po pomyślnej ponownej ochrony jest przenoszony główny cel, nie można odłączyć dysków maszyny wirtualnej (VMDKs). W takim przypadku powrotu po awarii nie powiedzie się.

* Główny cel nie powinna mieć żadnych migawek na maszynie wirtualnej. W przypadku migawki powrotu po awarii nie powiedzie się.

* Z powodu niektóre konfiguracje niestandardowe kart interfejsu sieciowego jest wyłączona podczas uruchamiania, a nie można zainicjować agenta docelowego elementu głównego. Upewnij się, że następujące właściwości są ustawione poprawnie. Sprawdź tych właściwości w sieci Ethernet karty /etc/sysconfig/network-scripts/ifcfg pliku-eth *.
    * BOOTPROTO=dhcp
    * ONBOOT = tak


## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu instalacji i rejestracji docelowego elementu głównego widać docelowego elementu głównego są wyświetlane na **docelowego elementu głównego** sekcji **infrastruktura usługi Site Recovery**, w konfiguracji Omówienie serwera.

Teraz można przystąpić do [przełączonej](site-recovery-how-to-reprotect.md), a następnie powrót po awarii.

