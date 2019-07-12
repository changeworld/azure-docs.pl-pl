---
title: Omówienie agenta maszyny Wirtualnej systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania agenta systemu Linux (waagent) do zarządzania interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59a0cdd29e50501f023faf323948a400f325df0b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706154"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Znajomość i wykorzystanie agenta systemu Linux dla platformy Azure

Linux Agent usług Microsoft Azure (waagent) zarządza systemu Linux i FreeBSD inicjowania obsługi administracyjnej i interakcji maszyny Wirtualnej z kontrolerem sieci szkieletowej platformy Azure. Oprócz agenta systemu Linux, dostarczanie funkcji inicjowania obsługi administracyjnej system Azure oferuje możliwość korzystania z pakietu cloud-init do niektórych systemów operacyjnych Linux. Agent systemu Linux udostępnia następujące funkcje dla systemów Linux i FreeBSD IaaS wdrożeń:

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Udostępnianie obrazów**
  
  * Tworzenie konta użytkownika
  * Konfigurowanie typów uwierzytelniania SSH
  * Wdrożenie par kluczy i kluczy publicznych SSH
  * Ustawianie nazwy hosta
  * Publikowanie nazwy hosta do korzystania z platformy DNS
  * Raportowanie odcisk palca klucza hosta SSH dla platformy
  * Zarządzanie dyskami zasobów
  * Formatowanie i zainstalować dysku zasobów
  * Konfigurowanie obszaru wymiany
* **Sieć**
  
  * Zarządza trasy, aby zwiększyć zgodność z serwerów DHCP platformy
  * Zapewnia stabilność Nazwa interfejsu sieciowego
* **Kernel**
  
  * Konfiguruje wirtualną architekturę NUMA (Wyłącz dla jądra <`2.6.37`)
  * Wykorzystuje entropii funkcji Hyper-V dla /dev/random
  * Konfiguruje SCSI przekroczeń limitu czasu dla głównego urządzenia, (która może być zdalne)
* **Diagnostyka**
  
  * Przekierowywanie konsoli do portu szeregowego
* **Wdrożenia programu SCVMM**
  
  * Wykrywa i używa do ładowania agenta programu VMM dla systemu Linux podczas pracy w środowisku programu System Center Virtual Machine Manager 2012 R2
* **Rozszerzenie maszyny Wirtualnej**
  
  * Wstrzykiwanie składnika opracowane przez firmę Microsoft i partnerów do maszyny Wirtualnej systemu Linux (IaaS) umożliwiające oprogramowania i konfiguracji usługi automation
  * Implementacja referencyjna rozszerzenia maszyny Wirtualnej na [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikacja
Przepływ informacji z platformą do agenta odbywa się za pośrednictwem dwóch kanałów:

* Czas rozruchu dołączonych dysków DVD dla wdrożeń rozwiązań IaaS. Ten dysk DVD obejmuje CLS OVF pliku konfiguracji, który zawiera wszystkie informacje o udostępnianiu niż rzeczywista keypairs SSH.
* Punkt końcowy protokołu TCP, udostępnianie interfejsu API REST używany można uzyskać, wdrażania i konfiguracji topologii.

## <a name="requirements"></a>Wymagania
Poniższe systemy zostały przetestowane i znane pracy z agentem systemu Linux platformy Azure:

> [!NOTE]
> Ta lista może różnić się od oficjalną listę obsługiwanych systemów na platformie Microsoft Azure, tak jak opisano tutaj: [https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Inne obsługiwane systemy:

* FreeBSD 10 + (Agent systemu Linux platformy Azure v2.0.10 i nowsze)

Agent systemu Linux jest zależna od niektórych pakietów systemu w celu poprawnego:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Filesystem utilities: sfdisk, fdisk, mkfs, parted
* Narzędzia hasło: chpasswd, "sudo"
* Narzędzia do przetwarzania tekstu: sed, grep
* Narzędzia sieci: trasy ip
* Jądra obsługuje instalowanie systemów plików funkcji zdefiniowanej przez użytkownika.

## <a name="installation"></a>Instalacja
Instalację przy użyciu RPM lub DEB pakietu z repozytorium pakietów w Twojej dystrybucji jest preferowaną metodą instalacji i uaktualniania agenta systemu Linux dla platformy Azure. Wszystkie [zatwierdzonego dla dostawców dystrybucji](../linux/endorsed-distros.md) Zintegruj pakiet Azure Linux agent repozytoriów i obrazów.

Zapoznaj się z dokumentacją w [repozytorium agenta systemu Linux platformy Azure w serwisie GitHub](https://github.com/Azure/WALinuxAgent) dla zaawansowane opcje instalacji, takich jak instalowanie ze źródła lub niestandardowe lokalizacje lub prefiksy.

## <a name="command-line-options"></a>Opcje wiersza polecenia
### <a name="flags"></a>flagi
* verbose: Zwiększ poziom szczegółowości określone polecenie
* Wymuś: Pomiń interaktywne potwierdzenie dla niektórych poleceń

### <a name="commands"></a>Polecenia
* Pomoc: Wyświetla listę obsługiwanych poleceń i flagi.
* Anulowanie aprowizacji: Spróbuj wyczyścić systemu i odpowiednie do reprovisioning. Następujące usuwa operacji:
  
  * Wszystkich kluczy hostów SSH (jeśli Provisioning.RegenerateSshHostKeyPair "y" w pliku konfiguracji)
  * Konfiguracja serwera nazw w /etc/resolv.conf
  * Hasło główne od użytkownika (jeśli Provisioning.DeleteRootPassword "y" w pliku konfiguracji)
  * Buforowanych dzierżaw klientów DHCP
  * Zresetowanie nazwy hosta na wartość localhost.localdomain

> [!WARNING]
> Anulowanie aprowizacji nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne i nadaje się do ponownej dystrybucji.
> 
> 

* Anulowanie aprowizacji i użytkownika: Wszystko, co wykonuje w - deprovision (powyżej) i również usuwa ostatnie aprowizowane konto użytkownika (uzyskana z /var/lib/waagent) i skojarzone dane. Ten parametr jest podczas anulowania obsługi obrazu, który został wcześniej aprowizacji na platformie Azure, dzięki czemu mogą być przechwytywane i ponownie.
* Wersja: Wyświetla wersję waagent
* serialconsole: Konfiguruje CHODNIKÓW, aby oznaczyć ttyS0 (pierwszy port szeregowy) jako konsola rozruchu. Gwarantuje to, że dzienniki rozruchu jądra są wysyłane do portu szeregowego i udostępnione do debugowania.
* daemon: Uruchomiono agenta waagent jako demon do zarządzania interakcją z platformą. Ten argument zostanie określony do waagent w skrypcie inicjowania waagent.
* Początek: Uruchomiono agenta waagent jako proces w tle

## <a name="configuration"></a>Konfigurowanie
Plik konfiguracji (/ etc/waagent.conf) Określa akcje waagent. Poniżej przedstawiono przykładowy plik konfiguracji:

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

Następujące czynności, które opisano różne opcje konfiguracji. Opcje konfiguracji są trzy typy; Atrybut typu wartość logiczna, ciąg lub liczba całkowita. Opcje konfiguracji logiczną, można określić jako "y" lub "n". Specjalne przez Ciebie słowo kluczowe "None" może być używany dla niektórych wpisów konfiguracji typu ciąg jako następujące informacje:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Dzięki temu użytkownikowi na włączanie lub wyłączanie funkcji inicjowania obsługi administracyjnej w agencie. Prawidłowe wartości to "y" lub "n". Jeśli Inicjowanie obsługi administracyjnej jest wyłączone, klucze hosta i użytkownika SSH na ilustracji są zachowywane i określona na platformie Azure aprowizujący interfejs API konfiguracja jest ignorowana.

> [!NOTE]
> `Provisioning.Enabled` Domyślnych wartości parametrów do "n" na obrazy systemu Ubuntu w chmurze, korzystających z pakietu cloud-init do inicjowania obsługi.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, hasła głównego w pliku/etc/w tle są usuwane podczas procesu inicjowania obsługi administracyjnej.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Jeśli zestawu i wszystkich hostów par kluczy SSH (ecdsa, dsa lub rsa) zostaną usunięte podczas procesu inicjowania obsługi administracyjnej z/etc/ssh /. I jest generowany przez pojedynczego nowej pary kluczy.

Konfiguruje we wpisie Provisioning.SshHostKeyPairType się typ szyfrowania dla nowej pary kluczy. Niektóre dystrybucji ponownie utworzyć par kluczy SSH dla wszelkich brakujących typów szyfrowania, po ponownym uruchomieniu demon SSH (na przykład po ponownym rozruchu).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Może być równa typ algorytmu szyfrowania, który jest obsługiwany przez demon SSH na maszynie wirtualnej. Zwykle obsługiwane wartości to "rsa", "dsa" i "ecdsa". "putty.exe" w Windows obsługuje tylko "ecdsa". Tak Jeśli zamierzasz użyć putty.exe w Windows, aby nawiązać połączenie z wdrożenia systemu Linux, użyj "rsa" lub "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Jeśli zestaw, waagent monitoruje maszyny wirtualnej systemu Linux, zmiany nazwy hosta (jak zwracany przez polecenie "hostname") i automatycznie zaktualizować konfiguracji sieci w obrazie w celu odzwierciedlenia zmiany. Aby wypchnąć zmiany nazwy do serwerów DNS, sieci zostanie ponownie uruchomiona na maszynie wirtualnej. W skrócie powoduje utratę łączności z Internetem.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, waagent dekoduje CustomData z formatu Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, waagent wykonuje CustomData po zainicjowaniu obsługi administracyjnej.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Ta opcja umożliwia hasło dla użytkownika sys zerującego; Domyślnie jest wyłączona.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algorytm używany przez crypt podczas generowania skrótów haseł.  
 1 - MD5  
 2a - Blowfish  
 5 — ALGORYTM SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Długość losowego ziarna używane podczas generowania skrótów haseł.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Jeśli ustawiona, dysk zasobów dostarczonego przez platformę jest sformatowany i zainstalowanych w wyniku waagent, jeśli typ systemu plików, żądane przez użytkownika w "ResourceDisk.Filesystem" jest inna niż "systemu plików ntfs". Jedna partycja typu systemu Linux (83) jest udostępniane na dysku. Ta partycja nie jest sformatowany, jeśli można zainstalować pomyślnie.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Określa typ systemu plików dla dysku zasobów. Obsługiwane wartości zależą od dystrybucji systemu Linux. Jeśli ciąg jest X, a następnie mkfs. X powinny znajdować się na obrazie systemu Linux. SLES 11 obrazów zwykle należy używać "ext3". FreeBSD obrazów należy używać "ufs2" w tym miejscu.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
To ustawienie określa ścieżkę, w którym jest zainstalowany dysk zasobów. Dysk zasobów jest *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Określa opcje instalacji dysku, które zostaną przekazane do polecenia -o instalacji. Jest to rozdzielana przecinkami lista wartości, np. "nodev, nosuid". Zobacz mount(8), aby uzyskać szczegółowe informacje.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Jeśli ustawiona pliku wymiany (/ swapfile) jest tworzone na dysku zasobów i dodane do obszaru wymiany w systemie.

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
Jeśli wzmocnione to zestaw, poziom szczegółowości dziennika. Waagent rejestruje /var/log/waagent.log i korzysta z funkcji logrotate systemu, aby obrócić dzienniki.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Jeśli zestaw, agent próbuje zainstalować, a następnie załadować sterownik jądra RDMA, które odpowiadają wersji oprogramowania układowego na bazowego sprzętu.

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
To ustawienie określa limit czasu SCSI, w ciągu kilku sekund na dyskach systemu operacyjnego dysku i danych. Jeśli nie jest ustawiona, system, które są używane wartości domyślne.

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
To ustawienie umożliwia określenie alternatywnej ścieżki dla biblioteki openssl binarne do użycia dla operacji kryptograficznych.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Jeśli zestaw, agent używa ten serwer proxy dostępu do Internetu. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Włącz lub wyłącz automatyczne aktualizowanie stanie celem przetwarzania; Domyślnie włączone.



## <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Images
Korzystanie z obrazów w chmurze systemu Ubuntu [pakietu cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) do wykonywania wielu zadań konfiguracji, które w przeciwnym razie będą zarządzane przez agenta systemu Linux dla platformy Azure. Stosuje się następujące różnice:

* **Provisioning.Enabled** wartość domyślna to "n" na obrazy systemu Ubuntu w chmurze, korzystających z pakietu cloud-init do wykonywania zadań inicjowania obsługi administracyjnej.
* Następujące parametry konfiguracji nie mają wpływu na obrazy systemu Ubuntu w chmurze, korzystających z pakietu cloud-init do zarządzania dyskami zasobów i obszar wymiany:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Aby uzyskać więcej informacji zobacz następujące zasoby do konfigurowania punktu instalacji dysku zasobów, a następnie zamienić miejsca na obrazy systemu Ubuntu w chmurze podczas inicjowania obsługi:
  
  * [Ubuntu Witryna typu Wiki: Konfigurowanie partycji wymiany](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Wstawianie danych niestandardowych do maszyny wirtualnej platformy Azure](../windows/classic/inject-custom-data.md)

