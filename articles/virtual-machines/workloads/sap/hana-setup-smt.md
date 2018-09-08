---
title: Sposób instalacji serwera SMT platformy SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak skonfigurować serwer SMT platformy SAP Hana na platformie Azure (duże wystąpienie).
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
ms.openlocfilehash: 5e729c2e3a802df15973fc6a43ee42265d1de654
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164746"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>Konfigurowanie serwera SMT w systemie SUSE Linux
Duże wystąpienia SAP HANA nie ma bezpośredniego połączenia z Internetem. Dlatego nie jest dość proste zarejestrować takiej jednostki u dostawcy systemu operacyjnego, a także pobieranie i stosowanie poprawek. Jeśli SUSE Linux jedno rozwiązanie może być Konfigurowanie serwera SMT w Maszynie wirtualnej platformy Azure. Maszyna wirtualna platformy Azure musi znajdować się w sieci wirtualnej platformy Azure, który jest podłączony do dużych wystąpień HANA. Za pomocą takiego serwera SMT jednostki dużych wystąpień HANA może zarejestrować, a następnie pobrania poprawek. 

SUSE zawiera większych Przewodnik po ich [subskrypcji narzędzia do zarządzania w systemie SLES 12 z dodatkiem SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Jako warunek wstępny do instalacji serwera SMT spełniającego zadania dla dużych wystąpień HANA będzie potrzebne są:

- Siecią wirtualną platformy Azure podłączonej do obwodu dużych ER wystąpienie oprogramowania HANA.
- Konto SUSE, który jest skojarzony z organizacji. Natomiast organizacji musi mieć pewne ważnej subskrypcji SUSE.

## <a name="installation-of-smt-server-on-azure-vm"></a>Instalacja serwera SMT na maszynie Wirtualnej platformy Azure

W tym kroku należy zainstalować serwer SMT w Maszynie wirtualnej platformy Azure. Pierwsza miara jest zalogować się do [Centrum klienta SUSE](https://scc.suse.com/)

Ponieważ użytkownik jest zalogowany, przejdź do organizacji--> poświadczeń organizacji. Poświadczenia, które są niezbędne do skonfigurowania serwera SMT powinien znajdować się w tej sekcji.

Trzecim krokiem jest instalacji maszyny Wirtualnej z systemem SUSE Linux w sieci wirtualnej platformy Azure. Aby wdrożyć maszynę Wirtualną, należy podjąć w systemie SLES 12 z dodatkiem SP2 obrazu z galerii platformy Azure. W procesie wdrożenia nie należy zdefiniować nazwę DNS i nie używać statycznych adresów IP, jak pokazano w tym zrzucie ekranu

![Wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image3_vm_deployment.png)

Wdrożonej maszyny Wirtualnej było mniejsze maszyny Wirtualnej i masz wewnętrzny adres IP w sieci wirtualnej platformy Azure z 10.34.1.4. Nazwa maszyny wirtualnej była smtserver. Po zakończeniu instalacji łączność z platformy HANA dużych jednostek wystąpienia została sprawdzona. Zależy od sposobu organizowania rozpoznawania nazw użytkownik może być konieczne skonfigurowanie rozpoznawania jednostek dużych wystąpień HANA w etc/hosty, maszyny wirtualnej platformy Azure. Dodaj dodatkowy dysk do maszyny Wirtualnej, która ma być używana do przechowywania poprawki. Dysk rozruchowy, sama może być za mały. W przypadku pokazano dysk zainstalowany stało się do /srv/www/htdocs, jak pokazano na poniższym zrzucie ekranu. Dysk o pojemności 100 GB, powinien wystarczyć.

![Wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Zaloguj się do jednostek dużych wystąpień HANA, obsługa/etc/hosts i sprawdź, czy można uzyskać dostęp do maszyny Wirtualnej platformy Azure, która ma zostać uruchomiony serwer SMT za pośrednictwem sieci.

Po ten test zakończy się pomyślnie, należy zalogować się do maszyny Wirtualnej platformy Azure, które należy uruchomić serwer SMT. Jeśli używasz programu putty zalogować się do maszyny Wirtualnej, trzeba wykonać ta sekwencja poleceń w oknie programu bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Po wykonaniu tych poleceń, uruchom ponownie usługi powłokę bash w celu aktywowania ustawienia. Następnie uruchom YAST.

W YAST przejdź do obsługi oprogramowania i wyszukaj smt. Wybierz smt, który automatycznie przełącza się yast2 smt, jak pokazano poniżej

![SMT w yast](./media/hana-installation/image5_smt_in_yast.PNG)


Zaakceptuj wybór instalacji na smtserver. Po zakończeniu instalacji przejdź do sekcji Konfiguracja serwera SMT oraz wprowadzanie poświadczeń w organizacji z Centrum klienta SUSE pobranym wcześniej. Jako adres URL serwera SMT wprowadzenia nazwy hosta usługi maszyny Wirtualnej platformy Azure. W tej prezentacji było https://smtserver wyświetlaną w następnym grafiki.

![Konfiguracja serwera SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

W następnym kroku należy sprawdzić, czy połączenie z Centrum klientów SUSE działa. Jak widać w poniższej grafice w przypadku demonstracji, czy działało.

![Test nawiązać połączenie z Centrum klienta SUSE](./media/hana-installation/image7_test_connect.png)

Po rozpoczęciu instalacji SMT należy podać hasło bazy danych. Ponieważ jest nowa instalacja, musisz zdefiniować to hasło, jak pokazano w następnym grafiki.

![Zdefiniuj hasło dla bazy danych](./media/hana-installation/image8_define_db_passwd.PNG)

Dalej interakcji, które należy jest, gdy certyfikat zostanie utworzona. Przejdź w oknie dialogowym, jak pokazano dalej i ten krok należy kontynuować.

![Utwórz certyfikat z serwera SMT](./media/hana-installation/image9_certificate_creation.PNG)

Może istnieć kilka minut, spędzony w kroku "Uruchom sprawdzanie synchronizacji" na końcu konfiguracji. Po instalacji i konfiguracji serwera SMT, należy odnaleźć repozytorium katalogu w ramach instalacji punktu /srv/www/htdocs/oraz niektóre podkatalogów w ramach repozytorium. 

Uruchom ponownie serwer SMT i powiązanych usług przy użyciu następujących poleceń.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>Pobierz pakiety na serwerze SMT

Po uruchomieniu wszystkich usług wybierz odpowiednie pakiety zarządzania SMT przy użyciu Yast. Wybór pakietów zależy od obrazu systemu operacyjnego serwera dużych wystąpień HANA nie wydania systemu SLES lub wersję maszyny Wirtualnej z systemem SMT server. Poniżej przedstawiono przykład ekranu wyboru.

![Wybierz pakiety](./media/hana-installation/image10_select_packages.PNG)

Po zakończeniu wybór pakietu należy uruchomić początkowe kopiowanie Wybierz pakiety do serwera SMT, z którym można skonfigurować. Ta kopia zostanie wywołany w powłoce, za pomocą polecenia smt — dublowanie pokazany poniżej


![Pobierz pakiety, serwer SMT](./media/hana-installation/image11_download_packages.PNG)

Jak widać powyżej pakietów należy skopiowania do katalogi utworzone w ramach instalacji punktu /srv/www/htdocs. Ten proces może wymagać trochę czasu. Zależy od liczby pakietów, możesz wybrać, może potrwać do godziny lub dłużej.
Po zakończeniu tego procesu, musisz przenieść SMT instalację klienta. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurowanie klienta SMT w jednostkach dużych wystąpień HANA

Klientów w tym przypadku są duże wystąpienie oprogramowania HANA. Konfiguracja serwera SMT kopiowane clientSetup4SMT.sh skrypt do maszyny Wirtualnej platformy Azure. Kopia skrypt przez jednostkę dużych wystąpień HANA chcesz połączyć się z serwerem SMT. Uruchom skrypt z opcją -h i nadaj jako parametr nazwy serwera SMT. W tym przykładzie smtserver.

![Konfigurowanie klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Może to być scenariusz, w którym obciążenia certyfikatu z serwera przez klienta zakończyło się pomyślnie, ale rejestracja nie powiodła się, jak pokazano poniżej.

![Rejestracja klienta zakończy się niepowodzeniem](./media/hana-installation/image13_registration_failed.PNG)

Jeśli rejestracja nie powiodła się, przeczytaj ten [SUSE obsługiwała dokumentów](https://www.suse.com/de-de/support/kb/doc/?id=7006024) i wykonaj kroki opisane w.

> [!IMPORTANT] 
> Jako nazwę serwera należy podać nazwę maszyny Wirtualnej, w tym smtserver wielkości liter, bez w pełni kwalifikowaną nazwę domeny. Po prostu działa nazwę maszyny Wirtualnej. 

Po wykonaniu tych kroków, należy wydać następujące polecenie w jednostce dużych wystąpień HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> W naszym testy mieliśmy zawsze Poczekaj kilka minut po wykonaniu tego kroku. ClientSetup4SMT.sh natychmiastowe wykonanie, po naprawcze opisaną w artykule SUSE zakończyła się z wiadomości, że certyfikat nie jest prawidłowym jeszcze. Oczekiwanie na 5 – 10 minut zwykle i wykonywanie clientSetup4SMT.sh została zakończona w konfiguracji klienta się pomyślnie.

Rozwiąże problem, który jest potrzebny do naprawienia oparte na krokach artykułu SUSE należy ponownie uruchomić clientSetup4SMT.sh w jednostce dużych wystąpień HANA. Teraz powinno to zająć pomyślnie, jak pokazano poniżej.

![Rejestracja klienta powiodła się](./media/hana-installation/image14_finish_client_config.PNG)

Ten krok należy skonfigurować klienta SMT jednostki dużych wystąpień HANA, aby połączyć względem serwera SMT, który został zainstalowany w maszynie Wirtualnej platformy Azure. Teraz można wykonać "zypper się" lub "zypper w" instalowania poprawek systemu operacyjnego do dużych wystąpień HANA lub zainstalować dodatkowe pakiety. Przyjmuje się, że tylko ci poprawek, które pobrany przed na serwerze SMT.

**Następne kroki**
- Zapoznaj się [instalacji oprogramowania HANA na HLI](hana-example-installation.md).











