---
title: Jak skonfigurować serwer SMT dla sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Jak skonfigurować serwer SMT dla sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 699a8a69621642d07d3547c07bb20c0d32ca7686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616995"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Konfigurowanie serwera SMT dla systemu SUSE Linux
Duże wystąpienia sap HANA nie mają bezpośredniej łączności z Internetem. Rejestrowanie takiej jednostki u dostawcy systemu operacyjnego i pobieranie i stosowanie aktualizacji nie jest prostym procesem. Rozwiązaniem dla systemu SUSE Linux jest skonfigurowanie serwera SMT na maszynie wirtualnej platformy Azure. Hostuj maszynę wirtualną w sieci wirtualnej platformy Azure, która jest połączona z wystąpieniem dużym HANA. Przy takim serwerze SMT jednostka dużych instancji HANA może rejestrować i pobierać aktualizacje. 

Aby uzyskać więcej dokumentacji dotyczącej SUSE, zobacz ich [narzędzie do zarządzania subskrypcją dla SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Wymagania wstępne dotyczące instalowania serwera SMT, który spełnia zadanie dla dużych wystąpień HANA są:

- Sieć wirtualna platformy Azure, która jest podłączona do obwodu usługi ExpressRoute dużych wystąpień HANA.
- Konto SUSE skojarzone z organizacją. Organizacja powinna mieć prawidłową subskrypcję SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalowanie serwera SMT na maszynie wirtualnej platformy Azure

Najpierw zaloguj się do [Centrum Obsługi Klienta SUSE](https://scc.suse.com/).

Przejdź do **poświadczenia** > **organizacji**organizacji . W tej sekcji należy znaleźć poświadczenia, które są niezbędne do skonfigurowania serwera SMT.

Następnie zainstaluj maszynę wirtualną SUSE z systemem Linux w sieci wirtualnej platformy Azure. Aby wdrożyć maszynę wirtualną, należy wziąć SLES 12 SP2 obraz galerii platformy Azure (wybierz obraz SUSE SYSTEMU SOS). W procesie wdrażania nie definiuj nazwy DNS i nie używaj statycznych adresów IP.

![Zrzut ekranu przedstawiający wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image3_vm_deployment.png)

Wdrożona maszyna wirtualna jest mniejsza i uzyskała wewnętrzny adres IP w sieci wirtualnej platformy Azure 10.34.1.4. Nazwa maszyny wirtualnej to *smtserver*. Po instalacji sprawdzana jest łączność z jednostką lub jednostkami dużych instancji HANA. W zależności od sposobu organizowania rozpoznawania nazw może być konieczne skonfigurowanie rozpoznawania jednostek dużych wystąpień HANA w etc/hosts maszyny wirtualnej platformy Azure. 

Dodaj dysk do maszyny wirtualnej. Ten dysk służy do przechowywania aktualizacji, a sam dysk rozruchowy może być zbyt mały. Tutaj dysk został zamontowany do /srv/www/htdocs, jak pokazano na poniższym zrzucie ekranu. Dysk o gb 100 GB powinien wystarczyć.

![Zrzut ekranu przedstawiający wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Zaloguj się do jednostki lub jednostek dużych wystąpień HANA, zachowaj /etc/hosts i sprawdź, czy możesz dotrzeć do maszyny wirtualnej platformy Azure, która ma uruchamiać serwer SMT za pośrednictwem sieci.

Po tym sprawdzeniu zaloguj się do maszyny wirtualnej platformy Azure, która powinna uruchomić serwer SMT. Jeśli używasz kit do logowania się do maszyny wirtualnej, uruchom tę sekwencję poleceń w oknie bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Uruchom ponownie bash, aby aktywować ustawienia. Następnie uruchom YAST.

Podłącz maszynę wirtualną (smtserver) do witryny SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Po podłączeniu maszyny wirtualnej do witryny SUSE zainstaluj pakiety smt. Użyj następującego polecenia kit, aby zainstalować pakiety smt.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Można również użyć narzędzia YAST, aby zainstalować pakiety smt. W YAST przejdź do **usługi Konserwacja oprogramowania**i wyszukaj smt. Wybierz **smt**, który przełącza się automatycznie na yast2-smt.

![Zrzut ekranu przedstawiający SMT w YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Zaakceptuj wybór do instalacji na serwerze smtserver. Po zakończeniu instalacji przejdź do konfiguracji serwera SMT. Wprowadź poświadczenia organizacyjne z centrum obsługi klienta SUSE pobranego wcześniej. Wprowadź również adres hosta maszyny wirtualnej platformy Azure jako adres URL serwera SMT. W tej demonstracji jest to\/https: /smtserver.

![Zrzut ekranu przedstawiający konfigurację serwera SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Teraz sprawdź, czy połączenie z Centrum obsługi klienta SUSE działa. Jak widać na poniższym zrzucie ekranu, w tym przypadku demonstracji, to nie działa.

![Zrzut ekranu przedstawiający testowanie połączenia z Centrum obsługi klienta SUSE](./media/hana-installation/image7_test_connect.png)

Po uruchomieniu instalacji SMT podaj hasło bazy danych. Ponieważ jest to nowa instalacja, należy zdefiniować to hasło, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający definiowanie hasła do bazy danych](./media/hana-installation/image8_define_db_passwd.PNG)

Następnym krokiem jest utworzenie certyfikatu.

![Zrzut ekranu przedstawiający tworzenie certyfikatu dla serwera SMT](./media/hana-installation/image9_certificate_creation.PNG)

Po zakończeniu konfiguracji może upłynąć kilka minut, aby uruchomić sprawdzanie synchronizacji. Po zainstalowaniu i konfiguracji serwera SMT należy znaleźć repozytorium katalogu pod punktem instalacji /srv/www/htdocs/. Istnieją również niektóre podkatalogi w obszarze repozytorium. 

Uruchom ponownie serwer SMT i powiązane z nim usługi za pomocą tych poleceń.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Pobieranie pakietów na serwer SMT

Po ponownym uruchomieniu wszystkich usług wybierz odpowiednie pakiety w zarządzaniu SMT przy użyciu yast. Wybór pakietu zależy od obrazu systemu operacyjnego serwera dużych wystąpień HANA. Wybór pakietu nie zależy od wersji SLES lub wersji maszyny wirtualnej z uruchomionym serwerem SMT. Poniższy zrzut ekranu przedstawia przykład ekranu wyboru.

![Zrzut ekranu przedstawiający wybór pakietów](./media/hana-installation/image10_select_packages.PNG)

Następnie uruchom początkową kopię wybranych pakietów do skonfigurowanego serwera SMT. Ta kopia jest wyzwalana w powłoce za pomocą polecenia smt-mirror.

![Zrzut ekranu przedstawiający pobieranie pakietów na serwer SMT](./media/hana-installation/image11_download_packages.PNG)

Pakiety powinny być kopiowane do katalogów utworzonych pod punktem instalacji /srv/www/htdocs. Ten proces może potrwać godzinę lub więcej, w zależności od liczby pakietów, które wybierzesz. Po zakończeniu tego procesu przejdź do konfiguracji klienta SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurowanie klienta SMT w jednostkach dużych wystąpień HANA

Klient lub klienci w tym przypadku są jednostkami dużych wystąpień HANA. Instalator serwera SMT skopiował skrypt clientSetup4SMT.sh do maszyny wirtualnej platformy Azure. Skopiuj ten skrypt do jednostki dużych wystąpień HANA, którą chcesz połączyć z serwerem SMT. Uruchom skrypt z opcją -h i podaj nazwę serwera SMT jako parametr. W tym przykładzie nazwa to *smtserver*.

![Zrzut ekranu przedstawiający konfigurowanie klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Jest możliwe, że ładowanie certyfikatu z serwera przez klienta powiedzie się, ale rejestracja kończy się niepowodzeniem, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający niepowodzenie rejestracji klienta](./media/hana-installation/image13_registration_failed.PNG)

Jeśli rejestracja nie powiedzie się, zobacz [dokument pomocy technicznej SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024)i uruchom opisane tam kroki.

> [!IMPORTANT] 
> Dla nazwy serwera podaj nazwę maszyny wirtualnej (w tym przypadku *smtserver*), bez w pełni kwalifikowanej nazwy domeny. 

Po uruchomieniu tych kroków uruchom następujące polecenie na jednostce dużego wystąpienia HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Odczekaj kilka minut po tym kroku. Jeśli uruchomisz clientSetup4SMT.sh natychmiast, może pojawić się błąd.

Jeśli napotkasz problem, który należy rozwiązać na podstawie kroków artykułu SUSE, uruchom ponownie clientSetup4SMT.sh na jednostce dużych wystąpień HANA. Teraz powinno zakończyć się pomyślnie.

![Zrzut ekranu przedstawiający powodzenie rejestracji klienta](./media/hana-installation/image14_finish_client_config.PNG)

Skonfigurowano klienta SMT jednostki dużych wystąpień HANA, aby połączyć się z serwerem SMT zainstalowanym na maszynie wirtualnej platformy Azure. Teraz możesz wziąć "zypper w górę" lub "zypper in", aby zainstalować aktualizacje systemu operacyjnego do hana dużych wystąpień lub zainstalować dodatkowe pakiety. Aktualizacje pobrane wcześniej na serwerze SMT można pobierać tylko wcześniej.

## <a name="next-steps"></a>Następne kroki
- [Instalacja HANA na HLI](hana-example-installation.md).











