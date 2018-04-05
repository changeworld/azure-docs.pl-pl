---
title: Monitorowanie Maszynę wirtualną z rozszerzenia maszyny Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzenie diagnostycznych Linux służy do monitorowania wydajności i danych diagnostycznych maszyny wirtualnej systemu Linux na platformie Azure.
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
ms.openlocfilehash: cd22188042c60da7c761e1fa00a12921146caf25
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania wydajności i danych diagnostycznych maszyny wirtualnej systemu Linux

W tym dokumencie opisano wersji 2.3 rozszerzenia diagnostyczne systemu Linux.

> [!IMPORTANT]
> Ta wersja jest przestarzały i może być nieopublikowane dowolnym momencie po 30 czerwca 2018. Zastąpiono wersji 3.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [dokumentację w wersji 3.0 rozszerzenia diagnostycznych Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Wprowadzenie

(**Uwaga**: rozszerzenie diagnostyczne systemu Linux jest open-powierzając jej ich konserwację na [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) gdzie najbardziej aktualnych informacji o rozszerzeniu pierwszej publikacji. Należy sprawdzić [GitHub strony](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) pierwszej.)

Rozszerzenie diagnostycznych Linux pomaga monitor użytkownika maszyn wirtualnych systemu Linux, które działają w systemie Microsoft Azure. Ma ona następujące możliwości:

* Zbiera i przekazuje informacje o wydajności systemu z maszyny Wirtualnej systemu Linux do tabeli magazynu użytkownika, w tym informacji diagnostycznych i syslog.
* Umożliwia dostosowanie metryk danych, które zostaną zebrane i przekazane.
* Umożliwia użytkownikom na przekazywanie do tabeli magazynu wyznaczonego określone pliki dziennika.

W bieżącej wersji 2.3 dane obejmują:

* Wszystkie dzienniki Linux Rsyslog, w tym system, zabezpieczenia i dzienniki aplikacji.
* Wszystkie dane systemu, które jest określone w [lokacji rozwiązania Cross platformy w programie System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Pliki dziennika zdefiniowane przez użytkownika.

To rozszerzenie działa zarówno z klasycznego i modeli wdrażania Menedżera zasobów.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Bieżąca wersja rozszerzenia i amortyzacja stare wersje

Najnowsza wersja rozszerzenia **2.3**, i **wszystkie starsze wersje (2.0, 2.1 i 2.2) zostanie zastąpiona i nieopublikowane końca roku (2017)**. Po zainstalowaniu rozszerzenia Diagnostyka systemu Linux wraz z uaktualnieniem automatyczne wersja pomocnicza wyłączone, zdecydowanie zaleca się odinstalować rozszerzenia, a następnie zainstaluj go ponownie z uaktualnieniem automatyczne wersja pomocnicza włączone. W klasycznym (ASM) maszyn wirtualnych można to osiągnąć, określając "2.*" jako wersja instalowania rozszerzenia za pomocą interfejsu wiersza polecenia XPLAT platformy Azure lub programu Powershell. Na maszynach ARM można to osiągnąć poprzez włączenie ""autoUpgradeMinorVersion": true" w szablonie wdrożenia maszyny Wirtualnej. Ponadto żadnej nowej instalacji rozszerzenia powinien mieć wersja pomocnicza automatycznego uaktualniania opcja jest włączona.

## <a name="enable-the-extension"></a>Włącz rozszerzenia

To rozszerzenie można włączyć za pomocą [portalu Azure](https://portal.azure.com/#), programu Azure PowerShell lub skryptów wiersza polecenia platformy Azure.

Aby wyświetlić i skonfigurować wydajność systemu i dane bezpośrednio z portalu Azure, wykonaj [następujące kroki na blogu Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

W tym artykule przedstawiono sposób włączania i konfigurowania rozszerzenia przy użyciu poleceń wiersza polecenia platformy Azure. Dzięki temu można odczytać oraz jak wyświetlać dane bezpośrednio z tabeli magazynu.

Należy pamiętać, że metody konfiguracji, które zostały opisane w tym miejscu nie będzie działać dla portalu Azure. Aby wyświetlić i skonfigurować wydajność systemu i dane bezpośrednio z portalu Azure, należy włączyć rozszerzenie za pośrednictwem portalu.

## <a name="prerequisites"></a>Wymagania wstępne

* **Agenta systemu Linux platformy Azure w wersji 2.0.6 lub nowszym**.

  Należy pamiętać, że większość obrazów Galeria Linux maszyny Wirtualnej Azure obejmuje wersji 2.0.6 lub nowszej. Można uruchomić **agenta WAAgent-wersja** o potwierdzenie, która wersja jest zainstalowana na maszynie Wirtualnej. Jeśli maszyna wirtualna działa w wersji starszej niż 2.0.6, można wykonać [tych instrukcji w serwisie GitHub](https://github.com/Azure/WALinuxAgent "instrukcje") go zaktualizować.
* **Interfejs wiersza polecenia platformy Azure**. Postępuj zgodnie z [tym instrukcje dotyczące instalowania interfejsu wiersza polecenia](../../../cli-install-nodejs.md) do skonfigurowania środowiska wiersza polecenia platformy Azure na tym komputerze. Po zainstalowaniu interfejsu wiersza polecenia Azure, możesz użyć **azure** polecenie z interfejsu wiersza polecenia (Bash, Terminal lub wiersza polecenia) do poleceń wiersza polecenia platformy Azure. Na przykład:

  * Uruchom **zestaw rozszerzenie azure maszyny wirtualnej — Pomoc** uzyskać szczegółową pomoc.
  * Uruchom **logowanie w usłudze azure** logować się do platformy Azure.
  * Uruchom **listy maszyna wirtualna platformy azure** do tworzenia listy wszystkich maszyn wirtualnych, które masz na platformie Azure.
* Konto magazynu do przechowywania danych. Konieczne będzie nazwa konta magazynu, które zostało utworzone wcześniej i klucza dostępu, aby przekazać dane do usługi magazynu.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Użyj polecenia interfejsu wiersza polecenia Azure, aby włączyć rozszerzenie diagnostyczne systemu Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scenariusz 1. Włącz rozszerzenie z domyślnego zestawu danych

W wersji 2.3 lub nowszej zawiera wartości domyślne, które będą zbierane:

* Wszystkie informacje Rsyslog (w tym system, zabezpieczenia i dzienniki aplikacji).  
* Podstawowy zestaw danych systemowych podstawy. Należy pamiętać, że pełny zestaw danych jest opisany w [lokacji rozwiązania Cross platformy w programie System Center](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Jeśli chcesz włączyć dodatkowe dane, wykonaj czynności w scenariuszach 2 i 3.

Krok 1. Utwórz plik o nazwie PrivateConfig.json o następującej treści:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Krok 2. Run **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scenariusz 2. Dostosowanie metryk monitora wydajności

Ta sekcja opisuje sposób dostosowywania wydajności i tabelę danych diagnostycznych.

Krok 1. Utwórz plik o nazwie PrivateConfig.json z zawartością, który został opisany w scenariuszu 1. Utwórz również plik o nazwie PublicConfig.json. Umożliwia określenie danych, które mają być zbierane.

Dla wszystkich obsługiwanych dostawców i zmienne odwołania [lokacji rozwiązania Cross platformy w programie System Center](https://scx.codeplex.com/wikipage?title=xplatproviders). Możesz mieć wiele zapytań i przechowywać je w wielu tabel, dodając więcej kwerend do skryptu.

Domyślnie dane Rsyslog zawsze są zbierane.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Krok 2. Run **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scenariusz 3. Przekazanie plików dzienników

W tej sekcji opisano, jak do gromadzenia i przekazywania określone pliki dziennika na koncie magazynu. Należy określić ścieżkę do pliku dziennika i nazwę tabeli, w której chcesz zapisać dziennik. Można utworzyć wiele plików dziennika, dodając wiele wpisów tabeli/plików do skryptu.

Krok 1. Utwórz plik o nazwie PrivateConfig.json z zawartością, który został opisany w scenariuszu 1. Następnie utwórz plik o nazwie PublicConfig.json o następującej treści:

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

Należy pamiętać, że to ustawienie w wersjach rozszerzenia przed 2.3 wszystkie dzienniki zapisane `/var/log/mysql.err` mogą być zduplikowane do `/var/log/syslog` (lub `/var/log/messages` w zależności od Linux distro) również. Jeśli chcesz uniknąć zduplikowanych rejestrowanie, można wykluczyć rejestrowanie `local6` zakładzie dzienniki w konfiguracji rsyslog. Zależy on od Linux distro, ale w systemie Ubuntu 14.04 jest plik, aby zmodyfikować `/etc/rsyslog.d/50-default.conf` można zastąpić wiersza `*.*;auth,authpriv.none -/var/log/syslog` do `*.*;auth,authpriv,local6.none -/var/log/syslog`. Tego problemu w najnowszym wydaniu poprawki 2.3 (2.3.9007), więc jeśli wersja rozszerzenia 2.3, ten problem nie powinno się zdarzyć. Jeśli nadal nie nawet po ponownym uruchomieniu maszyny Wirtualnej, skontaktuj się z nami i pomóc nam Rozwiązywanie problemów z powodu najnowszej wersji poprawki nie jest instalowana automatycznie.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scenariusz 4. Zatrzymaj rozszerzenia ze zbierania żadnych dzienników

W tej sekcji opisano, jak zatrzymać rozszerzenia ze zbierania dzienników. Należy pamiętać, że proces agenta monitorowania będzie nadal uruchomione i działają prawidłowo nawet w przypadku tego procesu ponownej konfiguracji. Jeśli chcesz całkowicie zatrzymać procesu agenta monitorowania, możesz to zrobić po wyłączeniu rozszerzenia. To polecenie, aby wyłączyć rozszerzenie `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Krok 1. Utwórz plik o nazwie PrivateConfig.json z zawartością, który został opisany w scenariuszu 1. Utwórz plik o nazwie PublicConfig.json o następującej treści:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Krok 2. Run **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Przejrzyj dane

Wydajność i danych diagnostycznych są przechowywane w tabeli magazynu Azure. Przegląd [jak używać magazynu tabel Azure w języku Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) więcej informacji na temat dostępu do danych w tabeli magazynu za pomocą skryptów wiersza polecenia platformy Azure.

Ponadto służy następujące narzędzia interfejsu użytkownika do uzyskania dostępu do danych:

1. Eksplorator serwera programu Visual Studio. Przejdź do swojego konta magazynu. Po uruchomieniu maszyny Wirtualnej dla około pięciu minut, zostanie wyświetlony w tabelach cztery domyślne: "LinuxCpu", "LinuxDisk", "LinuxMemory" i "Linuxsyslog". Kliknij dwukrotnie nazwy tabeli, aby wyświetlić dane.
1. [Eksplorator usługi Azure Storage](https://azurestorageexplorer.codeplex.com/ "Eksploratora usługi Azure Storage").

![Obraz](./media/diagnostic-extension/no1.png)

Jeśli włączono fileCfg lub perfCfg (zgodnie z opisem w scenariuszach 2 i 3), można użyć programu Visual Studio w Eksploratorze serwera i Eksploratora usługi Storage platformy Azure do wyświetlania danych innych niż domyślne.

## <a name="known-issues"></a>Znane problemy

* Rsyslog informacji i określić klienta pliku dziennika można uzyskać tylko za pomocą skryptów.
