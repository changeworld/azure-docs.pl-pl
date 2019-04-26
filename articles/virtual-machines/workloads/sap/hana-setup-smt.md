---
title: Jak skonfigurować serwer SMT platformy SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak skonfigurować serwer SMT platformy SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 690f41e941f2d1db8fc92d225a54d07570299222
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478110"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Konfigurowanie serwera SMT dla systemu Linux SUSE
Duże wystąpienia SAP HANA nie ma bezpośredniego połączenia z Internetem. Nie jest dość proste, aby zarejestrować takiej jednostki z dostawcą systemu operacyjnego i pobieranie i stosowanie aktualizacji. Rozwiązania dla systemu SUSE Linux jest konfigurowanie serwera SMT w maszynie wirtualnej platformy Azure. Hosta maszyny wirtualnej w sieci wirtualnej platformy Azure, który jest podłączony do dużych wystąpień HANA. Za pomocą takiego serwera SMT jednostki dużych wystąpień HANA można zarejestrować i pobierania aktualizacji. 

Aby uzyskać więcej dokumentacji w systemie SUSE Zobacz ich [subskrypcji narzędzia do zarządzania w systemie SLES 12 z dodatkiem SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Dostępne są następujące wymagania wstępne dotyczące instalowania serwera SMT spełniającego zadania dla dużych wystąpień HANA:

- Sieć wirtualna platformy Azure jest połączony z obwodem usługi ExpressRoute platformy HANA dużych wystąpień.
- Konto SUSE, który jest skojarzony z organizacji. Organizacja powinna posiadać prawidłową subskrypcję SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Zainstaluj serwer SMT na maszynie wirtualnej platformy Azure

Najpierw zaloguj się do [Centrum klienta SUSE](https://scc.suse.com/).

Przejdź do **organizacji** > **poświadczeń organizacji**. Poświadczenia, które są niezbędne do skonfigurowania serwera SMT powinien znajdować się w tej sekcji.

Następnie należy zainstalować maszyny Wirtualnej z systemem SUSE Linux w sieci wirtualnej platformy Azure. Aby wdrożyć maszynę wirtualną, należy podjąć wyświetlania obrazu z galerii w systemie SLES 12 z dodatkiem SP2 (Wybierz obraz BYOS SUSE) platformy Azure. W procesie wdrożenia nie należy zdefiniować nazwę DNS, a nie należy używać statycznych adresów IP.

![Zrzut ekranu przedstawiający wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image3_vm_deployment.png)

Wdrożonej maszyny wirtualnej jest mniejszy i masz wewnętrzny adres IP w sieci wirtualnej platformy Azure z 10.34.1.4. Nazwa maszyny wirtualnej jest *smtserver*. Po zakończeniu instalacji jest sprawdzany w łączności do dużych wystąpień HANA jednostki lub jednostek. W zależności od tego, jak zorganizowane jest rozpoznawania nazw może być konieczne skonfigurowanie rozpoznawania jednostek duże wystąpienie oprogramowania HANA w etc/hosty, maszyny wirtualne platformy Azure. 

Dodaj dysk do maszyny wirtualnej. Użyj tego dysku do przechowywania aktualizacji i samego dysku rozruchowego może być za mały. W tym miejscu dysk został zainstalowany /srv/www/htdocs, jak pokazano na poniższym zrzucie ekranu. Dysk o pojemności 100 GB, powinien wystarczyć.

![Zrzut ekranu przedstawiający wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Zaloguj się do platformy HANA, duże wystąpienie jednostki lub jednostek, obsługa/etc/hosts i sprawdź, czy można uzyskać dostęp do maszyny wirtualnej platformy Azure, która ma zostać uruchomiony serwer SMT za pośrednictwem sieci.

Po tej kontroli Zaloguj się do maszyny wirtualnej platformy Azure, które należy uruchomić serwer SMT. Jeśli używasz programu putty zalogować się do maszyny wirtualnej, należy uruchomić ta sekwencja poleceń w oknie programu bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Uruchom ponownie usługi powłokę bash w celu aktywowania ustawienia. Następnie uruchom YAST.

Połączenie z maszyną Wirtualną (smtserver) z lokacją SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Po maszyny wirtualnej jest połączona z lokacją SUSE, należy zainstalować pakiety smt. Użyj następującego polecenia programu putty, aby zainstalować pakiety smt.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Możesz także narzędzie YAST, aby zainstalować pakiety smt. W YAST, przejdź do **Software Maintenance**i poszukaj smt. Wybierz **smt**, który automatycznie zmienia się na yast2 smt.

![Zrzut ekranu przedstawiający SMT w YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Zaakceptuj wybór instalacji na smtserver. Po zakończeniu instalacji przejdź do konfiguracji serwera SMT. Wprowadzanie poświadczeń w organizacji z Centrum klienta SUSE pobranym wcześniej. Jako adres URL serwera SMT wprowadzenia nazwy hosta usługi maszyny wirtualnej platformy Azure. Podczas tego pokazu tego protokołu https:\//smtserver.

![Zrzut ekranu SMT konfiguracji serwera](./media/hana-installation/image6_configuration_of_smtserver1.png)

Teraz można przetestować, czy połączenie z Centrum klientów SUSE działa. Jak widać na następującym zrzucie ekranu, w tym przypadku demonstracji czy działało.

![Zrzut ekranu przedstawiający testowanie połączenia z Centrum klienta SUSE](./media/hana-installation/image7_test_connect.png)

Po uruchomieniu Instalatora SMT Podaj hasło bazy danych. Ponieważ jest ona nowej instalacji, należy zdefiniować to hasło, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający definiowanie hasło dla bazy danych](./media/hana-installation/image8_define_db_passwd.PNG)

Następnym krokiem jest, aby utworzyć certyfikat.

![Zrzut ekranu przedstawiający Tworzenie certyfikatu dla serwera SMT](./media/hana-installation/image9_certificate_creation.PNG)

Po zakończeniu konfiguracji może upłynąć kilka minut, aby uruchomić sprawdzanie synchronizacji. Po instalacji i konfiguracji serwera SMT, należy odnaleźć repozytorium katalogu w ramach instalacji punktu /srv/www/htdocs /. Istnieją również pewne podkatalogów w ramach repozytorium. 

Uruchom ponownie serwer SMT i powiązanych usług przy użyciu następujących poleceń.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Pobierz pakiety na serwerze SMT

Po uruchomieniu wszystkich usług wybierz odpowiednie pakiety zarządzania SMT przy użyciu YAST. Wybór pakietów zależy od obrazu systemu operacyjnego serwera dużych wystąpień HANA. Wybór pakietu nie są zależne od wersji systemu SLES lub wersję maszyny wirtualnej z systemem SMT server. Poniższy zrzut ekranu przedstawia przykład ekranu wyboru.

![Zrzut ekranu przedstawiający Wybieranie pakietów](./media/hana-installation/image10_select_packages.PNG)

Następnie należy uruchomić początkowe kopiowanie wybierz pakietów serwerowi SMT skonfigurowane. Ta kopia zostanie wywołany w powłoce za pomocą polecenia smt dublowania.

![Zrzut ekranu przedstawiający pobieranie pakietów serwerowi SMT](./media/hana-installation/image11_download_packages.PNG)

Pakiety powinien pobrać skopiowany do katalogów, utworzone w ramach instalacji punktu /srv/www/htdocs. Ten proces może potrwać godzinę lub dłużej w zależności od tego, jak wiele pakietów, możesz wybrać. Po zakończeniu tego procesu, należy przenieść SMT instalacji klienta. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurowanie klienta SMT w jednostkach dużych wystąpień HANA

Klienta lub klientów w tym przypadku są duże wystąpienie oprogramowania HANA. Konfiguracja serwera SMT kopiowane clientSetup4SMT.sh skrypt do maszyny wirtualnej platformy Azure. Kopia skrypt przez jednostkę dużych wystąpień HANA chcesz połączyć się z serwerem SMT. Uruchom skrypt z opcją -h, a następnie nadaj nazwę serwera SMT jako parametr. W tym przykładzie nazwa to *smtserver*.

![Zrzut ekranu przedstawiający Konfigurowanie klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Jest to możliwe, że obciążenie certyfikat z serwera przez klienta zakończy się pomyślnie, ale rejestracja nie powiedzie się, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający niepowodzenia rejestracji klienta](./media/hana-installation/image13_registration_failed.PNG)

Jeśli rejestracja nie powiedzie się, zobacz [SUSE obsługiwała dokumentów](https://www.suse.com/de-de/support/kb/doc/?id=7006024), i uruchom kroki opisane w.

> [!IMPORTANT] 
> Nazwa serwera, podaj nazwę maszyny wirtualnej (w tym przypadku *smtserver*), bez w pełni kwalifikowaną nazwę domeny. 

Po uruchomieniu tych kroków, uruchom następujące polecenie w jednostce dużych wystąpień HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Poczekaj kilka minut po wykonaniu tego kroku. Jeśli natychmiastowe uruchomienie clientSetup4SMT.sh można otrzymać błąd.

Jeśli wystąpi problem, który trzeba naprawić na podstawie kroków artykułu SUSE ponownie clientSetup4SMT.sh w jednostce dużych wystąpień HANA. Teraz powinno zostać zakończone pomyślnie.

![Zrzut ekranu przedstawiający Powodzenie rejestracji klienta](./media/hana-installation/image14_finish_client_config.PNG)

Należy skonfigurować klienta SMT jednostki dużych wystąpień HANA, aby połączyć się z serwerem SMT, który został zainstalowany na maszynie wirtualnej platformy Azure. Teraz można wykonać "zypper się" lub "zypper w", aby zainstalować aktualizacje systemu operacyjnego do dużych wystąpień HANA lub zainstalować dodatkowe pakiety. Można pobierać tylko aktualizacje pobrane przed na serwerze SMT.

## <a name="next-steps"></a>Kolejne kroki
- [Instalacja oprogramowania HANA na HLI](hana-example-installation.md).











