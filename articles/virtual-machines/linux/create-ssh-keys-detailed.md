---
title: Szczegółowe kroki tworzenia pary kluczy SSH
description: Zapoznaj się ze szczegółowymi instrukcjami tworzenia pary kluczy publicznych i prywatnych SSH dla maszyn wirtualnych z systemem Linux na platformie Azure i zarządzania nimi.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969537"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Szczegółowe kroki: Tworzenie kluczy SSH i zarządzanie nimi w celu uwierzytelniania na maszynie Wirtualnej z systemem Linux na platformie Azure 
Za pomocą pary kluczy bezpiecznej powłoki (SSH) można utworzyć maszynę wirtualną systemu Linux na platformie Azure, która domyślnie używa kluczy SSH do uwierzytelniania, eliminując konieczność logowania się haseł. Maszyny wirtualne utworzone za pomocą witryny Azure portal, interfejsu wiersza polecenia platformy Azure, szablonów usługi Resource Manager lub innych narzędzi mogą zawierać klucz publiczny SSH w ramach wdrożenia, które konfiguruje uwierzytelnianie klucza SSH dla połączeń SSH. 

Ten artykuł zawiera szczegółowe informacje i kroki tworzenia pary plików klucza publiczno-prywatnego SSH RSA dla połączeń klientów SSH i zarządzania nimi. Jeśli chcesz szybkie polecenia, zobacz [Jak utworzyć parę kluczy publiczno-prywatnych SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md).

Aby uzyskać dodatkowe sposoby generowania i używania kluczy SSH na komputerze z systemem Windows, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Hasło klucza prywatnego
Klucz prywatny SSH powinien mieć bardzo bezpieczne hasło, aby go chronić. To hasło jest po prostu dostęp do prywatnego pliku klucza SSH i *nie jest* hasło konta użytkownika. Dodanie hasła do klucza SSH spowoduje zaszyfrowanie klucza prywatnego przy użyciu 128-bitowego standardu AES, co oznacza, że klucz prywatny jest bezużyteczny bez hasła do jego odszyfrowania. Jeśli osoba atakująca ukradła klucz prywatny, a ten klucz nie miał hasła, będzie mógł użyć tego klucza prywatnego do zalogowania się na dowolnych serwerach, które mają odpowiedni klucz publiczny. Jeśli klucz prywatny jest chroniony przez hasło, nie może być używany przez tę osobę atakującą, zapewniając dodatkową warstwę zabezpieczeń infrastruktury na platformie Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Korzystanie z kluczy SSH i ich zalety

Podczas tworzenia maszyny Wirtualnej platformy Azure przez określenie klucza publicznego, `.pub` platforma Azure `~/.ssh/authorized_keys` kopiuje klucz publiczny (w formacie) do folderu na maszynie Wirtualnej. Klucze SSH w `~/.ssh/authorized_keys` są używane do kwestionowania klienta, aby dopasować odpowiedni klucz prywatny w połączeniu SSH. W maszynie wirtualnej systemu Azure z systemem Linux, która używa kluczy SSH do uwierzytelniania, platforma Azure konfiguruje serwer SSHD, aby nie zezwalał na logowanie hasłem, tylko klucze SSH. W związku z tym tworząc maszynę wirtualną systemu Azure Linux z kluczami SSH, można pomóc zabezpieczyć wdrożenie maszyny Wirtualnej i zapisać sobie typowy krok konfiguracji po wdrożeniu wyłączania haseł w `sshd_config` pliku.

Jeśli nie chcesz używać kluczy SSH, możesz skonfigurować maszynę wirtualną z systemem Linux do używania uwierzytelniania hasłem. Jeśli maszyna wirtualna nie jest narażona na działanie Internetu, użycie haseł może być wystarczające. Jednak nadal trzeba zarządzać hasłami dla każdej maszyny Wirtualnej z systemem Linux i zachować zasady i praktyki dotyczące zdrowych haseł, takie jak minimalna długość hasła i regularne aktualizacje. Korzystanie z kluczy SSH zmniejsza złożoność zarządzania poszczególnymi poświadczeniami na wielu maszynach wirtualnych.

## <a name="generate-keys-with-ssh-keygen"></a>Generowanie kluczy za pomocą ssh-keygen

Aby utworzyć klucze, preferowanym poleceniem jest `ssh-keygen`, które jest dostępne z narzędziami OpenSSH w usłudze Azure Cloud Shell, hostem macOS lub Linux, [podsystemem Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/about)i innymi narzędziami. `ssh-keygen`zadaje serię pytań, a następnie zapisuje klucz prywatny i pasujący klucz publiczny. 

Klucze SSH są domyślnie przechowywane w katalogu `~/.ssh`.  Jeśli nie masz katalogu `~/.ssh`, polecenie `ssh-keygen` tworzy ten katalog z odpowiednimi uprawnieniami.

### <a name="basic-example"></a>Przykład podstawowy

Następujące `ssh-keygen` polecenie domyślnie generuje 2048-bitowe pliki kluczy publicznych i `~/.ssh` prywatnych SSH RSA w katalogu. Jeśli para kluczy SSH istnieje w bieżącej lokalizacji, pliki te są zastępowane.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Szczegółowy przykład
W poniższym przykładzie przedstawiono dodatkowe opcje poleceń służące do utworzenia pary kluczy RSA SSH. Jeśli para kluczy SSH istnieje w bieżącej lokalizacji, pliki te są zastępowane. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Opis polecenia**

`ssh-keygen` — program używany do tworzenia kluczy

`-m PEM`= sformatować klucz jako PEM

`-t rsa`= typ klucza do utworzenia, w tym przypadku w formacie RSA

`-b 4096`= liczba bitów w kluczu, w tym przypadku 4096

`-C "azureuser@myserver"` — komentarz dodany na końcu pliku klucza publicznego, aby umożliwić jego łatwą identyfikację. Zwykle adres e-mail jest używany jako komentarz, ale użyj tego, co działa najlepiej dla Twojej infrastruktury.

`-f ~/.ssh/mykeys/myprivatekey`= nazwa pliku klucza prywatnego, jeśli nie chcesz używać nazwy domyślnej. Odpowiedni plik klucza publicznego `.pub` dołączany do tego samego katalogu jest generowany. Ten katalog musi istnieć.

`-N mypassphrase`= dodatkowe hasło używane do uzyskiwania dostępu do pliku klucza prywatnego. 

### <a name="example-of-ssh-keygen"></a>Przykład polecenia ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Zapisane pliki kluczy

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nazwa pary kluczy dla potrzeb tego artykułu. Posiadanie pary kluczy o nazwie `id_rsa` jest ustawieniem domyślnym; niektóre narzędzia mogą `id_rsa` oczekiwać nazwy pliku klucza prywatnego, więc posiadanie jednego z nich jest dobrym pomysłem. Katalog `~/.ssh/` jest domyślną lokalizacją par kluczy SSH oraz pliku konfiguracyjnego SSH. Jeśli nie określono pełnej ścieżki, polecenie `ssh-keygen` spowoduje utworzenie kluczy w bieżącym katalogu roboczym, a nie domyślnym katalogu `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista `~/.ssh` katalogu

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Hasło klawiszy

`Enter passphrase (empty for no passphrase):`

*Zdecydowanie* zaleca się dodanie hasła do klucza prywatnego. Bez hasła do ochrony pliku klucza, każdy z pliku można go używać do logowania się na dowolnym serwerze, który ma odpowiedni klucz publiczny. Dodanie hasła zapewnia większą ochronę w przypadku, gdy ktoś jest w stanie uzyskać dostęp do pliku klucza prywatnego, co daje czas na zmianę kluczy.

## <a name="generate-keys-automatically-during-deployment"></a>Automatyczne generowanie kluczy podczas wdrażania

Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) do utworzenia maszyny Wirtualnej, `--generate-ssh-keys` można opcjonalnie wygenerować pliki kluczy publicznych i prywatnych SSH, uruchamiając polecenie [az vm create](/cli/azure/vm) z opcją. Klucze są przechowywane w katalogu ~/.ssh. Należy zauważyć, że ta opcja polecenia nie zastępuje kluczy, jeśli już istnieją w tej lokalizacji.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny Wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal, interfejsu wiersza polecenia, szablonów Menedżera zasobów lub innych metod. Podczas korzystania z portalu należy wprowadzić sam klucz publiczny. Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) do utworzenia maszyny Wirtualnej z istniejącym kluczem publicznym, określ wartość lub lokalizację tego klucza publicznego, uruchamiając polecenie [az vm create](/cli/azure/vm) z `--ssh-key-value` opcją. 

Jeśli nie znasz formatu klucza publicznego SSH, możesz zobaczyć klucz `cat` publiczny, `~/.ssh/id_rsa.pub` uruchamiając go w następujący sposób, zastępując własną lokalizacją pliku klucza publicznego:

```bash
cat ~/.ssh/id_rsa.pub
```

Dane wyjściowe są podobne do następujących (tutaj redacted):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Jeśli skopiujesz i wkleisz zawartość pliku klucza publicznego do witryny Azure portal lub szablonu Menedżera zasobów, upewnij się, że nie kopiujesz żadnych dodatkowych odstępów ani nie wprowadzasz dodatkowych podziałów wierszy. Na przykład, jeśli używasz systemu macOS, można potok `~/.ssh/id_rsa.pub`pliku klucza publicznego (domyślnie), do **pbcopy** skopiować zawartość `xclip`(istnieją inne programy Linux, które robią to samo, takich jak ).

Jeśli wolisz używać klucza publicznego, który jest w formacie wielowierszowym, można wygenerować klucz sformatowany RFC4716 w kontenerze pem z klucza publicznego, który został wcześniej utworzony.

Aby utworzyć klucz w formacie RFC4716 przy użyciu istniejącego publicznego klucza SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH do maszyny Wirtualnej z klientem SSH
Po wdrożeniu klucza publicznego na maszynie wirtualnej platformy Azure i klucza prywatnego w systemie lokalnym ssh do maszyny wirtualnej przy użyciu adresu IP lub nazwy DNS maszyny Wirtualnej. Zastąp *użytkownika platformy Azure* i *myvm.westus.cloudapp.azure.com* w następującym poleceniu nazwą użytkownika administratora i w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy podano hasło, wprowadź hasło po wyświetleniu monitu podczas procesu logowania. (Serwer zostanie dodany do folderu `~/.ssh/known_hosts` i monit o ponowne połączenie nie zostanie wyświetlony, dopóki klucz publiczny na maszynie wirtualnej nie ulegnie zmianie lub nazwa serwera nie zostanie usunięta z folderu `~/.ssh/known_hosts`).

Jeśli maszyna wirtualna używa zasad dostępu just-in-time, należy zażądać dostępu, zanim będzie można połączyć się z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [Zarządzanie dostępem do maszyny wirtualnej przy użyciu zasad just in time](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Użyj ssh-agent do przechowywania hasła klucza prywatnego

Aby uniknąć wpisywania hasła pliku klucza prywatnego przy każdym logowaniu `ssh-agent` SSH, można użyć do buforowania hasła pliku klucza prywatnego. Jeśli używasz komputera Mac, pęk kluczy systemu macOS bezpiecznie przechowuje `ssh-agent`hasło klucza prywatnego podczas wywoływania .

Sprawdź i `ssh-agent` `ssh-add` używaj oraz aby poinformować system SSH o plikach kluczy, aby nie trzeba było używać hasła interaktywnie.

```bash
eval "$(ssh-agent -s)"
```

Następnie dodaj klucz prywatny do programu `ssh-agent` przy użyciu polecenia `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Hasło klucza prywatnego jest teraz `ssh-agent`przechowywane w pliku .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Kopiowanie klucza do istniejącej maszyny Wirtualnej za pomocą identyfikatora ssh-copy-id
Jeśli maszyna wirtualna została już utworzona, można zainstalować nowy klucz publiczny SSH na maszynie wirtualnej w systemie Linux za pomocą polecenia podobnego do następującego:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Tworzenie i konfigurowanie pliku konfiguracyjnego SSH

Można utworzyć i skonfigurować plik konfiguracyjny SSH (`~/.ssh/config`) w celu przyspieszenia logowania i optymalizacji zachowania klienta SSH. 

W poniższym przykładzie przedstawiono prostą konfigurację, której można użyć do szybkiego zalogowania się jako użytkownik do określonej maszyny Wirtualnej przy użyciu domyślnego klucza prywatnego SSH. 

### <a name="create-the-file"></a>Utwórz plik

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edytowanie pliku w celu dodania nowej konfiguracji SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Przykładowa konfiguracja

Dodaj ustawienia konfiguracji odpowiednie dla maszyny Wirtualnej hosta.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Można dodać konfiguracje dla dodatkowych hostów, aby umożliwić każdemu z nich użycie własnej dedykowanej pary kluczy. Zobacz [plik konfiguracyjny SSH, aby](https://www.ssh.com/ssh/config/) uzyskać bardziej zaawansowane opcje konfiguracji.

Teraz, gdy masz parę kluczy SSH i skonfigurowany plik konfiguracyjny SSH, możesz zalogować się do maszyny Wirtualnej z systemem Linux szybko i bezpiecznie. Po uruchomieniu następującego polecenia SSH lokalizuje i ładuje wszelkie ustawienia z `Host myvm` bloku w pliku konfiguracyjnym SSH.

```bash
ssh myvm
```

Przy pierwszym logowanie się do serwera przy użyciu klucza SSH polecenie monituje o hasło dla tego pliku klucza.

## <a name="next-steps"></a>Następne kroki

W dalszej kolejności należy utworzyć maszyny wirtualne systemu Linux platformy Azure przy użyciu nowego klucza publicznego SSH. Maszyny wirtualne platformy Azure, które są tworzone przy pomocą klucza publicznego SSH jako logowania są lepiej zabezpieczone niż maszyny wirtualne utworzone przy za pomocą domyślnej metody logowania, hasła.

* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
