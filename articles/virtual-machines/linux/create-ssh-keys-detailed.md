---
title: Szczegółowy opis kroków - SSH pary kluczy dla maszyn wirtualnych systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, szczegółowy opis kroków, aby utworzyć i zarządzać publicznymi i prywatnymi pary kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: gwallace
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
ms.openlocfilehash: 1859cdfaead27fda1956b553ebea06374c9cdc6a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668079"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Szczegółowy opis kroków: Tworzenie i zarządzanie nimi kluczy SSH do uwierzytelniania do maszyny Wirtualnej z systemem Linux na platformie Azure 
Przy użyciu protokołu secure shell (SSH) pary kluczy można utworzyć maszynę wirtualną z systemem Linux na platformie Azure, która domyślnie korzysta z kluczy SSH do uwierzytelniania, eliminując konieczność łączenia się haseł do logowania. Szablony maszyn wirtualnych utworzone w witrynie Azure portal, interfejsu wiersza polecenia platformy Azure Resource Manager lub innych narzędzi może zawierać klucz publiczny SSH w ramach wdrażania, która konfiguruje uwierzytelnianie klucza SSH na potrzeby połączeń SSH. 

Ten artykuł zawiera szczegółowe tła i kroki, aby tworzyć i zarządzać nimi parę plików kluczy publiczny prywatny SSH RSA dla połączeń klienta SSH. Szybkie polecenia, zobacz [sposób tworzenia pary kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux na platformie Azure](mac-create-ssh-keys.md).

Aby poznać dodatkowe sposoby generowania i używania kluczy SSH na komputerze Windows, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Hasło klucza prywatnego
Prywatny klucz SSH powinien być bardzo bezpieczne hasło, aby chronić je. To hasło służy wyłącznie do do dostępu do pliku klucza prywatnego SSH i *nie* hasło konta użytkownika. Po dodaniu hasła do Twojego klucza SSH koduje klucza prywatnego za pomocą szyfrowania AES 128-bitowego, tak, aby klucz prywatny jest bezużyteczne, jeśli hasło do odszyfrowania. Jeśli osoba atakująca udałoby klucz prywatny tego klucza nie ma hasła, będzie mogła użyć tego klucza, aby zalogować się do żadnych serwerów, które mają odpowiadający mu klucz publiczny. Jeśli klucz prywatny jest chroniony hasłem, nie można użyć, a osoba niepowołana zapewnia dodatkową warstwę ochronną dla infrastruktury na platformie Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Korzystanie z kluczy SSH i ich zalety

Po utworzeniu maszyny Wirtualnej platformy Azure, określając klucz publiczny kopiowany klucza publicznego (w `.pub` format) do `~/.ssh/authorized_keys` folder na maszynie Wirtualnej. Klucze SSH w `~/.ssh/authorized_keys` są używane jako wezwania klienta do dopasowania odpowiedniego klucza prywatnego na połączenie SSH. W maszynie Wirtualnej systemu Linux platformy Azure, która używa kluczy SSH do uwierzytelniania platforma Azure konfiguruje serwer SSHD tak, aby nie zezwalaj na hasło logowania, tylko kluczy SSH. W związku z tym, tworząc Maszynę wirtualną systemu Linux platformy Azure przy użyciu kluczy SSH, możesz pomóc zabezpieczenie wdrażania maszyny Wirtualnej i typowego po wdrożeniu kroku konfiguracji wyłączenia haseł w `sshd_config` pliku.

Jeśli nie chcesz używać kluczy SSH, możesz skonfigurować maszyny Wirtualnej systemu Linux, aby używać uwierzytelniania za pomocą hasła. Jeśli maszyna wirtualna nie jest połączone z Internetem, przy użyciu hasła może być wystarczające. Jednakże nadal należy zarządzanie hasłami dla każdej maszyny Wirtualnej systemu Linux i obsługi zasad dotyczących haseł w dobrej kondycji i rozwiązań, takich jak minimalna długość hasła oraz regularne aktualizacje. Używanie kluczy SSH zmniejsza złożoność zarządzania poszczególnych poświadczeń na wielu maszynach wirtualnych.

## <a name="generate-keys-with-ssh-keygen"></a>Generuj klucze za pomocą protokołu ssh-keygen

Do tworzenia kluczy, jest preferowany polecenia `ssh-keygen`, który jest dostępny za pomocą narzędzi usługi Azure Cloud Shell, macOS lub Linux host OpenSSH [podsystem Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/about)i inne narzędzia. `ssh-keygen` najpierw zadaje szereg pytań, a następnie zapisuje klucz prywatny i dopasowany klucz publiczny. 

Klucze SSH są domyślnie przechowywane w katalogu `~/.ssh`.  Jeśli nie masz katalogu `~/.ssh`, polecenie `ssh-keygen` tworzy ten katalog z odpowiednimi uprawnieniami.

### <a name="basic-example"></a>Podstawowy przykład

Następujące `ssh-keygen` polecenie generuje 2048-bitowego SSH RSA pliki publicznych i prywatnych kluczy domyślnie `~/.ssh` katalogu. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Szczegółowy przykład
Poniższy przykład przedstawia opcje dodatkowe polecenia, aby utworzyć parę kluczy RSA protokołu SSH. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione. 

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

`-t rsa` = Typ klucza do utworzenia w tym przypadku w formacie RSA

`-b 4096` Liczba bitów klucza, w tym przypadku 4096

`-C "azureuser@myserver"` — komentarz dodany na końcu pliku klucza publicznego, aby umożliwić jego łatwą identyfikację. Zwykle jako komentarz używany jest adres e-mail, ale użyj sprawdzą się najlepiej w danej infrastrukturze.

`-f ~/.ssh/mykeys/myprivatekey` = Nazwa pliku klucza prywatnego, jeśli nie chcesz użyć nazwy domyślnej. Odpowiedni plik klucza publicznego z dołączonym `.pub` jest generowany w tym samym katalogu. Ten katalog musi istnieć.

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

Nazwa pary kluczy dla potrzeb tego artykułu. Para kluczy o nazwie `id_rsa` domyślny; niektóre narzędzia mogą oczekiwać `id_rsa` nazwę pliku klucza prywatnego, dlatego mających jeden jest dobrym pomysłem. Katalog `~/.ssh/` jest domyślną lokalizacją par kluczy SSH oraz pliku konfiguracyjnego SSH. Jeśli nie określono pełnej ścieżki, polecenie `ssh-keygen` spowoduje utworzenie kluczy w bieżącym katalogu roboczym, a nie domyślnym katalogu `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista `~/.ssh` katalogu

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Hasło klucza

`Enter passphrase (empty for no passphrase):`

Jest *silnie* zalecane jest dodanie hasła do klucza prywatnego. Bez hasła do ochrony plik klucza każda osoba mająca plik może użyć do logowania się na dowolnym serwerze, który ma odpowiadający mu klucz publiczny. Oferuje dodanie hasła zwiększa ochronę w przypadku, gdy ktoś jest w stanie uzyskać dostęp do Twojego pliku klucza prywatnego dając Ci czas na zmiany kluczy.

## <a name="generate-keys-automatically-during-deployment"></a>Automatycznie Generuj klucze podczas wdrażania

Jeśli używasz [wiersza polecenia platformy Azure](/cli/azure) tworzysz maszynę Wirtualną, może opcjonalnie generować SSH pliki publicznych i prywatnych kluczy, uruchamiając [tworzenie az vm](/cli/azure/vm) polecenia `--generate-ssh-keys` opcji. Klucze są przechowywane w katalogu ~/.ssh. Należy pamiętać, że to polecenie nie powoduje zastąpienia klucze Jeśli już istnieją w tej lokalizacji.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH w przypadku wdrażania maszyny Wirtualnej

Aby utworzyć maszynę Wirtualną systemu Linux, która używa kluczy SSH do uwierzytelniania, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal, interfejsu wiersza polecenia szablonów usługi Resource Manager lub innej metody. Korzystając z portalu, możesz wprowadzić sam klucz publiczny. Jeśli używasz [wiersza polecenia platformy Azure](/cli/azure) tworzysz maszynę Wirtualną przy użyciu istniejącego publicznego klucza, określ wartość lub lokalizację tego klucza publicznego, uruchamiając [tworzenie az vm](/cli/azure/vm) polecenia `--ssh-key-value` opcji. 

Jeśli nie jesteś zaznajomiony z formatem klucza publicznego SSH, możesz wyświetlić swój klucz publiczny, uruchamiając `cat` następująco, zastępując `~/.ssh/id_rsa.pub` z tej lokalizacji pliku klucza publicznego:

```bash
cat ~/.ssh/id_rsa.pub
```

Dane wyjściowe będą podobne do następujących (tutaj zredagowanego):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Jeśli skopiujesz i wkleisz zawartość pliku klucza publicznego w witrynie Azure portal lub szablonu usługi Resource Manager, upewnij się, nie kopiować dodatkowych spacji lub wprowadzać dodatkowe podziały wierszy. Na przykład, jeśli używasz systemu macOS, można przekazać pliku klucza publicznego (domyślnie `~/.ssh/id_rsa.pub`) do **pbcopy** skopiować zawartość (istnieją inne programy dla systemu Linux, które obsługują to samo, takich jak `xclip`).

Jeśli wolisz użyć klucza publicznego, który jest w formacie wielowierszowym, można wygenerować klucza sformatowane RFC4716 w kontenerze pem z klucza publicznego, który został wcześniej utworzony.

Aby utworzyć klucz w formacie RFC4716 przy użyciu istniejącego publicznego klucza SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH z maszyną Wirtualną przy użyciu klienta SSH
Za pomocą klucza publicznego, wdrożone na maszynie Wirtualnej platformy Azure, a klucz prywatny w systemie lokalnym SSH z maszyną Wirtualną przy użyciu adresu IP lub nazwę DNS maszyny wirtualnej. Zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* w następującym poleceniu przy użyciu podanej nazwy użytkownika administratora i w pełni kwalifikowaną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy podano hasło, wprowadź hasło po wyświetleniu monitu podczas procesu logowania. (Serwer zostanie dodany do folderu `~/.ssh/known_hosts` i monit o ponowne połączenie nie zostanie wyświetlony, dopóki klucz publiczny na maszynie wirtualnej nie ulegnie zmianie lub nazwa serwera nie zostanie usunięta z folderu `~/.ssh/known_hosts`).

Jeśli maszyna wirtualna używa zasad dostępu just in time, należy zażądać dostępu, aby nawiązać połączenie z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [zarządzanie dostępem maszyny wirtualnej przy użyciu tylko w zasadach czasu](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Umożliwia przechowywanie hasło prywatnego klucza ssh-agent

Aby uniknąć wpisywania hasło pliku klucza prywatnego za pomocą każdego logowania SSH, możesz użyć `ssh-agent` w pamięci podręcznej hasło pliku klucza prywatnego. Jeśli używasz komputera Mac, Pęk kluczy systemu macOS x bezpiecznie przechowa Twoje hasło klucza prywatnego po wywołaniu `ssh-agent`.

Sprawdzenia i użyć `ssh-agent` i `ssh-add` w celu poinformowania systemu SSH o plikach kluczy aby nie trzeba używać hasła interaktywnie.

```bash
eval "$(ssh-agent -s)"
```

Następnie dodaj klucz prywatny do programu `ssh-agent` przy użyciu polecenia `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Hasło klucza prywatnego jest teraz przechowywany w `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Użyj ssh kopiowania id, aby skopiuj klucz z istniejącej maszyny Wirtualnej
Jeśli już utworzono Maszynę wirtualną, można zainstalować nowy publiczny klucz SSH dla maszyny Wirtualnej systemu Linux przy użyciu polecenia podobnego do następującego:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Tworzenie i konfigurowanie pliku konfiguracyjnego SSH

Można tworzyć i konfigurować pliku konfiguracyjnego SSH (`~/.ssh/config`) do przyspieszenia dziennika dodatków i optymalizacji zachowania klienta SSH. 

Poniższy przykład pokazuje prostej konfiguracji, umożliwia szybkie Zaloguj się jako użytkownika do określonej maszyny Wirtualnej przy użyciu domyślnego klucza prywatnego SSH. 

### <a name="create-the-file"></a>Utwórz plik

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edytuj plik, aby dodać nową konfigurację SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Przykładowa konfiguracja

Dodaj odpowiednie dla maszyny Wirtualnej hosta, ustawienia konfiguracji.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Można dodać konfiguracje dla dodatkowych hostów, aby włączyć do użycia własnej pary kluczy. Zobacz [pliku konfiguracyjnego SSH](https://www.ssh.com/ssh/config/) dla bardziej zaawansowanych opcji konfiguracji.

Teraz, gdy masz pary kluczy SSH i skonfigurowany plik konfiguracji SSH, możesz się zalogować się do maszyny Wirtualnej systemu Linux, szybko i bezpiecznie. Po uruchomieniu następującego polecenia SSH lokalizuje i ładuje wszystkie ustawienia z `Host myvm` blok w pliku konfiguracyjnego SSH.

```bash
ssh myvm
```

Podczas pierwszego logowania się do serwera przy użyciu klucza SSH polecenie monituje możesz o wpisanie hasła do pliku danego klucza.

## <a name="next-steps"></a>Następne kroki

W dalszej kolejności należy utworzyć maszyny wirtualne systemu Linux platformy Azure przy użyciu nowego klucza publicznego SSH. Maszyny wirtualne platformy Azure, które zostały utworzone z publicznym kluczem SSH jako identyfikator logowania, są lepiej chronione niż maszyny wirtualne utworzone przy użyciu domyślnego logowania metody hasła.

* [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny Wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
