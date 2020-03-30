---
title: Instalowanie środowiska deweloperskiego/testowego IBM zD&T na platformie Azure | Dokumenty firmy Microsoft
description: Wdrażanie infrastruktury IBM Z Development and Test Environment (zD&T) w infrastrukturze maszyny wirtualnej platformy Azure (VM) jako usługi (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025942"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalowanie środowiska deweloperskiego/testowego IBM zD&T na platformie Azure

Aby utworzyć środowisko deweloperów/testów dla obciążeń komputerów mainframe w systemach IBM Z, można wdrożyć środowisko ibm z development and test environment (zD&T) w infrastrukturze maszyny wirtualnej platformy Azure (VM) jako usługi (IaaS).

Dzięki zD&T możesz skorzystać z oszczędności kosztów platformy x86 dla mniej krytycznych środowisk programistów i testów, a następnie przesunąć aktualizacje z powrotem do środowiska produkcyjnego systemu Z. Aby uzyskać więcej informacji, zapoznaj się z [instrukcjami instalacji IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Platforma Azure i usługa Azure Stack obsługują następujące wersje:

- zD&T Edycja osobista
- zD&T Równoległy Sysplex
- zD&T Enterprise Edition

Wszystkie wersje zD&T działają tylko na systemach x86 Linux, a nie Windows Server. Enterprise Edition jest obsługiwana na red hat enterprise linux (RHEL) lub Ubuntu/Debian. Obrazy maszyn wirtualnych RHEL i Debian są dostępne na platformie Azure.

W tym artykule pokazano, jak skonfigurować zD&T Enterprise Edition na platformie Azure, aby można było używać serwera sieci web zD&T Enterprise Edition do tworzenia środowisk i zarządzania nimi. Instalacja zD&T nie instaluje żadnych środowisk. Należy je utworzyć oddzielnie jako pakiety instalacyjne. Na przykład dystrybucje kontrolowane przez deweloperów aplikacji (ADCD) są obrazami woluminów środowisk testowych. Są one zawarte w obrazach zip na dystrybucji multimediów dostępnych od IBM. Zobacz, jak [skonfigurować środowisko ADCD na platformie Azure](demo.md).

Aby uzyskać więcej informacji, zobacz [omówienie zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) w Centrum wiedzy IBM.

W tym artykule pokazano, jak skonfigurować Z Development and Test Environment (zD&T) Enterprise Edition na platformie Azure. Następnie można użyć serwera sieci web zD&T Enterprise Edition do tworzenia środowisk opartych na zużyciu na platformie Azure i zarządzania nimi.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> IBM umożliwia instalację zD&T Enterprise Edition tylko w środowiskach deweloperskich/testowych,*a nie* w środowiskach produkcyjnych.

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Potrzebny jest dostęp do nośników, które są dostępne tylko dla klientów i partnerów IBM. Aby uzyskać więcej informacji, skontaktuj się z przedstawicielem IBM lub zapoznaj się z informacjami kontaktowym na stronie [internetowej zD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- [Serwer licencjonowania](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Jest to wymagane dla dostępu do środowiska. Sposób jego tworzenia zależy od sposobu licencjonowannia oprogramowania od IBM:

     - **Sprzętowy serwer licencjonowania** wymaga urządzenia sprzętowego USB, które zawiera tokeny Rational niezbędne do uzyskania dostępu do wszystkich części oprogramowania. Należy to uzyskać od IBM.

     - **Serwer licencjonowania oparty na oprogramowaniu** wymaga skonfigurowania scentralizowanego serwera do zarządzania kluczami licencyjnymi. Ta metoda jest preferowana i wymaga skonfigurowania kluczy otrzymywanych od IBM na serwerze zarządzania.

## <a name="create-the-base-image-and-connect"></a>Tworzenie obrazu podstawowego i łączenie

1. W witrynie Azure portal [utwórz maszynę wirtualną](/azure/virtual-machines/linux/quick-create-portal) z odpowiednią konfiguracją systemu operacyjnego. W tym artykule założono maszynę wirtualną B4ms (z 4 procesorami wirtualnymi i 16 GB pamięci) z uruchomionym Ubuntu 16.04.

2. Po utworzeniu maszyny Wirtualnej otwórz porty przychodzące 22 dla SSH, 21 dla FTP i 9443 dla serwera sieci web.

3. Pobierz poświadczenia SSH wyświetlane na **przegląd** bloku maszyny Wirtualnej za pomocą przycisku **Połącz.** Wybierz kartę **SSH** i skopiuj polecenie logowania SSH do schowka.

4. Zaloguj się do [powłoki Bash](/azure/cloud-shell/quickstart) z lokalnego komputera i wklej polecenie. Będzie w formie **ssh\<identyfikator\>\@\<ip\>użytkownika .** Po wyświetleniu monitu o podanie poświadczeń wprowadź je w celu nawiązania połączenia z katalogiem macierzystym.

## <a name="copy-the-installation-file-to-the-server"></a>Kopiowanie pliku instalacyjnego na serwer

Plik instalacyjny serwera www to **ZDT\_\_Install EE\_V12.0.0.1.tgz**. Jest on zawarty w mediach dostarczanych przez IBM. Musisz przesłać ten plik do maszyny wirtualnej Ubuntu.

1. W wierszu polecenia wprowadź następujące polecenie, aby upewnić się, że wszystko jest aktualne na nowo utworzonym obrazie:

    ```
    sudo apt-get update
    ```

2. Utwórz katalog do zainstalowania w:

    ```
    mkdir ZDT
    ```

3. Skopiuj plik z komputera lokalnego na maszynę wirtualną:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> To polecenie kopiuje plik instalacyjny do katalogu ZDT w katalogu Home, który różni się w zależności od tego, czy klient jest uruchamiany w systemie Windows czy Linux.

## <a name="install-the-enterprise-edition"></a>Instalowanie wersji Enterprise Edition

1. Przejdź do katalogu ZDT i zdekompresuj plik ZDT\_Install\_EE\_V12.0.0.1.tgz za pomocą następujących poleceń:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Uruchom instalator:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Wybierz **1,** aby zainstalować serwer Enterprise Server.

4. Naciśnij **enter** i przeczytaj uważnie umowy licencyjne. Po zakończeniu licencji wprowadź **tak,** aby kontynuować.

5. Po wyświetleniu monitu o zmianę hasła dla nowo utworzonego użytkownika, **ibmsys1**, użyj polecenia **sudo passwd ibmsys1** i wprowadź nowe hasło.

6. Aby sprawdzić, czy instalacja zakończyła się pomyślnie,

    ```
    dpkg -l | grep zdtapp
    ```

7. Sprawdź, czy wyjście zawiera ciąg **zdtapp 12.0.0.0,** wskazując, że gaz pakietowy został pomyślnie zainstalowany

### <a name="starting-enterprise-edition"></a>Uruchamianie wersji Enterprise Edition

Należy pamiętać, że po uruchomieniu serwera sieci web działa on pod identyfikatorem użytkownika zD&T, który został utworzony podczas procesu instalacji.

1. Aby uruchomić serwer sieci web, użyj głównego identyfikatora użytkownika, aby uruchomić następujące polecenie:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Skopiuj wyjście adresu URL przez skrypt, który wygląda następująco:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Wklej adres URL do przeglądarki internetowej, aby otworzyć składnik zarządzania instalacją zD&T.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1](./demo.md)
