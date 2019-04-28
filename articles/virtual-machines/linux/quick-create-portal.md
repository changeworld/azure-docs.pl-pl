---
title: Szybki start — tworzenie maszyny wirtualnej z systemem Linux w witrynie Azure Portal | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux w witrynie Azure Portal
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/12/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 693f9144d1cb454b0a9dd98b5ae63938abd7d26d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473850"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Szybki start: Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą witryny Azure Portal wdrożyć maszynę wirtualną z system Linux (Ubuntu 16.04 LTS). Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu SSH i zainstalujesz serwer internetowy NGINX.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Otwórz powłokę Bash i użyj polecenia [ssh-keygen](https://www.ssh.com/ssh/keygen/), aby utworzyć parę kluczy SSH. Jeśli nie masz powłoki Bash na swoim komputerze lokalnym, możesz użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).  

```bash
ssh-keygen -t rsa -b 2048
```

Powyższe polecenie spowoduje wygenerowanie kluczy publicznych i prywatnych przy użyciu domyślnej nazwy `id_rsa` w katalogu `~/.ssh directory`. Polecenie zwraca pełną ścieżkę do klucza publicznego. Użyj ścieżki do klucza publicznego, aby wyświetlić jego zawartość za pomocą polecenia `cat`.

```bash 
cat ~/.ssh/id_rsa.pub
```

Zapisz dane wyjściowe tego polecenia. Będą one potrzebne podczas konfigurowania konta administratora w celu zalogowania się do maszyny wirtualnej.

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia par kluczy SSH, łącznie z użyciem programu PuTTy, zobacz [Jak używać kluczy SSH w systemie Windows](ssh-from-windows.md).

Jeśli tworzysz parę kluczy SSH przy użyciu usługi Cloud Shell, będzie ona przechowywana w udziale plików platformy Azure, który jest [automatycznie instalowany za pomocą usługi Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Nie usuwaj tego udziału plików ani konta magazynu przed pobraniem kluczy, ponieważ utracisz dostęp do maszyny wirtualnej. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.

1. W polu wyszukiwania nad listą zasobów Microsoft Azure Marketplace wpisz **Ubuntu Server 16.04 LTS by Canonical**, wybierz odpowiednią pozycję i kliknij **Utwórz**.

1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** w obszarze **Grupa zasobów**. W wyskakującym okienku wpisz wartość *MyResourceGroup* w polu nazwy grupy zasobów, a następnie wybierz przycisk **OK**. 

    ![Tworzenie nowej grupy zasobów dla maszyny wirtualnej](./media/quick-create-portal/project-details.png)

1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Region**. Inne wartości pozostaw domyślne.

    ![Sekcja Szczegóły wystąpienia](./media/quick-create-portal/instance-details.png)

1. W obszarze **Konto administratora** wybierz opcję **klucz publiczny SSH**, wpisz nazwę użytkownika, a następnie wklej swój klucz publiczny w polu tekstowym. Usuń wszystkie wiodące i końcowe białe znaki z klucza publicznego.

    ![Konto administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze **Reguły portów wejściowych** > **Publiczne porty wejściowe** wybierz opcję **Zezwalaj na wybranych portach**, a następnie wybierz z listy rozwijanej pozycje **SSH (22)** i **HTTP (80)**. 

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

3. Korzystając z tej samej powłoki Bash, która została użyta do utworzenia pary kluczy SSH, takiej jak usługa [Azure Cloud Shell](https://shell.azure.com/bash) lub lokalna powłoka Bash, wklej polecenie połączenia SSH w powłoce, aby utworzyć sesję SSH. 

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

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, utworzono sieciową grupę zabezpieczeń i regułę oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)
