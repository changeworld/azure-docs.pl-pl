---
title: Instalowanie IBM zD & T, środowisko programistyczne/testowe na platformie Azure | Dokumentacja firmy Microsoft
description: Wdróż programowania Z firmy IBM i środowiska testowego (zD & T) w infrastrukturze platformy Azure maszyna wirtualna (VM) jako usługa (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859345"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalowanie IBM zD & T, środowisko programistyczne/testowe na platformie Azure

Do tworzenia środowiska deweloperskiego/testowego, w przypadku obciążeń mainframe na systemach Z firmy IBM, można wdrożyć programowania Z firmy IBM i środowisko testowe (zD & T) w infrastrukturze platformy Azure maszyna wirtualna (VM) jako usługa (IaaS).

Za pomocą zD & T, możesz korzystać z zalet oszczędność kosztów x86 platforma dla mniej ważnych środowiska deweloperskie i testowe i następnie wypychane aktualizacje z powrotem do środowiska produkcyjnego Z systemu. Aby uzyskać więcej informacji, zobacz [IBM ZD & T instrukcje dotyczące instalacji](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Platforma Azure i usługi Azure Stack obsługuje następujące wersje:

- zD & T Personal Edition
- zD & T Sysplex równoległe
- zD&T Enterprise Edition

Wszystkie wersje zD & T uruchomić tylko na x86 systemów Linux, a nie w systemie Windows Server. Enterprise Edition jest obsługiwana w systemie Red Hat Enterprise Linux (RHEL) lub Ubuntu/Debian. Obrazy systemu RHEL i Debian maszyny Wirtualnej są dostępne na platformie Azure.

W tym artykule przedstawiono sposób konfigurowania zD & T Enterprise Edition na platformie Azure, aby można było używać zD & T Enterprise Edition, serwer sieci web do tworzenia i zarządzania środowiskami. Instalowanie zD & T nie instaluje żadnych środowisk. Należy utworzyć je oddzielnie jako pakiety instalacyjne. Na przykład dystrybucje kontrolowane deweloperom aplikacji (ADCD) są obrazy woluminów środowisk testowych. Są zawarte w obrazach zip na dostępnych od firmy IBM dystrybucji multimediów. Zobacz jak [skonfigurować środowisko ADCD na platformie Azure](demo.md).

Aby uzyskać więcej informacji, zobacz [zD & T Przegląd](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) w centrum wiedzy firmy IBM.

W tym artykule dowiesz się, jak skonfigurować Z programowania i środowiska testowego (zD & T) Enterprise Edition na platformie Azure. Następnie przy użyciu zD & T Enterprise Edition, serwer sieci web tworzyć i zarządzać nimi Z środowisk na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> IBM umożliwia zD & T Enterprise Edition, można zainstalować w środowisku deweloperskim/testowym tylko —*nie* środowisk produkcyjnych.

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Musisz mieć dostęp do nośnika, który jest dostępny tylko dla klientów firmy IBM i partnerów. Aby uzyskać więcej informacji, skontaktuj się z przedstawicielem firmy IBM lub informacji kontaktowych w [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) witryny sieci Web.

- A [serwer licencyjny](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Jest to wymagane, aby uzyskać dostęp do tych środowisk. Sposób jego tworzenia, zależy od tego, jak licencji oprogramowania od firmy IBM:

     - **Oparte na sprzęcie serwer licencyjny** wymaga urządzenie USB, które zawiera tokeny wymierne niezbędnych do uzyskania dostępu wszystkie składniki oprogramowania. Należy to uzyskać od firmy IBM.

     - **Serwer licencyjny opartych na oprogramowaniu** wymaga skonfigurowania scentralizowanego serwera zarządzania kluczami licencjonowania. Ta metoda jest preferowana i wymaga skonfigurowania kluczy otrzymany od firmy IBM na serwerze zarządzania.

## <a name="create-the-base-image-and-connect"></a>Tworzenie obrazu podstawowego i łączenie

1. W witrynie Azure portal [Utwórz Maszynę wirtualną](/azure/virtual-machines/linux/quick-create-portal) z konfiguracją systemu operacyjnego ma. W tym artykule założono B4ms maszyny Wirtualnej (z 4 procesorów wirtualnych i 16 GB pamięci) systemem Ubuntu 16.04.

2. Po utworzeniu maszyny Wirtualnej, należy otworzyć porty dla ruchu przychodzącego 22 dla protokołu SSH i 21 dla połączenia FTP 9443 dla serwera sieci web.

3. Uzyskiwanie poświadczeń SSH, wyświetlane na **Przegląd** bloku maszyny Wirtualnej za pomocą **Connect** przycisku. Wybierz **SSH** kartę, a następnie skopiuj polecenie logowania SSH do Schowka.

4. Zaloguj się do [powłokę Bash](/azure/cloud-shell/quickstart) z Twojego komputera lokalnego i Wklej polecenie. Będzie on w formie **ssh\<identyfikator użytkownika\>\@\<adresu IP\>** . Po wyświetleniu monitu o podanie poświadczeń, wprowadź je do nawiązania połączenia z katalogiem w domu.

## <a name="copy-the-installation-file-to-the-server"></a>Skopiuj plik instalacyjny na serwer

Plik instalacyjny dla serwera sieci web jest **ZDT\_zainstalować\_EE\_V12.0.0.1.tgz**. Znajduje się na nośniku, dostarczane przez firmy IBM. Ten plik należy przekazać do maszyny Wirtualnej systemu Ubuntu.

1. W wierszu polecenia wprowadź następujące polecenie, aby upewnić się, że wszystko jest aktualne w nowo utworzony obraz:

    ```
    sudo apt-get update
    ```

2. Utwórz katalog do instalacji:

    ```
    mkdir ZDT
    ```

3. Skopiuj plik z komputera lokalnego do maszyny Wirtualnej:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> To polecenie powoduje skopiowanie pliku instalacyjnego do katalogu ZDT w katalog macierzysty, który różni się zależnie od tego, czy klient jest uruchamiany, Windows lub Linux.

## <a name="install-the-enterprise-edition"></a>Instalowanie wersji Enterprise Edition

1. Przejdź do katalogu ZDT i zdekompresować go ZDT\_zainstalować\_EE\_pliku V12.0.0.1.tgz przy użyciu następujących poleceń:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Uruchom program instalacyjny:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Wybierz **1** do zainstalowania serwera przedsiębiorstwa.

4. Naciśnij klawisz **Enter** i należy uważnie przeczytać umów licencyjnych. Na koniec licencji należy wprowadzić **tak** aby kontynuować.

5. Po wyświetleniu monitu o zmianę hasła dla nowo utworzonego użytkownika **ibmsys1**, użyj polecenia **ibmsys1 haseł "sudo"** i wprowadź nowe hasło.

6. Aby sprawdzić, czy instalacja się powiodła wprowadź

    ```
    dpkg -l | grep zdtapp
    ```

7. Sprawdź, czy dane wyjściowe zawierają ciąg **zdtapp 12.0.0.0**oznaczający, gaz pakiet został zainstalowany pomyślnie

### <a name="starting-enterprise-edition"></a>Począwszy od wersji Enterprise Edition

Należy pamiętać, że po uruchomieniu serwera sieci web jest uruchamiana przy użyciu Identyfikatora użytkownika zD & T, który został utworzony podczas procesu instalacji.

1. Można uruchomić serwera sieci web, należy użyć głównej nazwy użytkownika do uruchom następujące polecenie:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Skopiuj dane wyjściowe adres URL skryptu, który wygląda następująco:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Wklej adres URL w przeglądarce sieci web, aby otworzyć składnik zarządzania dla Twojego zD & T instalacji.

## <a name="next-steps"></a>Kolejne kroki

[Ustaw się aplikacja deweloperów kontrolowane dystrybucji (ADCD) w IBM zD & T w wersji 1](./demo.md)
