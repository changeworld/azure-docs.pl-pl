---
title: Szybki start — tworzenie maszyny wirtualnej z systemem Linux w witrynie Azure Portal | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux w witrynie Azure Portal
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 8/16/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 189d6d0030264590986d6fe2af47d35705cfb08b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575817"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Szybki start: Utwórz maszynę wirtualną z systemem Linux w Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą witryny Azure Portal wdrożyć maszynę wirtualną z system Linux (Ubuntu 16.04 LTS). Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu SSH i zainstalujesz serwer internetowy NGINX.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Otwórz powłokę Bash i użyj polecenia [ssh-keygen](https://www.ssh.com/ssh/keygen/), aby utworzyć parę kluczy SSH. Przykładowe polecenie do wykonania jest wymienione poniżej. Jeśli nie masz powłoki Bash na swoim komputerze lokalnym, możesz użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).

```bash
ssh-keygen -t rsa -b 2048
```

Zostanie wyświetlony monit o wprowadzenie pliku, w którym ma zostać zapisana para kluczy. Możesz wprowadzić konkretną lokalizację pliku lub po prostu "Enter", aby zapisać w domyślnej lokalizacji wskazanej w nawiasach. Następnie zostanie wyświetlony monit o wprowadzenie hasła. Możesz wprowadzić hasło do klucza SSH lub można nacisnąć klawisz ENTER, aby kontynuować bez hasła.

```bash
[root@linuxvm ~]$ ssh-keygen -t rsa -b 2048
Enter the file in which to save the key (home/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your public key has been saved in /home/root/.ssh/id_rsa.pub.
The key fingerprint is: SHA256:kkQS13gbaxevy4ULH0mW6wLIkcFm0twx/rIlSo1fIqU
The key's randomart image is:
+---[RSA 2018]----+
|   +oo=+         |
|  . B=o.+ .      |
|   + o+. + +     |
|    o* o+ = .    |
|   .EoB.S+ =     |
|   .o+.O. * .    |
|    . o. = =     |
|        . *      |
|         .       |
+----[SHA256]-----+
```
Polecenie generuje klucze publiczne i prywatne z domyślną `id_rsa` nazwą w `~/.ssh directory`. `ssh-keygen` Polecenie zwraca pełną ścieżkę do klucza publicznego. Użyj ścieżki do klucza publicznego, aby wyświetlić jego zawartość za pomocą polecenia `cat`.

```bash
cat ~/.ssh/id_rsa.pub
```

>[!NOTE]
> W przypadku wybrania opcji zapisania klucza SSH w innej lokalizacji niż domyślna, konieczne będzie użycie tej lokalizacji podczas uruchamiania`cat`

Zapisz dane wyjściowe tego polecenia. Jest to Twój klucz publiczny, który będzie potrzebny podczas konfigurowania konta administratora, aby zalogować się do maszyny wirtualnej.

Aby uzyskać więcej informacji na temat polecenia ssh-keygen, odwiedź [stronę Man (mężczyzna](https://linux.die.net/man/1/ssh-keygen)).

Jeśli używasz komputera z systemem Windows i chcesz uzyskać więcej informacji na temat tworzenia par kluczy SSH, w tym korzystania z tego polecenia, zobacz [jak używać kluczy SSH w systemie Windows](ssh-from-windows.md).

Jeśli tworzysz parę kluczy SSH przy użyciu usługi Cloud Shell, będzie ona przechowywana w udziale plików platformy Azure, który jest [automatycznie instalowany za pomocą usługi Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Nie usuwaj tego udziału plików ani konta magazynu przed pobraniem kluczy, ponieważ utracisz dostęp do maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.

1. W polu wyszukiwania powyżej listy zasobów portalu Azure Marketplace Wyszukaj ciąg **Ubuntu Server 18,04** i wybierz ofertę Ubuntu 18,04 LTS, a następnie wybierz pozycję **Utwórz**.

1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** w obszarze **Grupa zasobów**. W wyskakującym okienku wpisz wartość *MyResourceGroup* w polu nazwy grupy zasobów, a następnie wybierz przycisk **OK**.

    ![Tworzenie nowej grupy zasobów dla maszyny wirtualnej](./media/quick-create-portal/project-details.png)

1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Region**. Inne wartości pozostaw domyślne.

    ![Sekcja Szczegóły wystąpienia](./media/quick-create-portal/instance-details.png)

1. W obszarze **konto administratora**wybierz opcję **klucz publiczny SSH**, wpisz swoją nazwę użytkownika, a następnie wklej swój klucz publiczny, który został wcześniej zapisany w polu tekstowym. Usuń wszystkie wiodące i końcowe białe znaki z klucza publicznego.

    ![Konto administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze **Reguły portów wejściowych** > **Publiczne porty wejściowe** wybierz opcję **Zezwalaj na wybranych portach**, a następnie wybierz z listy rozwijanej pozycje **SSH (22)** i **HTTP (80)** .

    ![Otwieranie portów dla protokołów RDP i HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.

1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.


## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Utwórz połączenie SSH z maszyną wirtualną.

1. Na stronie przeglądu wybierz przycisk **Połącz** dla swojej maszyny wirtualnej.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** pozostaw opcje domyślne, aby nawiązać połączenie za pomocą adresu IP na porcie 22. W obszarze **Logowanie za pomocą konta lokalnego maszyny wirtualnej** jest wyświetlane polecenie połączenia. Kliknij przycisk, aby skopiować polecenie. W poniższym przykładzie pokazano, jak wygląda polecenie połączenia SSH:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Korzystając z tej samej powłoki bash, która została użyta do utworzenia pary kluczy SSH (na przykład [Azure Cloud Shell](https://shell.azure.com/bash) lub lokalnej powłoki bash), Wklej polecenie połączenia SSH do powłoki, aby utworzyć sesję SSH.

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy NGINX. Z poziomu sesji SSH zaktualizuj źródła pakietu, a następnie zainstaluj najnowszą wersję pakietu NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Gdy skończysz, wpisz polecenie `exit`, aby opuścić sesję SSH.


## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Użyj wybranej przeglądarki internetowej, aby wyświetlić domyślną strona powitalną serwera NGINX. Wprowadź publiczny adres IP maszyny wirtualnej jako adres internetowy. Publiczny adres IP można znaleźć na stronie przeglądu maszyny wirtualnej lub jako część wcześniej użytych parametrów połączenia SSH.

![Domyślna witryna serwera NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, utworzono sieciową grupę zabezpieczeń i regułę oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)
