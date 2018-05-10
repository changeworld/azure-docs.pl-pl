---
title: Omówienie agenta maszyny Wirtualnej systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania agenta systemu Linux (agenta waagent) do zarządzania na komputerze wirtualnym interakcji z kontrolerem sieci szkieletowej Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2fe93cba2c8b295925ce4cfa8c3017ee1373261
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Opis i przy użyciu agenta systemu Linux platformy Azure

Agent systemu Linux usługi Microsoft Azure (agenta waagent) zarządza systemu Linux i FreeBSD inicjowania obsługi administracyjnej i maszyny Wirtualnej interakcji z kontrolera sieci szkieletowej Azure. Oprócz agenta systemu Linux, zapewniając funkcjonalność inicjowania obsługi administracyjnej platforma Azure udostępnia opcję użycia chmury init dla niektórych systemów operacyjnych Linux. Agent systemu Linux udostępnia następujące funkcje dla systemów Linux i FreeBSD IaaS wdrożenia:

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Inicjowanie obsługi obrazu**
  
  * Tworzenie konta użytkownika
  * Konfigurowanie typów uwierzytelniania SSH
  * Wdrożenie par kluczy i kluczy publicznych SSH
  * Ustawienie nazwy hosta
  * Publikowanie nazwy hosta DNS platformy
  * Raportowanie odcisku klucza SSH hosta do platformy
  * Zarządzanie zasobami dysku
  * Formatowanie i Instalowanie zasobu dysku
  * Konfigurowanie obszaru wymiany
* **Sieć**
  
  * Zarządza trasy, aby zwiększyć zgodność z serwerów DHCP platformy
  * Zapewnia stabilność Nazwa interfejsu sieciowego
* **Kernel**
  
  * Konfiguruje wirtualną architekturę NUMA (Wyłącz dla jądra <`2.6.37`)
  * Wykorzystuje entropii funkcji Hyper-V dla /dev/random
  * Konfiguruje SCSI przekroczeń limitu czasu dla urządzenia katalogu głównego (który może być zdalne)
* **Diagnostyka**
  
  * Przekierowywanie konsoli do portu szeregowego
* **Wdrożenia programu SCVMM**
  
  * Wykrywa i używa do ładowania agenta programu VMM dla systemu Linux podczas uruchamiania w środowisku programu System Center Virtual Machine Manager 2012 R2
* **Rozszerzenia maszyny Wirtualnej**
  
  * Wstaw składnik utworzone przez firmę Microsoft i partnerów do maszyny Wirtualnej systemu Linux (IaaS) umożliwiające oprogramowania i konfiguracji automatyzacji
  * Implementacja odwołanie rozszerzenia maszyny Wirtualnej na [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikacja
Przepływ informacji od platformy do agenta odbywa się za pośrednictwem dwóch kanałów:

* Czas rozruchu dołączonych dysków DVD do wdrożenia IaaS. Ten dysk DVD zawiera zgodne OVF pliku konfiguracji, który zawiera wszystkie informacje o udostępnianiu niż rzeczywista keypairs SSH.
* Punkt końcowy protokołu TCP udostępnianie interfejsu API REST używany do uzyskania wdrażanie i Konfigurowanie topologii.

## <a name="requirements"></a>Wymagania
Poniższe systemy zostały przetestowane i są znane, aby pracować z agenta systemu Linux platformy Azure:

> [!NOTE]
> Ta lista może się różnić od oficjalnego listę obsługiwanych systemów na platformie Microsoft Azure, zgodnie z opisem w tym miejscu: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 Z DODATKIEM SP3 +
* Oracle Linux 6.4 +

Inne obsługiwane systemy:

* FreeBSD 10 + (Azure agenta systemu Linux v2.0.10 +)

Agent systemu Linux jest zależna od niektórych pakietów systemu prawidłowego działania:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Narzędzia systemu plików: ulec sfdisk fdisk, mkfs,
* Narzędzia hasło: chpasswd, sudo
* Narzędzia do edycji tekstu: mniejszyć, grep
* Narzędzia sieci: trasy ip
* Obsługuje jądra służący do instalowania funkcji zdefiniowanej przez użytkownika, systemy plików.

## <a name="installation"></a>Instalacja
Instalacja przy użyciu obr. / min lub pakietu DEB z repozytorium pakietów z dystrybucji jest preferowaną metodą instalacji i uaktualniania agenta systemu Linux platformy Azure. Wszystkie [zatwierdzone dostawców dystrybucji](../linux/endorsed-distros.md) integracji pakiet agenta systemu Linux platformy Azure z repozytoriami i obrazów.

Zapoznaj się z dokumentacją w [repozytorium agenta systemu Linux platformy Azure w serwisie GitHub](https://github.com/Azure/WALinuxAgent) dla zaawansowane opcje instalacji, takich jak instalowanie ze źródła lub niestandardowe lokalizacje lub prefiksów.

## <a name="command-line-options"></a>Opcje wiersza polecenia
### <a name="flags"></a>Flagi
* verbose: Zwiększ poziom szczegółowości określonego polecenia
* Wymuś: Pomiń interakcyjne potwierdzenie dla niektórych poleceń

### <a name="commands"></a>Polecenia
* Pomoc: Wyświetla listę obsługiwanych poleceń i flagi.
* deprovision: próba czyszczenia systemu i była odpowiednia dla reprovisioning. Następujące usuwa operacji:
  
  * Wszystkie klucze hosta SSH (jeśli Provisioning.RegenerateSshHostKeyPair "y" w pliku konfiguracji)
  * Konfiguracja serwera nazw w /etc/resolv.conf
  * Hasła głównego z /etc/shadow (jeśli Provisioning.DeleteRootPassword "y" w pliku konfiguracji)
  * Buforowane dzierżawy klientów DHCP
  * Zresetowanie nazwy hosta na wartość localhost.localdomain

> [!WARNING]
> Cofanie zastrzegania nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne i nadaje się do ponownej dystrybucji.
> 
> 

* deprovision + użytkownika: wykonuje wszystkie elementy w - deprovision (powyżej) i również usuwa ostatnie konto użytkownika elastycznie (uzyskane z /var/lib/waagent) i skojarzone dane. Ten parametr jest podczas usuwania inicjowania obsługi administracyjnej z obrazem, który został wcześniej inicjowania obsługi administracyjnej na platformie Azure, mogą być przechwytywane i użyć ponownie.
* Wersja: Wyświetla wersję agenta waagent
* serialconsole: konfiguruje CHODNIKÓW, aby oznaczyć ttyS0 (pierwszy port szeregowy) jako konsoli rozruchu. Dzięki temu, że jądra rozruchu dzienniki są wysyłane do portu szeregowego i udostępnione do debugowania.
* demon: uruchomiono agenta waagent jako demon do interakcji z platformą zarządzania. Ten argument zostanie określony do agenta waagent w skrypcie inicjowania agenta waagent.
* Rozpocznij: uruchomiono agenta waagent jako proces w tle

## <a name="configuration"></a>Konfigurowanie
Plik konfiguracji (/ etc/waagent.conf) steruje agenta waagent akcje. Poniżej przedstawiono przykładowy plik konfiguracji:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

Następujące czynności, które opisano różne opcje konfiguracji. Opcje konfiguracji są trzy typy; Wartość logiczna, ciąg lub liczba całkowita. Opcje konfiguracji logicznych można określić jako "y" lub "n". Specjalne słowo kluczowe "None" mogą być używane dla niektórych pozycji konfiguracji typu ciąg jako następujące informacje:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Dzięki temu użytkownik włączyć lub wyłączyć funkcję inicjowania obsługi administracyjnej w agencie. Prawidłowe wartości to "y" lub "n". Jeśli Inicjowanie obsługi administracyjnej jest wyłączone, hosta i użytkownika kluczy SSH w obrazie zostaną zachowane i określona na platformie Azure, inicjowanie obsługi interfejsu API konfiguracja jest ignorowana.

> [!NOTE]
> `Provisioning.Enabled` Jest domyślnie "n" na Ubuntu chmury obrazów użyj init chmury w celu obsługi.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Jeśli podczas procesu inicjowania obsługi administracyjnej jest usuwane zestawu, w pliku/etc/tle hasła głównego.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Jeśli zestawu i wszystkich hostów par kluczy SSH (ecdsa, dsa lub rsa) zostaną usunięte podczas procesu inicjowania obsługi administracyjnej z/etc/ssh /. I generowany jest jeden świeże pary kluczy.

Można skonfigurować we wpisie Provisioning.SshHostKeyPairType jest typ szyfrowania dla świeże pary kluczy. Niektórych dystrybucji ponownie utworzyć pary kluczy SSH dla dowolnego Brak typów szyfrowania, podczas ponownego uruchamiania demona SSH (na przykład po ponownym rozruchu).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
To może należeć do typu algorytmu szyfrowania, który jest obsługiwany przez demon SSH na maszynie wirtualnej. Zazwyczaj obsługiwane wartości to "rsa", "dsa" i "ecdsa". "putty.exe" w systemie Windows nie obsługuje "ecdsa". Tak Jeśli zamierzasz używać putty.exe w systemie Windows do nawiązania połączenia wdrożenia systemu Linux, użyj "rsa" lub "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Jeśli zestaw, agenta waagent monitoruje maszyny wirtualnej systemu Linux zmiany nazwy hosta (jak zwracany przez polecenie "Nazwa hosta") i automatycznie zaktualizować konfiguracji sieci w obrazie w celu odzwierciedlenia zmian. Aby wypchnąć zmiany nazwy do serwerów DNS, sieci zostanie ponownie uruchomiony w maszynie wirtualnej. Krótko mówiąc powoduje utratę łączności z Internetem.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, agenta waagent dekoduje CustomData z formatu Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, agenta waagent wykonuje CustomData po zainicjowaniu obsługi administracyjnej.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Ta opcja umożliwia hasło użytkownika sys zresetować; Domyślnie jest wyłączona.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algorytm używany przez crypt podczas generowania skrótu hasła.  
 1 - MD5  
 2a - Blowfish  
 5 - ALGORYTMU SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Długość losowe soli używana podczas generowania skrótu hasła.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Jeśli ustawiona, dysk zasobu dostarczone przez platformę jest sformatowany i zainstalowanych w wyniku agenta waagent, jeśli typ filesystem żądanej przez użytkownika w "ResourceDisk.Filesystem" jest inna niż "ntfs". Jednej partycji typu systemu Linux (83) są udostępniane na dysku. Ta partycja nie jest sformatowany, jeśli można zainstalować pomyślnie.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
To ustawienie określa typ systemu plików dla zasobu dysku. Obsługiwane wartości różnią się dystrybucji systemu Linux. Jeśli ciąg jest następnie mkfs X. X powinny znajdować się w obrazie systemu Linux. SLES 11 obrazów zwykle należy użyć "ext3". Obrazy FreeBSD należy użyć "ufs2" w tym miejscu.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Określa ścieżkę, w którym jest zainstalowany dysk zasobu. Dysk zasobów jest *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Określa opcje instalacji dysku do przekazania do polecenia -o instalacji. Jest to rozdzielana przecinkami lista wartości, np. "nodev, nosuid". Zobacz mount(8), aby uzyskać szczegółowe informacje.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Jeśli ustawiona, plik wymiany (/ swapfile) jest utworzony na dysku zasobów i dodana do obszaru wymiany w systemie.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
Rozmiar pliku wymiany w megabajtach.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Jeśli jest boosted zestawu, szczegółowości dziennika. Agenta Waagent rejestruje /var/log/waagent.log i korzysta z funkcji logrotate systemu obracania dzienników.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, agent podejmie próbę instalacji, a następnie załadować sterownik jądra RDMA, które odpowiadają wersji oprogramowania układowego od używanego sprzętu.

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
To ustawienie określa SCSI limit czasu w sekundach na dyskach systemu operacyjnego dysku i danych. Jeśli nie jest ustawiony, systemu, które będą używane wartości domyślne.

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
To ustawienie może być używane do określenia ścieżki alternatywnej dla biblioteki openssl binarnego do użycia dla operacji kryptograficznych.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Jeśli zestaw, agent używa tego serwera proxy do uzyskania dostępu do Internetu. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Włącz lub wyłącz automatyczne aktualizowanie stanu celem przetwarzania; Domyślnie włączone.



## <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Images
Korzystanie z obrazów chmury Ubuntu [init chmury](https://launchpad.net/ubuntu/+source/cloud-init) do wykonywania wielu zadań konfiguracji, które w przeciwnym razie będą zarządzane przez agenta systemu Linux platformy Azure. Zastosuj następujące różnice:

* **Provisioning.Enabled** wartość domyślna to "n" na Ubuntu chmury obrazów użyj init chmury do wykonywania zadań inicjowania obsługi administracyjnej.
* Poniższe parametry konfiguracji nie mają wpływu na obrazy chmury Ubuntu, używanym do zarządzania dyskami zasobów i obszar wymiany init chmury:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Aby uzyskać więcej informacji zobacz następujące zasoby do konfigurowania punktu instalacji zasobów dysku i Zamień miejsce na obrazy chmury Ubuntu podczas inicjowania obsługi:
  
  * [Ubuntu Witryna typu Wiki: Konfigurowanie partycji wymiany](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Wstrzykiwania niestandardowe dane do maszyny wirtualnej platformy Azure](../windows/classic/inject-custom-data.md)

