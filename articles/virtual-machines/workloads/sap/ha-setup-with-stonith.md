---
title: Wysoką dostępność, skonfiguruj za pomocą metody STONITH platformy SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Nawiązania wysokiej dostępności dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) przy użyciu pomocą metody STONITH SUSE
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7cbec63cb04075977c167d8b21bf3128e91434f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710056"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Wysoka dostępność skonfigurowane w systemie SUSE przy użyciu pomocą metody STONITH
Ten dokument zawiera szczegółowe instrukcje krok po kroku, aby skonfigurować wysoką dostępność w systemie SUSE działających za pomocą urządzenia pomocą metody STONITH.

**Zastrzeżenie:** *Ten przewodnik jest uzyskiwana w wyniku testowania konfiguracji w środowisku dużych wystąpień HANA firmy Microsoft, w którym pomyślnie działa. Ponieważ zespół zarządzania usług firmy Microsoft dla dużych wystąpień HANA nie obsługuje systemu operacyjnego, może być konieczne skontaktuj się z systemem SUSE uzyskać dodatkowe informacje o rozwiązywaniu lub objaśnień do warstwy system operacyjny. Zespół zarządzający usługi Microsoft konfigurowanie za pomocą metody STONITH urządzenia i w pełni obsługuje i mogą brać udział w celu rozwiązywania problemów dla problemów z urządzeniami pomocą metody STONITH.*
## <a name="overview"></a>Omówienie
Aby skonfigurować wysoką dostępność przy użyciu klastra SUSE, musi spełniać następujące wymagania wstępne.
### <a name="pre-requisites"></a>Wymagania wstępne
- Aprowizowanych dużych wystąpień HANA
- System operacyjny jest zarejestrowany.
- Serwery dużych wystąpień HANA są podłączone do serwera SMT, aby uzyskać poprawek/pakietów
- System operacyjny mają zainstalowane najnowsze poprawki.
- Skonfigurowano NTP (serwer czasu)
- Przeczytać i sprawdzić najnowszą wersję dokumentacji SUSE na temat instalacji o wysokiej dostępności

### <a name="setup-details"></a>Szczegóły ustawień
W tym przewodniku używane są następujące ustawienia:
- System operacyjny: SLES 12 z dodatkiem SP1 dla programu SAP
- Dużych wystąpień HANA: 2xS192 (czterema gniazd, 2 TB)
- Wersja platformy HANA: HANA 2.0 Z DODATKIEM SP1
- Nazwy serwerów: sapprdhdb95 (Węzeł1) i sapprdhdb96 (Węzeł2)
- : Pomocą metody STONITH iSCSI na podstawie pomocą metody STONITH urządzenia
- NTP w jednym z węzłów dużych wystąpień HANA

Po skonfigurowaniu dużych wystąpieniach HANA z replikacji systemu HANA mogą żądać zespół zarządzania usług firmy Microsoft do skonfigurowania pomocą metody STONITH. Jeśli masz już istniejący klient mający dużych wystąpień HANA zainicjowano obsługę administracyjną i potrzeb pomocą metody STONITH urządzenia dla Twojego istniejących blokach, należy podać następujące informacje do zespołu zarządzania usługami firmy Microsoft w formularzu żądania usługi (SRF). Możesz poprosić SRF formularza za pomocą Opiekun klienta lub Contact Twojej firmy Microsoft związane z dołączaniem dużych wystąpień HANA. Nowi klienci mogą żądać pomocą metody STONITH urządzenia podczas przeprowadzania obsługi administracyjnej. Dane wejściowe są dostępne w formularzu żądania aprowizacji.

- Nazwa serwera i serwera adres IP (na przykład myhanaserver1, 10.35.0.1)
- Lokalizacja (na przykład wschodnie stany USA)
- Nazwa klienta (na przykład Microsoft)
- Identyfikator SID — identyfikator systemu HANA (na przykład H11)

Po skonfigurowaniu urządzenia pomocą metody STONITH zespół zarządzania usług firmy Microsoft umożliwiają interwencja nazwę urządzenia i adres IP magazynu iSCSI, które służy do konfigurowania ustawień pomocą metody STONITH. 

Aby skonfigurować połączenie typu end to end wysokiej dostępności, używając pomocą metody STONITH, poniższe kroki musi występować:

1.  Identyfikowanie urządzenia interwencja
2.  Inicjowanie urządzenia interwencja
3.  Konfigurowanie klastra
4.  Konfigurowanie strażnika Softdog
5.  Dołącz do węzła do klastra
6.  Sprawdź poprawność klastra
7.  Konfigurowanie zasobów do klastra
8.  Testowanie procesu pracy awaryjnej

## <a name="1---identify-the-sbd-device"></a>1.   Identyfikowanie urządzenia interwencja
W tej sekcji opisano sposób określić urządzenie interwencja ustawień, po skonfigurowaniu pomocą metody STONITH zespołu zarządzania usługi Microsoft. **Ta sekcja ma zastosowanie tylko do istniejącego klienta**. Jeśli jesteś nowego klienta, zespół zarządzający usługi Microsoft zapewniają interwencja nazwę urządzenia dla Ciebie i możesz pominąć tę sekcję.

1.1 modyfikowanie */etc/iscsi/initiatorname.isci* do 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Zarządzanie usługami firmy Microsoft zapewniają te parametry. Zmodyfikuj plik w **zarówno** węzłów, jednak numer węzła różni się w każdym węźle.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 modyfikowanie */etc/iscsi/iscsid.conf*: Ustaw *node.session.timeo.replacement_timeout=5* i *node.startup = automatyczne*. Zmodyfikuj plik w **zarówno** węzłów.

1.3 wykonaj polecenie odnajdywania, pokazuje czterech sesji. Uruchom go na obu węzłach.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 wykonaj polecenie, aby zalogować się do urządzenie iSCSI, pokazuje czterech sesji. Uruchom go na **zarówno** węzłów.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 wykonywanie skryptu ponownego skanowania: *ponowne skanowanie scsi-bus.sh*.  Ten skrypt zawiera nowe dyski utworzone automatycznie.  Uruchom go na obu węzłach. Powinien zostać wyświetlony numer LUN jest większa od zera (na przykład: 1, 2 itd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

W wersji 1.6 można pobrać nazwy urządzenia, uruchom polecenie *fdisk-l*. Uruchom go na obu węzłach. Wybierz urządzenie z rozmiarem **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicjowanie urządzenia interwencja

2.1 zainicjować urządzenia interwencja na **zarówno** węzłów

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Sprawdź, co został zapisany na urządzeniu. Nie **zarówno** węzłów

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurowanie klastra
W tej sekcji opisano kroki konfigurowania klastra SUSE HA.
### <a name="31-package-installation"></a>3.1 instalacja pakietu
3.1.1 Sprawdź ha_sles i SAPHanaSR doc wzorce są zainstalowane. Jeśli nie jest zainstalowany, zainstaluj je. Zainstaluj ją na **zarówno** węzłów.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Konfigurowanie klastra
3.2.1 można użyć *zaświadczanie o kondycji klastra — inicjowania* polecenie lub skonfigurowania klastra za pomocą Kreatora yast2. W takim przypadku Kreator yast2 jest używany. Wykonaj ten krok **tylko w węźle podstawowym**.

Postępuj zgodnie z yast2 > wysokiej dostępności > klastra ![yast — kontrola center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kliknij przycisk **anulować** ponieważ pakiet halk2 jest już zainstalowany.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kliknij przycisk **kontynuować**

Oczekiwana wartość = Liczba wdrożonych (w tym przypadku 2) węzłów ![yast klastra-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) kliknij **dalej**
![yast klastra — Konfigurowanie csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Dodaj nazwy węzłów, a następnie kliknij przycisk "Dodaj pliki sugerowane"

Kliknij przycisk "Włącz csync2"

Kliknij pozycję "Generuj wstępnej Shared-Keys", prezentuje poniżej okna podręcznego

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kliknij przycisk **OK**.

Uwierzytelnianie jest wykonywane przy użyciu adresów IP i wstępnie przygotowany shared kluczy w Csync2. Plik klucza jest generowany przy użyciu /etc/csync2/key_hagroup -k csync2. Key_hagroup pliku powinny być skopiowane do wszystkich elementów członkowskich klastra ręcznie po jej utworzeniu. **Upewnij się, aby skopiować plik z węzła 1 do Węzeł2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Click **Next**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

W przypadku opcji domyślnej rozruch zostało wyłączone, zmień ją na "włączone", więc program pacemaker jest uruchomiona podczas rozruchu. Istnieje możliwość wyboru, zgodnie z wymaganiami konfiguracji.
Kliknij przycisk **dalej** i zakończeniu konfiguracji klastra.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Konfigurowanie strażnika Softdog
W tej sekcji opisano konfigurację strażnika (softdog).

4.1, Dodaj następujący wiersz do */etc/init.d/boot.local* na **zarówno** węzłów.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2, zaktualizuj plik */etc/sysconfig/sbd* na **zarówno** węzłów jako pokazano poniżej:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 załadowania modułu jądra na **zarówno** węzłów, uruchamiając następujące polecenie
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

W wersji 4.4 Sprawdź i upewnij się, że softdog działa jako pokazano poniżej na **zarówno** węzły:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 uruchomienia urządzenia interwencja na **zarówno** węzłów
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 testować demona interwencja **zarówno** węzłów. Po skonfigurowaniu na znajdują się dwie pozycje **zarówno** węzłów
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Wyślij testową wiadomość **jeden** węzły
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 na **drugi** węzła (Węzeł2), możesz sprawdzić stan komunikatu
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 do przyjęcia config interwencja, zaktualizuj plik */etc/sysconfig/sbd* jako pokazano poniżej. Aktualizowanie pliku na **zarówno** węzłów
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10. Uruchom usługę program pacemaker w **węzła podstawowego** (Węzeł1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Jeśli usługa program pacemaker *nie powiedzie się*, można znaleźć *scenariusz 5: Program pacemaker usługi kończy się niepowodzeniem*

## <a name="5---joining-the-cluster"></a>5.   Dołączenie do klastra
W tej sekcji opisano sposób dołączania węzła do klastra.

### <a name="51-add-the-node"></a>5.1 Dodaj węzeł
Uruchom następujące polecenie **Węzeł2** umożliwiające Węzeł2 dołączą do klastra.
```
ha-cluster-join
```
Jeśli zostanie wyświetlony *błąd* podczas przyłączania do klastra, można znaleźć *6 scenariusza: Węzeł 2 nie można dołączyć do klastra*.

## <a name="6---validating-the-cluster"></a>6.   Sprawdzanie poprawności klastra

### <a name="61-start-the-cluster-service"></a>6.1 Uruchom usługę klastrowania
Aby sprawdzić i opcjonalnie uruchomienie klastra po raz pierwszy w **zarówno** węzłów.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 monitorowania stanu
Uruchom polecenie *crm_mon* zapewnienie **zarówno** węzły są w trybie online. Zostanie uruchomiony w **dowolnego z węzłów** klastra
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) możesz również zalogować się do hawk, aby sprawdzić stan klastra *https://\<adres IP węzła >: 7630*. Użytkownik domyślny jest hacluster, a hasło to linux. Jeśli to konieczne, można zmienić hasło przy użyciu *haseł* polecenia.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurowanie właściwości klastra i zasoby 
W tej sekcji opisano kroki, aby skonfigurować zasoby klastra.
W tym przykładzie, skonfiguruj następujące zasoby pozostałe można skonfigurować (jeśli jest to konieczne), odwołując się do przewodnika SUSE wysokiej dostępności. Wykonaj konfiguracji w **jeden z węzłów** tylko. To w węźle podstawowym.

- Usługa ładowania początkowego klastra
- Urządzenie pomocą metody STONITH
- Wirtualny adres IP


### <a name="71-cluster-bootstrap-and-more"></a>7.1 bootstrap klastra i nie tylko
Dodaj klaster bootstrap. Utwórz plik i Dodaj tekst jako pokazano poniżej:
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

### <a name="72-stonith-device"></a>7.2 urządzenie pomocą metody STONITH
Dodaj zasób pomocą metody STONITH. Utwórz plik i Dodaj tekst jako pokazano poniżej.
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

### <a name="73-the-virtual-ip-address"></a>7.3 wirtualnego adresu IP
Dodaj wirtualny adres IP zasobu. Utwórz plik i Dodaj tekst zgodnie z poniższymi instrukcjami.
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

### <a name="74-validate-the-resources"></a>7.4 sprawdzanie zasobów

Po uruchomieniu polecenia *crm_mon*, zobaczysz ma dwa zasoby.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ponadto możesz zobaczyć stan w *https://\<adres IP węzła >: 7630/cib/live/stanu*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testowanie procesu pracy awaryjnej
Aby testowanie procesu pracy awaryjnej, należy zatrzymać usługę program pacemaker w Węzeł1 i zasobów, tryb failover Węzeł2.
```
Service pacemaker stop
```
Teraz, Zatrzymaj usługę program pacemaker na **Węzeł2** i zasoby do trybu failover **Węzeł1**

**Przed włączeniem trybu failover**
![przed failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**po włączeniu trybu failover**
![po failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM mon po failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Rozwiązywanie problemów
W tej sekcji opisano kilka scenariuszy awarii, które mogą wystąpić podczas instalacji. Te problemy nie może być koniecznie twarzy.

### <a name="scenario-1-cluster-node-not-online"></a>Scenariusz 1: Węzeł klastra nie online
Jeśli dowolny węzeł nie są wyświetlane w trybie online w Menedżerze klastra, możesz wypróbować następujące opcje, aby przełączyć do trybu online.

Uruchomienie usługi iSCSI
```
service iscsid start
```

A teraz powinno być możliwe do logowania się w tym węźle iSCSI
```
iscsiadm -m node -l
```
Wygląda oczekiwane dane wyjściowe poniżej
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenariusz 2: yast2 nie pokazuje widok graficzny
Na ekranie graficzny yast2 służy do skonfigurowania klastra o wysokiej dostępności, w tym dokumencie. Jeśli yast2 otwarty z oknem graficzny, jak pokazano i nie zgłaszać błędu Qt, wykonaj kroki, jako pokazano poniżej. Jeśli zostanie otwarty z oknem graficznych, można pominąć kroki.

**Error**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Oczekiwane dane wyjściowe**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Jeśli yast2 nie można otworzyć za pomocą widok graficzny, wykonaj następujące kroki.

Zainstaluj wymagane pakiety. Musi być zalogowany jako użytkownik "root" i zostały skonfigurowane do SMT pobieranie/instalowanie pakietów.

Aby zainstalować te pakiety, należy użyć yast > oprogramowanie > zarządzania oprogramowaniem > zależności > Opcja "Zainstaluj zalecane pakiety...". Poniższy zrzut ekranu przedstawia oczekiwanego ekranów.
>[!NOTE]
>Należy wykonać czynności opisane w obu węzłach, dzięki czemu dostęp do widoku graficznego yast2 z obu węzłów.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

W obszarze zależności, wybierz pozycję "Zainstaluj zalecane Packages" ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Przejrzyj zmiany, a następnie kliknij przycisk OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Pakiet instalacji kontynuowane ![yast — wykonywanie installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknij przycisk Dalej

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kliknij przycisk Zakończ

Należy także zainstalować pakiety libqt4 i libyui qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 powinien móc otworzyć widok graficzny teraz, jak pokazano poniżej.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenariusz 3: yast2 jest nie opcję wysokiej dostępności
Opcję wysokiej dostępności, które mają być wyświetlane w centrum sterowania yast2 musisz zainstalować dodatkowe pakiety.

Za pomocą Yast2 > oprogramowanie > zarządzania oprogramowaniem > Wybierz następujące wzorce

- Podstawowy serwer SAP HANA
- Kompilator C/C++ i narzędzia
- Wysoka dostępność
- Podstawowy serwer aplikacji SAP

Na poniższym ekranie przedstawiono kroki, aby zainstalować wzorców.

Za pomocą yast2 > oprogramowanie > Zarządzanie oprogramowaniem

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Wybierz wzorców

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kliknij przycisk **zaakceptować**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kliknij przycisk **kontynuować**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Kliknij przycisk **dalej** po zakończeniu instalacji

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenariusz 4: Instalacja oprogramowania HANA kończy się niepowodzeniem z powodu błędu zestawy gcc
Instalacja oprogramowania HANA kończy się niepowodzeniem z powodu następującego błędu.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Aby rozwiązać ten problem, należy zainstalować biblioteki (libgcc_sl i libstdc ++ 6) jako pokazano poniżej.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenariusz 5: Program pacemaker usługi kończy się niepowodzeniem

Wystąpił następujący problem podczas uruchamiania usługi. program pacemaker.

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

Aby rozwiązać ten problem, usuń następujący wiersz z pliku */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenariusz 6: Węzeł 2 nie można dołączyć do klastra

Podczas przyłączania Węzeł2 do istniejącego klastra przy użyciu *zaświadczanie o kondycji klastra — sprzężenia* polecenia wystąpił następujący błąd.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Aby rozwiązać problem, uruchom następujące polecenie w obu węzłach

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po poprzedniej poprawki Węzeł2, Pobierz dodane do klastra

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Ogólna dokumentacja
Więcej informacji na temat instalacji SUSE wysokiej dostępności można znaleźć w następujących artykułach: 

- [SAP HANA SR zoptymalizowane pod kątem scenariusza](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Na podstawie magazynu ogrodzenia](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog — przy użyciu klastra program Pacemaker for SAP HANA — część 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog — przy użyciu klastra program Pacemaker for SAP HANA — część 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
