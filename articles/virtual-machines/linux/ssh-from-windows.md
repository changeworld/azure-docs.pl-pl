---
title: Używanie kluczy SSH z Windows maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak generować i używania kluczy SSH na komputerze Windows, aby nawiązać połączenie z maszyną wirtualną systemu Linux na platformie Azure.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630215"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak używanie kluczy SSH w systemie Windows na platformie Azure

W tym artykule przedstawiono sposób generowania i użytkowania kluczy protokołu secure shell (SSH) na komputerze Windows utworzyć i połączyć z maszyną wirtualną systemu Linux (VM) na platformie Azure. Aby użyć kluczy SSH z systemem Linux lub macOS klienta, zobacz [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) wskazówki.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pakiety Windows i klientów SSH
Nawiązywanie połączenia i zarządzanie maszynami wirtualnymi systemu Linux w systemie Azure za pomocą *klienta SSH*. Komputery z systemem Linux lub macOS, zwykle mają zestawu poleceń SSH, aby wygenerować i zarządzać kluczami SSH i nawiązywanie połączeń SSH. 

Windows komputery nie zawsze mają porównywalne polecenia SSH zainstalowane. Wersje systemu Windows 10, które obejmują [podsystem Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/about) umożliwiają uruchamianie i dostęp do narzędzia, takie jak klient SSH natywnie w powłoce Bash. 

Jeśli chcesz użyć czegoś innego niż Bash Windows, typowe Windows SSH klienci instalowanej lokalnie znajdują się w następujących pakietów:

* [Programu puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git Pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Innym rozwiązaniem jest użycie narzędzia SSH, dostępne w programie Bash w [usługi Azure Cloud Shell](../../cloud-shell/overview.md). 

* Dostęp do usługi Cloud Shell w przeglądarce sieci web pod adresem [ https://shell.azure.com ](https://shell.azure.com) lub [witryny Azure portal](https://portal.azure.com). 
* Dostęp do usługi Cloud Shell jako terminala z programu Visual Studio Code, instalując [rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
W tej sekcji przedstawiono dwie opcje, aby utworzyć parę kluczy SSH na Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Tworzenie kluczy SSH przy użyciu protokołu ssh-keygen

Jeśli uruchamiasz powłoki poleceń, takiej jak powłoka Bash Windows GitBash (lub Bash w usłudze Azure Cloud Shell), utworzyć parę kluczy SSH przy użyciu `ssh-keygen` polecenia. Wpisz następujące polecenie, a następnie wprowadź dane w monitach. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione. 

```bash
ssh-keygen -t rsa -b 2048
```

Aby uzyskać więcej ogólnych informacji i informacji, zobacz [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) kroki, aby utworzyć klucze za pomocą `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Tworzenie kluczy SSH za pomocą programu PuTTYgen

Jeśli wolisz używać narzędzi z Graficznym interfejsem użytkownika do tworzenia kluczy SSH, można użyć generator kluczy PuTTYgen, dołączone do [pakiet do pobrania programu PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Aby utworzyć parę kluczy RSA protokołu SSH za pomocą programu PuTTYgen:

1. Uruchom program PuTTYgen.

2. Kliknij przycisk **Generowanie**. Domyślnie program PuTTYgen generuje klucz SSH-2 RSA 2048-bitowych.

4. Wskaźnik myszy nad pusty obszar, aby wygenerować niektóre losowości dla klucza.

5. Po wygenerowaniu klucza publicznego, opcjonalnie wprowadź i Potwierdź hasło. Użytkownik jest monitowany o wpisanie hasła podczas uwierzytelniania przy użyciu klucza SSH z maszyną wirtualną. Bez hasła Jeśli ktoś uzyskania klucza prywatnego, mogą zalogować się do dowolnej maszyny Wirtualnej lub usługi, która używa tego klucza. Firma Microsoft zaleca się utworzenie hasła. Jeśli zapomnisz hasła, jego odzyskanie nie będzie możliwe.

6. Klucz publiczny jest wyświetlany w górnej części okna. Skopiuj i Wklej klucz publiczny tej format jednego wiersza w witrynie Azure portal lub szablonu usługi Azure Resource Manager, po utworzeniu maszyny Wirtualnej z systemem Linux. Możesz również kliknąć **Zapisz klucz publiczny** można zapisać kopię na komputerze:

    ![Zapisz plik klucza publicznego w PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Opcjonalnie, aby zapisać klucz prywatny w PuTTy formatem klucza prywatnego (plik ppk), kliknij przycisk **Zapisz klucz prywatny**. Będzie potrzebny plik ppk chcesz przy użyciu programu PuTTY później Utwórz połączenie SSH z maszyną wirtualną.

    ![Zapisz PuTTY pliku klucza prywatnego](./media/ssh-from-windows/save-ppk-file.png)

    Jeśli chcesz zapisać klucz prywatny w formacie OpenSSH, formatem klucza prywatnego używany przez wielu klientów SSH, kliknij przycisk **konwersje** > **Eksportuj klucz OpenSSH**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH w przypadku wdrażania maszyny Wirtualnej

Aby utworzyć maszynę Wirtualną systemu Linux, która używa kluczy SSH do uwierzytelniania, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal lub innych metod.

Poniższy przykład pokazuje, jak będzie skopiować i wkleić klucz publiczny do witryny Azure portal, podczas tworzenia maszyny Wirtualnej z systemem Linux. Klucz publiczny następnie są zwykle przechowywane w `~/.ssh/authorized_keys` na nowej maszynie Wirtualnej.

   ![Użyć klucza publicznego podczas tworzenia maszyny Wirtualnej w witrynie Azure portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Jednym ze sposobów zapewnienia połączenie SSH z maszyną wirtualną systemu Linux z Windows ma użyć klienta SSH. Jest to preferowana metoda, jeśli masz zainstalowane w systemie Windows klienta SSH lub użyj narzędzia SSH w programie Bash w usłudze Azure Cloud Shell. Jeśli wolisz narzędzia z Graficznym interfejsem użytkownika, możesz połączyć się przy użyciu programu PuTTY.  

### <a name="use-an-ssh-client"></a>Użyj klienta SSH
Za pomocą klucza publicznego, wdrożone na maszynie Wirtualnej platformy Azure, a klucz prywatny w systemie lokalnym SSH z maszyną Wirtualną przy użyciu adresu IP lub nazwę DNS maszyny wirtualnej. Zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* w następującym poleceniu przy użyciu podanej nazwy użytkownika administratora i w pełni kwalifikowaną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli hasło jest skonfigurowana, podczas tworzenia pary kluczy, wprowadź hasło po wyświetleniu monitu podczas procesu logowania.

### <a name="connect-with-putty"></a>Połącz przy użyciu programu PuTTY

Jeśli zainstalowano [pakiet do pobrania programu PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) wcześniej wygenerowany klucza prywatnego PuTTY (plik ppk), możesz nawiązać połączenie maszyny Wirtualnej systemu Linux przy użyciu programu PuTTY.

1. Uruchom program PuTTy.

2. Wprowadź nazwę hosta lub adres IP maszyny wirtualnej w witrynie Azure portal:

    ![Otwórz nowe połączenie programu PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Przed wybraniem **Otwórz**, kliknij przycisk **połączenia** > **SSH** > **uwierzytelniania** kartę. Wyszukaj i wybierz klucza prywatnego PuTTY (plik ppk):

    ![Wybierz klucz prywatny PuTTY do uwierzytelniania](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kliknij przycisk **Otwórz** nawiązać połączenia z maszyną Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe kroki i opcje zaawansowane przykłady pracy przy użyciu kluczy SSH, zobacz [par kluczy szczegółowe instrukcje dotyczące tworzenia SSH](create-ssh-keys-detailed.md).

* Umożliwia także środowiska PowerShell w usłudze Azure Cloud Shell do generowania kluczy SSH i nawiązywać połączenia SSH do maszyn wirtualnych systemu Linux. Zobacz [szybkiego startu programu PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Jeśli masz problemy przy użyciu protokołu SSH, aby nawiązać połączenie z maszyn wirtualnych systemu Linux, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z Maszyną wirtualną systemu Linux platformy Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
