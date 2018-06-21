---
title: Instalowanie serwera głównego celu Linux do trybu failover z platformy Azure do lokalnego | Dokumentacja firmy Microsoft
description: Przed ponownej ochrony maszyny wirtualnej systemu Linux, należy Linux głównego serwera docelowego. Dowiedz się, jak zainstalować jedną.
author: nsoneji
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: nisoneji
ms.openlocfilehash: 338a104de6fadeff842e6e135db395758c8f8dce
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285937"
---
# <a name="install-a-linux-master-target-server"></a>Zainstaluj serwer główny cel systemu Linux
Po przejścia w tryb failover maszyn wirtualnych na platformie Azure, możesz w trybie wstecz maszyn wirtualnych do lokacji lokalnej. Aby wykonaj powrót po awarii, musisz Włącz ponownie ochronę maszyny wirtualnej z platformy Azure do lokacji lokalnej. W przypadku tego procesu należy lokalny główny serwer docelowy do odbierania ruchu. 

Chronione maszyny wirtualnej w przypadku maszyny wirtualnej systemu Windows, należy główny cel systemu Windows. W przypadku maszyny wirtualnej systemu Linux należy główny cel systemu Linux. Przeczytaj poniższe kroki, aby dowiedzieć się, jak utworzyć i zainstalować główny cel systemu Linux.

> [!IMPORTANT]
> Począwszy od wersji 9.10.0 główny serwer docelowy, najnowsze głównego serwera docelowego można zainstalować tylko na serwerze Ubuntu 16.04. Nowe instalacje nie są dozwolone w CentOS6.6 serwerów. Jednak możesz uaktualnić starego głównego serwerów docelowych przy użyciu 9.10.0 wersji.

## <a name="overview"></a>Przegląd
Ten artykuł zawiera instrukcje dotyczące sposobu instalowania główny cel systemu Linux.

Opublikuj komentarze lub pytania na końcu tego artykułu lub na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wybrać host, na którym chcesz wdrożyć główny cel, ustal, jeśli powrót po awarii będzie istniejących na lokalnej maszynie wirtualnej lub nowej maszyny wirtualnej. 
    * Dla istniejącej maszyny wirtualnej host główny cel powinien mieć dostęp do magazynów danych maszyny wirtualnej.
    * Jeśli nie istnieje na lokalnej maszynie wirtualnej (w przypadku odzyskiwania do alternatywnej lokalizacji), powrotu po awarii maszyny wirtualnej zostanie utworzony na tym samym hoście jako głównego celu. Można wybrać dowolnego hosta ESXi, aby zainstalować główny cel.
* Główny cel powinna być w sieci, który może komunikować się z serwerem przetwarzania i serwer konfiguracji.
* Wersja głównego elementu docelowego musi być późniejsza niż w przypadku wersji serwera przetwarzania i serwer konfiguracji. Na przykład jeśli wersja serwera konfiguracji jest 9.4, wersja głównego celu może być 9.4 lub 9.3, ale nie 9.5.
* Główny cel może być tylko maszyny wirtualne VMware, a nie serwera fizycznego.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ustawianie rozmiaru wytyczne dotyczące tworzenia głównego serwera docelowego

Tworzenie głównego celu zgodnie z wytycznymi następujące zmiany rozmiaru:
- **Pamięć RAM**: 6 GB lub więcej
- **Rozmiar dysku systemu operacyjnego**: 100 GB lub więcej (zainstalowania systemu operacyjnego)
- **Dodatkowy dysk rozmiar dysku przechowywania**: 1 TB
- **Rdzenie Procesora**: rdzenie 4 lub więcej

Obsługiwane są następujące jądra Ubuntu obsługiwane.


|Seria jądra  |Obsługuje maksymalnie  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Wdrażanie główny serwer docelowy

### <a name="install-ubuntu-16042-minimal"></a>Zainstaluj Ubuntu 16.04.2 minimalnego

Wziąć pod uwagę następujące kroki, aby zainstalować 64-bitowym systemie operacyjnym Ubuntu 16.04.2.

1.   Przejdź do [pobrać link](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), wybierz najbliższą anddownload dublowany ISO Ubuntu 16.04.2 minimalnego 64-bitowych.
Zachowaj ISO Ubuntu 16.04.2 minimalnego 64-bitowe w stacji dysków DVD i uruchomić system.

1.  Wybierz **angielski** jako preferowany język, a następnie wybierz **Enter**.
    
    ![Wybierz język](./media/vmware-azure-install-linux-master-target/image1.png)
1. Wybierz **zainstalować Ubuntu Server**, a następnie wybierz **Enter**.

    ![Wybierz opcję instalacji Ubuntu Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Wybierz **angielski** jako preferowany język, a następnie wybierz **Enter**.

    ![Wybierz preferowany język angielski](./media/vmware-azure-install-linux-master-target/image3.png)

1. Wybierz odpowiednią opcję z **strefy czasowej** listy Opcje, a następnie wybierz **Enter**.

    ![Wybierz odpowiednią strefę czasową](./media/vmware-azure-install-linux-master-target/image4.png)

1. Wybierz **nr** (opcja domyślna), a następnie wybierz **Enter**.

     ![Konfigurowanie klawiatury](./media/vmware-azure-install-linux-master-target/image5.png)
1. Wybierz **języka angielskiego (US)** jako kraju pochodzenia dla klawiatury, a następnie wybierz **Enter**.

1. Wybierz **języka angielskiego (US)** jako układu klawiatury, a następnie wybierz **Enter**.

1. Wprowadź nazwę hosta serwera w **Hostname** , a następnie wybierz **Kontynuuj**.

1. Aby utworzyć konto użytkownika, wprowadź nazwę użytkownika, a następnie wybierz **Kontynuuj**.

      ![Tworzenie konta użytkownika](./media/vmware-azure-install-linux-master-target/image9.png)

1. Wprowadź hasło dla nowego konta użytkownika, a następnie wybierz **Kontynuuj**.

1.  Potwierdź hasło dla nowego użytkownika, a następnie wybierz **Kontynuuj**.

    ![Potwierdzenie hasła](./media/vmware-azure-install-linux-master-target/image11.png)

1.  W następnej wybór szyfrowania katalogu macierzystego, wybierz **nr** (opcja domyślna), a następnie wybierz **Enter**.

1. Jeśli strefy czasowej, która jest wyświetlana jest poprawny, wybierz **tak** (opcja domyślna), a następnie wybierz **Enter**. Aby ponownie skonfigurować strefę czasową, wybierz **nr**.

1. Wybierz z opcji partycjonowania — metoda **z przewodnikiem - Użyj cały dysk**, a następnie wybierz **Enter**.

     ![Wybierz opcję partycjonowania — metoda](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Wybierz odpowiedni dysk z **wybierz dysk do partycji** opcje, a następnie wybierz **Enter**.

    ![Wybierz dysk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Wybierz **tak** Aby zapisać zmiany na dysku, a następnie wybierz **Enter**.

    ![Wybierz opcję domyślną](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Przy wyborze Konfiguracja serwera proxy, zaznacz opcję domyślną, wybierz **Kontynuuj**, a następnie wybierz **Enter**.
     
     ![Wybierz sposób zarządzania uaktualnień](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Wybierz **nie aktualizacje automatyczne** opcji w zaznaczeniu uaktualnień w systemie zarządzania, a następnie wybierz **Enter**.

     ![Wybierz sposób zarządzania uaktualnień](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Azure Site Recovery główny serwer docelowy wymaga bardzo określonej wersji Ubuntu, dlatego należy upewnić się, że jądra, które aktualizacje są wyłączone dla maszyny wirtualnej. Jeśli są one włączone regularne uaktualnienia spowodować główny serwer docelowy do nieprawidłowego działania. Upewnij się, że wybrano **nie aktualizacje automatyczne** opcji.

1.  Wybierz opcje domyślne. Jeśli chcesz openSSH dla połączenia SSH, wybierz opcję **OpenSSH serwera** opcji, a następnie wybierz **Kontynuuj**.

    ![Wybierz oprogramowanie](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Selction instalowania moduł ładujący rozruchu CHODNIKÓW, wybierz **tak**, a następnie wybierz **Enter**.
     
    ![Instalator rozruchu CHODNIKÓW](./media/vmware-azure-install-linux-master-target/image20.png)


1. Wybierz odpowiednie urządzenie do instalacji moduł ładujący rozruchu (najlepiej **/dev/sda**), a następnie wybierz **Enter**.
     
    ![Wybierz odpowiednie urządzenie.](./media/vmware-azure-install-linux-master-target/image21.png)

1. Wybierz **Kontynuuj**, a następnie wybierz **Enter** do zakończenia instalacji.

    ![Zakończenie instalacji](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po zakończeniu instalacji, zaloguj się do maszyny Wirtualnej z nowymi poświadczeniami użytkownika. (Zobacz **kroku 10** Aby uzyskać więcej informacji.)

1. Wykonaj kroki opisane na poniższym zrzucie ekranu, aby ustawić hasło użytkownika głównego. Następnie zaloguj się jako użytkownik ROOT.

    ![Ustaw główny hasło użytkownika](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Skonfiguruj maszynę jako główny serwer docelowy

Aby uzyskać identyfikator dla każdego dysku twardego SCSI na maszynie wirtualnej systemu Linux **dysku. EnableUUID = TRUE** parametru musi być włączona. Aby włączyć ten parametr, należy wykonać następujące czynności:

1. Zamknij maszynę wirtualną.

2. Kliknij prawym przyciskiem myszy wpis dla maszyny wirtualnej w okienku po lewej stronie, a następnie wybierz **Edytuj ustawienia**.

3. Wybierz **opcje** kartę.

4. W okienku po lewej stronie wybierz **zaawansowane** > **ogólne**, a następnie wybierz **parametry konfiguracji** przycisk w prawej dolnej części ekranu.

    ![Parametr konfiguracji otwarte](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    **Parametry konfiguracji** opcja jest niedostępna, gdy komputer jest uruchomiony. Aby uaktywnić na tej karcie, zamknij maszynę wirtualną.

5. Zobacz, czy wiersz z **dysku. EnableUUID** już istnieje.

    - Jeśli wartość istnieje i ma ustawioną wartość **False**, zmień wartość na **True**. (Wartości nie jest rozróżniana).

    - Jeśli wartość istnieje i ma ustawioną wartość **True**, wybierz pozycję **anulować**.

    - Jeśli wartość nie istnieje, wybierz **Dodaj wiersz**.

    - W kolumnie Nazwa Dodaj **dysku. EnableUUID**, a następnie ustaw wartość **TRUE**.

    ![Sprawdzanie Określa, czy na dysku. EnableUUID już istnieje.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Wyłącz uaktualnienia jądra

Azure Site Recovery główny serwer docelowy wymaga określonych wersji systemu Ubuntu, upewnij się, że uaktualnienia jądra są wyłączone dla maszyny wirtualnej. Uaktualnienia jądra są włączone, może spowodować główny serwer docelowy do nieprawidłowego działania.

#### <a name="download-and-install-additional-packages"></a>Pobieranie i instalowanie dodatkowych pakietów

> [!NOTE]
> Upewnij się, że masz połączenie z Internetem na pobieranie i instalowanie dodatkowych pakietów. Jeśli nie masz połączenie z Internetem, musisz ręcznie te pakiety RPM Znajdź i zainstaluj je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Pobierz Instalator dla Instalatora

Jeśli urządzenie docelowe głównej ma połączenie z Internetem, można użyć następujących kroków pobrać Instalatora. W przeciwnym razie należy skopiować Instalator z serwera przetwarzania, a następnie zainstaluj go.

#### <a name="download-the-master-target-installation-packages"></a>Pobierz pakiety instalacyjne główny serwer docelowy

[Pobrać najnowsze oprogramowanie instalacji główny cel Linux](https://aka.ms/latestlinuxmobsvc).

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

    ![Zrzut ekranu przedstawiający polecenia do uruchomienia](./media/vmware-azure-install-linux-master-target/image16.png)

2. Uruchom następujące polecenie, aby udzielić zezwolenia.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Uruchom następujące polecenie, aby uruchomić skrypt.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Uruchom skrypt tylko raz na serwerze. Następnie należy wyłączyć serwer. Uruchom ponownie serwer, po dodaniu dysku, zgodnie z opisem w następnej sekcji.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Dodaj dysk przechowywania do głównej docelowej maszyny wirtualnej systemu Linux

Wykonaj następujące kroki, aby utworzyć dysk przechowywania:

1. Dołącz nowy dysk 1 TB do głównej docelowej maszyny wirtualnej systemu Linux, a następnie uruchomienie maszyny.

2. Użyj **wielościeżkowe -ll** polecenie, aby dowiedzieć się wielościeżkowe identyfikator dysku przechowywania: **wielościeżkowe — wszystko**

    ![Identyfikator wielościeżkowych](./media/vmware-azure-install-linux-master-target/image27.png)

3. Sformatuj dysk, a następnie utworzyć systemu plików na nowym dysku: **mkfs.ext4 /dev/mapowania/< identyfikator wielościeżkowe dysku przechowywania >**.
    
    ![System plików](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po utworzeniu systemu plików, należy zainstalować dysk przechowywania.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Utwórz **fstab** wejścia, należy zainstalować dysk przechowywania, przy każdym uruchomieniu systemu.
    
    `vi /etc/fstab`
    
    Wybierz **Wstaw** aby rozpocząć edytowanie pliku. Utwórz nowy wiersz, a następnie wstaw następujący tekst. Edytuj identyfikator wielościeżkowe dysku, na podstawie Identyfikatora wielościeżkowe wyróżnione z poprzednie polecenie.

    **/dev/mapowania/ <Retention disks multipath id> /mnt/rw ext4 przechowywania 0 0**

    Wybierz **Esc**, a następnie wpisz **: wq** (zapisać i zamknąć) aby zamknąć okno edytora.

### <a name="install-the-master-target"></a>Zainstaluj główny cel

> [!IMPORTANT]
> Wersja główny serwer docelowy musi być późniejsza niż w przypadku wersji serwera przetwarzania i serwer konfiguracji. Jeśli ten warunek nie jest spełniony, ponownej ochrony zakończy się pomyślnie, ale replikacja nie powiedzie się.


> [!NOTE]
> Przed zainstalowaniem głównego serwera docelowego sprawdź, czy **/etc/hosts** pliku na maszynie wirtualnej zawiera wpisy, które mapują lokalną nazwą hosta do adresów IP, które są skojarzone z wszystkich kart sieciowych.

1. Kopiuj hasło z **Recovery\private\connection.passphrase witryny Azure C:\ProgramData\Microsoft** na serwerze konfiguracji. Następnie zapisz go jako **passphrase.txt** w tym samym katalogu lokalnego, uruchamiając następujące polecenie:

    `echo <passphrase> >passphrase.txt`

    Przykład: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Zanotuj adres IP serwera konfiguracji. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy i Zarejestruj serwer z serwerem konfiguracji.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Przykład: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Poczekaj na zakończenie skryptu. Czy główny cel rejestruje pomyślnie, główny cel są dostępne w **infrastruktura usługi Site Recovery** strony portalu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Zainstaluj główny cel, używając instalacja interakcyjna

1. Uruchom następujące polecenie, aby zainstalować główny cel. W roli agenta, wybierz opcję **główny cel**.

    ```
    ./install
    ```

2. Wybierz domyślną lokalizację instalacji, a następnie wybierz **Enter** aby kontynuować.

    ![Wybieranie domyślnej lokalizacji instalacji główny serwer docelowy](./media/vmware-azure-install-linux-master-target/image17.png)

Po zakończeniu instalacji należy zarejestrować serwer konfiguracji przy użyciu wiersza polecenia.

1. Zanotuj adres IP serwera konfiguracji. Należy go w następnym kroku.

2. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy i Zarejestruj serwer z serwerem konfiguracji.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Przykład: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Poczekaj na zakończenie skryptu. Czy główny cel został pomyślnie zarejestrowany, główny docelowy są dostępne w **infrastruktura usługi Site Recovery** strony portalu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instalowanie narzędzi VMware / open-vm-tools na głównym serwerze docelowym

Musisz zainstalować narzędzia VMware lub open-vm-tools w głównym celu tak, aby go odnaleźć w magazynie danych. Jeśli narzędzia nie są zainstalowane, ekranu ponownej ochrony nie ma na liście w magazynie danych. Po zakończeniu instalacji narzędzi VMware należy ponownie uruchomić.

### <a name="upgrade-the-master-target-server"></a>Uaktualnij główny serwer docelowy

Uruchom Instalatora. Automatycznie wykrywa, że agent jest zainstalowany w głównym celu. Aby przeprowadzić uaktualnienie, wybierz **Y**.  Po zakończeniu instalacji sprawdź wersję programu głównego celu zainstalowane za pomocą następującego polecenia:

`cat /usr/local/.vx_version`


Zostanie wyświetlone **wersji** pola zwraca numer wersji głównego celu.

## <a name="common-issues"></a>Typowe problemy

* Upewnij się, że nie należy włączać narzędzia Storage vMotion na wszystkie składniki zarządzania, takie jak głównego celu. Jeśli po pomyślnej ponownej ochrony jest przenoszony główny cel, nie można odłączyć dysków maszyny wirtualnej (VMDKs). W takim przypadku powrotu po awarii nie powiedzie się.

* Główny cel nie powinna mieć żadnych migawek na maszynie wirtualnej. W przypadku migawki powrotu po awarii nie powiedzie się.

* Z powodu niektóre konfiguracje niestandardowe kart interfejsu sieciowego jest wyłączona podczas uruchamiania, a agent główny cel nie można zainicjować. Upewnij się, że następujące właściwości są ustawione poprawnie. Sprawdź tych właściwości w sieci Ethernet karty /etc/sysconfig/network-scripts/ifcfg pliku-eth *.
    * BOOTPROTO=dhcp
    * ONBOOT = tak


## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu instalacji i rejestracji główny cel widać głównego celu, które są wyświetlane na **główny cel** sekcji **infrastruktura usługi Site Recovery**, w konfiguracji Omówienie serwera.

Teraz można przystąpić do [przełączonej](vmware-azure-reprotect.md), a następnie powrót po awarii.

