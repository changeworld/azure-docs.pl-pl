---
title: Szczegółowe instrukcje dotyczące tworzenia pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Poznaj dodatkowe czynności dotyczące tworzenia pary publicznych i prywatnych kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure wraz z określonymi certyfikatami dla różnych przypadków zastosowań.
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
ms.date: 6/28/2017
ms.author: danlep
ms.openlocfilehash: 20d36f5e377f2d5af588319cee2be1808571f905
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Szczegółowy przewodnik tworzenia pary kluczy SSH oraz dodatkowe certyfikaty dla maszyn wirtualnych z systemem Linux na platformie Azure
Para kluczy SSH umożliwia tworzenie na platformie Azure maszyn wirtualnych, które domyślnie używają kluczy SSH do uwierzytelniania, eliminując konieczność logowania przy użyciu haseł. Hasła można złamać i narażają one maszyny wirtualne na bezlitosne próby ataków siłowych w celu odgadnięcia hasła. Maszyny wirtualne tworzone za pomocą interfejsu wiersza polecenia platformy Azure lub szablonów usługi Resource Manager mogą mieć dołączony publiczny klucz SSH w ramach wdrożenia, co eliminuje potrzebę wykonania po wdrożeniu kroku konfiguracji polegającego na wyłączeniu logowania przy użyciu haseł dla protokołu SSH. Ten artykuł zawiera szczegółowe kroki i dodatkowe przykłady generowania certyfikatów, takich jak do użycia z maszyn wirtualnych systemu Linux. Jeśli chcesz szybko utworzyć parę prywatnych i publicznych kluczy SSH i używać ich, zobacz [Jak utworzyć parę kluczy publicznych i prywatnych dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Informacje o kluczach SSH

Publiczne i prywatne klucze SSH umożliwiają logowanie do serwerów z systemem Linux w najłatwiejszy sposób. [Kryptografia klucza publicznego](https://en.wikipedia.org/wiki/Public-key_cryptography) zapewnia znacznie bezpieczniejsze logowanie do maszyn wirtualnych z systemem Linux lub BSD na platformie Azure niż hasła, które mogą być dużo łatwiej złamane za pomocą ataków siłowych.

Klucz publiczny można udostępniać wszystkim, ale tylko Ty (lub lokalna infrastruktura zabezpieczeń) posiadasz klucz prywatny.  Prywatny klucz SSH powinien być zabezpieczony [bardzo bezpiecznym hasłem](https://www.xkcd.com/936/) (źródło:[xkcd.com](https://xkcd.com)).  To hasło służy wyłącznie do uzyskiwania dostępu do pliku prywatnego klucza SSH i **nie jest** hasłem do konta użytkownika.  Dodanie hasła do klucza SSH spowoduje zaszyfrowanie klucza prywatnego przy użyciu 128-bitowego standardu AES, aby odszyfrowanie i użycie klucza nie było możliwe bez podania hasła.  Jeżeli osobie niepowołanej udałoby się wykraść Twój klucz prywatny, który nie został zabezpieczony hasłem, osoba ta mogłaby użyć tego klucza w celu zalogowania się na dowolne serwery z odpowiadającym mu kluczem publicznym.  Zabezpieczenie klucza prywatnego hasłem zapewnia dodatkową warstwę ochronną dla infrastruktury na platformie Azure, a osoba niepowołana nie może go użyć.

W tym artykule utworzono parę plików prywatnych i publicznych kluczy RSA protokołu SSH w wersji 2 (zwanych także kluczami „shh-rsa”), które są zalecane w przypadku wdrożeń z usługą Azure Resource Manager. Klucze *ssh-rsa* są wymagane w [portalu](https://portal.azure.com) w przypadku zarówno wdrożeń klasycznych, jak i wdrożeń przy użyciu usługi Resource Manager.

## <a name="ssh-keys-use-and-benefits"></a>Korzystanie z kluczy SSH i ich zalety

Platforma Azure wymaga co najmniej 2048-bitowych publicznych i prywatnych kluczy RSA protokołu SSH w wersji 2, przy czym format kontenera pliku klucza publicznego to `.pub`. Aby utworzyć klucze, należy użyć funkcji `ssh-keygen`, która najpierw zadaje szereg pytań, a następnie zapisuje klucz prywatny i dopasowany klucz publiczny. Po utworzeniu maszyny wirtualnej platformy Azure klucz publiczny jest kopiowany do folderu `~/.ssh/authorized_keys` na maszynie wirtualnej. Klucze SSH w katalogu `~/.ssh/authorized_keys` są używane jako wezwania klienta do dopasowania odpowiedniego klucza prywatnego w celu zalogowania za pomocą połączenia przy użyciu protokołu SSH.  W przypadku tworzenia maszyny wirtualnej platformy Azure z systemem Linux i możliwością uwierzytelniania za pomocą kluczy SSH platforma Azure konfiguruje serwer SSHD tak, aby zezwalał na użycie tylko kluczy SSH, a nie na logowanie przy użyciu hasła.  W związku z tym utworzenie maszyny wirtualnej platformy Azure z systemem Linux przy użyciu kluczy SSH może ułatwić zabezpieczenie wdrożenia maszyny wirtualnej i nie trzeba będzie wtedy wykonywać typowego kroku konfiguracji po wdrażaniu, czyli wyłączenia haseł w pliku **sshd_config**.

## <a name="using-ssh-keygen"></a>Korzystanie z programu ssh-keygen

To polecenie umożliwia utworzenie zabezpieczonej (zaszyfrowanej) pary kluczy SSH o długości 2048 bitów w standardzie RSA. W celu dokonania łatwej identyfikacji zostanie ona opatrzona komentarzem.  

Klucze SSH są domyślnie przechowywane w katalogu `~/.ssh`.  Jeśli nie masz katalogu `~/.ssh`, polecenie `ssh-keygen` tworzy ten katalog z odpowiednimi uprawnieniami.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*Opis polecenia*

`ssh-keygen` — program używany do tworzenia kluczy

`-t rsa` = Typ klucza do utworzenia, która jest [formacie RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) 
 `-b 2048` = liczba bitów klucza

`-C "azureuser@myserver"` — komentarz dodany na końcu pliku klucza publicznego, aby umożliwić jego łatwą identyfikację.  Zwykle jako komentarz używany jest adres e-mail, ale można też użyć innych informacji, które sprawdzą się najlepiej w danej infrastrukturze.

## <a name="classic-deploy-using-asm"></a>Wdrażanie klasyczne przy użyciu programu `asm`

Jeśli używasz klasycznym modelu wdrażania (`asm` tryb interfejsu wiersza polecenia), można użyć klucza publicznego SSH-RSA lub sformatowany RFC4716 klucza w kontenerze pem.  Klucz publiczny SSH-RSA utworzono wcześniej w tym artykule przy użyciu polecenia `ssh-keygen`.

Aby utworzyć klucz w formacie RFC4716 przy użyciu istniejącego publicznego klucza SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Przykład polecenia ssh-keygen

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

Zapisane pliki klucza:

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nazwa pary kluczy dla potrzeb tego artykułu.  Para kluczy o nazwie **id_rsa** jest domyślna. Niektóre narzędzia mogą wymagać podania nazwy **id_rsa** pliku klucza prywatnego, dlatego zaleca się jego utworzenie. Katalog `~/.ssh/` jest domyślną lokalizacją par kluczy SSH oraz pliku konfiguracyjnego SSH.  Jeśli nie określono pełnej ścieżki, polecenie `ssh-keygen` spowoduje utworzenie kluczy w bieżącym katalogu roboczym, a nie domyślnym katalogu `~/.ssh`.

Lista plików w katalogu `~/.ssh`.

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Hasło klucza:

`Enter passphrase (empty for no passphrase):`

Polecenie `ssh-keygen` odnosi się do hasła pliku klucza prywatnego.  *Zdecydowanie* zalecane jest dodanie hasła do klucza prywatnego. Bez hasła chroniącego plik klucza każda osoba mająca ten plik może użyć go do logowania na dowolnym serwerze, który ma odpowiadający mu klucz publiczny. Dodanie hasła zwiększa ochronę w przypadku, gdy osoba niepowołana jest w stanie uzyskać dostęp do Twojego pliku klucza prywatnego. Dzięki temu zabezpieczeniu masz czas na dokonanie zmiany kluczy używanych do uwierzytelnienia.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Korzystanie z programu ssh-agent do przechowywania hasła klucza prywatnego

Aby uniknąć wpisywania hasła do pliku klucza prywatnego przy każdym logowaniu przez protokół SSH, można skorzystać z programu `ssh-agent`, który przechowuje hasło w pamięci podręcznej. Jeśli korzystasz z komputera Mac, pęk kluczy systemu OS X bezpiecznie przechowa Twoje hasła do klucza prywatnego po wywołaniu programu `ssh-agent`.

Zweryfikuj program ssh-agent oraz ssh-add i użyj ich w celu poinformowania systemu SSH o plikach kluczy, aby nie trzeba było używać hasła interaktywnie.

```bash
eval "$(ssh-agent -s)"
```

Następnie dodaj klucz prywatny do programu `ssh-agent` przy użyciu polecenia `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Hasło klucza prywatnego jest teraz przechowywane w programie `ssh-agent`.

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Kopiowanie klucza do istniejącej maszyny wirtualnej przy użyciu polecenia `ssh-copy-id`
Jeśli już utworzono maszynę wirtualną, można zainstalować nowy publiczny klucz SSH na maszynie wirtualnej z systemem Linux przy użyciu polecenia:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Tworzenie i konfigurowanie pliku konfiguracyjnego SSH

Zalecanym najlepszym rozwiązaniem jest utworzenie i skonfigurowanie pliku `~/.ssh/config` w celu przyspieszenia logowania i optymalizacji zachowania klienta SSH.

W poniższym przykładzie pokazano standardową konfigurację. 

### <a name="create-the-file"></a>Utwórz plik

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edytuj plik, aby dodać nową konfigurację SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Przykład pliku `~/.ssh/config`:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Ta konfiguracja protokołu SSH udostępnia sekcje dla każdego serwera, aby umożliwić każdemu z nich posiadanie własnej pary kluczy. Ustawienia domyślne (`Host *`) są stosowane do hostów, które nie są zgodne z żadnym z określonych hostów we wcześniejszej części pliku konfiguracyjnego.

### <a name="config-file-explained"></a>Opis pliku konfiguracji

`Host` — nazwa hosta wywołanego na terminalu.  Polecenie `ssh fedora22` informuje protokół `SSH`, aby użyć wartości z bloku ustawień z etykietą `Host fedora22`. UWAGA: hostem może być jakakolwiek etykieta zrozumiała dla Ciebie, która jednocześnie nie jest faktyczną nazwą hosta żadnego serwera.

`Hostname 102.160.203.241` — adres IP lub nazwa DNS serwera, do którego uzyskiwany jest dostęp.

`User ahmet` — konto użytkownika zdalnego używane podczas logowania do serwera.

`PubKeyAuthentication yes` — informuje protokół SSH, że chcesz użyć klucza SSH do logowania.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` — prywatny klucz SSH i odpowiedni klucz publiczny, który ma zostać użyty do uwierzytelniania.

## <a name="ssh-into-linux-without-a-password"></a>Korzystanie z protokołu SSH w systemie Linux bez użycia hasła

Jeśli masz już parę kluczy SSH i skonfigurowany plik konfiguracji SSH, możesz szybko i bezpiecznie zalogować się do maszyny wirtualnej systemu Linux. Podczas pierwszego logowania do serwera przy użyciu klucza SSH polecenie monituje o wpisanie hasła do pliku danego klucza.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Opis polecenia

Gdy polecenie `ssh fedora22` jest wykonywane, protokół SSH najpierw lokalizuje i ładuje wszystkie ustawienia z bloku `Host fedora22`, a następnie ładuje wszystkie pozostałe ustawienia z ostatniego bloku `Host *`.

## <a name="next-steps"></a>Następne kroki

W dalszej kolejności należy utworzyć maszyny wirtualne systemu Linux platformy Azure przy użyciu nowego klucza publicznego SSH.  Maszyny wirtualne platformy Azure, które zostały utworzone z publicznym kluczem SSH jako loginem, są lepiej chronione niż maszyny wirtualne utworzone z hasłami stosowanymi w domyślnej metodzie logowania.  W domyślnej konfiguracji maszyn wirtualnych platformy Azure utworzonych przy użyciu kluczy SSH hasła są wyłączone, aby uniknąć prób odgadnięcia hasła za pomocą ataków siłowych.

* [Tworzenie bezpiecznej maszyny wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie bezpiecznej maszyny wirtualnej systemu Linux przy użyciu witryny Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie bezpiecznej maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
