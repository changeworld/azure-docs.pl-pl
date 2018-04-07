---
title: Używanie kluczy SSH w systemie Windows dla maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Generowanie i używanie kluczy SSH na komputerze z systemem Windows do nawiązania połączenia maszyny wirtualnej systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: fcc2365c3b41fb69492aa68bf7c48c2d3b8ee5f3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak klucze używanie SSH z usługą Microsoft Azure
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Po podłączeniu do maszyn wirtualnych systemu Linux (VM) na platformie Azure, należy użyć [kryptografii klucza publicznego](https://wikipedia.org/wiki/Public-key_cryptography) bardziej bezpieczny sposób na logowanie się do maszyny Wirtualnej systemu Linux. Ten proces obejmuje wymianę kluczy publicznych i prywatnych za pomocą polecenia bezpiecznej powłoki (SSH) do uwierzytelniania użytkownika, a nie nazwę użytkownika i hasło. Hasła są narażone na ataki, szczególnie w przypadku maszyn wirtualnych połączonych z Internetem, takich jak serwery sieci web siłowych. Ten artykuł zawiera omówienie kluczy SSH i sposób generowania odpowiednich kluczy na komputerze z systemem Windows.

## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołu SSH oraz kluczy
Można bezpiecznie zalogować się do maszyny Wirtualnej systemu Linux przy użyciu kluczy publicznych i prywatnych:

* **Klucz publiczny** znajduje się w sieci maszyny Wirtualnej systemu Linux lub innych usług, które mają być używane z kryptografii klucza publicznego.
* **Klucza prywatnego** jest co przedstawienie do maszyny Wirtualnej systemu Linux podczas logowania, aby zweryfikować swoją tożsamość. Klucz prywatny należy chronić. Nie należy go udostępniać.

Te klucze publiczne i prywatne mogą być używane na wiele maszyn wirtualnych i usług. Nie trzeba pary kluczy dla każdej maszyny Wirtualnej lub usługi, które chcesz uzyskać dostęp. Aby uzyskać bardziej szczegółowe informacje, zobacz [kryptografii klucza publicznego](https://wikipedia.org/wiki/Public-key_cryptography).

SSH jest protokołem zaszyfrowanego połączenia, który umożliwia bezpiecznego logowania za pośrednictwem niezabezpieczonego połączenia. Jest domyślnym protokołem połączenia dla maszyn wirtualnych systemu Linux hostowanych w systemie Azure. SSH sam zapewnia połączenie szyfrowane, jednak za pomocą hasła przy użyciu połączeń SSH nadal pozostawia maszyny Wirtualnej podatne na siłowych ataków lub zgadywania haseł. Jest większe bezpieczeństwo i preferowaną metodę połączenie z maszyną wirtualną przy użyciu protokołu SSH przy użyciu tych kluczy publicznych i prywatnych, znanej także jako kluczy SSH.

Jeśli nie chcesz używać kluczy SSH, można nadal logowania do maszyn wirtualnych systemu Linux przy użyciu hasła. Jeśli maszyna wirtualna nie jest połączenie z Internetem, przy użyciu hasła mogą być wystarczające. Jednak nadal należy zarządzać hasła dla każdej maszyny Wirtualnej systemu Linux i obsługiwać zasady haseł w dobrej kondycji i rozwiązań, takich jak minimalna długość hasła i ich regularne aktualizowanie. Używanie kluczy SSH zmniejsza się złożoność zarządzania poszczególnych poświadczeń między wieloma maszynami wirtualnymi.

## <a name="windows-packages-and-ssh-clients"></a>Pakiety systemu Windows i klientów SSH
Nawiązywanie połączenia i zarządzać maszyn wirtualnych systemu Linux w programie Azure przy użyciu **klienta SSH**. Komputery z systemem Windows nie zazwyczaj jest zainstalowany klient SSH. Aktualizacja dla systemu Windows 10 Anniversary dodawane Bash dla systemu Windows, a najnowszą aktualizację twórców systemu Windows 10 udostępnia dodatkowe aktualizacje. Podsystem ten systemu Windows dla systemu Linux służy do uruchamiania i dostępu do narzędzi, takich jak klient SSH natywnie w powłoki Bash. Można następnie wykonasz którąkolwiek z dokumentów dotyczących systemu Linux, takich jak [jak wygenerować pary kluczy SSH w systemie Linux](mac-create-ssh-keys.md). Bash systemu Windows jest nadal w fazie projektowania i jest uznawany za wydania beta. Aby uzyskać więcej informacji na temat Bash systemu Windows, zobacz [Bash na Ubuntu w systemie Windows](https://msdn.microsoft.com/commandline/wsl/about).

Jeśli chcesz użyć czegoś innego niż Bash dla systemu Windows, typowe Windows SSH klienci które można zainstalować znajdują się w następujących pakietów:

* [Git dla systemu Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Pliki, które klucza musisz utworzyć?
Platforma Azure wymaga co najmniej 2048-bitowego, **ssh-rsa** sformatowany klucze publiczne i prywatne. W przypadku zarządzania zasobami Azure przy użyciu klasycznego modelu wdrożenia, należy wygenerować PEM (`.pem` pliku).

Poniżej przedstawiono scenariusze wdrażania i typy plików, których można używać w każdym:

1. **SSH-rsa** klucze są wymagane do dowolnego wdrożenia przy użyciu [portalu Azure](https://portal.azure.com)i wdrożenia usługi Resource Manager przy użyciu [interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md).
   * Klucze te są zazwyczaj potrzebne wszystkie większości użytkowników.
2. A `.pem` plik jest niezbędny do tworzenia maszyn wirtualnych za pomocą klasycznego. Klucze te są obsługiwane w klasycznym wdrożenia przy użyciu [portalu Azure](https://portal.azure.com) lub [interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md).
   * Należy utworzyć tych dodatkowych kluczy i certyfikatów, jeśli zarządzasz zasoby utworzone przy użyciu klasycznego modelu wdrożenia.

## <a name="install-git-for-windows"></a>Zainstaluj usługę Git dla systemu Windows
Poprzedniej sekcji wymienionych kilka pakietów, które obejmują `openssl` narzędzia dla systemu Windows. To narzędzie jest potrzebne do tworzenia kluczy publicznych i prywatnych. Poniższe przykłady szczegółowe instrukcje dotyczące instalacji i używania **Git dla systemu Windows**, ale można wybrać niezależnie od tego pakietu preferowane. **Git dla systemu Windows** zapewnia dostęp do niektórych dodatkowe oprogramowanie open source ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) narzędzi, które mogą być przydatne podczas pracy z maszyn wirtualnych systemu Linux.

1. Pobierz i zainstaluj **Git dla systemu Windows** z następującej lokalizacji: [ https://git-for-windows.github.io/ ](https://git-for-windows.github.io/).
2. Podczas procesu instalacji należy zaakceptować wartości domyślne, chyba że należy je zmienić.
3. Uruchom **Git Bash** z **Start Menu** > **Git** > **Git Bash**. Konsolę wygląda podobnie do poniższego przykładu:

    ![Git dla Bash systemu Windows shell](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Utwórz klucz prywatny
1. W Twojej **Git Bash** okna, użyj `openssl.exe` można utworzyć klucza prywatnego. W poniższym przykładzie jest tworzony klucz o nazwie `myPrivateKey` i certyfikat o nazwie `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    Dane wyjściowe wygląda podobnie do poniższego przykładu:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Jeśli bash zgłasza błąd, spróbuj otworzyć nowy **Git Bash** okno z podwyższonym poziomem uprawnień. Następnie uruchom ponownie `openssl` polecenia.

2. Odpowiedz monity nazwa kraju, lokalizacji, nazwę organizacji, itp.
3. Twoje nowy klucz prywatny i certyfikat są tworzone w bieżącym katalogu roboczym. Ze względów bezpieczeństwa należy ustawić uprawnienia na klucz prywatny, aby tylko można do niego dostęp:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. [Następnej sekcji](#create-a-private-key-for-putty) szczegóły zarówno wyświetlać i używać klucza publicznego i utworzyć klucz prywatny specyficzne dla przy użyciu programu PuTTY SSH do maszyn wirtualnych systemu Linux przy użyciu PuTTYgen. Polecenie generuje plik klucza publicznego o nazwie `myPublicKey.key` od razu pomocne:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Konwertuj również należy zarządzać zasobami Classic `myCert.pem` do `myCert.cer` (X509 algorytmem DER certyfikatu). Ten opcjonalny krok należy wykonać tylko wtedy, gdy trzeba zarządzać w szczególności starszych zasobów klasycznego.

    Konwertuj certyfikat przy użyciu następującego polecenia:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Utwórz klucz prywatny dla programu PuTTY
PuTTY jest typowe klient SSH dla systemu Windows. Jesteś klienta SSH, który chcesz korzystać. Przy użyciu programu PuTTY, musisz utworzyć dodatkowy typ klucza - prywatnego klucza PuTTY (PPK). Jeśli nie chcesz, aby przy użyciu programu PuTTY, Pomiń tę sekcję.

Poniższy przykład tworzy dodatkowe klucz prywatny specjalnie dla programu PuTTY do użycia:

1. Użyj **Git Bash** do konwertowania klucz prywatny do klucza prywatnego RSA, który można poznać PuTTYgen. W poniższym przykładzie jest tworzony klucz o nazwie `myPrivateKey_rsa` z istniejącego klucza o nazwie `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Ze względów bezpieczeństwa należy ustawić uprawnienia na klucz prywatny, aby tylko można do niego dostęp:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Pobierz i uruchom PuTTYgen z następującej lokalizacji: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Kliknij menu: **pliku** > **klucza prywatnego obciążenia**
4. Zlokalizuj klucz prywatny (`myPrivateKey_rsa` w poprzednim przykładzie). Domyślny katalog po uruchomieniu **Git Bash** jest `C:\Users\%username%`. Zmień filtr plików, aby pokazać **wszystkie pliki (\*.\*)** :

    ![Ładowanie istniejącego klucza prywatnego do PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Kliknij przycisk **Otwórz**. Wiersz wskazuje, że klucz został pomyślnie zaimportowany:

    ![Pomyślnie zaimportowano klucza PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Kliknij przycisk **OK** aby zamknąć wiersz.
7. Klucz publiczny jest wyświetlany w górnej części **PuTTYgen** okna. Skopiuj i wklej ten klucz publiczny w portalu Azure lub usługi Azure Resource Manager szablonu podczas tworzenia maszyny Wirtualnej systemu Linux. Możesz również kliknąć **Zapisz klucz publiczny** Aby zapisać kopię na komputerze:

    ![Zapisz PuTTY pliku klucza publicznego](./media/ssh-from-windows/save-public-key.png)

    W poniższym przykładzie pokazano, jak będzie skopiuj i wklej ten klucz publiczny w portalu Azure, podczas tworzenia maszyny Wirtualnej systemu Linux. Klucz publiczny następnie jest zwykle przechowywany w `~/.ssh/authorized_keys` na nowej maszynie Wirtualnej.

    ![Klucz publiczny używany podczas tworzenia maszyny Wirtualnej w portalu Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. W **PuTTYgen**, kliknij przycisk **zapisać klucz prywatny**:

    ![Zapisz plik klucza prywatnego PuTTY](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > Monit z pytaniem, czy chcesz kontynuować bez wprowadzania hasła dla klucza. Hasło jest takie jak hasła dołączona do klucza prywatnego. Nawet wtedy, gdy ktoś zostały uzyskać klucz prywatny, one nadal nie będzie do uwierzytelniania przy użyciu właśnie klucza. Czy muszą hasło. Bez hasła Jeśli ktoś uzyskuje klucz prywatny one zalogować się do dowolnej maszyny Wirtualnej lub usługi, która używa tego klucza. Zaleca się Utwórz hasło. Jeśli zapomnisz hasła, jego odzyskanie nie będzie możliwe.
   >
   >

    Jeśli chcesz wprowadzić hasło, kliknij przycisk **nr**, wprowadź hasło w oknie głównym PuTTYgen, a następnie kliknij przycisk **Zapisz klucz prywatny** ponownie. W przeciwnym razie kliknij przycisk **tak** aby kontynuować bez podawania opcjonalne hasło.
9. Wprowadź nazwę i lokalizację, aby zapisać plik PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>SSH maszyny z systemem Linux przy użyciu programu Putty
Ponownie PuTTY jest typowe klient SSH dla systemu Windows. Jesteś klienta SSH, który chcesz korzystać. Poniższe kroki szczegółowo opisano sposób użycia klucza prywatnego do uwierzytelniania przy użyciu protokołu SSH maszyny Wirtualnej Azure. Te kroki są podobne w innych SSH klucza klientów pod względem konieczności załadować klucz prywatny do uwierzytelniania połączenia SSH.

1. Pobierz i uruchom program putty z następującej lokalizacji: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Wypełnij nazwę hosta lub adres IP maszyny Wirtualnej w portalu Azure:

    ![Otwórz nowe połączenie programu PuTTY](./media/ssh-from-windows/putty-new-connection.png)
3. Przed wybraniem **Otwórz**, kliknij przycisk **połączenia** > **SSH** > **uwierzytelniania** kartę. Wyszukaj i wybierz Twój klucz prywatny:

    ![Wybierz klucza prywatnego PuTTY dla uwierzytelniania](./media/ssh-from-windows/putty-auth-dialog.png)
4. Kliknij przycisk **Otwórz** nawiązywania połączenia z maszyną wirtualną

## <a name="next-steps"></a>Kolejne kroki
Można także wygenerować klucze publiczne i prywatne [przy użyciu OS X i Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać więcej informacji na temat Bash systemu Windows i korzyści narzędzia OSS gotowe na komputerze z systemem Windows, temacie [Bash na Ubuntu w systemie Windows](https://msdn.microsoft.com/commandline/wsl/about).

Jeśli masz problem przy użyciu protokołu SSH, aby nawiązać połączenie z maszyn wirtualnych systemu Linux, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyny Wirtualnej systemu Linux Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
