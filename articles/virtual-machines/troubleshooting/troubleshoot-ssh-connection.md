---
title: Rozwiązywanie problemów z połączeniem SSH z maszyną wirtualną platformy Azure | Dokumenty firmy Microsoft
description: Jak rozwiązać problemy, takie jak "Połączenie SSH nie powiodło się" lub "Odmowa połączenia SSH" dla maszyny Wirtualnej Platformy Azure z systemem Linux.
keywords: połączenie ssh odmówił, błąd ssh, azure ssh, połączenie SSH nie powiodło się
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060660"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Troubleshoot SSH connections to an Azure Linux VM that fails, errors out, or is refused (Rozwiązywanie problemów dotyczących połączeń SSH z maszyną wirtualną z systemem Linux — niepowodzenia, błędy lub odmowa połączenia)
Ten artykuł pomaga znaleźć i rozwiązać problemy, które występują z powodu błędów secure shell (SSH), błędy połączenia SSH lub SSH odmawia podczas próby połączenia z maszyną wirtualną systemu Linux (VM). Aby rozwiązać i rozwiązać problemy z połączeniem, można użyć usługi Azure portal, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure lub rozszerzenia programu VM Access dla systemu Linux.


Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z maszyną wirtualną.

1. [Zresetuj konfigurację SSH](#reset-config).
2. [Zresetuj poświadczenia](#reset-credentials) dla użytkownika.
3. Sprawdź, czy [reguły sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md) zezwalają na ruch SSH.
   * Upewnij się, że istnieje [reguła sieciowej grupy zabezpieczeń](#security-rules) zezwalana na ruch SSH (domyślnie port TCP 22).
   * Nie można użyć przekierowania /mapowania portów bez użycia modułu równoważenia obciążenia platformy Azure.
4. Sprawdź [kondycję zasobu maszyny Wirtualnej](../../resource-health/resource-health-overview.md).
   * Upewnij się, że maszyny Wirtualnej raportów jako w dobrej kondycji.
   * Jeśli [masz włączoną diagnostykę rozruchu,](boot-diagnostics.md)sprawdź, czy maszyna wirtualna nie zgłasza błędów rozruchowych w dziennikach.
5. [Uruchom ponownie maszynę wirtualną](#restart-vm).
6. [Ponowne wdrożenie maszyny Wirtualnej](#redeploy-vm).

Kontynuuj czytanie, aby uzyskać bardziej szczegółowe kroki rozwiązywania problemów i wyjaśnienia.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostępne metody rozwiązywania problemów z połączeniem SSH
Poświadczenia lub konfigurację SSH można zresetować przy użyciu jednej z następujących metod:

* [Portal Azure](#use-the-azure-portal) — świetnie, jeśli chcesz szybko zresetować konfigurację SSH lub klucz SSH i nie masz zainstalowanych narzędzi platformy Azure.
* [Konsola szeregowa maszyny Wirtualnej platformy Azure](https://aka.ms/serialconsolelinux) — konsola szeregowa maszyny Wirtualnej będzie działać niezależnie od konfiguracji SSH i zapewni ci interaktywną konsolę do maszyny Wirtualnej. W rzeczywistości sytuacje "nie mogą SSH" są w szczególności tym, co konsola seryjna została zaprojektowana, aby pomóc rozwiązać. Szczegółowe informacje można znaleźć poniżej.
* [Interfejsu wiersza polecenia platformy Azure](#use-the-azure-cli) — jeśli jesteś już w wierszu polecenia, szybko zresetuj konfigurację lub poświadczenia SSH. Jeśli pracujesz z klasyczną maszyną wirtualną, możesz użyć [klasycznego interfejsu wiersza polecenia platformy Azure.](#use-the-azure-classic-cli)
* [Rozszerzenie usługi Azure VMAccessForLinux](#use-the-vmaccess-extension) — tworzenie i ponowne używanie plików definicji json w celu zresetowania konfiguracji SSH lub poświadczeń użytkownika.

Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z maszyną wirtualną. Jeśli nadal nie możesz się połączyć, spróbuj wykonać następny krok.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Portal Azure zawiera szybki sposób resetowania konfiguracji SSH lub poświadczeń użytkownika bez instalowania żadnych narzędzi na komputerze lokalnym.

Aby rozpocząć, wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół do sekcji **Pomoc techniczna + Rozwiązywanie problemów** i wybierz **pozycję Resetuj hasło,** jak w poniższym przykładzie:

![Resetowanie konfiguracji ssh lub poświadczeń w witrynie Azure portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />Resetowanie konfiguracji SSH
Aby zresetować konfigurację `Reset configuration only` SSH, wybierz w sekcji **Tryb,** jak na poprzednim zrzucie ekranu, a następnie wybierz pozycję **Aktualizuj**. Po zakończeniu tej akcji spróbuj ponownie uzyskać dostęp do maszyny Wirtualnej.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Aby zresetować poświadczenia istniejącego użytkownika, `Reset SSH public key` wybierz `Reset password` jedną lub w sekcji **Tryb,** jak w poprzednim zrzucie ekranu. Określ nazwę użytkownika i klucz SSH lub nowe hasło, a następnie wybierz pozycję **Aktualizuj**.

Można również utworzyć użytkownika z uprawnieniami sudo na maszynie wirtualnej z tego menu. Wprowadź nową nazwę użytkownika i skojarzone hasło lub klucz SSH, a następnie wybierz pozycję **Aktualizuj**.

### <a name="check-security-rules"></a><a id="security-rules" />Sprawdzanie reguł zabezpieczeń

Sprawdź [przepływ ip,](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) aby potwierdzić, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Można również przejrzeć reguły skutecznej grupy zabezpieczeń, aby upewnić się, że przychodząca reguła sieciowej grupy zabezpieczeń istnieje i jest priorytetowa dla portu SSH (domyślnie 22). Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych przy użyciu skutecznych reguł zabezpieczeń](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Sprawdź routing

Użyj funkcji [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) obserwatora sieci, aby potwierdzić, że trasa nie uniemożliwia kierowania ruchu do lub z maszyny wirtualnej. Można również przejrzeć efektywne trasy, aby wyświetlić wszystkie skuteczne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych za pomocą skutecznych tras](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Korzystanie z konsoli szeregowej maszyny Wirtualnej platformy Azure
[Konsola szeregowa maszyny Wirtualnej Platformy Azure](./serial-console-linux.md) zapewnia dostęp do konsoli tekstowej dla maszyn wirtualnych systemu Linux. Za pomocą konsoli można rozwiązywać problemy z połączeniem SSH w interaktywnej powłoce. Upewnij się, że spełniasz [wymagania wstępne dotyczące](./serial-console-linux.md#prerequisites) korzystania z konsoli szeregowej i wypróbuj poniższe polecenia, aby dalej rozwiązywać problemy z łącznością SSH.

### <a name="check-that-ssh-is-running"></a>Sprawdź, czy SSH jest uruchomiony
Aby sprawdzić, czy na maszynie wirtualnej jest uruchomiony następujący komunikat:

```console
ps -aux | grep ssh
```

Jeśli istnieje jakiekolwiek dane wyjściowe, SSH jest uruchomiony.

### <a name="check-which-port-ssh-is-running-on"></a>Sprawdź, który port SSH jest uruchomiony

Za pomocą następującego polecenia można sprawdzić, na którym porcie działa SSH:

```console
sudo grep Port /etc/ssh/sshd_config
```

Twoje dane wyjściowe będą wyglądać mniej więcej tak:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Jeśli jeszcze tego nie zrobiłeś, zainstaluj najnowszą [platformę Cli platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

Jeśli utworzono i udostępnino niestandardowy obraz dysku systemu Linux, upewnij się, że jest zainstalowany [agent Microsoft Azure Linux Agent](../extensions/agent-linux.md) w wersji 2.0.5 lub nowszej. W przypadku maszyn wirtualnych utworzonych przy użyciu obrazów galerii to rozszerzenie dostępu jest już zainstalowane i skonfigurowane.

### <a name="reset-ssh-configuration"></a>Resetowanie konfiguracji SSH
Początkowo można spróbować zresetować konfigurację SSH do wartości domyślnych i ponownie uruchomić serwer SSH na maszynie wirtualnej. Nie zmienia to nazwy konta użytkownika, hasła ani kluczy SSH.
W poniższym przykładzie użyto [resetowania przez użytkownika az vm,](/cli/azure/vm/user) `myVM` aby `myResourceGroup`zresetować konfigurację SSH na maszynie wirtualnej o nazwie w programie . Użyj własnych wartości w następujący sposób:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
W poniższym przykładzie użyto [aktualizacji użytkownika az vm,](/cli/azure/vm/user) `myPassword`aby zresetować poświadczenia `myVM` `myResourceGroup` `myUsername` do wartości określonej w programie , na maszynie Wirtualnej o nazwie w programie . Użyj własnych wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

W przypadku korzystania z uwierzytelniania za pomocą klucza SSH można zresetować klucz SSH dla danego użytkownika. W poniższym przykładzie użyto **az vm access set-linux-user** do zaktualizowania klucza SSH przechowywanego `~/.ssh/id_rsa.pub` dla użytkownika o nazwie `myUsername`, na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Korzystanie z rozszerzenia VMAccess
Rozszerzenie dostępu do maszyn wirtualnych dla systemu Linux odczytuje w pliku json, który definiuje akcje do przeprowadzenia. Akcje te obejmują resetowanie dysków SSHD, resetowanie klucza SSH lub dodawanie użytkownika. Nadal używasz interfejsu wiersza polecenia platformy Azure do wywołania rozszerzenia VMAccess, ale w razie potrzeby można ponownie użyć plików json na wielu maszynach wirtualnych. Takie podejście umożliwia utworzenie repozytorium plików json, które następnie można wywołać dla danego scenariusza.

### <a name="reset-sshd"></a>Resetowanie dysków SSHD
Utwórz plik `settings.json` o nazwie o następującej zawartości:

```json
{
    "reset_ssh":"True"
}
```

Za pomocą interfejsu wiersza `VMAccessForLinux` polecenia platformy Azure, następnie wywołać rozszerzenie, aby zresetować połączenie SSHD, określając plik json. W poniższym przykładzie użyto [zestawu rozszerzeń az vm,](/cli/azure/vm/extension) aby zresetować dysk SSHD na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Jeśli SSHD wydaje się działać poprawnie, można zresetować poświadczenia dla użytkownika daję. Aby zresetować hasło użytkownika, utwórz `settings.json`plik o nazwie . Poniższy przykład resetuje poświadczenia `myUsername` do wartości `myPassword`określonej w . Wprowadź następujące wiersze `settings.json` do pliku, używając własnych wartości:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Lub aby zresetować klucz SSH dla użytkownika, `settings.json`najpierw utwórz plik o nazwie . Poniższy przykład resetuje poświadczenia `myUsername` do wartości `myPassword`określonej w programie `myVM` `myResourceGroup`Maszyny Wirtualnej o nazwie w programie . Wprowadź następujące wiersze `settings.json` do pliku, używając własnych wartości:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po utworzeniu pliku json użyj interfejsu wiersza polecenia platformy Azure, aby wywołać `VMAccessForLinux` rozszerzenie, aby zresetować poświadczenia użytkownika SSH, określając plik json. Poniższy przykład resetuje poświadczenia na `myVM` maszynie wirtualnej o nazwie w `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure
Jeśli jeszcze tego nie zrobiłeś, [zainstaluj klasyczny interfejs wiersza polecenia platformy Azure i połącz się z subskrypcją platformy Azure.](../../cli-install-nodejs.md) Upewnij się, że w trybie Menedżera zasobów jest używany tryb:

```azurecli
azure config mode arm
```

Jeśli utworzono i udostępnino niestandardowy obraz dysku systemu Linux, upewnij się, że jest zainstalowany [agent Microsoft Azure Linux Agent](../extensions/agent-linux.md) w wersji 2.0.5 lub nowszej. W przypadku maszyn wirtualnych utworzonych przy użyciu obrazów galerii to rozszerzenie dostępu jest już zainstalowane i skonfigurowane.

### <a name="reset-ssh-configuration"></a>Resetowanie konfiguracji SSH
Sama konfiguracja SSHD może zostać błędnie skonfigurowana lub usługa napotkała błąd. Można zresetować dysk SSHD, aby upewnić się, że sama konfiguracja SSH jest prawidłowa. Resetowanie dysków SSHD powinno być pierwszym krokiem rozwiązywania problemów.

Poniższy przykład resetuje dysk SSHD `myVM` na maszynie `myResourceGroup`wirtualnej o nazwie w grupie zasobów o nazwie . Użyj własnych nazw maszyn wirtualnych i grup zasobów w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Jeśli dysk SSHD wydaje się działać poprawnie, można zresetować hasło dla użytkownika daję. Poniższy przykład resetuje poświadczenia `myUsername` do wartości `myPassword`określonej w programie `myVM` `myResourceGroup`Maszyny Wirtualnej o nazwie w programie . Użyj własnych wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

W przypadku korzystania z uwierzytelniania za pomocą klucza SSH można zresetować klucz SSH dla danego użytkownika. Poniższy przykład aktualizuje klucz `~/.ssh/id_rsa.pub` SSH przechowywany `myUsername`dla użytkownika o `myVM` `myResourceGroup`nazwie , na maszynie Wirtualnej o nazwie w . Użyj własnych wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Ponowne uruchamianie maszyny wirtualnej
Jeśli zresetowano konfigurację SSH i poświadczenia użytkownika lub wystąpił błąd w ten sposób, możesz spróbować ponownie uruchomić maszynę wirtualną, aby rozwiązać podstawowe problemy obliczeniowe.

### <a name="azure-portal"></a>Portal Azure
Aby ponownie uruchomić maszynę wirtualną przy użyciu witryny Azure portal, wybierz maszynę wirtualną, a następnie wybierz **pozycję Uruchom ponownie,** jak w poniższym przykładzie:

![Ponowne uruchamianie maszyny wirtualnej w witrynie Azure portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W poniższym przykładzie użyto [ponownego uruchomienia az vm,](/cli/azure/vm) aby ponownie uruchomić maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Poniższy przykład ponownie uruchamia `myVM` maszynę wirtualną `myResourceGroup`o nazwie w grupie zasobów o nazwie . Użyj własnych wartości w następujący sposób:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Ponowne wdrożenie maszyny Wirtualnej
Można ponownie wdrożyć maszynę wirtualną do innego węzła na platformie Azure, co może rozwiązać wszelkie podstawowe problemy z siecią. Aby uzyskać informacje dotyczące ponownego wdrożenia maszyny wirtualnej, zobacz [Ponowne wdrożenie maszyny wirtualnej do nowego węzła platformy Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Po zakończeniu tej operacji dane dysku efemerycznego zostaną utracone, a dynamiczne adresy IP skojarzone z maszyną wirtualną są aktualizowane.
>
>

### <a name="azure-portal"></a>Portal Azure
Aby ponownie wdrożyć maszynę wirtualną przy użyciu witryny Azure portal, wybierz maszynę wirtualną i przewiń w dół do sekcji **Pomoc techniczna + rozwiązywanie problemów.** Wybierz **pozycję Ponownie rozmieszczaj,** jak w poniższym przykładzie:

![Ponowne wdrożenie maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W [poniższym przykładzie użyto az vm ponownego rozmieszczenia,](/cli/azure/vm) aby ponownie wdrożyć maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

W poniższym przykładzie ponownie wcieli się maszyna wirtualna o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`. Użyj własnych wartości w następujący sposób:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Wypróbuj te kroki, aby rozwiązać najczęstsze błędy połączenia SSH dla maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Po każdym kroku spróbuj ponownie połączyć się z maszyną wirtualną.

* Resetowanie dostępu zdalnego z [witryny Azure portal](https://portal.azure.com). W witrynie Azure portal wybierz maszynę wirtualną, a następnie wybierz pozycję **Resetuj zdalne...**.
* Uruchom ponownie maszynę wirtualną. W [witrynie Azure portal](https://portal.azure.com)wybierz maszynę wirtualną i wybierz pozycję **Uruchom ponownie**.

* Ponowne wdrożenie maszyny Wirtualnej do nowego węzła platformy Azure. Aby uzyskać informacje dotyczące ponownego rozmieszczenia maszyny wirtualnej, zobacz [Ponowne wdrożenie maszyny wirtualnej do nowego węzła platformy Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Po zakończeniu tej operacji dane dysku efemerycznego zostaną utracone, a dynamiczne adresy IP skojarzone z maszyną wirtualną zostaną zaktualizowane.
* Postępuj zgodnie z instrukcjami w [jak zresetować hasło lub SSH dla maszyn wirtualnych opartych na systemie Linux,](../linux/classic/reset-access-classic.md) aby:

  * Zresetuj hasło lub klucz SSH.
  * Utwórz konto użytkownika *sudo.*
  * Zresetuj konfigurację SSH.
* Sprawdź kondycję zasobów maszyny Wirtualnej pod kątem problemów z platformą.<br>
     Wybierz maszynę wirtualną i przewiń w dół Sprawdź**kondycję** **ustawienia** > .

## <a name="additional-resources"></a>Zasoby dodatkowe
* Jeśli nadal nie można SSH do maszyny Wirtualnej po wykonać następujące kroki, zobacz [bardziej szczegółowe kroki rozwiązywania problemów,](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aby przejrzeć dodatkowe kroki, aby rozwiązać problem.
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z dostępem do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z maszynami wirtualnymi utworzonymi przy użyciu klasycznego modelu wdrażania, zobacz [Jak zresetować hasło lub SSH dla maszyn wirtualnych opartych na systemie Linux](../linux/classic/reset-access-classic.md).
