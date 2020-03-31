---
title: Instalowanie głównego serwera docelowego dla odzyskiwania zwrotnego maszyny wirtualnej z systemem Linux za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować główny serwer docelowy systemu Linux do powrotu po awarii do lokacji lokalnej podczas odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 5b4d625d28584bb601905e9439c112c845219e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954377"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalowanie głównego serwera docelowego z systemem Linux na potrzeby powrotu po awarii
Po przebłaju maszyn wirtualnych na platformie Azure można przywrócić maszyny wirtualne w trybie fail,.After you fail over your virtual machines to Azure, you can fail back the virtual machines to the premises sites. Aby przywrócić po awarii, należy ponownie wykonać ponowne wymiary ceł maszyny wirtualnej z platformy Azure do lokacji lokalnej. W tym procesie potrzebny jest lokalny główny serwer docelowy do odbierania ruchu. 

Jeśli chroniona maszyna wirtualna jest maszyną wirtualną systemu Windows, potrzebujesz głównego obiektu docelowego systemu Windows. Dla maszyny wirtualnej Systemu Linux potrzebny jest główny cel systemu Linux. Przeczytaj poniższe kroki, aby dowiedzieć się, jak utworzyć i zainstalować główny cel systemu Linux.

> [!IMPORTANT]
> Począwszy od wydania głównego serwera docelowego 9.10.0, najnowszy główny serwer docelowy można zainstalować tylko na serwerze Ubuntu 16.04. Nowe instalacje nie są dozwolone na serwerach CentOS6.6. Można jednak kontynuować uaktualnianie starych głównych serwerów docelowych przy użyciu wersji 9.10.0.
> Główny serwer docelowy w LVM nie jest obsługiwany.

## <a name="overview"></a>Omówienie
Ten artykuł zawiera instrukcje dotyczące instalowania głównego obiektu docelowego systemu Linux.

Na końcu tego artykułu lub na [forum usługi Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)( zamieszczaj komentarze lub pytania na końcu tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wybrać hosta, na którym ma zostać wdrożony główny obiekt docelowy, należy określić, czy powrót po awarii będzie do istniejącej lokalnej maszyny wirtualnej lub do nowej maszyny wirtualnej. 
    * W przypadku istniejącej maszyny wirtualnej host głównego obiektu docelowego powinien mieć dostęp do magazynów danych maszyny wirtualnej.
    * Jeśli lokalna maszyna wirtualna nie istnieje (w przypadku odzyskiwania lokalizacji alternatywnej), maszyna wirtualna po awarii jest tworzona na tym samym hoście co główny obiekt docelowy. Możesz wybrać dowolny host ESXi, aby zainstalować główny obiekt docelowy.
* Główny obiekt docelowy powinien znajdować się w sieci, która może komunikować się z serwerem przetwarzania i serwerem konfiguracji.
* Wersja głównego obiektu docelowego musi być równa lub wcześniejsza niż wersje serwera przetwarzania i serwera konfiguracji. Na przykład jeśli wersja serwera konfiguracji jest 9.4, wersja głównego obiektu docelowego może być 9.4 lub 9.3, ale nie 9.5.
* Głównym celem może być tylko maszyna wirtualna VMware, a nie serwer fizyczny.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Wskazówki dotyczące zmiany rozmiaru podczas tworzenia głównego serwera docelowego

Utwórz główny cel zgodnie z następującymi wytycznymi dotyczącymi rozmiaru:
- **Pamięć RAM:** 6 GB lub więcej
- **Rozmiar dysku systemu operacyjnego:** 100 GB lub więcej (aby zainstalować system operacyjny)
- **Dodatkowy rozmiar dysku dla dysku retencyjnego:** 1 TB
- **Rdzenie procesora:** 4 rdzenie lub więcej

Obsługiwane są następujące jądra Ubuntu.


|Seria jądra  |Wsparcie do  |
|---------|---------|
|4.4      |4.4.0-81-rodzajowy         |
|4.8      |4.8.0-56-ogólny         |
|4.10     |4.10.0-24-ogólny        |


## <a name="deploy-the-master-target-server"></a>Wdrażanie głównego serwera docelowego

### <a name="install-ubuntu-16042-minimal"></a>Zainstaluj Ubuntu 16.04.2 Minimal

Aby zainstalować 64-bitowy system operacyjny Ubuntu 16.04.2, należy wykonać następujące czynności.

1.   Przejdź do [linku](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso)do pobrania , wybierz najbliższe lustro i pobierz Ubuntu 16.04.2 minimalne 64-bitowe ISO.
Ubuntu 16.04.2 minimalny 64-bitowy ISO w napędzie DVD i uruchomić system.

1.  Wybierz **język angielski** jako preferowany język, a następnie wybierz pozycję **Enter**.
    
    ![Wybierz język](./media/vmware-azure-install-linux-master-target/image1.png)
1. Wybierz **pozycję Zainstaluj serwer Ubuntu**, a następnie wybierz pozycję **Enter**.

    ![Wybierz pozycję Zainstaluj serwer Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Wybierz **język angielski** jako preferowany język, a następnie wybierz pozycję **Enter**.

    ![Wybierz angielski jako preferowany język](./media/vmware-azure-install-linux-master-target/image3.png)

1. Wybierz odpowiednią opcję z listy opcji **strefy czasowej,** a następnie wybierz pozycję **Wprowadź**.

    ![Wybierz właściwą strefę czasową](./media/vmware-azure-install-linux-master-target/image4.png)

1. Wybierz **pozycję Nie** (opcja domyślna), a następnie wybierz pozycję **Wprowadź**.

     ![Konfigurowanie klawiatury](./media/vmware-azure-install-linux-master-target/image5.png)
1. Wybierz **angielski (USA)** jako kraj/region pochodzenia klawiatury, a następnie wybierz pozycję **Wprowadź**.

1. Wybierz **pozycję Angielski (USA)** jako układ klawiatury, a następnie wybierz pozycję **Enter**.

1. Wprowadź nazwa hosta serwera w polu **Nazwa hosta,** a następnie wybierz pozycję **Kontynuuj**.

1. Aby utworzyć konto użytkownika, wprowadź nazwę użytkownika, a następnie wybierz pozycję **Kontynuuj**.

      ![Tworzenie konta użytkownika](./media/vmware-azure-install-linux-master-target/image9.png)

1. Wprowadź hasło dla nowego konta użytkownika, a następnie wybierz pozycję **Kontynuuj**.

1.  Potwierdź hasło dla nowego użytkownika, a następnie wybierz pozycję **Kontynuuj**.

    ![Potwierdź hasła](./media/vmware-azure-install-linux-master-target/image11.png)

1.  W następnym zaznaczeniu szyfrowania katalogu macierzystego wybierz pozycję **Nie** (opcja domyślna), a następnie wybierz pozycję **Enter**.

1. Jeśli wyświetlana strefa czasowa jest poprawna, wybierz **tak** (opcja domyślna), a następnie wybierz pozycję **Enter**. Aby ponownie skonfigurować strefę czasową, wybierz pozycję **Nie**.

1. Z opcji metody partycjonowania wybierz opcję **Z asystą — użyj całego dysku**, a następnie wybierz pozycję **Enter**.

     ![Wybierz opcję metody partycjonowania](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Wybierz odpowiedni dysk z opcji **Wybierz dysk do partycji,** a następnie wybierz pozycję **Enter**.

    ![Wybierz dysk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Wybierz **pozycję Tak,** aby zapisać zmiany na dysku, a następnie wybierz pozycję **Enter**.

    ![Wybierz opcję domyślną](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  W wyborze konfiguruj serwer proxy wybierz opcję domyślną, wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **Wprowadź**.
     
     ![Wybieranie sposobu zarządzania uaktualnieniami](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Wybierz **opcję Brak aktualizacji automatycznych** w wyborze do zarządzania uaktualnieniami w systemie, a następnie wybierz pozycję **Enter**.

     ![Wybieranie sposobu zarządzania uaktualnieniami](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Ponieważ serwer docelowy wzorca usługi Azure Site Recovery wymaga bardzo określonej wersji Ubuntu, należy upewnić się, że uaktualnienia jądra są wyłączone dla maszyny wirtualnej. Jeśli są włączone, wszystkie regularne uaktualnienia powodują awarię głównego serwera docelowego. Upewnij się, że wybrano opcję **Brak aktualizacji automatycznych.**

1.  Wybierz opcje domyślne. Jeśli chcesz otworzyćsh dla połączenia SSH, wybierz opcję **Serwer OpenSSH,** a następnie wybierz przycisk **Kontynuuj**.

    ![Wybierz oprogramowanie](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. W wyborze do zainstalowania modułu ładującego GRUB wybierz **tak**, a następnie wybierz pozycję **Enter**.
     
    ![Instalator rozruchowy GRUB](./media/vmware-azure-install-linux-master-target/image20.png)


1. Wybierz odpowiednie urządzenie do instalacji ładowarki rozruchowej (najlepiej **/dev/sda),** a następnie wybierz **enter**.
     
    ![Wybierz odpowiednie urządzenie](./media/vmware-azure-install-linux-master-target/image21.png)

1. Wybierz **przycisk Kontynuuj**, a następnie wybierz pozycję **Wprowadź,** aby zakończyć instalację.

    ![Zakończenie instalacji](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po zakończeniu instalacji zaloguj się do maszyny Wirtualnej przy użyciu nowych poświadczeń użytkownika. (Więcej informacji można znaleźć w **kroku 10).**

1. Aby ustawić hasło użytkownika ROOT, należy wykonać kroki opisane na poniższym zrzucie ekranu. Następnie zaloguj się jako użytkownik ROOT.

    ![Ustawianie hasła użytkownika ROOT](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurowanie komputera jako głównego serwera docelowego

Aby uzyskać identyfikator dla każdego dysku twardego SCSI na maszynie wirtualnej systemu Linux, **dysk. EnableUUID =** parametr TRUE musi być włączony. Aby włączyć ten parametr, należy wykonać następujące kroki:

1. Zamknij maszynę wirtualną.

2. Kliknij prawym przyciskiem myszy wpis maszyny wirtualnej w lewym okienku, a następnie wybierz polecenie **Edytuj ustawienia**.

3. Wybierz kartę **Opcje.**

4. W lewym okienku wybierz pozycję **Zaawansowane** > **ogólne**, a następnie wybierz przycisk **Parametry konfiguracji** w prawej dolnej części ekranu.

    ![Parametr konfiguracji otwórz](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Opcja **Parametry konfiguracji** nie jest dostępna, gdy urządzenie jest uruchomione. Aby ta karta była aktywna, zamknij maszynę wirtualną.

5. Zobacz, czy wiersz z **dyskiem. EnableUUID** już istnieje.

   - Jeśli wartość istnieje i jest ustawiona na **Fałsz,** zmień wartość **true**. (W wartościach nie rozróżnia się wielkość liter).

   - Jeśli wartość istnieje i jest ustawiona na **Wartość True**, wybierz **pozycję Anuluj**.

   - Jeśli wartość nie istnieje, wybierz pozycję **Dodaj wiersz**.

   - W kolumnie nazwa dodaj **dysk. EnableUUID**, a następnie ustaw wartość **TRUE**.

     ![Sprawdzanie, czy dysk. EnableUUID już istnieje](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Wyłączanie uaktualnień jądra

Serwer docelowy wzorca usługi Azure Site Recovery wymaga określonej wersji Ubuntu, upewnij się, że uaktualnienia jądra są wyłączone dla maszyny wirtualnej. Jeśli aktualizacje jądra są włączone, może to spowodować awarię głównego serwera docelowego.

#### <a name="download-and-install-additional-packages"></a>Pobieranie i instalowanie dodatkowych pakietów

> [!NOTE]
> Upewnij się, że masz połączenie z Internetem, aby pobrać i zainstalować dodatkowe pakiety. Jeśli nie masz połączenia z Internetem, musisz ręcznie znaleźć te pakiety Deb i zainstalować je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Pobierz instalator do konfiguracji

Jeśli główny obiekt docelowy ma łączność z Internetem, można pobrać instalatora za pomocą następujących kroków. W przeciwnym razie można skopiować instalatora z serwera przetwarzania, a następnie zainstalować go.

#### <a name="download-the-master-target-installation-packages"></a>Pobierz główne pakiety instalacyjne docelowe

[Pobierz najnowsze bity instalacji docelowej systemu Linux.](https://aka.ms/latestlinuxmobsvc)

Aby pobrać go za pomocą Linuksa, wpisz:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Upewnij się, że pobierasz i rozpakujesz instalatora w katalogu domowym. Jeśli rozpakujesz do **/usr/Local**, instalacja zakończy się niepowodzeniem.


#### <a name="access-the-installer-from-the-process-server"></a>Dostęp do instalatora z serwera przetwarzania

1. Na serwerze przetwarzania przejdź do **folderu C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repozytorium**.

2. Skopiuj wymagany plik instalatora z serwera przetwarzania i zapisz go jako **latestlinuxmobsvc.tar.gz** w katalogu domowym.


### <a name="apply-custom-configuration-changes"></a>Stosowanie niestandardowych zmian konfiguracji

Aby zastosować niestandardowe zmiany konfiguracji, należy wykonać następujące kroki jako użytkownik root:

1. Uruchom następujące polecenie, aby odtarć binarny.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Zrzut ekranu przedstawiający polecenie do uruchomienia](./media/vmware-azure-install-linux-master-target/image16.png)

2. Uruchom następujące polecenie, aby udzielić uprawnień.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Uruchom poniższe polecenie, aby uruchomić skrypt.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Uruchom skrypt tylko raz na serwerze. Następnie zamknij serwer. Uruchom ponownie serwer po dodaniu dysku, zgodnie z opisem w następnej sekcji.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Dodawanie dysku retencyjnego do docelowej maszyny wirtualnej systemu Linux

Aby utworzyć dysk retencyjny, należy wykonać następujące czynności:

1. Dołącz nowy dysk o pojemności 1 TB do głównej maszyny wirtualnej systemu Linux, a następnie uruchom maszynę.

2. Użyj polecenia **multipath -ll,** aby poznać wielościeżkowy identyfikator dysku retencyjnego: **multipath -ll**

    ![Identyfikator wielościeżki](./media/vmware-azure-install-linux-master-target/image27.png)

3. Sformatuj dysk, a następnie utwórz system plików na nowym dysku: **mkfs.ext4 /dev/mapper/\<Retencja dysku wielościeżkowego>**.
    
    ![System plików](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po utworzeniu systemu plików należy zainstalować dysk retencyjny.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Utwórz wpis **fstab,** aby zamontować dysk retencyjny przy każdym uruchomieniu systemu.
    
    `vi /etc/fstab`
    
    Wybierz **pozycję Wstaw,** aby rozpocząć edycję pliku. Utwórz nowy wiersz, a następnie wstaw następujący tekst. Edytuj identyfikator wielościeżki dysku na podstawie podświetlony identyfikator wielościeżkowy z poprzedniego polecenia.

    **/dev/mapper/\<Dyski retencyjne multipath id> /mnt/retention ext4 rw 0 0**

    Wybierz **polecenie Esc**, a następnie wpisz **:wq** (write and quit), aby zamknąć okno edytora.

### <a name="install-the-master-target"></a>Instalowanie obiektu docelowego głównego

> [!IMPORTANT]
> Wersja głównego serwera docelowego musi być równa lub wcześniejsza niż wersje serwera przetwarzania i serwera konfiguracji. Jeśli ten warunek nie jest spełniony, ponowne przetwarzanie powiedzie się, ale replikacja kończy się niepowodzeniem.


> [!NOTE]
> Przed zainstalowaniem głównego serwera docelowego sprawdź, czy plik **/etc/hosts** na maszynie wirtualnej zawiera wpisy mapujące lokalną nazwę hosta na adresy IP skojarzone ze wszystkimi kartami sieciowymi.

1. Skopiuj hasło z **programu C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** na serwerze konfiguracji. Następnie zapisz go jako **passphrase.txt** w tym samym katalogu lokalnym, uruchamiając następujące polecenie:

    `echo <passphrase> >passphrase.txt`

    Przykład: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Zanotuj adres IP serwera konfiguracji. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy i zarejestrować serwer na serwerze konfiguracji.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Przykład: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Poczekaj, aż skrypt się zakończy. Jeśli główny obiekt docelowy zarejestruje się pomyślnie, główny obiekt docelowy jest wyświetlany na stronie **Infrastruktura odzyskiwania lokacji** w portalu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Zainstaluj główny cel przy użyciu instalacji interaktywnej

1. Uruchom następujące polecenie, aby zainstalować główny obiekt docelowy. Dla roli agenta wybierz główny **cel**.

    ```
    ./install
    ```

2. Wybierz domyślną lokalizację instalacji, a następnie wybierz pozycję **Enter,** aby kontynuować.

    ![Wybieranie domyślnej lokalizacji instalacji obiektu docelowego głównego](./media/vmware-azure-install-linux-master-target/image17.png)

Po zakończeniu instalacji zarejestruj serwer konfiguracji za pomocą wiersza polecenia.

1. Zanotuj adres IP serwera konfiguracji. Potrzebujesz go w następnym kroku.

2. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy i zarejestrować serwer na serwerze konfiguracji.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Przykład: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Poczekaj, aż skrypt się zakończy. Jeśli główny cel zostanie pomyślnie zarejestrowany, główny obiekt docelowy jest wyświetlany na stronie **Infrastruktura odzyskiwania lokacji** w portalu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instalowanie narzędzi VMware / open-vm-tools na głównym serwerze docelowym

Należy zainstalować narzędzia VMware lub open-vm-tools na głównym miejscu docelowym, aby można było odnajdować magazyny danych. Jeśli narzędzia nie są zainstalowane, ekran ponownego przekazywania danych nie jest wyświetlany w magazynach danych. Po zainstalowaniu narzędzi VMware należy ponownie uruchomić komputer.

### <a name="upgrade-the-master-target-server"></a>Uaktualnianie głównego serwera docelowego

Uruchom instalatora. Automatycznie wykrywa, że agent jest zainstalowany na głównym celu. Aby uaktualnić, wybierz **Y**.  Po zakończeniu instalacji sprawdź wersję głównego obiektu docelowego zainstalowanego za pomocą następującego polecenia:

`cat /usr/local/.vx_version`


Zobaczysz, że pole **Wersja** podaje numer wersji głównego obiektu docelowego.

## <a name="common-issues"></a>Typowe problemy

* Upewnij się, że nie włączysz pamięci masowej vMotion dla żadnych składników zarządzania, takich jak główny obiekt docelowy. Jeśli główny obiekt docelowy zostanie przeniesiony po pomyślnym przerówce, nie można odłączyć dysków maszyny wirtualnej (VMDK). W takim przypadku powrót po awarii kończy się niepowodzeniem.

* Główny obiekt docelowy nie powinien mieć żadnych migawek na maszynie wirtualnej. Jeśli istnieją migawki, powrót po awarii nie powiedzie się.

* Ze względu na niektóre niestandardowe konfiguracje kart sieciowych interfejs sieciowy jest wyłączony podczas uruchamiania, a główny agent docelowy nie może zainicjować. Upewnij się, że następujące właściwości są poprawnie ustawione. Sprawdź te właściwości w pliku karty Ethernet /etc/sysconfig/network-scripts/ifcfg-eth*.
    * BOOTPROTO=dhcp
    * ONBOOT=tak


## <a name="next-steps"></a>Następne kroki
Po zakończeniu instalacji i rejestracji głównego obiektu docelowego można zobaczyć główny obiekt docelowy wyświetlany w sekcji **docelowej wzorca** w **infrastrukturze odzyskiwania lokacji**w obszarze Przegląd serwera konfiguracji.

Teraz można kontynuować [reprotection](vmware-azure-reprotect.md), a następnie po awarii.

