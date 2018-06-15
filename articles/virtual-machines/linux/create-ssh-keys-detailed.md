---
title: Szczegółowy opis kroków - SSH parę kluczy dla maszyn wirtualnych systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się szczegółowy opis kroków, aby utworzyć i zarządzać SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31600346"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Szczegółowe kroki: tworzenie i zarządzanie nimi kluczy SSH do uwierzytelniania Maszynę wirtualną systemu Linux na platformie Azure 
Przy użyciu pary kluczy bezpiecznej powłoki (SSH) można utworzyć maszyny wirtualnej systemu Linux na platformie Azure, który domyślnie przy użyciu kluczy SSH do uwierzytelnienia, eliminując konieczność hasła do logowania. Maszyny wirtualne utworzone za pomocą portalu Azure, interfejsu wiersza polecenia Azure Resource Manager szablonów lub innych narzędzi może zawierać klucz publiczny SSH jako część wdrożenia, który konfiguruje uwierzytelniania klucza SSH dla połączenia SSH. 

Ten artykuł zawiera szczegółowe tła i kroki tworzenia i zarządzania nimi parę pliku klucza publicznego i prywatnego SSH RSA dla połączeń klienta SSH. Szybkie polecenia, zobacz [tworzenie pary kluczy publicznych i prywatnych SSH dla maszyn wirtualnych systemu Linux na platformie Azure](mac-create-ssh-keys.md).

Aby uzyskać dodatkowe sposoby Generowanie i używanie kluczy SSH na komputerze z systemem Windows, zobacz [kluczy sposobu korzystania z protokołu SSH z systemem Windows Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Hasło klucza prywatnego
Klucz prywatny SSH powinna mieć bardzo bezpiecznego hasła w celu zabezpieczenia go. To hasło jest po prostu Aby uzyskać dostęp do pliku klucza prywatnego SSH i *nie jest* hasło do konta użytkownika. Po dodaniu hasło do klucza SSH koduje klucza prywatnego za pomocą 128-bitowego szyfrowania AES, tak, aby klucz prywatny jest bezużyteczny bez hasła do odszyfrowania. Jeśli osoba atakująca kraść klucz prywatny tego klucza nie ma hasła, będzie mógł używać tego klucza prywatnego na logowanie się do żadnych serwerów, które mają odpowiedni klucz publiczny. Jeśli klucz prywatny jest chroniony hasłem, nie można użyć, a osoba niepowołana, zapewniając dodatkową warstwę zabezpieczeń dla infrastruktury na platformie Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Korzystanie z kluczy SSH i ich zalety

Po utworzeniu maszyny Wirtualnej platformy Azure, określając klucz publiczny Azure kopiuje klucza publicznego (w `.pub` format) do `~/.ssh/authorized_keys` folderu na maszynie Wirtualnej. Klucze SSH w katalogu `~/.ssh/authorized_keys` są używane jako wezwania klienta do dopasowania odpowiedniego klucza prywatnego w celu zalogowania za pomocą połączenia przy użyciu protokołu SSH. W Azure Linux maszynę Wirtualną, która używa kluczy SSH do uwierzytelniania Azure pozwala na skonfigurowanie serwera SSHD nie zezwalaj na hasło logowania, tylko kluczy SSH. W związku z tym, tworząc maszyny Wirtualnej systemu Linux platformy Azure z kluczy SSH, możesz pomóc bezpiecznego wdrażania maszyny Wirtualnej i Zapisz sobie krok typowej konfiguracji po wdrożeniu wyłączenia haseł w `sshd_config` pliku.

Jeśli nie chcesz używać kluczy SSH, można skonfigurować sieci maszyny Wirtualnej systemu Linux do uwierzytelniania za pomocą hasła. Jeśli maszyna wirtualna nie jest połączenie z Internetem, przy użyciu hasła mogą być wystarczające. Jednak nadal należy zarządzać hasła dla każdej maszyny Wirtualnej systemu Linux i obsługiwać zasady haseł w dobrej kondycji i rozwiązań, takich jak minimalna długość hasła i regularnego aktualizacje. Przy użyciu kluczy SSH, zmniejsza się złożoność zarządzania poszczególnych poświadczeń między wieloma maszynami wirtualnymi.

## <a name="generate-keys-with-ssh-keygen"></a>Generuj klucze ssh-keygen

Do tworzenia kluczy, jest preferowanym polecenia `ssh-keygen`, który jest dostępny z narzędzia OpenSSH w powłoce chmury Azure, macOS lub hoście z systemem Linux, [podsystemu systemu Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/about)i inne narzędzia. `ssh-keygen` zadaje szereg pytań, a następnie zapisuje klucz prywatny i dopasowany klucz publiczny. 

Klucze SSH są domyślnie przechowywane w katalogu `~/.ssh`.  Jeśli nie masz katalogu `~/.ssh`, polecenie `ssh-keygen` tworzy ten katalog z odpowiednimi uprawnieniami.

### <a name="basic-example"></a>Podstawowy przykład

Następujące `ssh-keygen` polecenie generuje 2048-bitowego SSH RSA publicznego i prywatnego klucza pliki domyślnie w `~/.ssh` katalogu. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Szczegółowy przykład
W poniższym przykładzie przedstawiono polecenie dodatkowe opcje, aby utworzyć parę kluczy SSH RSA. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Opis polecenia**

`ssh-keygen` — program używany do tworzenia kluczy

`-t rsa` = Typ klucza do utworzenia w takim przypadku w formacie RSA

`-b 4096` Liczba bitów w kluczu, w tym przypadku 4096

`-C "azureuser@myserver"` — komentarz dodany na końcu pliku klucza publicznego, aby umożliwić jego łatwą identyfikację. Zwykle jako komentarz używany jest adres e-mail, ale użyj sprawdzą się najlepiej dla infrastruktury.

`-f ~/.ssh/mykeys/myprivatekey` = Nazwa pliku klucza prywatnego, jeśli nie chcesz używać domyślnej nazwy. Dołączany plik odpowiedniego klucza publicznego z `.pub` jest generowany w tym samym katalogu. Ten katalog musi istnieć.

`-N mypassphrase` = dodatkowe hasło używane do dostępu do pliku klucza prywatnego. 

### <a name="example-of-ssh-keygen"></a>Przykład polecenia ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>Zapisane pliki klucza

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nazwa pary kluczy dla potrzeb tego artykułu. Para kluczy o nazwie `id_rsa` domyślny; niektóre narzędzia mogą wymagać `id_rsa` nazwę pliku klucza prywatnego, dobrym pomysłem jest o jeden. Katalog `~/.ssh/` jest domyślną lokalizacją par kluczy SSH oraz pliku konfiguracyjnego SSH. Jeśli nie określono pełnej ścieżki, polecenie `ssh-keygen` spowoduje utworzenie kluczy w bieżącym katalogu roboczym, a nie domyślnym katalogu `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista `~/.ssh` katalogu

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Hasło klucza

`Enter passphrase (empty for no passphrase):`

Jest *silnie* zaleca się dodanie hasła do klucza prywatnego. Bez hasła, aby chronić plik klucza każdy użytkownik z pliku można użyć logować się do dowolnego serwera, który ma odpowiedni klucz publiczny. Dodawanie hasło oferuje lepszą ochronę w przypadku, gdy ktoś jest w stanie uzyskać dostęp do Twojego pliku klucza prywatnego nadanie czasu dokonanie zmiany kluczy.

## <a name="generate-keys-automatically-during-deployment"></a>Generuj klucze automatycznie podczas wdrażania

Jeśli używasz [Azure CLI 2.0](/cli/azure) do utworzenia maszyny Wirtualnej, może opcjonalnie generować SSH publicznego i prywatnego klucza pliki, uruchamiając [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenie z `--generate-ssh-keys` opcji. Klucze są przechowywane w katalogu ~/.ssh. Należy pamiętać, że to polecenie nie powoduje zastąpienia klucze Jeśli już istnieją w tej lokalizacji.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH, podczas wdrażania maszyny Wirtualnej

Aby utworzyć maszynę Wirtualną systemu Linux, która używa kluczy SSH do uwierzytelnienia, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu portalu Azure, interfejsu wiersza polecenia, szablony usługi Resource Manager lub innych metod. Podczas korzystania z portalu, należy wprowadzić samego klucza publicznego. Jeśli używasz [Azure CLI 2.0](/cli/azure) do utworzenia maszyny Wirtualnej z istniejącego klucza publicznego, określ wartość lub lokalizacji tego klucza publicznego, uruchamiając [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) z `--ssh-key-value` opcji. 

Jeśli nie masz doświadczenia w obsłudze format klucz publiczny SSH, można wyświetlić klucz publiczny, uruchamiając `cat` , zastępując `~/.ssh/id_rsa.pub` lokalizację pliku klucza publicznego:

```bash
cat ~/.ssh/id_rsa.pub
```

Dane wyjściowe są podobne do następujących (w tym miejscu zredagowanego):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Jeśli skopiuj i Wklej zawartość pliku klucza publicznego w portalu Azure lub w szablonie usługi Resource Manager, upewnij się, nie skopiować wszystkie dodatkowe odstępem lub wprowadzić dodatkowe znaki końca linii. Na przykład, jeśli używasz macOS, możesz przekazać plik klucza publicznego (domyślnie `~/.ssh/id_rsa.pub`) do **pbcopy** Aby skopiować zawartość (istnieją inne programy systemu Linux, które wykonują tę samą operację, takich jak **xclip**).

Jeśli wolisz korzystać w formacie wielowierszowy klucz publiczny, można wygenerować klucz sformatowany RFC4716 w kontenerze pem z klucza publicznego, który został wcześniej utworzony.

Aby utworzyć klucz w formacie RFC4716 przy użyciu istniejącego publicznego klucza SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH do maszyny Wirtualnej za pomocą klienta SSH
Z kluczem publicznym wdrożone na maszynie Wirtualnej platformy Azure i klucza prywatnego w systemie lokalnym SSH do maszyny Wirtualnej przy użyciu adresu IP lub nazwę DNS maszyny Wirtualnej. Zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* w poniższym poleceniu z nazwą użytkownika administratora i pełną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy podano hasło, wprowadź je, gdy podczas logowania zostanie wyświetlony odpowiedni monit. (Serwer zostanie dodany do folderu `~/.ssh/known_hosts` i monit o ponowne połączenie nie zostanie wyświetlony, dopóki klucz publiczny na maszynie wirtualnej nie ulegnie zmianie lub nazwa serwera nie zostanie usunięta z folderu `~/.ssh/known_hosts`).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Użyj ssh-agent do przechowywania hasła klucza prywatnego

Aby uniknąć wpisywania hasła pliku klucza prywatnego przy każdym logowaniu przez protokół SSH, można użyć `ssh-agent` do pamięci podręcznej hasło pliku klucza prywatnego. Jeśli używasz Mac macOS łańcucha kluczy bezpiecznie przechowuje hasło klucza prywatnego po wywołaniu `ssh-agent`.

Sprawdź i użyj `ssh-agent` i `ssh-add` informowanie o plików kluczy systemu SSH, dzięki czemu nie trzeba używać interakcyjnie hasło.

```bash
eval "$(ssh-agent -s)"
```

Następnie dodaj klucz prywatny do programu `ssh-agent` przy użyciu polecenia `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Hasło klucza prywatnego jest teraz przechowywane w `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Użyj ssh kopiowania id, aby skopiować klucz do istniejącej maszyny Wirtualnej
Jeśli utworzono już maszyny Wirtualnej, można zainstalować nowego klucza publicznego SSH do maszyny Wirtualnej systemu Linux za pomocą polecenia podobny do następującego:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Tworzenie i konfigurowanie pliku konfiguracyjnego SSH

Można tworzyć i konfigurować pliku konfiguracyjnego SSH (`~/.ssh/config`) w celu przyspieszenia dziennika dodatków i zoptymalizować Twoje zachowanie klienta SSH. 

Poniższy przykład przedstawia prostą konfigurację której można szybko zalogować się jako użytkownik do określonej maszyny Wirtualnej przy użyciu klucza prywatnego SSH domyślne. 

### <a name="create-the-file"></a>Utwórz plik

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edytuj plik, aby dodać nową konfigurację SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Przykładowa konfiguracja

Dodaj odpowiednie dla hosta maszyny Wirtualnej, ustawienia konfiguracji.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Można dodać konfiguracje dla dodatkowych hostów umożliwić każdej z nich korzystać z własnej pary kluczy. Zobacz [pliku konfiguracyjnego SSH](https://www.ssh.com/ssh/config/) przypadku bardziej zaawansowanych opcji konfiguracji.

Jeśli masz już parę kluczy SSH i skonfigurowany plik konfiguracji SSH, możesz szybko i bezpiecznie zalogować się do maszyny wirtualnej systemu Linux. Po uruchomieniu poniższego polecenia SSH lokalizuje i ładuje wszystkie ustawienia z `Host myvm` bloku w pliku konfiguracyjnego SSH.

```bash
ssh myvm
```

Podczas pierwszego logowania do serwera przy użyciu klucza SSH polecenie monituje o możesz hasła dla tego pliku klucza.

## <a name="next-steps"></a>Kolejne kroki

W dalszej kolejności należy utworzyć maszyny wirtualne systemu Linux platformy Azure przy użyciu nowego klucza publicznego SSH. Maszyny wirtualne platformy Azure, które zostały utworzone z publicznym kluczem SSH jako loginem, są lepiej chronione niż maszyny wirtualne utworzone z hasłami stosowanymi w domyślnej metodzie logowania.

* [Utwórz maszynę wirtualną systemu Linux przy użyciu portalu Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz maszynę wirtualną systemu Linux z wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz Maszynę wirtualną systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
