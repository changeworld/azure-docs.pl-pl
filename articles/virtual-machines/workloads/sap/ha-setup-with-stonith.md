---
title: Wysoka dostępność skonfigurowana przy pomocą stonith dla SAP HANA na platformie Azure (duże wystąpienia)| Dokumenty firmy Microsoft
description: Ustal wysoką dostępność dla sap HANA na platformie Azure (duże wystąpienia) w SUSE przy użyciu stonith
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4060dbe936af8ff1f9dd8c958f64834cb06525de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77615081"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Konfigurowanie wysokiej dostępności w systemie SUSE przy użyciu urządzenia STONITH
Ten dokument zawiera szczegółowe instrukcje krok po kroku, aby skonfigurować wysoką dostępność w systemie operacyjnym SUSE przy użyciu urządzenia STONITH.

**Zastrzeżenie:** *Ten przewodnik jest pochodną testowania konfiguracji w środowisku dużych wystąpień sieci Hana firmy Microsoft, który pomyślnie działa. Ponieważ zespół zarządzania usługami firmy Microsoft dla dużych wystąpień HANA nie obsługuje systemu operacyjnego, może być konieczne skontaktowanie się z SUSE w celu uzyskania dalszych informacji dotyczących rozwiązywania problemów lub wyjaśnienia dotyczące warstwy systemu operacyjnego. Zespół zarządzania usługami firmy Microsoft skonfiguruje urządzenie STONITH i w pełni obsługuje i może być zaangażowany w rozwiązywanie problemów z urządzeniami STONITH.*
## <a name="overview"></a>Omówienie
Aby skonfigurować wysokiej dostępności przy użyciu klastra SUSE, następujące wymagania wstępne muszą spełniać.
### <a name="pre-requisites"></a>Wymagania wstępne
- Duże wystąpienia HANA są aprowizacji
- System operacyjny jest zarejestrowany
- Serwery hana dużych wystąpień są połączone z serwerem SMT, aby uzyskać poprawki/pakiety
- System operacyjny ma zainstalowane najnowsze poprawki
- NTP (serwer czasu) jest skonfigurowany
- Przeczytaj i zrozum najnowszą wersję dokumentacji SUSE w konfiguracji wysokiej jakości

### <a name="setup-details"></a>Szczegóły instalacji
W tym przewodniku użyto następującej konfiguracji:
- System operacyjny: SLES 12 SP1 dla SAP
- Duże wystąpienia HANA: 2xS192 (cztery gniazda, 2 TB)
- Wersja HANA: HANA 2.0 SP1
- Nazwy serwerów: sapprdhdb95 (node1) i sapprdhdb96 (node2)
- Urządzenie STONITH: urządzenie STONITH oparte na iSCSI
- Ntp skonfigurowany w jednym z węzłów dużych wystąpień HANA

Podczas konfigurowania dużych wystąpień HANA z HSR, można zażądać zespołu zarządzania usługami firmy Microsoft, aby skonfigurować STONITH. Jeśli jesteś już istniejącym klientem, który ma administracyjne wystąpienia hana duże i trzeba skonfigurować urządzenie STONITH dla istniejących bloków, należy podać następujące informacje do zespołu zarządzania usługami firmy Microsoft w formularzu żądania usługi (SRF). Formularz SRF można zażądać za pośrednictwem menedżera konta technicznego lub kontaktu firmy Microsoft dla dołączania do dużych wystąpień HANA. Nowi klienci mogą zażądać urządzenia STONITH w momencie inicjowania obsługi administracyjnej. Dane wejściowe są dostępne w formularzu żądania inicjowania obsługi administracyjnej.

- Nazwa serwera i adres IP serwera (na przykład myhanaserver1, 10.35.0.1)
- Lokalizacja (na przykład wschód USA)
- Nazwa klienta (na przykład Microsoft)
- SID - identyfikator systemu HANA (na przykład H11)

Po skonfigurowaniu urządzenia STONITH zespół zarządzania usługami firmy Microsoft udostępnia nazwę urządzenia SBD i adres IP magazynu iSCSI, których można użyć do skonfigurowania konfiguracji STONITH. 

Aby skonfigurować ha end to end przy użyciu STONITH, należy wykonać następujące kroki:

1.  Identyfikowanie urządzenia SBD
2.  Inicjowanie urządzenia SBD
3.  Konfigurowanie klastra
4.  Konfigurowanie Softdog Watchdog
5.  Łączenie węzła z klastrem
6.  Sprawdzanie poprawności klastra
7.  Konfigurowanie zasobów w klastrze
8.  Testowanie procesu pracy awaryjnej

## <a name="1---identify-the-sbd-device"></a>1. Identyfikacja urządzenia SBD
W tej sekcji opisano sposób określania urządzenia SBD dla konfiguracji po skonfigurowaniu stonith przez zespół zarządzania usługami firmy Microsoft. **Ta sekcja dotyczy tylko istniejącego klienta.** Jeśli jesteś nowym klientem, zespół zarządzania usługami firmy Microsoft udostępnia nazwę urządzenia SBD i możesz pominąć tę sekcję.

1.1 Zmodyfikuj */etc/iscsi/initiatorname.isci,* aby 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Zarządzanie usługami firmy Microsoft udostępnia ten ciąg. Zmodyfikuj plik w **obu** węzłach, jednak numer węzła jest inny w każdym węźle.

![nazwa inicjatora.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Modify */etc/iscsi/iscsid.conf*: Set *node.session.timeo.replacement_timeout=5* and *node.startup = automatic*. Zmodyfikuj plik w **obu** węzłach.

1.3 Wykonaj polecenie odnajdywania, pokazuje cztery sesje. Uruchom go na obu węzłach.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Wykonaj polecenie, aby zalogować się do urządzenia iSCSI, pokazuje cztery sesje. Uruchom go na **obu** węzłach.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Wykonaj skrypt reskanu: *rescan-scsi-bus.sh*.  Ten skrypt pokazuje nowe dyski utworzone dla Ciebie.  Uruchom go na obu węzłach. Powinien zostać wyświetlony numer jednostki LUN, który jest większy niż zero (na przykład: 1, 2 itp.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Aby uzyskać nazwę urządzenia, uruchom polecenie *fdisk –l*. Uruchom go na obu węzłach. Wybierz urządzenie o rozmiarze **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Inicjowanie urządzenia SBD

2.1 Zainicjowanie urządzenia SBD w **obu** węzłach

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Sprawdź, co zostało zapisane w urządzeniu. Zrób to na **obu** węzłach

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Konfigurowanie klastra
W tej sekcji opisano kroki konfigurowania klastra usługi SUSE HA.
### <a name="31-package-installation"></a>3.1 Instalacja pakietowa
3.1.1 Sprawdź, czy są zainstalowane wzory ha_sles i SAPHanaSR-doc. Jeśli nie jest zainstalowany, zainstaluj je. Zainstaluj go na **obu** węzłach.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Konfigurowanie klastra
3.2.1 Można użyć polecenia *ha-cluster-init* lub użyć kreatora yast2 do skonfigurowania klastra. W takim przypadku używany jest kreator yast2. Ten krok można wykonać **tylko w węźle podstawowym**.

Śledź yast2> Wysoka dostępność > Klastra ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kliknij **przycisk Anuluj,** ponieważ pakiet halk2 jest już zainstalowany.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kliknij **przycisk Kontynuuj**

Wartość oczekiwana=Liczba wdrożonych węzłów (w ![](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) tym przypadku 2) yast-Cluster-Security.png Kliknij **przycisk Następny**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Dodaj nazwy węzłów, a następnie kliknij przycisk "Dodaj sugerowane pliki"

Kliknij "Włącz csync2 ON"

Kliknij "Generowanie pre-shared-keys", pokazuje poniżej popup

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kliknij **przycisk OK**

Uwierzytelnianie jest wykonywane przy użyciu adresów IP i kluczy współużytkowane w csync2. Plik klucza jest generowany za pomocą csync2 -k /etc/csync2/key_hagroup. Plik key_hagroup powinien zostać skopiowany do wszystkich członków klastra ręcznie po jego utworzeniu. **Upewnij się, że plik zostanie skopiowany z węzła 1 do węzła2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Kliknij **przycisk Dalej**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

W opcji domyślnej uruchamianie było wyłączone, zmień go na "on", więc rozrusznik serca jest uruchamiany przy rozruchu. Wybór można dokonać na podstawie wymagań dotyczących konfiguracji.
Kliknij **przycisk Dalej,** a konfiguracja klastra zostanie ukończona.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Konfigurowanie Softdog Watchdog
W tej sekcji opisano konfigurację watchdog (softdog).

4.1 Dodaj następujący wiersz do */etc/init.d/boot.local* w **obu** węzłach.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Zaktualizuj plik */etc/sysconfig/sbd* w **obu** węzłach w następujący sposób:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Załaduj moduł jądra na **oba** węzły, uruchamiając następujące polecenie
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Sprawdź i upewnij się, że softdog działa jak na **obu** węzłach:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Uruchom urządzenie SBD na **obu** węzłach
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Przetestuj demonA SBD na **obu** węzłach. Po skonfigurowaniu dwóch wpisów w **obu** węzłach
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Wysyłanie wiadomości testowej do **jednego** z węzłów
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 W **drugim** węźle (węzeł2) można sprawdzić stan wiadomości
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Aby przyjąć konfig sbd, zaktualizuj plik */etc/sysconfig/sbd* jako następujący. Aktualizowanie pliku w **obu** węzłach
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Uruchamianie usługi rozrusznika serca w **węźle Podstawowym** (węzeł1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Jeśli usługa rozrusznika *nie powiedzie się,* patrz *Scenariusz 5: Usługa rozrusznika nie powiedzie się*

## <a name="5---joining-the-cluster"></a>5. Dołączanie do klastra
W tej sekcji opisano sposób łączenia węzła z klastrem.

### <a name="51-add-the-node"></a>5.1 Dodawanie węzła
Uruchom następujące polecenie w **węźle2,** aby umożliwić node2 dołączenie do klastra.
```
ha-cluster-join
```
Jeśli podczas dołączania do klastra pojawia się *błąd,* zapoznaj się z *scenariuszem 6: Węzeł 2 nie może dołączyć do klastra*.

## <a name="6---validating-the-cluster"></a>6. Sprawdzanie poprawności klastra

### <a name="61-start-the-cluster-service"></a>6.1 Uruchamianie usługi klastrowania
Aby sprawdzić i opcjonalnie uruchomić klaster po raz pierwszy w **obu** węzłach.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Monitorowanie stanu
Uruchom polecenie *crm_mon,* aby upewnić się, że **oba** węzły są w trybie online. Można go uruchomić na **dowolnym z węzłów** klastra
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) Można również zalogować się do jastrzębia, aby sprawdzić stan klastra *https://\<węzła IP>:7630*. Domyślnym użytkownikiem jest hacluster, a hasło to Linux. W razie potrzeby można zmienić hasło za pomocą polecenia *passwd.*

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurowanie właściwości i zasobów klastra 
W tej sekcji opisano kroki konfigurowania zasobów klastra.
W tym przykładzie skonfigurować następujący zasób, reszta można skonfigurować (w razie potrzeby) odwołując się do przewodnika SUSE HA. Wykonaj config tylko w **jednym z węzłów.** Wykonaj w węźle podstawowym.

- Pułapka na boottrap klastra
- Urządzenie STONITH
- Wirtualny adres IP


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Pułapka na boottrap klastra i nie tylko
Dodaj boottrap klastra. Utwórz plik i dodaj tekst w następujący sposób:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 Urządzenie STONITH
Dodaj zasób STONITH. Utwórz plik i dodaj tekst w następujący sposób.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 Wirtualny adres IP
Dodaj wirtualny adres IP zasobów. Utwórz plik i dodaj tekst, jak poniżej.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Sprawdzanie poprawności zasobów

Po uruchomieniu polecenia *crm_mon*, można zobaczyć dwa zasoby tam.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ponadto, można zobaczyć stan w *https://\<węzeł adres IP>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testowanie procesu pracy awaryjnej
Aby przetestować proces pracy awaryjnej, zatrzymaj usługę rozrusznika serca w węźle1 i zasoby trybu failover do węzła2.
```
Service pacemaker stop
```
Teraz zatrzymaj usługę rozrusznika serca w **węźle2** i zasoby po awarii do **węzła1**

**Przed przejściem awaryjnym**  
![Przed-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Po pracy awaryjnej**  
![po pracy awaryjnej.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Rozwiązywanie problemów
W tej sekcji opisano kilka scenariuszy awarii, które można napotkać podczas instalacji. Niekoniecznie możesz zmierzyć się z tymi problemami.

### <a name="scenario-1-cluster-node-not-online"></a>Scenariusz 1: Węzeł klastra nie jest w trybie online
Jeśli którykolwiek z węzłów nie jest pokazywalny w trybie online w Menedżerze klastrów, możesz spróbować wykonać, aby przenieść go do trybu online.

Uruchamianie usługi iSCSI
```
service iscsid start
```

A teraz powinieneś być w stanie zalogować się do tego węzła iSCSI
```
iscsiadm -m node -l
```
Oczekiwana produkcja wygląda następująco:
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenariusz 2: yast2 nie pokazuje widoku graficznego
Ekran graficzny yast2 służy do konfigurowania klastra o wysokiej dostępności w tym dokumencie. Jeśli yast2 nie otwiera się z wyświetlonym oknem graficznym i wrzuć błąd Qt, wykonaj następujące czynności. Jeśli zostanie otwarte wraz z oknem graficznym, można pominąć kroki.

**Błąd**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Oczekiwane wyjście**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Jeśli widok yast2 nie otwiera się w widoku graficznym, wykonaj następujące kroki.

Zainstaluj wymagane pakiety. Musisz być zalogowany jako użytkownik "root" i mieć SMT skonfigurować do pobierania / instalowania pakietów.

Aby zainstalować pakiety, użyj yast>Software>Software Management>Dependencies> opcja "Zainstaluj zalecane pakiety...". Poniższy zrzut ekranu ilustruje oczekiwane ekrany.
>[!NOTE]
>Należy wykonać kroki na obu węzłach, aby uzyskać dostęp do widoku graficznego yast2 z obu węzłów.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

W obszarze Zależności wybierz opcję !["Zainstaluj zalecane pakiety" yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Przejrzyj zmiany i naciśnij przycisk OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Instalacja pakietu ![przebiega yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknij przycisk Dalej

![yast-instalacja-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kliknij przycisk Zakończ

Musisz również zainstalować pakiety libqt4 i libyui-qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 powinien być w stanie otworzyć graficzny widok teraz, jak pokazano tutaj.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenariusz 3: yast2 nie jest opcją Wysoka dostępność
Aby opcja Wysoka dostępność była widoczna w centrum sterowania yast2, należy zainstalować dodatkowe pakiety.

Korzystanie z zarządzania oprogramowaniem>oprogramowaniem Yast2>>Wybierz następujące wzorce

- Baza serwerów SAP HANA
- Kompilator i narzędzia C/C++
- Wysoka dostępność
- Baza serwera aplikacji SAP

Na poniższym ekranie przedstawiono kroki, aby zainstalować wzorce.

Korzystanie z zarządzania oprogramowaniem > > oprogramowaniem yast2

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Wybierz wzory

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kliknij **przycisk Zaakceptuj**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kliknij **przycisk Kontynuuj**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Po zakończeniu instalacji kliknij przycisk **Dalej**

![yast2-instalacja-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenariusz 4: Instalacja HANA kończy się niepowodzeniem z błędem zestawów gcc
Instalacja HANA kończy się niepowodzeniem z następującym błędem.

![Hana-instalacja-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Aby rozwiązać ten problem, należy zainstalować biblioteki (libgcc_sl i libstdc ++6) w następujący sposób.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenariusz 5: Usługa rozrusznika serca kończy się niepowodzeniem

Następujący problem wystąpił podczas uruchamiania usługi rozrusznika serca.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Aby to naprawić, usuń następujący wiersz z pliku */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenariusz 6: Węzeł 2 nie może dołączyć do klastra

Podczas łączenia węzła2 z istniejącym klastrem za pomocą polecenia *ha-cluster-join* wystąpił następujący błąd.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Aby naprawić, uruchom następujące czynności na obu węzłach

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po poprzedniej poprawce węzeł2 powinien zostać dodany do klastra

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Dokumentacja ogólna
Więcej informacji na temat konfiguracji usługi SUSE HA można znaleźć w następujących artykułach: 

- [Scenariusz zoptymalizowany pod kątem wydajności SAP HANA SR](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Ogrodzenia na bazie magazynowania](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Korzystanie z klastra rozrusznika dla SAP HANA- Część 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Korzystanie z klastra rozrusznika dla SAP HANA- Część 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
