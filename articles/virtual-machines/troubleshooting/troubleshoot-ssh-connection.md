---
title: Rozwiązywanie problemów z połączeniem SSH na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy, takie jak "Połączenie SSH nie powiodło się" lub "Odmowa połączenia SSH" na Maszynie wirtualnej platformy Azure z systemem Linux.
keywords: SSH połączenia zostało odrzucone, ssh błąd, azure ssh, połączenie SSH nie powiodło się
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 81e00c4a3b9490a05667d58952f7bdf8945bacdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61405258"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Rozwiązywanie problemów z połączeniami SSH na maszynie Wirtualnej systemu Linux platformy Azure, który zakończy się niepowodzeniem, błędy, lub w przypadku odmowy
Ten artykuł pomoże Ci znaleźć i rozwiązać problemy, które występują z powodu błędów protokołu Secure Shell (SSH), błędy połączenia SSH, lub odmówiono SSH, podczas próby nawiązania połączenia z maszyną wirtualną systemu Linux (VM). Można użyć witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenie dostępu do maszyny Wirtualnej dla systemu Linux, aby rozwiązać problemy z połączeniem.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Po każdym kroku rozwiązywania problemów spróbuj ponowne nawiązywanie połączenia z maszyną wirtualną.

1. [Resetowanie konfiguracji SSH](#reset-config).
2. [Resetuj poświadczenia](#reset-credentials) dla użytkownika.
3. Sprawdź [sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md) reguły zezwala na ruch SSH.
   * Upewnij się, że [reguła sieciowej grupy zabezpieczeń](#security-rules) istnieje, aby zezwalać na ruch SSH (domyślnie TCP port 22).
   * Nie można użyć przekierowania portu / mapowania bez korzystania z usługi Azure load balancer.
4. Sprawdź [kondycji zasobów maszyny Wirtualnej](../../resource-health/resource-health-overview.md).
   * Upewnij się, że maszyna wirtualna zgłasza jako będące w dobrej kondycji.
   * Jeśli masz [włączoną diagnostykę rozruchu](boot-diagnostics.md), sprawdź maszyny Wirtualnej nie zgłasza błędy w dziennikach.
5. [Uruchom ponownie maszynę Wirtualną](#restart-vm).
6. [Ponowne wdrażanie maszyny Wirtualnej](#redeploy-vm).

Kontynuuj lekturę, aby uzyskać bardziej szczegółowe kroki rozwiązywania problemów i wyjaśnienia.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostępne metody rozwiązywać problemy z połączeniem SSH
Możesz zresetować poświadczenia lub konfiguracji SSH przy użyciu jednej z następujących metod:

* [Witryna Azure portal](#use-the-azure-portal) — jest to świetny, jeśli musisz szybko Resetowanie konfiguracji SSH lub klucz SSH, a nie masz zainstalowane narzędzia platformy Azure.
* [Konsoli szeregowej maszyny Wirtualnej platformy Azure](https://aka.ms/serialconsolelinux) -konsoli szeregowej maszyny Wirtualnej będą działać niezależnie od konfiguracji SSH i umożliwi dzięki interakcyjnej konsoli dla maszyny wirtualnej. W rzeczywistości "nie może SSH" sytuacji są specjalnie konsoli szeregowej została zaprojektowana, aby pomóc w rozwiązaniu. Więcej szczegółów poniżej.
* [Interfejs wiersza polecenia Azure](#use-the-azure-cli) — Jeśli masz już w wierszu polecenia, szybko Resetowanie konfiguracji SSH lub poświadczenia. Jeśli pracujesz z klasycznej maszyny Wirtualnej, możesz użyć [klasycznego wiersza polecenia platformy Azure](#use-the-azure-classic-cli).
* [Usługa Azure rozszerzenia VMAccessForLinux](#use-the-vmaccess-extension) — tworzenie i ponowne użycie plików definicji json można zresetować poświadczenia protokołu SSH w konfiguracji lub użytkownika.

Każdy krok rozwiązywania problemów a następnie spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Jeśli nadal nie możesz się połączyć, przejdź do następnego kroku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Witryna Azure portal umożliwia szybkie zresetować poświadczenia protokołu SSH w konfiguracji lub użytkownika bez konieczności instalowania żadnych narzędzi na komputerze lokalnym.

Aby rozpocząć, wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół do **pomoc techniczna i rozwiązywanie problemów** i wybierz pozycję **Resetuj hasło** jak w poniższym przykładzie:

![Resetowanie konfiguracji SSH lub poświadczenia w witrynie Azure portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />Resetowanie konfiguracji SSH
Można zresetować konfiguracji SSH, wybierz `Reset configuration only` w **tryb** tak jak w poprzednim zrzucie ekranu, a następnie wybierz **aktualizacji**. Po wykonaniu tej akcji, spróbuj ponownie uzyskać dostęp z maszyną Wirtualną.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Resetowanie poświadczeń SSH dla użytkownika
Do zresetowania poświadczeń istniejącego użytkownika, wybierz opcję `Reset SSH public key` lub `Reset password` w **tryb** sekcji tak jak w poprzednim zrzucie ekranu. Określ nazwę użytkownika i nowego hasła lub klucza SSH, a następnie wybierz **aktualizacji**.

Można również utworzyć użytkownika z uprawnieniami "sudo" na maszynie Wirtualnej z tego menu. Wprowadź nową nazwę użytkownika oraz skojarzone hasło lub klucz SSH, a następnie wybierz **aktualizacji**.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />Sprawdź reguły zabezpieczeń

Użyj [weryfikowanie przepływu protokołu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) aby upewnić się, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące reguły grupy zabezpieczeń do zapewnienia ruchu przychodzącego "Zezwalaj" sieciowa grupa zabezpieczeń istnieje priorytetowa reguła dla portu SSH (domyślnie: 22). Aby uzyskać więcej informacji, zobacz [przepływu ruchu przy użyciu reguł efektywnym elementem systemu zabezpieczeń, aby rozwiązać maszyn wirtualnych](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Sprawdź routing

Usługa Network Watcher [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) możliwości, aby upewnić się, trasa nie jest zapobieganie ruch jest kierowany do lub z maszyny wirtualnej. Możesz również sprawdzić skuteczne trasy, aby wyświetlić wszystkie obowiązujące trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [przepływu ruchu Using obowiązujących tras, rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Użyj konsoli szeregowej maszyny Wirtualnej platformy Azure
[Konsoli szeregowej maszyny Wirtualnej platformy Azure](./serial-console-linux.md) zapewnia dostęp do konsoli usługi oparte na tekście dla maszyn wirtualnych systemu Linux. Aby rozwiązywać problemy dotyczące połączenia SSH w interaktywnej powłoki, można użyć konsoli. Upewnij się, że spełniasz [wymagania wstępne](./serial-console-linux.md#prerequisites) dotyczące korzystania z konsoli szeregowej i spróbuj wykonać poniższe polecenia w celu przeprowadzenia dalszej Rozwiązywanie problemów z łącznością usługi SSH.

### <a name="check-that-ssh-is-running"></a>Upewnij się, że protokół SSH jest uruchomiony.
Aby sprawdzić, czy protokół SSH jest uruchomiony na maszynie Wirtualnej, można użyć następującego polecenia:
```
$ ps -aux | grep ssh
```
Jeśli ma żadnych danych wyjściowych, protokół SSH jest uruchomiony.

### <a name="check-which-port-ssh-is-running-on"></a>Sprawdź port, który jest zasilany z protokołu SSH
Można użyć następującego polecenia, aby sprawdzić, który port SSH jest uruchomiona na:
```
$ sudo grep Port /etc/ssh/sshd_config
```
Dane wyjściowe będą wyglądać następująco:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Jeśli jeszcze nie, zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure przy użyciu [az login](/cli/azure/reference-index).

Jeśli utworzono i przekazano niestandardowy obraz dysku systemu Linux, upewnij się, [agenta usług Microsoft Azure Linux](../extensions/agent-windows.md) wersji 2.0.5 lub nowszy jest zainstalowany. W przypadku maszyn wirtualnych utworzonych za pomocą galerii obrazów to rozszerzenie dostępu jest już zainstalowane i skonfigurowane dla Ciebie.

### <a name="reset-ssh-configuration"></a>Resetowanie konfiguracji SSH
Początkowo można spróbować Resetowanie konfiguracji SSH do wartości domyślnych i ponownego uruchomienia serwera SSH na maszynie Wirtualnej. Nie ma to wpływu na nazwę konta użytkownika, hasła lub kluczy SSH.
W poniższym przykładzie użyto [Resetowanie użytkownika maszyny wirtualnej az-ssh](/cli/azure/vm/user) można zresetować konfiguracji SSH na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
W poniższym przykładzie użyto [az vm użytkownik aktualizację](/cli/azure/vm/user) zresetować poświadczenia dla `myUsername` wartość określoną w `myPassword`, na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Jeśli przy użyciu uwierzytelniania klucza SSH, możesz przywrócić klucz SSH dla danego użytkownika. W poniższym przykładzie użyto **az vm dostępu set-linux-user** można zaktualizować klucza SSH, przechowywane w `~/.ssh/id_rsa.pub` dla użytkownika o nazwie `myUsername`, na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Za pomocą rozszerzenia VMAccess
Odczytuje rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux w pliku json, który określa akcje do wykonania. Te akcje obejmują Resetowanie SSHD, resetowanie klucz SSH lub dodawania użytkownika. Nadal używać wiersza polecenia platformy Azure do wywoływania rozszerzenia VMAccess, ale można użyć ponownie pliki w formacie json na wielu maszynach wirtualnych w razie potrzeby. Takie podejście umożliwia tworzenie repozytorium pliki w formacie json, które następnie mogą być wywoływane dla danych scenariuszy.

### <a name="reset-sshd"></a>Resetuj SSHD
Utwórz plik o nazwie `settings.json` o następującej zawartości:

```json
{
    "reset_ssh":"True"
}
```

Przy użyciu wiersza polecenia platformy Azure, możesz następnie wywołać `VMAccessForLinux` rozszerzenie Zresetuj połączenie SSHD przez określenie pliku json. W poniższym przykładzie użyto [zestaw rozszerzeń maszyn wirtualnych az](/cli/azure/vm/extension) zresetować SSHD na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
Jeśli SSHD wydaje się działać prawidłowo, należy zresetować poświadczenia dla użytkownika giver. Aby zresetować hasło dla użytkownika, Utwórz plik o nazwie `settings.json`. Poniższy przykład spowoduje zresetowanie poświadczeń dla `myUsername` wartość określoną w `myPassword`. Wprowadź następujące wiersze do Twojej `settings.json` plików, przy użyciu własnych wartości:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Lub aby przywrócić klucz SSH dla użytkownika, najpierw utwórz plik o nazwie `settings.json`. Poniższy przykład spowoduje zresetowanie poświadczeń dla `myUsername` wartość określoną w `myPassword`, na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Wprowadź następujące wiersze do Twojej `settings.json` plików, przy użyciu własnych wartości:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po utworzeniu pliku json, należy użyć wiersza polecenia platformy Azure do wywołania `VMAccessForLinux` rozszerzenia do zresetowania poświadczeń użytkownika SSH, określając pliku json. Poniższy przykład spowoduje zresetowanie poświadczeń na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Użyj klasycznego wiersza polecenia platformy Azure
Jeśli jeszcze nie, [zainstalować klasycznego wiersza polecenia platformy Azure i połączyć się z subskrypcją platformy Azure](../../cli-install-nodejs.md). Upewnij się, że używasz trybu usługi Resource Manager w następujący sposób:

```azurecli
azure config mode arm
```

Jeśli utworzono i przekazano niestandardowy obraz dysku systemu Linux, upewnij się, [agenta usług Microsoft Azure Linux](../extensions/agent-windows.md) wersji 2.0.5 lub nowszy jest zainstalowany. W przypadku maszyn wirtualnych utworzonych za pomocą galerii obrazów to rozszerzenie dostępu jest już zainstalowane i skonfigurowane dla Ciebie.

### <a name="reset-ssh-configuration"></a>Resetowanie konfiguracji SSH
Konfiguracji SSHD, sama może być źle skonfigurowany lub Usługa napotkała błąd. Możesz zresetować SSHD, aby upewnić się, że konfiguracja protokołu SSH, sama jest prawidłowa. Resetowanie SSHD powinien być pierwszym krokiem rozwiązywania problemów, które należy podjąć.

Poniższy przykład powoduje zresetowanie SSHD na maszynie Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyć własnych nazw grup maszyn wirtualnych i zasobów w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
Jeśli SSHD wydaje się działać prawidłowo, można zresetować hasła dla użytkownika giver. Poniższy przykład spowoduje zresetowanie poświadczeń dla `myUsername` wartość określoną w `myPassword`, na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Jeśli przy użyciu uwierzytelniania klucza SSH, możesz przywrócić klucz SSH dla danego użytkownika. Poniższy przykład aktualizuje klucz SSH, przechowywane w `~/.ssh/id_rsa.pub` dla użytkownika o nazwie `myUsername`, na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />Uruchom ponownie Maszynę wirtualną
Jeśli masz zresetować poświadczenia protokołu SSH w konfiguracji i użytkowników lub wystąpił błąd w ten sposób, możesz spróbować ponownego uruchamiania maszyny Wirtualnej na adres bazowy problemów obliczeniowych.

### <a name="azure-portal"></a>Azure Portal
Aby ponownie uruchomić Maszynę wirtualną przy użyciu witryny Azure portal, wybierz maszynę Wirtualną, a następnie wybierz pozycję **ponowne uruchomienie** jak w poniższym przykładzie:

![Uruchom ponownie Maszynę wirtualną w witrynie Azure portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W poniższym przykładzie użyto [ponownego uruchamiania maszyny wirtualnej az](/cli/azure/vm) ponownego uruchomienia maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure
Poniższy przykład powoduje ponowne uruchomienie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />Ponowne wdrażanie maszyny Wirtualnej
Można wdrożyć ponownie Maszynę wirtualną do innego węzła w obrębie platformy Azure, która może rozwiązać podstawowe problemy sieciowe. Aby dowiedzieć się, jak ponowne wdrożenie maszyny Wirtualnej, zobacz [ponowne wdrażanie maszyny wirtualnej w nowym węźle platformy Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Po zakończeniu tej operacji efemerycznego dysku dane zostaną utracone i zostaną zaktualizowane dynamiczne adresy IP, które są skojarzone z maszyną wirtualną.
>
>

### <a name="azure-portal"></a>Azure Portal
Aby przeprowadzić ponowne wdrożenie maszyny Wirtualnej przy użyciu witryny Azure portal, wybierz maszynę Wirtualną, a następnie przewiń w dół do **pomoc techniczna i rozwiązywanie problemów** sekcji. Wybierz **ponownie wdrożyć** jak w poniższym przykładzie:

![Ponowne wdrażanie maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Następujące przykładowe zastosowanie [ponowne wdrażanie maszyny wirtualnej az](/cli/azure/vm) Aby przeprowadzić ponowne wdrożenie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure
Poniższy przykład ponownie wdraża maszynę Wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania
Spróbuj wykonać następujące kroki, aby rozwiązać najbardziej typowe błędy połączenia SSH dla maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Po każdym kroku spróbuj ponowne nawiązywanie połączenia z maszyną wirtualną.

* Zresetuj dostęp zdalny z [witryny Azure portal](https://portal.azure.com). W witrynie Azure portal, wybierz maszynę Wirtualną, a następnie wybierz pozycję **Resetuj dostęp zdalny...** .
* Uruchom ponownie maszynę wirtualną. Na [witryny Azure portal](https://portal.azure.com), wybierz maszynę Wirtualną i wybierz **ponowne uruchomienie**.

* Ponowne wdrażanie maszyny Wirtualnej w nowym węźle platformy Azure. Aby uzyskać informacje o tym, jak można wdrożyć ponownie maszyny Wirtualnej, zobacz [ponowne wdrażanie maszyny wirtualnej w nowym węźle platformy Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Po zakończeniu tej operacji efemerycznego dysku dane zostaną utracone i zostaną zaktualizowane dynamiczne adresy IP, które są skojarzone z maszyną wirtualną.
* Postępuj zgodnie z instrukcjami w [jak zresetować hasło lub protokół SSH dla maszyn wirtualnych z systemem Linux](../linux/classic/reset-access-classic.md) do:

  * Resetowanie hasła lub klucza SSH.
  * Tworzenie *"sudo"* konta użytkownika.
  * Resetowanie konfiguracji SSH.
* Sprawdzanie kondycji zasobu maszyny Wirtualnej ewentualne problemy z platform.<br>
     Wybierz maszynę Wirtualną, a następnie przewiń w dół **ustawienia** > **sprawdzanie kondycji**.

## <a name="additional-resources"></a>Dodatkowe zasoby
* Jeśli nadal nie możesz SSH z maszyną wirtualną po wykonaniu kroków po, zobacz [bardziej szczegółowe kroki rozwiązywania problemów](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby przejrzeć dodatkowe kroki w celu rozwiązania problemu.
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z dostępem do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchamianej na maszynie wirtualnej platformy Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania, zobacz [jak zresetować hasło lub protokół SSH dla maszyn wirtualnych z systemem Linux](../linux/classic/reset-access-classic.md).
