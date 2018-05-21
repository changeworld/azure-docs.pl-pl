---
title: Za pomocą pulpitu zdalnego do maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak instalowanie i konfigurowanie pulpitu zdalnego (xrdp), aby nawiązać połączenie Maszynę wirtualną systemu Linux na platformie Azure za pomocą narzędzi graficznych
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: c47822bebdc8b3cc8896fe56b8f9a4ce317495c3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalowanie i konfigurowanie pulpitu zdalnego, aby nawiązać połączenie Maszynę wirtualną systemu Linux na platformie Azure
Maszyny wirtualne systemu Linux (VM) na platformie Azure zwykle są zarządzane z poziomu wiersza polecenia przy użyciu połączenia bezpiecznej powłoki (SSH). Gdy nowy, Linux lub szybkiego scenariuszach rozwiązywania problemów, użyj pulpitu zdalnego może być łatwiejsze. W tym artykule szczegółowo przedstawiają, jak zainstalować i skonfigurować środowisko pulpitu ([xfce](https://www.xfce.org)) i pulpitu zdalnego ([xrdp](http://www.xrdp.org)) dla maszyny Wirtualnej systemu Linux przy użyciu modelu wdrażania Menedżera zasobów.


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule wymaga istniejącego 16.04 LTS maszyny Wirtualnej systemu Ubuntu na platformie Azure. Jeśli musisz utworzyć maszyny Wirtualnej, użyj jednej z następujących metod:

- [Azure CLI 2.0](quick-create-cli.md)
- [Portalu Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Zainstaluj środowisko pulpitu na maszynie Wirtualnej systemu Linux
Większość maszyn wirtualnych systemu Linux na platformie Azure nie masz środowisko pulpitu instalowane domyślnie. Maszyn wirtualnych systemu Linux są często zarządzane za pomocą połączeń SSH, a nie środowiska pulpitu. Istnieją różne środowiska pulpitu w systemie Linux, możesz wybrać następujące opcje. W zależności od wybranych środowiska pulpitu może korzystać z jednego do 2 GB miejsca na dysku i podjąć 5-10 minut, aby zainstalować i skonfigurować wymagane pakiety.

W poniższym przykładzie instalowana niewielka [xfce4](https://www.xfce.org/) środowiska pulpitu na maszynie Wirtualnej systemu Ubuntu 16.04 LTS. Polecenia dla innych dystrybucje się nieco różnić (Użyj `yum` zainstalować w systemie Red Hat Enterprise Linux i skonfigurować odpowiednie `selinux` reguły lub użyj `zypper` do zainstalowania w systemie SUSE, na przykład).

Pierwszy, SSH do maszyny Wirtualnej. Poniższy przykład nawiązuje połączenie z maszyną wirtualną o nazwie *myvm.westus.cloudapp.azure.com* nazwy użytkownika *azureuser*:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli używasz systemu Windows i uzyskać więcej informacji o korzystaniu z protokołu SSH, zobacz [kluczy sposobu korzystania z protokołu SSH z systemem Windows](ssh-from-windows.md).

Następnie zainstaluj przy użyciu xfce `apt` w następujący sposób:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalowanie i konfigurowanie serwera usług pulpitu zdalnego
Teraz, gdy masz środowisko pulpitu, zainstalowane, należy skonfigurować usługi usług pulpitu zdalnego do nasłuchiwania przychodzących połączeń. [xrdp](http://xrdp.org) jest serwer protokołu RDP (Remote Desktop) typu open source, który jest dostępny na większości dystrybucje systemu Linux i dobrze działa z xfce. Zainstaluj xrdp na maszynie Wirtualnej systemu Ubuntu w następujący sposób:

```bash
sudo apt-get install xrdp
```

Poinformuj xrdp środowiska pulpitu do użycia podczas uruchamiania sesji. Skonfiguruj xrdp do użycia xfce jako środowisko pulpitu w następujący sposób:

```bash
echo xfce4-session >~/.xsession
```

Uruchom ponownie usługę xrdp, aby zmiany zostały wprowadzone w następujący sposób:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ustaw hasło konta użytkownika lokalnego
Jeśli hasło zostało utworzone dla tego konta użytkownika, po utworzeniu maszyny Wirtualnej, Pomiń ten krok. Jeśli tylko uwierzytelniania klucza SSH i nie masz hasła lokalnego konta ustawić, określ hasło, aby używać xrdp logować się do maszyny Wirtualnej. xrdp nie może zaakceptować kluczy SSH do uwierzytelniania. W poniższym przykładzie określa hasło dla konta użytkownika *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Określenie hasła nie powoduje aktualizacji konfiguracji SSHD, aby umożliwić hasło logowania, jeśli obecnie nie. Z punktu widzenia zabezpieczeń może chcesz nawiązać połączenie z maszyną Wirtualną z tunelu SSH przy użyciu uwierzytelniania opartego na kluczach, a następnie połącz się xrdp. Jeśli tak, pomiń następny krok na tworzenie reguły grupy zabezpieczeń sieci, aby zezwolić na ruch pulpitu zdalnego.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Tworzenie reguły grupy zabezpieczeń sieci dla ruchu pulpitu zdalnego
Aby zezwolić na ruch pulpitu zdalnego do maszyny Wirtualnej systemu Linux, zabezpieczenia sieci grupy reguł musi być utworzony umożliwia ruch TCP na porcie 3389 nawiązać połączenie z maszyną Wirtualną. Aby uzyskać więcej informacji dotyczących zasad grupy zabezpieczeń sieci, zobacz [co to jest grupa zabezpieczeń sieci?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Możesz również [Użyj portalu Azure, aby utworzyć regułę grupy zabezpieczeń sieci](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W poniższym przykładzie jest tworzona reguła grupy zabezpieczeń sieci o [port Otwórz az maszyny wirtualnej](/cli/azure/vm#az_vm_open_port) na porcie *3389*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Połączenie z maszyną Wirtualną systemu Linux za pomocą klienta usług pulpitu zdalnego
Otwórz klienta pulpitu zdalnego lokalnego i połącz się z adresu IP lub nazwę DNS maszyny Wirtualnej systemu Linux. Wprowadź nazwę użytkownika i hasło dla konta użytkownika na maszynie Wirtualnej w następujący sposób:

![Nawiązać xrdp przy użyciu klienta pulpitu zdalnego](./media/use-remote-desktop/remote-desktop-client.png)

Po uwierzytelnieniu środowiska pulpitu xfce obciążenia i wyglądać podobnie do poniższego przykładu:

![xfce środowisko pulpitu za pośrednictwem xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie można nawiązać połączenia z maszyny Wirtualnej systemu Linux przy użyciu klienta pulpitu zdalnego, należy użyć `netstat` na Twojej maszyny Wirtualnej systemu Linux, aby sprawdzić, czy maszyna wirtualna nasłuchuje połączeń protokołu RDP w następujący sposób:

```bash
sudo netstat -plnt | grep rdp
```

W poniższym przykładzie przedstawiono wirtualna nasłuchiwanie na porcie TCP 3389 zgodnie z oczekiwaniami:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Jeśli *xrdp sesman* nie nasłuchuje usługa, w przypadku maszyny Wirtualnej systemu Ubuntu ponownie uruchomić usługę w następujący sposób:

```bash
sudo service xrdp restart
```

Przejrzyj dzienniki w */var/log* na maszynie Wirtualnej systemu Ubuntu objętych danych łączem, dlaczego usługa może nie odpowiadać. Można również monitorować syslog, podczas próby połączeń usług pulpitu zdalnego, aby wyświetlić wszystkie błędy:

```bash
tail -f /var/log/syslog
```

Inne dystrybucje systemu Linux, takie jak Red Hat Enterprise Linux i SUSE mogą mieć różne sposoby, aby ponownie uruchomić usługi i lokalizacje plików dziennika alternatywne, aby przejrzeć.

Jeśli nie mają żadnych odpowiedzi na kliencie usług pulpitu zdalnego i nie ma żadnych zdarzeń w dzienniku systemowym, to zachowanie wskazuje, czy ruch pulpitu zdalnego nie może połączyć maszyny Wirtualnej. Przejrzyj reguły grupy zabezpieczeń sieci, tak aby upewnić się, że masz regułę zezwalającą na ruch TCP na porcie 3389. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością aplikacji](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia i używania kluczy SSH z maszyn wirtualnych systemu Linux, zobacz [utworzyć SSH kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](mac-create-ssh-keys.md).

Aby uzyskać informacji o korzystaniu z protokołu SSH z systemu Windows, zobacz [kluczy sposobu korzystania z protokołu SSH z systemem Windows](ssh-from-windows.md).

