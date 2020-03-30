---
title: Omówienie agenta maszyny wirtualnej systemu Azure z systemem Linux
description: Dowiedz się, jak zainstalować i skonfigurować agenta systemu Linux (waagent) do zarządzania interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073853"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Opis i korzystanie z agenta systemu Azure Linux

Agent Microsoft Azure Linux (waagent) zarządza programem inicjowania obsługi administracyjnej systemu Linux & FreeBSD i interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej platformy Azure. Oprócz agenta systemu Linux zapewniającego funkcje inicjowania obsługi administracyjnej platforma Azure udostępnia również opcję używania init w chmurze dla niektórych systemów operacyjnych Linux. Agent systemu Linux udostępnia następujące funkcje dla wdrożeń IaaS systemu Linux i FreeBSD:

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Inicjowanie obsługi administracyjnej obrazów**
  
  * Tworzenie konta użytkownika
  * Konfigurowanie typów uwierzytelniania SSH
  * Wdrażanie kluczy publicznych i par kluczy SSH
  * Ustawianie nazwy hosta
  * Publikowanie nazwy hosta na platformie DNS
  * Raportowanie odcisku palca klucza hosta SSH na platformę
  * Zarządzanie dyskami zasobów
  * Formatowanie i montaż dysku zasobów
  * Konfigurowanie przestrzeni wymiany
* **Obsługa sieci**
  
  * Zarządza trasami w celu poprawy zgodności z serwerami DHCP platformy
  * Zapewnia stabilność nazwy interfejsu sieciowego
* **Jądro**
  
  * Konfiguruje wirtualną numa (wyłącz dla `2.6.37`jądra <)
  * Zużywa entropię Hyper-V dla /dev/random
  * Konfiguruje limity czasu SCSI dla urządzenia głównego (które mogą być zdalne)
* **Diagnostyka**
  
  * Przekierowanie konsoli do portu szeregowego
* **Wdrożenia SCVMM**
  
  * Wykrywa i uruchamia agenta programu VMM dla systemu Linux podczas pracy w środowisku Menedżer maszyn wirtualnych Centrum systemu 2012 R2
* **Rozszerzenie maszyny Wirtualnej**
  
  * Wstrzyknąć składnik autorstwa firmy Microsoft i partnerów do Systemu VM (IaaS) linux, aby umożliwić automatyzację oprogramowania i konfiguracji
  * Implementacja referencyjna rozszerzenia maszyny Wirtualnej na[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikacja
Przepływ informacji z platformy do agenta odbywa się za pośrednictwem dwóch kanałów:

* Dysk DVD z dołączonym czasem rozruchu dla wdrożeń IaaS. Ten dysk DVD zawiera plik konfiguracyjny zgodny ze standardem OVF, który zawiera wszystkie informacje inicjujące inicjowanie obsługi administracyjnej inne niż rzeczywiste napary klawiszy SSH.
* Punkt końcowy TCP uwidaczniający interfejs API REST używany do uzyskiwania konfiguracji wdrażania i topologii.

## <a name="requirements"></a>Wymagania
Następujące systemy zostały przetestowane i wiadomo, że współpracują z agentem systemu Azure Linux:

> [!NOTE]
> Ta lista może różnić się od oficjalnej listy obsługiwanych systemów na platformie Microsoft Azure, jak opisano poniżej:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
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

* FreeBSD 10+ (Azure Linux Agent v2.0.10+)

Agent Linuksa zależy od niektórych pakietów systemowych, aby działać poprawnie:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Narzędzia systemu plików: sfdisk, fdisk, mkfs, parted
* Narzędzia haseł: chpasswd, sudo
* Narzędzia do przetwarzania tekstu: sed, grep
* Narzędzia sieciowe: ip-route
* Obsługa jądra do montażu systemów plików UDF.

## <a name="installation"></a>Instalacja
Instalacja przy użyciu rpm lub pakietu DEB z repozytorium pakietów dystrybucji jest preferowaną metodą instalowania i uaktualniania agenta systemu Azure Linux. Wszyscy [dostawcy zatwierdzonych dystrybucji](../linux/endorsed-distros.md) integrują pakiet agenta usługi Azure Linux z ich obrazami i repozytoriami.

Zapoznaj się z dokumentacją w [repozytorium agenta systemu Azure Linux w usłudze GitHub,](https://github.com/Azure/WALinuxAgent) aby uzyskać zaawansowane opcje instalacji, takie jak instalowanie ze źródła lub do niestandardowych lokalizacji lub prefiksów.

## <a name="command-line-options"></a>Opcje wiersza polecenia
### <a name="flags"></a>Flagi
* pełne: Zwiększenie szczegółowości określonego polecenia
* force: Pomiń interaktywne potwierdzenie dla niektórych poleceń

### <a name="commands"></a>Polecenia
* pomoc: Wyświetla listę obsługiwanych poleceń i flag.
* deprovision: Spróbuj oczyścić system i uczynić go odpowiednim do ponownego udostępnienia. Następującej operacji usuwa:
  
  * Wszystkie klucze hosta SSH (jeśli provisioning.RegenerateSshHostKeyPair jest "y" w pliku konfiguracyjnym)
  * Konfiguracja serwera nazw w /etc/resolv.conf
  * Hasło root z /etc/shadow (jeśli Provisioning.DeleteRootPassword jest 'y' w pliku konfiguracyjnym)
  * Buforowane dzierżawy klientów DHCP
  * Resetuje nazwę hosta do localhost.localdomain

> [!WARNING]
> Anulowanie obsługi administracyjnej nie gwarantuje, że obraz zostanie wyczyszczony ze wszystkich poufnych informacji i nadaje się do redystrybucji.
> 
> 

* deprovision+user: Wykonuje wszystko w -deprovision (powyżej), a także usuwa ostatnio aprowizowanego konta użytkownika (uzyskane z /var/lib/waagent) i skojarzonych danych. Ten parametr jest podczas usuwania obsługi administracyjnej obrazu, który był wcześniej inicjowania obsługi administracyjnej na platformie Azure, dzięki czemu może być przechwycone i ponownie.
* wersja: Wyświetla wersję waagent
* serialconsole: Konfiguruje GRUB do oznaczania ttyS0 (pierwszego portu szeregowego) jako konsoli rozruchowej. Dzięki temu dzienniki rozruchu jądra są wysyłane do portu szeregowego i udostępniane do debugowania.
* demon: Uruchom waagent jako demon, aby zarządzać interakcją z platformą. Ten argument jest określony do waagent w skrypt waagent init.
* start: Uruchom waagent jako proces w tle

## <a name="configuration"></a>Konfigurowanie
Plik konfiguracyjny (/etc/waagent.conf) steruje działaniami waagenta. Poniżej przedstawiono przykładowy plik konfiguracyjny:

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

Opisano następujące opcje konfiguracji. Opcje konfiguracji są trzy typy; Wartość logiczna, ciąg znaków lub liczba całkowita. Opcje konfiguracji logicznej można określić jako "y" lub "n". Specjalne słowo kluczowe "Brak" może być używane dla niektórych wpisów konfiguracji typu ciągu jako następujące szczegóły:

**Inicjowanie obsługi administracyjnej.Włączone:**  
```
Type: Boolean  
Default: y
```
Dzięki temu użytkownik może włączyć lub wyłączyć funkcje inicjowania obsługi administracyjnej w agencie. Prawidłowe wartości to "y" lub "n". Jeśli inicjowanie obsługi administracyjnej jest wyłączone, klucze hosta i użytkownika SSH w obrazie są zachowywane, a wszelkie konfiguracje określone w interfejsie API inicjowania obsługi administracyjnej platformy Azure są ignorowane.

> [!NOTE]
> Parametr `Provisioning.Enabled` domyślnie "n" na Ubuntu Cloud Images, które używają cloud-init do inicjowania obsługi administracyjnej.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Jeśli jest ustawiona, hasło główne w pliku /etc/shadow zostanie wymazane podczas procesu inicjowania obsługi administracyjnej.

**Inicjowanie obsługi administracyjnej.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Jeśli jest ustawiona, wszystkie pary kluczy hosta SSH (ecdsa, dsa i rsa) są usuwane podczas procesu inicjowania obsługi administracyjnej z /etc/ssh/. I generowane jest pojedyncza nowa para kluczy.

Typ szyfrowania dla pary kluczy świeżych można skonfigurować za pomocą wpisu Provisioning.SshHostKeyPairType. Niektóre dystrybucje odtwarzają pary kluczy SSH dla brakujących typów szyfrowania po ponownym uruchomieniu demona SSH (na przykład po ponownym uruchomieniu).

**Inicjowanie obsługi administracyjnej.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Można ustawić na typ algorytmu szyfrowania, który jest obsługiwany przez demona SSH na maszynie wirtualnej. Zazwyczaj obsługiwane wartości to "rsa", "dsa" i "ecdsa". "putty.exe" w systemie Windows nie obsługuje "ecdsa". Tak więc, jeśli zamierzasz użyć putty.exe w systemie Windows, aby połączyć się z wdrożeniem Linuksa, użyj "rsa" lub "dsa".

**Inicjowanie obsługi administracyjnej.Nazwa hosta:**  
```
Type: Boolean  
Default: y
```
Jeśli jest ustawiona, waagent monitoruje maszynę wirtualną systemu Linux pod kątem zmian nazwy hosta (zwracanej przez polecenie "nazwa hosta") i automatycznie aktualizuje konfigurację sieci na obrazie, aby odzwierciedlić zmianę. Aby wypchnąć zmianę nazwy na serwery DNS, sieć jest ponownie uruchamiana na maszynie wirtualnej. Powoduje to krótką utratę łączności z Internetem.

**Inicjowanie obsługi administracyjnej.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli jest ustawiona, waagent dekoduje Dane niestandardowe z bazy Base64.

**Inicjowanie obsługi administracyjnej.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli jest ustawiona, waagent wykonuje CustomData po inicjowaniu obsługi administracyjnej.

**Inicjowanie obsługi administracyjnej.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Ta opcja umożliwia zresetowanie hasła użytkownika sys; domyślnie jest wyłączona.

**Aprowowanie.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algorytm używany przez kryptę podczas generowania hash hasła.  
 1 - MD5  
 2a - Dmuchawa  
 5 - SHA-256  
 6 - SHA-512  

**Aprowizowanie.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Długość losowej soli używanej podczas generowania hash hasła.

**Format Dyskusku.Format zasobu:**  
```
Type: Boolean  
Default: y
```
Jeśli jest ustawiona, dysk zasobu dostarczony przez platformę jest formatowany i montowany przez program waagent, jeśli typ systemu plików wymagany przez użytkownika w "ResourceDisk.Filesystem" jest czymś innym niż "ntfs". Na dysku dostępna jest pojedyncza partycja typu Linux (83). Ta partycja nie jest sformatowana, jeśli można ją pomyślnie zamontować.

**System ResourceDisk.File:**  
```
Type: String  
Default: ext4
```
Określa typ systemu plików dla dysku zasobów. Obsługiwane wartości różnią się w zależności od dystrybucji linuksa. Jeśli ciąg jest X, a następnie mkfs. X powinien być obecny na obrazie Linuksa. Obrazy SLES 11 powinny zazwyczaj używać "ext3". Obrazy FreeBSD powinny używać "ufs2" tutaj.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Określa ścieżkę, przy której jest zainstalowany dysk zasobów. Dysk zasobu jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Określa opcje instalacji dysku, które mają być przekazywane do polecenia mount -o. Jest to oddzielona przecinkami lista wartości, np. 'nodev,nosuid'. Szczegółowe informacje można znaleźć w mount(8).

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Jeśli jest ustawiona, plik wymiany (/swapfile) jest tworzony na dysku zasobów i dodawany do przestrzeni wymiany systemu.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
Rozmiar pliku wymiany w megabajtach.

**Dzienniki.Pełne:**  
```
Type: Boolean  
Default: n
```
Jeśli ustawiona, szczegółowość dziennika jest zwiększana. Waagent loguje się do /var/log/waagent.log i wykorzystuje funkcję logrotate systemu do obracania dzienników.

**Os. Włącz RDMA**  
```
Type: Boolean  
Default: n
```
Jeśli jest ustawiona, agent próbuje zainstalować, a następnie załadować sterownik jądra RDMA, który pasuje do wersji oprogramowania układowego na podstawowym sprzęcie.

**Os. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
To ustawienie konfiguruje limit czasu SCSI w ciągu kilku sekund na dysku systemu operacyjnego i dyskach danych. Jeśli nie jest ustawiona, używane są ustawienia domyślne systemu.

**Os. OpensslPath:**  
```
Type: String  
Default: None
```
To ustawienie może służyć do określania alternatywnej ścieżki dla openssl binarny do użycia dla operacji kryptograficznych.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Jeśli jest ustawiona, agent używa tego serwera proxy, aby uzyskać dostęp do Internetu. 

**Autoupdate.Enabled**
```
Type: Boolean
Default: y
```
Włączanie lub wyłączanie automatycznej aktualizacji w celu przetwarzania stanu celu; domyślna jest włączona.



## <a name="ubuntu-cloud-images"></a>Obrazy ubuntu w chmurze
Ubuntu Cloud Images wykorzystują [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) do wykonywania wielu zadań konfiguracyjnych, które w przeciwnym razie byłyby zarządzane przez agenta systemu Azure Linux. Obowiązują następujące różnice:

* **Inicjowanie obsługi administracyjnej.Domyślne ustawienia** domyślne na "n" na Ubuntu Cloud Images, które używają cloud-init do wykonywania zadań inicjowania obsługi administracyjnej.
* Następujące parametry konfiguracji nie mają wpływu na obrazy Ubuntu Cloud Images, które używają cloud-init do zarządzania dyskiem zasobów i miejsca wymiany:
  
  * **Format dysku.ResourceDisk.Format**
  * **System ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Aby uzyskać więcej informacji, zobacz następujące zasoby, aby skonfigurować punkt instalacji dysku zasobów i zamienić miejsce na Ubuntu Cloud Images podczas inicjowania obsługi administracyjnej:
  
  * [Ubuntu Wiki: Konfigurowanie partycji wymiany](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Wstrzykujenie niestandardowych danych do maszyny wirtualnej platformy Azure](../windows/classic/inject-custom-data.md)

