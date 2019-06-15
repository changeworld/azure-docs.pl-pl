---
title: Pulpit zdalny do maszyny Wirtualnej z systemem Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak instalowanie i konfigurowanie pulpitu zdalnego (xrdp), aby nawiązać połączenie z maszyny Wirtualnej z systemem Linux na platformie Azure za pomocą narzędzi graficznych
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 56aa06ade50f6c0eb1467b1295cbebb907023398
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209372"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalowanie i konfigurowanie pulpitu zdalnego, aby nawiązać połączenie z maszyny Wirtualnej z systemem Linux na platformie Azure
Maszyny wirtualne systemu Linux (VM) na platformie Azure odbywa się zwykle z wiersza polecenia przy użyciu połączenia protokołu secure shell (SSH). Jeśli nowe z systemem Linux lub scenariuszach szybkiego rozwiązywania problemów, korzystanie z pulpitu zdalnego może być łatwiejsze. W tym artykule opisano, jak zainstalować i skonfigurować środowisko pulpitu ([xfce](https://www.xfce.org)) i usług pulpitu zdalnego ([xrdp](https://www.xrdp.org)) dla maszyny Wirtualnej systemu Linux przy użyciu modelu wdrażania usługi Resource Manager.


## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł wymaga istniejącej Ubuntu 16.04 LTS maszyny Wirtualnej na platformie Azure. Jeśli musisz utworzyć Maszynę wirtualną, użyj jednej z następujących metod:

- [Wiersza polecenia platformy Azure](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Zainstalować środowisko pulpitu na maszynie Wirtualnej systemu Linux
Większość maszyn wirtualnych systemu Linux na platformie Azure nie mają środowisko pulpitu instalowane domyślnie. Maszyny wirtualne systemu Linux często są zarządzane przy użyciu połączenia SSH, a nie w środowisku komputerowym. Istnieją różne środowiska pulpitu w systemie Linux, które można wybrać. W zależności od wybranego środowiska pulpitu może korzystać z jednego do 2 GB miejsca na dysku i zająć 5 – 10 minut, aby zainstalować i skonfigurować wymagane pakiety.

W poniższym przykładzie instalowana lekkiego [xfce4](https://www.xfce.org/) środowiska pulpitu w Maszynie wirtualnej systemu Ubuntu 16.04 LTS. Polecenia dla innych dystrybucji różnią się nieco (Użyj `yum` instalowanie w systemie Red Hat Enterprise Linux i Konfigurowanie odpowiedniej `selinux` reguł lub użycie `zypper` do zainstalowania w systemie SUSE, na przykład).

Pierwszy, SSH z maszyną Wirtualną. Poniższy przykład nawiązuje połączenie z maszyną wirtualną o nazwie *myvm.westus.cloudapp.azure.com* nazwy użytkownika *azureuser*. Użyj własnych wartości:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli używasz Windows i potrzebujesz więcej informacji na temat korzystania z protokołu SSH, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows](ssh-from-windows.md).

Następnie zainstaluj przy użyciu xfce `apt` w następujący sposób:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalowanie i konfigurowanie serwera usług pulpitu zdalnego
Teraz, gdy masz zainstalowane środowisko pulpitu, należy skonfigurować usługi usług pulpitu zdalnego do nasłuchiwania przychodzących połączeń. [xrdp](http://xrdp.org) to serwer protokołu RDP (Remote Desktop) typu open source, który jest dostępny na większość dystrybucje systemu Linux i dobrze działa z xfce. Zainstaluj xrdp na maszynie Wirtualnej z systemem Ubuntu w następujący sposób:

```bash
sudo apt-get install xrdp
sudo systemctl enable xrdp
```

Poinformuj xrdp jakiego środowiska pulpitu do użycia podczas uruchamiania sesji. Skonfiguruj xrdp służące xfce jako środowisko pulpitu w następujący sposób:

```bash
echo xfce4-session >~/.xsession
```

Uruchom ponownie usługę xrdp, aby zmiany zostały wprowadzone następujące zmiany:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ustaw hasło konta użytkownika lokalnego
Jeśli utworzono hasło dla konta użytkownika, po utworzeniu maszyny Wirtualnej, Pomiń ten krok. Jeśli tylko używają uwierzytelniania kluczem SSH i masz hasło do konta lokalnego zestawu, określ hasło, zanim użyjesz xrdp Zaloguj się do maszyny Wirtualnej. xrdp nie może akceptować kluczy SSH do uwierzytelniania. W poniższym przykładzie określono hasło dla konta użytkownika *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Określenie hasła nie aktualizuje konfigurację SSHD tak, aby zezwolić na hasło logowania, jeśli obecnie nie. Z punktu widzenia zabezpieczeń może chcesz nawiązać połączenie z maszyną Wirtualną przy użyciu tunelu SSH przy użyciu uwierzytelniania opartego na kluczu, a następnie połączyć xrdp. Jeśli tak, Pomiń kolejne kroki dotyczące tworzenia reguły sieciowej grupy zabezpieczeń zezwalającą na ruch pulpitu zdalnego.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Utwórz regułę sieciowej grupy zabezpieczeń dla ruchu pulpitu zdalnego
Aby zezwolić na ruch pulpitu zdalnego do maszyny Wirtualnej systemu Linux, zabezpieczenia sieci grupy reguł musi utworzyć, która umożliwia TCP na porcie 3389 nawiązać połączenie z maszyną Wirtualną. Aby uzyskać więcej informacji na temat reguł sieciowych grup zabezpieczeń, zobacz [co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Możesz również [umożliwia utworzenie reguły sieciowej grupy zabezpieczeń w witrynie Azure portal](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Poniższy przykład tworzy reguły sieciowej grupy zabezpieczeń z [az vm open-port](/cli/azure/vm#az-vm-open-port) na porcie *3389*. Z wiersza polecenia platformy Azure nie sesję SSH z maszyną wirtualną, otwórz następujące reguły sieciowej grupy zabezpieczeń:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Łączenie z maszyną Wirtualną z systemem Linux przy użyciu klienta pulpitu zdalnego
Otwórz klienta pulpitu zdalnego lokalnego i połącz się z adresu IP lub nazwę DNS maszyny wirtualnej systemu Linux. Wprowadź nazwę użytkownika i hasło dla konta użytkownika na maszynie Wirtualnej w następujący sposób:

![Nawiązać połączenie z xrdp przy użyciu klienta pulpitu zdalnego](./media/use-remote-desktop/remote-desktop-client.png)

Po uwierzytelnieniu w środowisku komputerowym xfce zostaną załadowane i wyglądać podobnie do poniższego przykładu:

![xfce środowiska pulpitu za pośrednictwem xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Jeśli klient lokalny RDP korzysta z uwierzytelniania na poziomie sieci (NLA), może być konieczne wyłączenie tego ustawienia połączenia. XRDP aktualnie nie obsługuje uwierzytelniania na poziomie sieci. Można również przeglądać alternatywne rozwiązania protokołu RDP, które obsługują uwierzytelniania na poziomie sieci, takich jak [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie możesz połączyć z maszyną wirtualną z systemem Linux przy użyciu klienta pulpitu zdalnego, należy użyć `netstat` na maszynie Wirtualnej systemu Linux, aby sprawdzić, czy maszyna wirtualna nasłuchuje połączeń protokołu RDP w następujący sposób:

```bash
sudo netstat -plnt | grep rdp
```

Nasłuchiwanie na porcie TCP 3389 zgodnie z oczekiwaniami maszyny Wirtualnej można znaleźć w poniższym przykładzie:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Jeśli *xrdp sesman* nie nasłuchuje usługa, na maszynie Wirtualnej systemu Ubuntu Uruchom ponownie usługę w następujący sposób:

```bash
sudo service xrdp restart
```

Przejrzyj dzienniki w */var/log* na maszynie Wirtualnej systemu Ubuntu, aby uzyskać wskazówki, dlaczego usługa może nie odpowiadać. Można również monitorować syslog, podczas próby połączenia pulpitu zdalnego, aby wyświetlić błędy:

```bash
tail -f /var/log/syslog
```

Inne dystrybucje systemu Linux, takie jak Red Hat Enterprise Linux i SUSE mogą mieć różne sposoby, aby ponownie uruchomić usługi i lokalizacje plików dziennika alternatywne, aby zapoznać się z.

Jeśli nie otrzymasz żadnych odpowiedzi w kliencie usług pulpitu zdalnego i nie ma żadnych zdarzeń w dzienniku systemu to zachowanie wskazuje, że maszyna wirtualna nie może połączyć się ruchu pulpitu zdalnego. Przejrzyj reguły grupy zabezpieczeń sieci, tak aby upewnić się, że masz regułę zezwalającą na ruch TCP na porcie 3389. Aby uzyskać więcej informacji, zobacz [aplikacji Rozwiązywanie problemów z łącznością](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia i używania kluczy SSH z maszynami wirtualnymi systemu Linux, zobacz [tworzenie kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](mac-create-ssh-keys.md).

Aby uzyskać informacje na temat korzystania z protokołu SSH z Windows, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows](ssh-from-windows.md).

