---
title: Instalowanie serwera głównego elementu docelowego systemu Linux do powrotu po awarii do lokacji lokalnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować serwerze główny serwer docelowy z systemem Linux do powrotu po awarii do lokacji lokalnej podczas odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: bcfeca34eb11caaddac06971fe7f825a142586a2
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602067"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalowanie serwera głównego elementu docelowego systemu Linux do powrotu po awarii
Po przełączeniu w tryb failover maszyn wirtualnych na platformie Azure możesz można wykonać powrotu po awarii maszyn wirtualnych do lokacji lokalnej. Do powrotu po awarii, należy ponownie włączyć ochronę maszyny wirtualnej z platformy Azure do lokacji lokalnej. Ten proces wymaga lokalny główny serwer docelowy serwer do odbierania ruchu. 

Jeśli chronione maszyny wirtualnej jest to maszyna wirtualna Windows, musisz Windows główny element docelowy. Dla maszyny wirtualnej systemu Linux konieczne jest główny element docelowy systemu Linux. Przeczytaj poniższe kroki, aby dowiedzieć się, jak utworzyć i zainstalować główny element docelowy systemu Linux.

> [!IMPORTANT]
> Począwszy od wersji 9.10.0 główny serwer docelowy, najnowszy główny serwer docelowy można zainstalować tylko na serwerze z systemem Ubuntu 16.04. Nowe instalacje nie są dozwolone na serwerach CentOS6.6. Jednak można kontynuować uaktualniania serwerów docelowych starego głównego przy użyciu 9.10.0 wersji.
> Główny serwer docelowy na LVM nie jest obsługiwane.

## <a name="overview"></a>Omówienie
Ten artykuł zawiera instrukcje dotyczące sposobu instalowania główny element docelowy systemu Linux.

Opublikuj komentarze lub pytania, na końcu tego artykułu lub na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wybrać host, na którym chcesz wdrożyć głównego elementu docelowego, należy określić, jeśli powrotu po awarii będzie to do istniejącej maszyny wirtualnej w środowisku lokalnym lub do nowej maszyny wirtualnej. 
    * Istniejącą maszynę wirtualną hosta głównego elementu docelowego powinny mieć dostępu do magazynów danych maszyny wirtualnej.
    * Jeśli nie istnieje lokalnej maszyny wirtualnej (w przypadku odzyskiwania do alternatywnej lokalizacji), maszyny wirtualnej powrotu po awarii zostanie utworzony na tym samym hoście jako głównego elementu docelowego. Można wybrać dowolnego hosta ESXi, aby zainstalować główny cel.
* Głównego elementu docelowego powinna być w sieci, który może komunikować się z serwerem przetwarzania a serwerem konfiguracji.
* Wersja głównego elementu docelowego musi być późniejsza niż wersje serwera przetwarzania i serwera konfiguracji. Na przykład jeśli wersja serwera konfiguracji jest 9.4, wersja głównego elementu docelowego można 9.4 lub 9.3, ale nie 9.5.
* Głównego elementu docelowego może być tylko maszyny wirtualne VMware, a nie serwera fizycznego.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ustalanie rozmiaru wytyczne dotyczące tworzenia głównego serwera docelowego

Utworzyć głównego elementu docelowego zgodnie z następujących wytycznych ustalania rozmiaru:
- **PAMIĘĆ RAM**: 6 GB lub więcej
- **Rozmiar dysku systemu operacyjnego**: 100 GB lub więcej (zainstalowania systemu operacyjnego)
- **Rozmiar dysku dodatkowy dysk przechowywania**: 1 TB
- **Liczba rdzeni procesora CPU**: 4 rdzeni lub więcej

Obsługiwane są następujące obsługiwanych jądra systemu Ubuntu.


|Jądra serii  |Obsługuje maksymalnie  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Wdrażanie na głównym serwerze docelowym

### <a name="install-ubuntu-16042-minimal"></a>Instalowanie systemu Ubuntu 16.04.2 minimalny

Wykonaj następujące kroki, aby zainstalować 64-bitowym systemie operacyjnym Ubuntu 16.04.2.

1.   Przejdź do [link pobierania](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), wybierz najbliższe dublowania i pobieranie obrazu ISO systemu Ubuntu 16.04.2 minimalny 64-bitowych.
Zachowaj obrazu ISO systemu Ubuntu 16.04.2 minimalny 64-bitowych w stacji dysków DVD i uruchom system.

1.  Wybierz **angielski** jako preferowany język, a następnie wybierz **Enter**.
    
    ![Wybierz język](./media/vmware-azure-install-linux-master-target/image1.png)
1. Wybierz **Zainstaluj serwer Ubuntu**, a następnie wybierz pozycję **Enter**.

    ![Wybierz opcję Zainstaluj serwer Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Wybierz **angielski** jako preferowany język, a następnie wybierz **Enter**.

    ![Wybierz język angielski jako preferowanego języka](./media/vmware-azure-install-linux-master-target/image3.png)

1. Wybierz odpowiednią opcję z **strefa czasowa** listy opcji, a następnie wybierz **Enter**.

    ![Wybierz prawidłowe strefę czasową](./media/vmware-azure-install-linux-master-target/image4.png)

1. Wybierz **nie** (opcja domyślna), a następnie wybierz pozycję **Enter**.

     ![Skonfiguruj klawiaturę](./media/vmware-azure-install-linux-master-target/image5.png)
1. Wybierz **języka angielskiego (US)** jako kraj/region punkt początkowy dla klawiatury, a następnie wybierz **Enter**.

1. Wybierz **języka angielskiego (US)** układ klawiatury, a następnie wybierz pozycję **Enter**.

1. Wprowadź nazwę hosta serwera w **Hostname** , a następnie wybierz **Kontynuuj**.

1. Aby utworzyć konto użytkownika, wprowadź nazwę użytkownika, a następnie wybierz **Kontynuuj**.

      ![Utwórz konto użytkownika](./media/vmware-azure-install-linux-master-target/image9.png)

1. Wprowadź hasło dla nowego konta użytkownika, a następnie wybierz **Kontynuuj**.

1.  Potwierdź hasło dla nowego użytkownika, a następnie wybierz **Kontynuuj**.

    ![Potwierdzenie hasła](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Przy wyborze dalej do szyfrowania katalogu macierzystego, wybierz **nie** (opcja domyślna), a następnie wybierz pozycję **Enter**.

1. Jeśli strefa czasowa, która jest wyświetlana jest prawidłowa, wybierz opcję **tak** (opcja domyślna), a następnie wybierz pozycję **Enter**. Aby ponownie skonfigurować swoją strefę czasową, wybierz **nie**.

1. Z partycjonowania opcje metody wybierz **z przewodnikiem — Użyj całego dysku**, a następnie wybierz pozycję **Enter**.

     ![Wybierz opcję partycjonowania — metoda](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Wybierz odpowiedni dysk z **wybierz dysk do partycji** opcje, a następnie wybierz **Enter**.

    ![Wybierz dysk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Wybierz **tak** można zapisać zmian na dysku, a następnie wybierz **Enter**.

    ![Wybierz opcję domyślną](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Przy wyborze Konfiguruj serwer proxy, wybierz opcję domyślną, wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **Enter**.
     
     ![Wybierz sposób zarządzania uaktualnieniami](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Wybierz **nie aktualizacje automatyczne** opcji wyboru uaktualnień w systemie zarządzania, a następnie wybierz **Enter**.

     ![Wybierz sposób zarządzania uaktualnieniami](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Usługi Azure Site Recovery główny serwer docelowy wymagają bardzo określoną wersję Ubuntu, dlatego należy upewnić się, że jądro uaktualnień są wyłączone dla maszyny wirtualnej. Jeśli są one włączone regularne uaktualnienia spowodować na głównym serwerze docelowym niepoprawne działanie. Upewnij się, możesz wybrać **nie aktualizacje automatyczne** opcji.

1.  Wybierz opcje domyślne. OpenSSH dla połączenia SSH, zaznacz **OpenSSH server** opcji, a następnie wybierz **Kontynuuj**.

    ![Wybierz oprogramowanie](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Przy wyborze dotyczące instalowania modułu ładującego rozruchu CHODNIKÓW, wybierz **tak**, a następnie wybierz pozycję **Enter**.
     
    ![Program GRUB rozruchu Instalatora](./media/vmware-azure-install-linux-master-target/image20.png)


1. Wybierz odpowiednie urządzenie do zainstalowania modułu ładującego rozruchu (najlepiej **/dev/sda**), a następnie wybierz pozycję **Enter**.
     
    ![Wybierz odpowiednie urządzenie.](./media/vmware-azure-install-linux-master-target/image21.png)

1. Wybierz **Kontynuuj**, a następnie wybierz pozycję **Enter** aby zakończyć instalację.

    ![Zakończ instalację](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po zakończeniu instalacji Zaloguj się do maszyny Wirtualnej za pomocą nowych poświadczeń użytkownika. (Zobacz **krok 10** Aby uzyskać więcej informacji.)

1. Wykonaj kroki, które są opisane na następującym zrzucie ekranu, aby ustawić hasło użytkownika głównego. Następnie zaloguj się jako użytkownik ROOT.

    ![Ustaw hasło użytkownika głównego](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Skonfiguruj maszynę jako główny serwer docelowy

Aby uzyskać identyfikator dla każdego dysku twardego SCSI na maszynie wirtualnej systemu Linux **dysku. EnableUUID = TRUE** parametru musi być włączona. Aby włączyć ten parametr, wykonaj następujące czynności:

1. Zamknij maszynę wirtualną.

2. Kliknij prawym przyciskiem myszy wpis dla maszyny wirtualnej, w okienku po lewej stronie, a następnie wybierz **edytowanie ustawień**.

3. Wybierz **opcje** kartę.

4. W okienku po lewej stronie wybierz **zaawansowane** > **ogólne**, a następnie wybierz pozycję **parametry konfiguracji** przycisk na prawej dolnej części ekranu.

    ![Otwórz konfigurację parametru](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    **Parametry konfiguracji** opcja jest niedostępna, gdy maszyna jest uruchomiona. Aby uaktywnić tę kartę, zamknij maszynę wirtualną.

5. Zobacz, czy wiersz z **dysku. EnableUUID** już istnieje.

   - Jeśli wartość istnieje i jest ustawiona na **False**, zmień wartość na **True**. (Wartości nie jest rozróżniana wielkość liter).

   - Jeśli wartość istnieje i jest ustawiona na **True**, wybierz opcję **anulować**.

   - Jeśli wartość nie istnieje, wybierz opcję **Dodaj wiersz**.

   - W kolumnie Nazwa Dodaj **dysku. EnableUUID**, a następnie ustaw wartość na **TRUE**.

     ![Sprawdzanie tego, czy na dysku. EnableUUID już istnieje.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Wyłącz uaktualniania jądra

Serwer główny serwer docelowy w usłudze Azure Site Recovery wymaga określonej wersji Ubuntu, upewnij się, że uaktualniania jądra są wyłączone dla maszyny wirtualnej. Uaktualnienia jądra są włączone, może spowodować na głównym serwerze docelowym niepoprawne działanie.

#### <a name="download-and-install-additional-packages"></a>Pobierz i zainstaluj dodatkowe pakiety

> [!NOTE]
> Upewnij się, że masz łączność z Internetem Aby pobrać i zainstalować dodatkowe pakiety. Jeśli nie masz łączności internetowej musisz ręcznie wyszukiwać te pakiety Deb i zainstaluj je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Pobierz Instalator do instalacji

Jeśli Twoje głównego elementu docelowego ma łączność z Internetem, można użyć następujące kroki do pobierania Instalatora. W przeciwnym razie możesz skopiować Instalator z serwera przetwarzania i zainstalować go.

#### <a name="download-the-master-target-installation-packages"></a>Pobierz pakiety instalacyjne głównego elementu docelowego

[Pobierz najnowsze elementy instalacji głównego elementu docelowego Linux](https://aka.ms/latestlinuxmobsvc).

Aby pobrać go przy użyciu systemu Linux, wpisz:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Upewnij się, że pobieranie i rozpakowywanie Instalatora w katalogu macierzystym. Jeśli plików do **/usr/Local**, instalacja zakończy się niepowodzeniem.


#### <a name="access-the-installer-from-the-process-server"></a>Dostęp do Instalatora z serwera przetwarzania

1. Na serwerze przetwarzania, przejdź do **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository C:\Program Files (x86)**.

2. Skopiuj plik Instalatora wymagane z serwera przetwarzania i zapisz go jako **latestlinuxmobsvc.tar.gz** w katalogu macierzystym.


### <a name="apply-custom-configuration-changes"></a>Zastosuj zmiany konfiguracji niestandardowej

Aby zastosować zmiany konfiguracji niestandardowej, użyj następujących kroków:


1. Uruchom następujące polecenie, aby untar pliku binarnego.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Zrzut ekranu przedstawiający polecenie do uruchomienia](./media/vmware-azure-install-linux-master-target/image16.png)

2. Uruchom następujące polecenie, aby przyznać uprawnienia.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Uruchom następujące polecenie, aby uruchomić skrypt.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Na serwerze, należy uruchomić skrypt tylko raz. Następnie należy wyłączyć serwer. Uruchom ponownie serwer, po dodaniu dysku, zgodnie z opisem w następnej sekcji.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Dodaj dysk przechowywania do maszyny wirtualnej z systemem Linux główny serwer docelowy

Użyj następujących kroków, aby utworzyć dysk przechowywania:

1. Dołącz nowy dysk 1 TB pojemności do maszyny wirtualnej z systemem Linux główny serwer docelowy, a następnie uruchomić maszynę.

2. Użyj **wielościeżkowe -ll** polecenie, aby dowiedzieć się wielościeżkowe identyfikator dysku przechowywania: **wielościeżkowe — wszystko**

    ![Identyfikator wielościeżkowych](./media/vmware-azure-install-linux-master-target/image27.png)

3. Sformatuj dysk, a następnie utwórz system plików na nowy dysk: **mkfs.ext4 /dev/mapowania/< dysk przechowywania wielościeżkowe identyfikator >**.
    
    ![System plików](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po utworzeniu systemu plików, należy zainstalować dysk przechowywania.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Tworzenie **fstab** wpis, aby zainstalować dysk przechowywania, za każdym razem, gdy system zostanie uruchomiony.
    
    `vi /etc/fstab`
    
    Wybierz **Wstaw** aby rozpocząć, edytując plik. Tworzenie nowego wiersza, a następnie wstaw poniższy tekst. Edytowanie Identyfikatora wielościeżkowe dysku, na podstawie Identyfikatora wielościeżkowe wyróżnione w poprzednim poleceniu.

    **/dev/mapowania/\<przechowywania dysków wielościeżkowe id >/mnt/rw ext4 przechowywania 0 0**

    Wybierz **Esc**, a następnie wpisz **: wq** (zapisać i zamknąć) aby zamknąć okno edytora.

### <a name="install-the-master-target"></a>Instalowanie głównego elementu docelowego

> [!IMPORTANT]
> Wersja głównego serwera docelowego musi być późniejsza niż wersje serwera przetwarzania i serwera konfiguracji. Jeśli ten warunek nie jest spełniony, ponowne objęcie ochroną zakończy się pomyślnie, ale replikacja nie powiedzie się.


> [!NOTE]
> Przed zainstalowaniem na głównym serwerze docelowym, sprawdź, czy **/etc/hosts** plik na maszynie wirtualnej zawiera wpisy mapujące lokalną nazwę hosta na adresy IP, które są skojarzone ze wszystkimi kartami sieciowymi.

1. Skopiuj hasło z **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** na serwerze konfiguracji. Następnie zapisz go jako **passphrase.txt** w tym samym katalogu lokalnego, uruchamiając następujące polecenie:

    `echo <passphrase> >passphrase.txt`

    Przykład: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Zanotuj adres IP serwera konfiguracji. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy i Zarejestruj serwer z serwerem konfiguracji.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Przykład: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Poczekaj na zakończenie skryptu. Jeśli pomyślnie rejestruje głównego elementu docelowego głównego elementu docelowego znajduje się na **infrastruktura usługi Site Recovery** strony portalu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalowanie głównego elementu docelowego przy użyciu instalacji interakcyjnej

1. Uruchom następujące polecenie, aby zainstalować główny cel. Dla tej roli agenta, wybierz **głównego elementu docelowego**.

    ```
    ./install
    ```

2. Wybierz domyślną lokalizację instalacji, a następnie wybierz **Enter** aby kontynuować.

    ![Wybranie domyślnej lokalizacji instalacji głównego elementu docelowego](./media/vmware-azure-install-linux-master-target/image17.png)

Po zakończeniu instalacji należy zarejestrować serwer konfiguracji za pomocą wiersza polecenia.

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

     Poczekaj na zakończenie skryptu. Jeśli główny cel został pomyślnie zarejestrowany, główny docelowy znajduje się na **infrastruktura usługi Site Recovery** strony portalu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Zainstaluj narzędzia VMware / open narzędzia maszyn wirtualnych na głównym serwerze docelowym

Musisz zainstalować narzędzia VMware lub narzędzia w przypadku maszyn wirtualnych Otwórz na głównego elementu docelowego, dzięki czemu można odnajdywać, magazyny danych. Jeśli narzędzia nie są zainstalowane, ekran ponowne objęcie ochroną nie ma na liście w magazynach danych. Po zakończeniu instalacji narzędzi VMware konieczne jest ponowne uruchomienie.

### <a name="upgrade-the-master-target-server"></a>Uaktualnij główny serwer docelowy

Uruchom Instalatora. Automatycznie wykrywa, że agent jest zainstalowany w głównym elemencie docelowym. Aby przeprowadzić uaktualnienie, wybierz **Y**.  Po zakończeniu instalacji sprawdź wersję głównego elementu docelowego zainstalowany przy użyciu następującego polecenia:

`cat /usr/local/.vx_version`


Zostanie wyświetlony **wersji** pole zawiera numer wersji głównego elementu docelowego.

## <a name="common-issues"></a>Typowe problemy

* Upewnij się, że nie należy włączać narzędzia Storage vMotion na wszystkie składniki zarządzania, takich jak głównego elementu docelowego. Jeśli przenosi głównego elementu docelowego po pomyślnej ponownej ochrony, nie można odłączyć dysków (Vmdk) maszyny wirtualnej. W takim przypadku podczas powrotu po awarii nie powiedzie się.

* Główny cel nie powinny mieć wszystkie migawki na maszynie wirtualnej. W przypadku migawki powrotu po awarii nie powiedzie się.

* Ze względu na pewne konfiguracje niestandardowe karty Sieciowej interfejs sieciowy jest wyłączona podczas uruchamiania i nie można zainicjować agenta główny serwer docelowy. Upewnij się, że następujące właściwości są ustawione poprawnie. Sprawdź tych właściwości w sieci Ethernet karty /etc/sysconfig/network-scripts/ifcfg pliku-eth *.
    * BOOTPROTO=dhcp
    * ONBOOT = tak


## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu instalacji i rejestracji głównego elementu docelowego widać głównego elementu docelowego są wyświetlane na **głównego elementu docelowego** sekcji **infrastruktura usługi Site Recovery**, w ramach konfiguracji Omówienie serwera.

Teraz możesz przejść z [ponownego włączania ochrony](vmware-azure-reprotect.md), a następnie przez powrót po awarii.

