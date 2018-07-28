---
title: Monitorowanie maszyny Wirtualnej systemu Linux, za pomocą rozszerzenia maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać rozszerzenia diagnostycznego systemu Linux do monitorowania wydajności i danych diagnostycznych maszyny Wirtualnej z systemem Linux na platformie Azure.
services: virtual-machines-linux
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: 13d7594c15959661f3f9c3ab2165739719beac07
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308225"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania wydajności i danych diagnostycznych maszyny wirtualnej systemu Linux

Ten dokument opisuje wersję 2.3 rozszerzenia diagnostycznego systemu Linux.

> [!IMPORTANT]
> Ta wersja jest przestarzała i może być nieopublikowane dowolnym momencie po 30 czerwca 2018 r. Zastąpiono wersji 3.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [dokumentacji wersję 3.0 rozszerzenia diagnostycznego systemu Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Wprowadzenie

(**Uwaga**: rozszerzenia diagnostycznego systemu Linux jest open source w serwisie [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) gdzie najbardziej aktualnych informacji o rozszerzeniu pierwszej publikacji. Możesz chcieć sprawdzić [strony GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) pierwszej.)

Rozszerzenia diagnostycznego systemu Linux pozwala monitor użytkownika maszyn wirtualnych systemu Linux, które są uruchomione w systemie Microsoft Azure. Ma następujące możliwości:

* Zbiera dane i przekazuje informacje o wydajności systemu z maszyny Wirtualnej systemu Linux do tabeli magazynu użytkownika, w tym informacje diagnostyczne i syslog.
* Umożliwia użytkownikom dostosować metryki danych, które zostaną zebrane i przekazane.
* Umożliwia użytkownikom przekazywanie określone pliki dziennika do tabeli magazynu wyznaczonego.

W bieżącej wersji 2.3 dane obejmują:

* Wszystkie dzienniki Rsyslog systemu Linux, w tym system, zabezpieczenia i dzienniki aplikacji.
* Wszystkie dane systemu, które jest określone w [witryny rozwiązania Cross platformy w programie System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Plików dziennika określonych przez użytkownika.

To rozszerzenie współpracuje z wdrożeń klasycznych, jak i modelem wdrażania usługi Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Aktualna wersja rozszerzenia oraz amortyzacja starsze wersje

Najnowsza wersja rozszerzenia jest **2.3**, i **wszystkie starsze wersje (w wersji 2.0, 2.1 i 2.2) będzie przestarzały i nieopublikowanych końca tego roku (2017)**. Jeśli zainstalowano rozszerzenie Linux Diagnostic uaktualnianie wersji pomocniczej automatyczne wyłączone, zdecydowanie zaleca się odinstalować rozszerzenie, a następnie ponownie ją zainstaluj przy uaktualnianiu automatyczne podverze włączone. Na klasyczny (ASM) maszyn wirtualnych można to osiągnąć, określając "2.*" jako wersję, w przypadku instalowania rozszerzeń przy użyciu wiersza polecenia XPLAT platformy Azure lub programu Powershell. Na maszynach wirtualnych ARM, można to osiągnąć przez dołączenie ""autoUpgradeMinorVersion": true" w szablonie wdrożenia maszyny Wirtualnej. Ponadto dowolnej nowej instalacji rozszerzenia powinny mieć wersję pomocniczą automatycznego uaktualniania opcja jest włączona.

## <a name="enable-the-extension"></a>Włącz rozszerzenie

To rozszerzenie można włączyć za pomocą [witryny Azure portal](https://portal.azure.com/#), programu Azure PowerShell lub skryptów wiersza polecenia platformy Azure.

Aby wyświetlić i skonfigurować wydajność systemu i dane bezpośrednio z witryny Azure portal, wykonaj [następujące czynności na blogu platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Ten artykuł koncentruje się na temat włączyć i skonfigurować rozszerzenia za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Pozwala na odczyt i wyświetlić dane bezpośrednio z tabeli magazynu.

Należy pamiętać, że metody konfiguracji, które są opisane w tym miejscu nie będzie działać dla witryny Azure portal. Aby wyświetlić i skonfigurować wydajność systemu i dane bezpośrednio z witryny Azure portal, rozszerzenie musi być włączona za pośrednictwem portalu.

## <a name="prerequisites"></a>Wymagania wstępne

* **Agent systemu Linux platformy Azure w wersji 2.0.6 lub nowszej**.

  Należy pamiętać, że większość obrazów w galerii Azure VM Linux obejmują wersji 2.0.6 lub nowszej. Możesz uruchomić **WAAgent-wersja** aby upewnić się, która wersja jest zainstalowana na maszynie Wirtualnej. Jeśli maszyna wirtualna jest w wersji starszej niż 2.0.6, możesz wykonać [te instrukcje w serwisie GitHub](https://github.com/Azure/WALinuxAgent "instrukcje") ją zaktualizować.
* **Interfejs wiersza polecenia platformy Azure**. Postępuj zgodnie z [tej wskazówki dotyczące instalacji interfejsu wiersza polecenia](../../../cli-install-nodejs.md) Aby skonfigurować środowisko wiersza polecenia platformy Azure na maszynie. Po zainstalowaniu interfejsu wiersza polecenia platformy Azure, można użyć **azure** polecenia z poziomu interfejsu wiersza polecenia (powłoki Bash, program Terminal lub wiersz polecenia), aby uzyskać dostęp do poleceń interfejsu wiersza polecenia platformy Azure. Na przykład:

  * Uruchom **zestawu rozszerzenia maszyny wirtualnej platformy azure — Pomoc** dla szczegółowych informacji pomocy.
  * Uruchom **logowanie do platformy azure** zalogować się do platformy Azure.
  * Uruchom **listy maszyn wirtualnych platformy azure** Aby wyświetlić listę wszystkich maszyn wirtualnych, które mają na platformie Azure.
* Konto magazynu do przechowywania danych. Konieczne będzie nazwa konta magazynu, który został utworzony wcześniej i klucza dostępu, aby przekazać dane do magazynu.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Użyj polecenia interfejsu wiersza polecenia platformy Azure, aby włączyć rozszerzenia diagnostycznego systemu Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scenariusz 1. Włączanie rozszerzenia z domyślnym zestawem danych

W wersji 2.3 lub nowszy domyślne dane, które mają być zbierane obejmuje:

* Wszystkie informacje Rsyslog (w tym system, zabezpieczenia i dzienniki aplikacji).  
* Podstawowy zestaw danych systemowych podstawy. Należy zauważyć, że pełny zestaw danych został opisany na [witryny rozwiązania Cross platformy w programie System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Jeśli chcesz włączyć dodatkowe dane, wykonaj kroki w scenariuszach 2 i 3.

Krok 1. Utwórz plik o nazwie PrivateConfig.json o następującej zawartości:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Krok 2. Uruchom **rozszerzenia maszyny wirtualnej platformy azure, ustaw vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.\* — prywatny config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scenariusz 2. Dostosowanie metryk monitora wydajności

W tej sekcji opisano sposób dostosowywania wydajności i tabelę danych diagnostycznych.

Krok 1. Utwórz plik o nazwie PrivateConfig.json z zawartością, który został opisany w scenariuszu 1. Utwórz również plik o nazwie PublicConfig.json. Umożliwia określenie danych, które mają być zbierane.

Dla wszystkich obsługiwanych dostawców i zmienne odwołania [witryny rozwiązania Cross platformy w programie System Center](https://scx.codeplex.com/wikipage?title=xplatproviders). Możesz mieć wiele zapytań i przechowywać je w wielu tabelach, dodając więcej zapytań do skryptu.

Domyślnie dane Rsyslog są zawsze zbierane.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Krok 2. Uruchom **rozszerzenia maszyny wirtualnej platformy azure, ustaw vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*"--PrivateConfig.json prywatny config-path--public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scenariusz 3. Przekazywanie plików dziennika

Tej sekcji opisano sposób zbierania i przekazywania określone pliki dziennika do konta magazynu. Należy określić ścieżkę do pliku dziennika i nazwę tabeli, w którym chcesz zapisać dziennik. Dodając wiele wpisów tabeli/plików do skryptu, można utworzyć wiele plików dziennika.

Krok 1. Utwórz plik o nazwie PrivateConfig.json z zawartością, który został opisany w scenariuszu 1. Następnie należy utworzyć inny plik o nazwie PublicConfig.json o następującej zawartości:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Krok 2. Uruchom polecenie `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Należy pamiętać, że to ustawienie w wersjach rozszerzenia przed 2.3 wszystkie dzienniki zapisane `/var/log/mysql.err` mogą zostać zduplikowane na `/var/log/syslog` (lub `/var/log/messages` w zależności od dystrybucji systemu Linux) również. Jeśli chcesz uniknąć zduplikowanych rejestrowanie, można wykluczyć rejestrowanie `local6` funkcji logowania w konfiguracji rsyslog. Jest to uzależnione od dystrybucji systemu Linux, ale w systemie Ubuntu 14.04 jest plik Aby zmodyfikować `/etc/rsyslog.d/50-default.conf` można zastąpić wiersz `*.*;auth,authpriv.none -/var/log/syslog` do `*.*;auth,authpriv,local6.none -/var/log/syslog`. Ten problem jest rozwiązany w najnowszej wersji poprawki zestawu 2.3 (2.3.9007), więc jeśli masz wersję 2.3 rozszerzenia, nie powinny występować ten problem. Jeśli nadal nie nawet po ponownym uruchomieniu maszyny Wirtualnej, skontaktuj się z nami i pomóc nam w rozwiązaniu, dlaczego najnowsza wersja poprawki nie jest zainstalowana automatycznie.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scenariusz 4. Zatrzymaj zbieranie żadnych dzienników rozszerzenia

W tej sekcji opisano sposób zatrzymania zbierania dzienników rozszerzenia. Należy zauważyć, że proces agenta monitorowania będzie nadal uruchomione i działają prawidłowo nawet w przypadku tego procesu ponownej konfiguracji. Jeśli chcesz całkowicie zatrzymać procesu agenta monitorowania, możesz to zrobić, wyłączając rozszerzenie. To polecenie, aby wyłączyć rozszerzenie `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Krok 1. Utwórz plik o nazwie PrivateConfig.json z zawartością, który został opisany w scenariuszu 1. Utwórz plik o nazwie PublicConfig.json o następującej zawartości:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Krok 2. Uruchom **rozszerzenia maszyny wirtualnej platformy azure, ustaw vm_name LinuxDiagnostic Microsoft.OSTCExtensions "2.*"--PrivateConfig.json prywatny config-path--public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Przejrzyj dane

Wydajność i dane diagnostyczne są przechowywane w tabeli usługi Azure Storage. Przegląd [jak używać usługi Azure Table Storage w języku Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) informacje na temat dostępu do danych w tabeli magazynu za pomocą skryptów wiersza polecenia platformy Azure.

Ponadto umożliwia następujące narzędzia interfejsu użytkownika uzyskuje dostęp do danych:

1. Eksplorator serwera programu Visual Studio. Przejdź do swojego konta magazynu. Po maszyny Wirtualnej trwa około pięciu minut, zostaną wyświetlone cztery domyślne tabele: "LinuxCpu", "LinuxDisk", "LinuxMemory" i "Linuxsyslog". Kliknij dwukrotnie nazwy tabel, aby wyświetlić dane.
1. [Eksplorator usługi Azure Storage](https://azurestorageexplorer.codeplex.com/ "Eksplorator usługi Azure Storage").

![image](./media/diagnostic-extension/no1.png)

Po włączeniu fileCfg lub perfCfg (zgodnie z opisem w scenariuszach 2 i 3), można użyć programu Visual Studio Server Explorer i Eksplorator usługi Azure Storage do wyświetlania danych innych niż domyślne.

## <a name="known-issues"></a>Znane problemy

* Informacje Rsyslog i plik dziennika określony przez klienta może zostać oceniony jedynie za pomocą skryptów.
