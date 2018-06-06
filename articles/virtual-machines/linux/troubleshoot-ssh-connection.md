---
title: Rozwiązywanie problemów połączenia SSH do maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy, takie jak "Połączenia SSH nie powiodło się" lub "Odmowa połączenia SSH" dla maszyny Wirtualnej platformy Azure, systemem Linux.
keywords: SSH połączenia zostało odrzucone, ssh błędu, platforma azure ssh, połączenia SSH nie powiodło się
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 9db1252ffdb705308c6bdaf77f394a0e57145fb5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701925"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Rozwiązywanie problemów z połączeń SSH maszyny Wirtualnej systemu Linux platformy Azure, który zakończy się niepowodzeniem, błędy, lub zostało odrzucone
Istnieją różne powody, czy występują błędy protokołu Secure Shell (SSH), błędów połączenia SSH, lub SSH zostało odrzucone podczas próby nawiązania połączenia z maszyną wirtualną systemu Linux (VM). Ten artykuł ułatwia znajdowanie i rozwiązać problemy. Można użyć portalu Azure, Azure CLI lub rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux, aby rozwiązać problemy z połączeniem.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](http://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](http://azure.microsoft.com/support/options/) i wybierz **uzyskać pomoc techniczną**. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Po wykonaniu każdego kroku rozwiązywania problemów spróbuj połączyć się ponownie do maszyny Wirtualnej.

1. Zresetuj konfigurację protokołu SSH.
2. Resetowanie poświadczeń dla użytkownika.
3. Sprawdź [sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md) reguły zezwala na ruch protokołu SSH.
   * Upewnij się, czy istnieje reguła sieciowej grupy zabezpieczeń, aby zezwolić na ruch protokołu SSH (domyślnie TCP port 22).
   * Nie można użyć przekierowania portu / mapowania bez korzystania z usługi równoważenia obciążenia Azure.
4. Sprawdź [kondycja zasobów maszyny Wirtualnej](../../resource-health/resource-health-overview.md). 
   * Upewnij się, że maszyna wirtualna raporty jako będące w dobrej kondycji.
   * Jeśli masz włączoną diagnostykę rozruchu, sprawdź, czy maszyna wirtualna nie jest raportowanie błędów rozruchu w dziennikach.
5. Uruchom ponownie maszynę wirtualną.
6. Należy ponownie wdrożyć maszyny Wirtualnej.

Kontynuuj lekturę dla bardziej szczegółowe kroki rozwiązywania problemów oraz objaśnienia.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostępne metody rozwiązywania problemów z połączeniem SSH
Możesz przywrócić poświadczeń lub konfiguracji SSH przy użyciu jednej z następujących metod:

* [Azure portal](#use-the-azure-portal) — jest to doskonały, jeśli chcesz szybko Resetowanie konfiguracji SSH lub klucz SSH i nie być zainstalowane narzędzia Azure.
* [Azure CLI 2.0](#use-the-azure-cli-20) — Jeśli masz już w wierszu polecenia, szybko Resetowanie konfiguracji SSH lub poświadczeń. Można również użyć [Azure CLI w wersji 1.0](#use-the-azure-cli-10)
* [Azure rozszerzenia VMAccessForLinux](#use-the-vmaccess-extension) — tworzenie i ponowne użycie plików definicji json można zresetować konfiguracji lub poświadczenia SSH.

Każdy krok rozwiązywania problemów a następnie spróbuj ponownie nawiązać połączenie z maszyną Wirtualną. Jeśli nadal nie można połączyć się, przejdź do następnego kroku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Azure portal umożliwia szybkie można zresetować konfiguracji lub poświadczenia SSH bez instalowania narzędzi na komputerze lokalnym.

Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół do **pomocy technicznej i rozwiązywania problemów** a następnie wybierz opcję **resetowania hasła** jak w poniższym przykładzie:

![Resetowanie konfiguracji SSH lub poświadczeń w portalu Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Zresetuj konfigurację protokołu SSH
Pierwszym krokiem, wybierz `Reset configuration only` z **tryb** menu rozwijane w poprzednim zrzucie ekranu, następnie kliknij przycisk **zresetować** przycisku. Po zakończeniu tej akcji, spróbuj ponownie uzyskać dostęp z maszyną Wirtualną.

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
Do zresetowania poświadczeń istniejącego użytkownika, wybierz `Reset SSH public key` lub `Reset password` z **tryb** menu rozwijanego, jak poprzedni zrzut ekranu. Określ nazwę użytkownika i klucz SSH lub nowe hasło, a następnie kliknij przycisk **zresetować** przycisku.

Można również utworzyć użytkownika z uprawnieniami sudo na Maszynie wirtualnej z tego menu. Wprowadź nową nazwę użytkownika i skojarzone hasła lub klucza SSH, a następnie kliknij przycisk **zresetować** przycisku.

### <a name="check-security-rules"></a>Sprawdź zasady zabezpieczeń

Użyj [Sprawdź przepływ IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) aby potwierdzić, czy reguły do grupy zabezpieczeń sieci blokuje ruch do i z maszyny wirtualnej. Można również przejrzeć efektywnym elementem systemu zabezpieczeń zasady grupy w celu zapewnienia ruchu przychodzącego "Zezwalaj" NSG reguły istnieje i jest priorytety dla portu SSH (wartość domyślna 22). Aby uzyskać więcej informacji, zobacz [przepływu ruchu reguły zabezpieczeń efektywne używanie rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Sprawdź routing

Użyj obserwatora sieciowego [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) możliwości, aby upewnić się, czy trasa nie jest uniemożliwia ruchu z rozsyłane do lub z maszyny wirtualnej. Można również przejrzeć skuteczne trasy, aby zobaczyć wszystkie skuteczne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [przepływu ruchu trasy efektywne korzystanie rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-cli-20"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure 2.0
Jeśli nie jest jeszcze, zainstaluj najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login).

Jeśli utworzono i przekazać niestandardowego obrazu dysku dla systemu Linux, upewnij się, [agenta usługi Microsoft Azure Linux](../extensions/agent-windows.md) wersji 2.0.5 lub nowszy jest zainstalowany. Dla maszyn wirtualnych utworzonych przy użyciu obrazów w galerii to rozszerzenie dostępu jest już zainstalowane i skonfigurowane dla Ciebie.

### <a name="reset-ssh-configuration"></a>Zresetuj konfigurację protokołu SSH
Możesz początkowo spróbuj zresetowanie konfiguracji SSH do wartości domyślnych i ponowny rozruch serwera SSH na maszynie Wirtualnej. Należy pamiętać, że nie ma to wpływu nazwę konta użytkownika, hasła lub kluczy SSH.
W poniższym przykładzie użyto [resetowania użytkownika maszyny wirtualnej az-ssh](/cli/azure/vm/user#az_vm_user_reset_ssh) można zresetować konfiguracji SSH na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
W poniższym przykładzie użyto [aktualizację użytkownika maszyny wirtualnej az](/cli/azure/vm/user#az_vm_user_update) do zresetowania poświadczeń `myUsername` wartość określoną w `myPassword`, na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Jeśli przy użyciu uwierzytelniania klucza SSH, można zresetować klucz SSH dla danego użytkownika. W poniższym przykładzie użyto **wirtualna az dostępu set-linux-user** można zaktualizować klucza SSH, przechowywane w `~/.ssh/id_rsa.pub` dla użytkownika o nazwie `myUsername`, na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Użyj rozszerzenia VMAccess
Odczytuje rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux w pliku json, który definiuje akcje do wykonania. Dostępne są akcje Resetowanie SSHD, resetowanie klucza SSH lub dodanie użytkownika. Nadal używać interfejsu wiersza polecenia Azure do wywoływania rozszerzenia VMAccess, ale można użyć ponownie pliki w formacie json między wieloma maszynami wirtualnymi w razie potrzeby. Takie podejście umożliwia tworzenie repozytorium pliki w formacie json, które następnie można wywołać dla danego scenariuszy.

### <a name="reset-sshd"></a>Resetuj SSHD
Utwórz plik o nazwie `settings.json` o następującej treści:

```json
{  
    "reset_ssh":"True"
}
```

Przy użyciu wiersza polecenia platformy Azure, możesz wywoływać `VMAccessForLinux` rozszerzenia do resetowania połączenia SSHD przez określenie pliku json. W poniższym przykładzie użyto [zestaw rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension#az_vm_extension_set) zresetować SSHD na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
Jeśli SSHD wydaje się działać prawidłowo, można zresetować poświadczeń użytkownika giver. Aby zresetować hasło dla użytkownika, Utwórz plik o nazwie `settings.json`. Poniższy przykład powoduje zresetowanie poświadczeń `myUsername` wartość określoną w `myPassword`. Wprowadź następujące wiersze do Twojej `settings.json` plików przy użyciu własnych wartości:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Lub aby zresetować klucz SSH dla użytkownika, najpierw utwórz plik o nazwie `settings.json`. Poniższy przykład powoduje zresetowanie poświadczeń `myUsername` wartość określoną w `myPassword`, na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Wprowadź następujące wiersze do Twojej `settings.json` plików przy użyciu własnych wartości:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po utworzeniu pliku json, należy użyć wiersza polecenia platformy Azure do wywołania `VMAccessForLinux` rozszerzenia, aby zresetować swoje poświadczenia użytkownika SSH, określając pliku json. Poniższy przykład powoduje zresetowanie poświadczeń na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Użyj Azure CLI 1.0
Jeśli nie jest jeszcze, [zainstalować 1.0 interfejsu wiersza polecenia platformy Azure i nawiązać połączenia z subskrypcją platformy Azure](../../cli-install-nodejs.md). Upewnij się, że używasz tryb usługi Resource Manager w następujący sposób:

```azurecli
azure config mode arm
```

Jeśli utworzono i przekazać niestandardowego obrazu dysku dla systemu Linux, upewnij się, [agenta usługi Microsoft Azure Linux](../extensions/agent-windows.md) wersji 2.0.5 lub nowszy jest zainstalowany. Dla maszyn wirtualnych utworzonych przy użyciu obrazów w galerii to rozszerzenie dostępu jest już zainstalowane i skonfigurowane dla Ciebie.

### <a name="reset-ssh-configuration"></a>Zresetuj konfigurację protokołu SSH
Konfiguracja SSHD sam może być niepoprawnie skonfigurowany lub Usługa napotkała błąd. Możesz resetować SSHD do upewnij się, że sama konfiguracji SSH jest prawidłowy. Resetowanie SSHD powinien być pierwszym krokiem rozwiązywania problemów, które należy wykonać.

Poniższy przykład resetuje SSHD na maszynie Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj nazwy grupy własną maszyny Wirtualnej i zasobów w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Resetowanie poświadczeń SSH dla użytkownika
Jeśli SSHD wydaje się działać prawidłowo, można zresetować hasło użytkownika giver. Poniższy przykład powoduje zresetowanie poświadczeń `myUsername` wartość określoną w `myPassword`, na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Jeśli przy użyciu uwierzytelniania klucza SSH, można zresetować klucz SSH dla danego użytkownika. Poniższy przykład aktualizuje klucz SSH, przechowywane w `~/.ssh/id_rsa.pub` dla użytkownika o nazwie `myUsername`, na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Ponowne uruchamianie maszyny wirtualnej
Jeśli masz zresetowania konfiguracji i użytkownika poświadczeń SSH, lub wystąpił błąd w ten sposób, można spróbować uruchomić maszyny Wirtualnej na adres bazowy problemów obliczeń.

### <a name="azure-portal"></a>Azure Portal
Aby ponownie uruchomić Maszynę wirtualną przy użyciu portalu Azure, wybierz maszyny Wirtualnej, a następnie kliknij przycisk **ponowne uruchomienie** przycisk jak w poniższym przykładzie:

![Uruchom ponownie Maszynę wirtualną w portalu Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0
Poniższy przykład ponowne uruchomienie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
W poniższym przykładzie użyto [ponownego uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_restart) ponownego uruchomienia maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Ponowne wdrażanie maszyny wirtualnej
Można wdrożyć ponownie Maszynę wirtualną do innego węzła w obrębie platformy Azure, która może rozwiązać problemy z siecią podstawowej. Informacje dotyczące ponownego wdrażania maszyny Wirtualnej, zobacz [ponownego wdrażania maszyny wirtualnej do nowego węzła Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Po zakończeniu tej operacji, tymczasowych dane zostaną utracone i zostaną zaktualizowane dynamicznych adresów IP, które są skojarzone z maszyną wirtualną.
> 
> 

### <a name="azure-portal"></a>Azure Portal
Można wdrożyć ponownie Maszynę wirtualną przy użyciu portalu Azure, wybierz maszyny Wirtualnej, a następnie przewiń w dół do **pomocy technicznej i rozwiązywania problemów** sekcji. Kliknij przycisk **ponownie wdrożyć** przycisk jak w poniższym przykładzie:

![Należy ponownie wdrożyć Maszynę wirtualną w portalu Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0
Poniższy przykład wdraża ponownie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Następujące przykładowe zastosowanie [ponownego wdrażania maszyny wirtualnej az](/cli/azure/vm#az_vm_redeploy) można wdrożyć ponownie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własne wartości w następujący sposób:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrożenia
Spróbuj wykonać następujące kroki, aby rozwiązać najbardziej typowe błędy połączenia SSH dla maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Po wykonaniu każdego kroku spróbuj połączyć się ponownie do maszyny Wirtualnej.

* Zresetuj dostęp zdalny z [portalu Azure](https://portal.azure.com). W portalu Azure, wybierz maszyny Wirtualnej, a następnie kliknij przycisk **zresetować zdalnego...**  przycisku.
* Uruchom ponownie maszynę wirtualną. Na [portalu Azure](https://portal.azure.com)wybierz maszyny Wirtualnej i kliknij **ponowne uruchomienie** przycisku.
    
* Należy ponownie wdrożyć maszynę Wirtualną do nowego węzła platformy Azure. Aby uzyskać informacje o tym, jak można wdrożyć ponownie Maszynę wirtualną, zobacz [ponownego wdrażania maszyny wirtualnej do nowego węzła Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Po zakończeniu tej operacji, tymczasowych dane zostaną utracone i zostaną zaktualizowane dynamicznych adresów IP, które są skojarzone z maszyną wirtualną.
* Postępuj zgodnie z instrukcjami [sposób resetowania hasła lub SSH dla maszyn wirtualnych z systemem Linux](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) do:
  
  * Resetowanie hasła lub klucza SSH.
  * Utwórz *sudo* konta użytkownika.
  * Zresetuj konfigurację protokołu SSH.
* Sprawdź kondycję zasobu maszyny Wirtualnej dla problemów z platformą.<br>
     Wybierz maszyny Wirtualnej, a następnie przewiń w dół **ustawienia** > **kondycji Sprawdź**.

## <a name="additional-resources"></a>Zasoby dodatkowe
* Jeśli nadal nie możesz SSH do maszyny Wirtualnej po wykonaniu czynności po, zobacz [bardziej szczegółowe kroki rozwiązywania problemów](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby przejrzeć dodatkowe kroki w celu rozwiązania problemu.
* Aby uzyskać więcej informacji dotyczących rozwiązywania problemów z dostęp do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji działających na maszynie wirtualnej platformy Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania, zobacz [sposób resetowania hasła lub SSH dla maszyn wirtualnych z systemem Linux](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

