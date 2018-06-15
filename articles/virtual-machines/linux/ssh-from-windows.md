---
title: Używanie kluczy SSH w systemie Windows dla maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Generowanie i używanie kluczy SSH na komputerze z systemem Windows do nawiązania połączenia maszyny wirtualnej systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31601026"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak używać kluczy SSH z usługą Microsoft Azure

W tym artykule przedstawiono sposób generowania i użytkowania kluczy bezpiecznej powłoki (SSH) na komputerze z systemem Windows, aby utworzyć i nawiązywanie z maszyny wirtualnej systemu Linux (VM) na platformie Azure. Aby używać kluczy SSH z systemem Linux lub macOS klienta, zobacz [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) wskazówki.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pakiety systemu Windows i klientów SSH
Nawiązywanie połączenia i zarządzać maszyn wirtualnych systemu Linux w programie Azure przy użyciu *klienta SSH*. Komputery z systemem Linux lub macOS zazwyczaj mają zestawu poleceń SSH, aby wygenerować i zarządzać nimi kluczy SSH i nawiązywanie połączeń SSH. 

Komputery z systemem Windows nie zawsze być porównywalne polecenia SSH zainstalowane. Wersje systemu Windows 10, które obejmują [podsystemu systemu Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/about) umożliwiają uruchamiania i dostępu do narzędzi, takich jak klient SSH natywnie w powłoki Bash. 

Jeśli chcesz użyć czegoś innego niż Bash dla systemu Windows, typowe Windows SSH klienci które można zainstalować lokalnie znajdują się w następujących pakietów:

* [Programu puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git dla systemu Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Inną opcją jest użycie narzędzi SSH dostępnych w Bash w [powłoki chmury Azure](../../cloud-shell/overview.md). 

* Dostęp w przeglądarce sieci web w chmurze powłoki [ https://shell.azure.com ](https://shell.azure.com) lub [portalu Azure](https://portal.azure.com). 
* Dostęp do chmury powłoki jako terminali z kodem Visual Studio, instalując [rozszerzenia konta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
W tej sekcji przedstawiono dwie opcje, aby utworzyć parę kluczy SSH w systemie Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Tworzenie kluczy SSH z ssh-keygen

Po uruchomieniu powłoki poleceń, takich jak Bash dla systemu Windows lub GitBash (lub Bash w powłoce chmury Azure), należy utworzyć pary kluczy SSH za pomocą `ssh-keygen` polecenia. Wpisz następujące polecenie, a odpowiedź monitów. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione. 

```bash
ssh-keygen -t rsa -b 2048
```

Aby uzyskać więcej tła i informacje, zobacz [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) kroki, aby utworzyć klucze z `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Tworzenie kluczy SSH z PuTTYgen

Jeśli wolisz za pomocą graficznego interfejsu użytkownika narzędzia do tworzenia kluczy SSH, można użyć generatora klucza PuTTYgen dołączonego [pakiet pobierania programu PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Aby utworzyć parę kluczy SSH RSA z PuTTYgen:

1. Uruchom PuTTYgen.

2. Kliknij przycisk **Generowanie**. Domyślnie PuTTYgen generuje klucz SSH-2 RSA 2048-bitowego.

4. Wskaźnik myszy nad obszarem puste, aby wygenerować niektórych losowości dla klucza.

5. Po wygenerowaniu klucza publicznego, opcjonalnie wprowadź i Potwierdź hasło. Pojawi się monit hasła podczas uwierzytelniania do maszyny Wirtualnej z kluczem SSH. Bez hasła Jeśli ktoś uzyskuje klucz prywatny one zalogować się do dowolnej maszyny Wirtualnej lub usługi, która używa tego klucza. Zaleca się Utwórz hasło. Jeśli zapomnisz hasła, jego odzyskanie nie będzie możliwe.

6. Klucz publiczny jest wyświetlany w górnej części okna. Skopiuj i Wklej klucz publiczny tej format jednego wiersza w portalu Azure lub szablonu usługi Azure Resource Manager podczas tworzenia maszyny Wirtualnej systemu Linux. Możesz również kliknąć **Zapisz klucz publiczny** Aby zapisać kopię na komputerze:

    ![Zapisz PuTTY pliku klucza publicznego](./media/ssh-from-windows/save-public-key.png)

7. Opcjonalnie, aby zapisać klucza prywatnego PuTTy format klucza prywatnego (plik ppk), kliknij przycisk **Zapisz klucz prywatny**. Będzie potrzebny plik ppk chcesz później przy użyciu programu PuTTY do nawiązywania połączeń SSH z maszyną wirtualną.

    ![Zapisz plik PuTTY klucza prywatnego](./media/ssh-from-windows/save-ppk-file.png)

    Jeśli chcesz zapisać klucz prywatny w formacie OpenSSH, używane przez wielu klientów SSH, formatem klucza prywatnego kliknij **konwersje** > **klucz OpenSSH wyeksportować**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH, podczas wdrażania maszyny Wirtualnej

Aby utworzyć maszynę Wirtualną systemu Linux, która używa kluczy SSH do uwierzytelnienia, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu portalu Azure lub innych metod.

W poniższym przykładzie pokazano, jak będzie skopiuj i wklej ten klucz publiczny w portalu Azure, podczas tworzenia maszyny Wirtualnej systemu Linux. Klucz publiczny następnie jest zwykle przechowywany w `~/.ssh/authorized_keys` na nowej maszynie Wirtualnej.

   ![Klucz publiczny używany podczas tworzenia maszyny Wirtualnej w portalu Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Połączenie z maszyną Wirtualną

Jest jednym ze sposobów nawiązywania połączeń SSH, do maszyny Wirtualnej systemu Linux z systemu Windows do użycia klienta SSH. Jeśli klient SSH zainstalowanych w systemie Windows, lub użyj narzędzia SSH w Bash w powłoce chmury Azure jest preferowana metoda. Jeśli wolisz to narzędzie z Graficznym interfejsem użytkownika, mogą łączyć się z programu PuTTY.  

### <a name="use-an-ssh-client"></a>Używanie klienta SSH
Z kluczem publicznym wdrożone na maszynie Wirtualnej platformy Azure i klucza prywatnego w systemie lokalnym SSH do maszyny Wirtualnej przy użyciu adresu IP lub nazwę DNS maszyny Wirtualnej. Zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* w poniższym poleceniu z nazwą użytkownika administratora i pełną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli zostały skonfigurowane hasło podczas tworzenia Twojej parę kluczy, wprowadź hasło po wyświetleniu monitu w procesie logowania.

### <a name="connect-with-putty"></a>Uzyskuj dostęp do programu PuTTY

Jeśli zainstalowano [pakiet pobierania programu PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) i wcześniej wygenerowany klucza prywatnego PuTTY (plik ppk), możesz nawiązać połączenie maszyny Wirtualnej systemu Linux z programu PuTTY.

1. Uruchom program PuTTy.

2. Wypełnij nazwę hosta lub adres IP maszyny Wirtualnej w portalu Azure:

    ![Otwórz nowe połączenie programu PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Przed wybraniem **Otwórz**, kliknij przycisk **połączenia** > **SSH** > **uwierzytelniania** kartę. Wyszukaj i wybierz klucza prywatnego PuTTY (plik ppk):

    ![Wybierz klucz prywatny PuTTY do uwierzytelniania](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kliknij przycisk **Otwórz** do nawiązania połączenia z maszyną Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowy opis kroków i opcje zaawansowane przykłady pracy z kluczy SSH, zobacz [par kluczy szczegółowy opis kroków, aby utworzyć SSH](create-ssh-keys-detailed.md).

* Umożliwia także programu PowerShell w powłoce chmury Azure do generowania kluczy SSH i nawiązywania połączeń SSH do maszyn wirtualnych systemu Linux. Zobacz [szybkiego startu PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Jeśli masz problem przy użyciu protokołu SSH, aby nawiązać połączenie z maszyn wirtualnych systemu Linux, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyny Wirtualnej systemu Linux Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
