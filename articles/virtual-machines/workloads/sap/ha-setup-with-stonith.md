---
title: Wysoką dostępność, skonfiguruj za pomocą STONITH dla SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Ustanowienie wysokiej dostępności dla SAP HANA na platformie Azure (duże wystąpień) w SUSE przy użyciu STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 344a48ff82bd93bf8dc9924e09399e72b9f88e2f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656367"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Wysoka dostępność skonfigurowane w systemie SUSE przy użyciu STONITH
Ten dokument zawiera szczegółowe instrukcje krok po kroku, aby skonfigurować wysoką dostępność w systemie operacyjnym SUSE przy użyciu urządzenia STONITH.

**Zastrzeżenie:** *ten przewodnik jest uzyskiwany w wyniku testowanie ustawienia w środowisko Microsoft HANA dużych wystąpień, w którym pomyślnie działa. Ponieważ zespołu zarządzania usługami firmy Microsoft dla wystąpień dużych HANA nie obsługuje systemu operacyjnego, konieczne może być skontaktuj się z SUSE dla dalszego rozwiązywania problemów lub wyjaśnienia dotyczące warstwy systemu operacyjnego. Zespół zarządzania usługi Microsoft Konfigurowanie urządzenia STONITH i w pełni obsługuje i mogą brać udział uzyskać informacje o rozwiązywaniu problemów urządzenia STONITH.*
## <a name="overview"></a>Przegląd
Aby skonfigurować wysoką dostępność korzystania z klastra SUSE, musi spełniać następujące wymagania wstępne.
### <a name="pre-requisites"></a>Wymagania wstępne
- Udostępnieniu wystąpień dużych HANA
- System operacyjny jest zarejestrowany.
- Duże wystąpień HANA serwery są podłączone do serwera SMT, aby uzyskać poprawek/pakietów
- System operacyjny ma zainstalowane najnowsze poprawki.
- Skonfigurowano NTP (serwer czasu)
- Przeczytane i zrozumiane najnowszej wersji dokumentacji SUSE w konfiguracji wysokiej dostępności

### <a name="setup-details"></a>Szczegóły ustawień
W tym przewodniku są używane następujące ustawienia:
- System operacyjny: SLES 12 dodatku SP1 dla programu SAP
- HANA dużych wystąpień: 2xS192 (cztery gniazda, 2 TB)
- Wersja HANA: HANA 2.0 z dodatkiem SP1
- Nazwy serwera: sapprdhdb95 (node1) i sapprdhdb96 (node2)
- STONITH urządzenia: urządzenie STONITH iSCSI na podstawie
- Na jednym z węzłów wystąpienia dużych HANA NTP

Po skonfigurowaniu HANA dużych wystąpień HSR zespołu zarządzania usługami firmy Microsoft do skonfigurowania STONITH może wysłać żądanie. Jeśli masz już istniejący klient posiadający HANA wystąpień dużych elastycznie i potrzeb urządzenia STONITH dla Twojego istniejących bloków, musisz podać następujące informacje do zespołu zarządzania usługami firmy Microsoft w formularzu żądania usługi (SRF). Można zażądać SRF formularza za pomocą kierownika lub Contact Twojej firmy Microsoft do dołączenia do wystąpienia dużych HANA. Nowi klienci mogą żądać STONITH urządzenia w czasie inicjowania obsługi administracyjnej. Dane wejściowe są dostępne w formularzu żądania inicjowania obsługi administracyjnej.

- Adres IP serwera i nazwy serwera (na przykład myhanaserver1, 10.35.0.1)
- Lokalizacja (na przykład nam wschodnie)
- Nazwa klienta (na przykład Microsoft)
- Identyfikator SID - HANA identyfikatora systemowego (na przykład H11)

Po skonfigurowaniu urządzenia STONITH zespołu zarządzania usługami Microsoft umożliwiają interwencja nazwa urządzenia i adres IP z magazynu iSCSI, które służy do konfigurowania ustawień STONITH. 

Aby skonfigurować HA kompleksowemu użyciu STONITH, należy wprowadzić następujące czynności:

1.  Identyfikowanie urządzeń interwencja
2.  Inicjowanie urządzenia interwencja
3.  Konfigurowanie klastra
4.  Konfigurowanie programu alarmowego Softdog
5.  Dołącz węzła do klastra
6.  Sprawdzanie poprawności klastra
7.  Konfigurowanie zasobów do klastra
8.  Testowanie procesu trybu failover

## <a name="1---identify-the-sbd-device"></a>1.   Identyfikowanie urządzeń interwencja
W tej sekcji opisano w sposób określania urządzeń interwencja ustawienia, po skonfigurowaniu STONITH zespół zarządzania usługi Microsoft. **Ta sekcja dotyczy tylko istniejący klient**. W przypadku nowego klienta zespół zarządzania usługi Microsoft Podaj interwencja nazwy urządzenia dla Ciebie i możesz pominąć tę sekcję.

1.1 modyfikowanie */etc/iscsi/initiatorname.isci* do 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Zarządzanie usługami firmy Microsoft Podaj ten ciąg. Zmodyfikuj plik na **zarówno** węzłów, jednak numer węzła jest inna w każdym węźle.

![initiatorname.PNG](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 modyfikowanie */etc/iscsi/iscsid.conf*: Ustaw *node.session.timeo.replacement_timeout=5* i *node.startup = automatyczne*. Zmodyfikuj plik na **zarówno** węzłów.

1.3 wykonaj polecenie odnajdywania, zawiera cztery sesji. Uruchom go na obu węzłach.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 wykonaj polecenie, aby zalogować się do urządzenia iSCSI, zawiera cztery sesji. Uruchom go na **zarówno** węzłów.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 wykonać ponowne skanowanie skryptów: *ponownego skanowania scsi-bus.sh*.  Ten skrypt zawiera nowe dyski utworzone automatycznie.  Uruchom go na obu węzłach. Powinny pojawić się numer LUN większą od zera (na przykład: 1, 2 itd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.PNG](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 można uzyskać nazwy urządzenia, uruchom polecenie *fdisk — l*. Uruchom go na obu węzłach. Wybierz urządzenie z rozmiarem **178 MiB**.

```
  fdisk –l
```

![Fdisk l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicjowanie urządzenia interwencja

2.1 inicjowania urządzenia interwencja w **zarówno** węzłów

```
sbd -d <SBD Device Name> create
```
![sbdcreate.PNG](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 sprawdzić, jakie został zapisany na urządzeniu. To robić na **zarówno** węzłów

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurowanie klastra
W tej sekcji opisano kroki konfigurowania klastra SUSE HA.
### <a name="31-package-installation"></a>3.1 instalacja pakietu
3.1.1 Sprawdź ha_sles i wzorce SAPHanaSR doc są zainstalowane. Jeśli nie jest zainstalowany, zainstaluj je. Zainstaluj ją na **zarówno** węzłów.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.PNG](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Konfigurowanie klastra
3.2.1 można użyć *ha klastra init* polecenie lub Kreator yast2 służy do konfigurowania klastra. W takim przypadku Kreator yast2 jest używany. Wykonaj ten krok **tylko w węźle podstawowym**.

Wykonaj yast2 > wysokiej dostępności > klastra ![yast formant center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kliknij przycisk **anulować** ponieważ halk2 pakiet jest już zainstalowany.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kliknij przycisk **kontynuować**

Oczekiwana wartość = Liczba wdrożonych (w tym przypadku 2) węzły ![yast klastra-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) kliknij **dalej**
![yast klastra skonfigurować csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Dodaj nazwy węzłów, a następnie kliknij przycisk "Dodaj sugerowane plików"

Kliknij pozycję "Włącz csync2"

Kliknij pozycję "Generuj wstępnie przygotowany Shared kluczy", będzie wyświetlana poniżej menu podręczne

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kliknij przycisk **OK**.

Uwierzytelnianie jest wykonywane przy użyciu adresów IP i wstępnie przygotowany shared klucze w Csync2. Plik klucza jest generowany z csync2 /etc/csync2/key_hagroup -k. Key_hagroup pliku powinien zostać skopiowany do wszystkich elementów członkowskich klastra ręcznie po jej utworzeniu. **Upewnij się, aby skopiować plik z węzła 1 do node2**.

![yast klastra conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Kliknij przycisk **dalej**
![yast klastra service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

W przypadku opcji domyślnej rozruch był wyłączony, zmień go na "on", aby rozrusznik jest uruchomiona na rozruchu. Umożliwia wybór zgodnie z wymaganiami konfiguracji.
Kliknij przycisk **dalej** i Konfiguracja klastra została ukończona.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Konfigurowanie programu alarmowego Softdog
W tej sekcji opisano konfigurację programu alarmowego (softdog).

4.1 następujący wiersz do dodania */etc/init.d/boot.local* na **zarówno** węzłów.
```
modprobe softdog
```
![modprobe softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 zaktualizować pliku */etc/sysconfig/sbd* na **zarówno** węzłami, ponieważ następujące czynności:
```
SBD_DEVICE="<SBD Device Name>"
```
![interwencja device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 załadować moduł jądra na **zarówno** węzłów, uruchamiając następujące polecenie
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Sprawdź i upewnij się, że softdog jest uruchomiona jako następujące czynności na **zarówno** węzły:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 uruchomić urządzenia interwencja na **zarówno** węzłów
```
/usr/share/sbd/sbd.sh start
```
![interwencja sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 test demona interwencja na **zarówno** węzłów. Po skonfigurowaniu na znajdują się dwie pozycje **zarówno** węzłów
```
sbd -d <SBD Device Name> list
```
![interwencja list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Wyślij testową wiadomość **jeden** węzły
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![interwencja list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 na **drugi** węzła (node2), można sprawdzić stan wiadomości
```
sbd  -d <SBD Device Name> list
```
![interwencja — lista message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 do przyjęcia config interwencja, zaktualizuj plik */etc/sysconfig/sbd* jako poniżej. Zaktualizuj plik na **zarówno** węzłów
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10. Uruchom usługę rozrusznik na **węzła podstawowego** (node1)
```
systemctl start pacemaker
```
![Start pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Jeśli usługa rozrusznik *nie powiedzie się*, zapoznaj się *scenariusz 5: rozrusznik usługi kończy się niepowodzeniem*

## <a name="5---joining-the-cluster"></a>5.   Dołączenie do klastra
W tej sekcji opisano, jak sprzęgać węzła do klastra.

### <a name="51-add-the-node"></a>5.1 Dodaj węzeł
Uruchom następujące polecenie **node2** aby umożliwić node2 dołączą do klastra.
```
ha-cluster-join
```
Jeśli zostanie wyświetlony *błąd* podczas przyłączania do klastra, można znaleźć *scenariusz 6: nie można dołączyć do klastra w węźle 2*.

## <a name="6---validating-the-cluster"></a>6.   Sprawdzanie poprawności klastra

### <a name="61-start-the-cluster-service"></a>6.1 Uruchom usługę klastrowania
Aby sprawdzić i opcjonalnie uruchomienie klastra po raz pierwszy w **zarówno** węzłów.
```
systemctl status pacemaker
systemctl start pacemaker
```
![pacemaker.png-systemctl — stan](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 monitorowanie stanu
Uruchom polecenie *crm_mon* zapewnienie **zarówno** węzły są online. Można uruchomić na **dowolnego z węzłów** klastra
```
crm_mon
```
![CRM mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) możesz także zalogować się do hawk, aby sprawdzić stan klastra *https://<node IP>: 7630*. Domyślny użytkownik jest hacluster, a hasło to linux. W razie potrzeby można zmienić hasła przy użyciu *haseł* polecenia.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurowanie właściwości klastra i zasoby 
W tej sekcji opisano kroki konfigurowania zasobów klastra.
W tym przykładzie, skonfiguruj następujący zasób pozostałe można skonfigurować (w razie potrzeby), umieszczając odwołanie do przewodnika SUSE HA. Wykonaj w pliku config **jednym z węzłów** tylko. Czy na węzła podstawowego.

- Bootstrap klastra
- STONITH urządzenia
- Wirtualny adres IP


### <a name="71-cluster-bootstrap-and-more"></a>7.1 bootstrap klastra i nie tylko
Dodaj klaster ładowania początkowego. Tworzenie pliku i Dodaj następujący tekst:
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
![CRM — Konfigurowanie crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH urządzenia
Dodaj zasób STONITH. Tworzenie pliku i Dodaj następujący tekst.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 wirtualnego adresu IP
Dodaj zasób wirtualnego adresu IP. Utwórz plik i Dodaj tekst zgodnie z poniższymi instrukcjami.
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

### <a name="74-validate-the-resources"></a>7.4 weryfikacji zasobów

Po uruchomieniu polecenia *crm_mon*, dwa zasoby jest widoczny.
![crm_mon_command.PNG](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ponadto można zobaczyć stan w *https://<node IP address>: 7630/cib/live/stanu*

![page.png-hawlk — stan](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testowanie procesu trybu failover
Aby przetestować proces trybu failover, Zatrzymaj usługę rozrusznik na node1 i pracy awaryjnej zasobów do node2.
```
Service pacemaker stop
```
Teraz, Zatrzymaj usługę rozrusznik na **node2** i nie można za pośrednictwem zasobów **node1**

**Przed trybu failover**
![przed failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**po pracy awaryjnej**
![po failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM mon po failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Rozwiązywanie problemów
W tej sekcji opisano kilka scenariuszy awarii, które mogą wystąpić podczas instalacji. Nie może zawsze napotkają tych problemów.

### <a name="scenario-1-cluster-node-not-online"></a>Scenariusz 1: Węzeł klastra nie w trybie online
Jeśli dowolny z węzłów nie są wyświetlane w Menedżerze klastra online, możesz je wypróbować, po przełączenia do trybu online.

Uruchomienie usługi iSCSI
```
service iscsid start
```

I teraz można logować się do tego węzła iSCSI
```
iscsiadm -m node -l
```
Oczekiwane dane wyjściowe prawdopodobnie poniżej
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenariusz 2: yast2 nie są wyświetlane widoku graficznego
Na ekranie graficznego yast2 jest używana do konfigurowania klastra o wysokiej dostępności w tym dokumencie. Jeśli yast2 nie Otwórz okno z graficznego, jak pokazano i zgłosić błąd Qt, wykonaj następujące czynności w krokach. Jeśli zostanie ona otwarta z graficznego okna, można pominąć kroki.

**Error**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Oczekiwane dane wyjściowe**

![center.png-yast — formant](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Jeśli yast2 nie można otworzyć widoku graficznego, wykonaj następujące kroki.

Zainstaluj wymagane pakiety. Musi być zalogowany jako użytkownik "root" i ma SMT zdefiniowano pobieranie/instalowanie pakietów.

Aby zainstalować te pakiety, należy użyć yast > oprogramowania > zarządzania oprogramowaniem > zależności > opcję "Zainstaluj zalecane pakietów...". Poniższy zrzut ekranu przedstawia oczekiwanego ekranów.
>[!NOTE]
>Należy wykonać czynności opisane w obu węzłach, dzięki czemu dostęp do widoku graficznego yast2 z obu węzłów.

![yast sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

W obszarze zależności, wybierz opcję "Zainstaluj zalecane Packages" ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Przejrzyj zmiany i kliknij przycisk OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Pakiet instalacji będzie kontynuowane ![yast wykonywania installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknij przycisk Dalej

![yast — instalacja report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kliknij przycisk Zakończ

Należy również zainstalować pakiety libqt4 i libyui qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 powinno być możliwe do otwierania widoku graficznego teraz, jak pokazano poniżej.
![center.png-yast2 — formant](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenariusz 3: yast2 jest nie opcję wysokiej dostępności
W przypadku opcji wysokiej dostępności mają być wyświetlane na centrum sterowania yast2 należy zainstalować dodatkowe pakiety.

Przy użyciu Yast2 > oprogramowania > zarządzania oprogramowaniem > Wybierz następujące wzorce

- Podstawowy serwer SAP HANA
- Narzędzi i kompilatora C/C++
- Wysoka dostępność
- Podstawowy serwer aplikacji SAP

Poniższym ekranie przedstawiono kroki, aby zainstalować te wzorce.

Przy użyciu yast2 > oprogramowania > Zarządzanie oprogramowaniem

![center.png-yast2 — formant](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Wybierz wzorce

![yast pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kliknij przycisk **zaakceptować**

![yast zmienić packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kliknij przycisk **kontynuować**

![yast2 — wykonywanie installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Kliknij przycisk **dalej** po zakończeniu instalacji

![yast2 — instalacja report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenariusz 4: Instalacja HANA kończy się niepowodzeniem z powodu błędu zestawy gcc
Instalacja HANA kończy się niepowodzeniem z powodu następującego błędu.

![Hana — instalacja error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Aby rozwiązać ten problem, należy zainstalować bibliotek (libgcc_sl i libstdc ++ 6) jako poniżej.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenariusz 5: Wystąpiła awaria usługi rozrusznik

Następujący problem wystąpił podczas uruchamiania usługi rozrusznik.

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
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
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

Aby rozwiązać problem, należy usunąć następujący wiersz z pliku */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.PNG](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenariusz 6: Węzeł 2 nie można dołączyć do klastra

Podczas przyłączania node2 do istniejącego klastra przy użyciu *ha klastra sprzężenia* polecenia wystąpił następujący błąd.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha klastra sprzężenia error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Aby rozwiązać problem, uruchom następujące polecenie na obu węzłach

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![SSH-keygen-node1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![SSH-keygen-node2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po poprzednim poprawkę Węzeł2 powinien zostaną dodane do klastra

![ha klastra sprzężenia fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Ogólna dokumentacja
Więcej informacji na temat SUSE HA instalacji można znaleźć w następujących artykułach: 

- [SAP HANA SR zoptymalizowana scenariusza](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Oparty na magazynie ogrodzenia](https://www.suse.com/documentation/sle-ha-2/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog — przy użyciu klastra rozrusznik dla SAP HANA — część 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog — przy użyciu klastra rozrusznik dla SAP HANA — część 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)