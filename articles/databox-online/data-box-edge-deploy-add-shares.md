---
title: Transfer danych za pomocą usługi Azure Data Box Edge | Microsoft Docs
description: Dowiedz się, jak dodawać udziały i łączyć się z nimi na urządzeniu Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 6810818e48329d883961c840fa83857d84b98fd4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112873"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Samouczek: Transfer danych za pomocą usługi Azure Data Box Edge (wersja zapoznawcza)

W tym samouczku opisano sposób dodawania udziałów i łączenia się z nimi na urządzeniu Data Box Edge. Po dodaniu udziałów urządzenie Data Box Edge może przesłać dane na platformę Azure.

Wykonanie tej procedury może zająć około 10 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie z udziałem

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem udziałów do urządzenia Data Box Edge upewnij się, że spełniono następujące warunki:
* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Instalowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-install.md). 

* Urządzenie fizyczne zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). 

* Urządzenie jest gotowe do tworzenia udziałów i przesyłania danych.


## <a name="add-a-share"></a>Dodawanie udziału

Aby utworzyć udział, wykonaj następującą procedurę:

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do obszaru **Wszystkie zasoby**, a następnie wyszukaj zasób usługi Data Box Edge.
    
1. Na filtrowanej liście zasobów wybierz zasób usługi Data Box Edge.

1. W okienku po lewej stronie wybierz pozycję **Omówienie**, a następnie wybierz pozycję **Dodaj udział**.
   
   ![Dodawanie udziału](./media/data-box-edge-deploy-add-shares/click-add-share.png)

1. W okienku **Dodawanie udziału** wykonaj następującą procedurę:

    a. W polu **Nazwa** podaj unikatową nazwę udziału.  
    Nazwa udziału może zawierać tylko małe litery, cyfry i łączniki. Musi mieć od 3 do 63 znaków i rozpoczynać się od litery lub cyfry. Za i przed łącznikiem musi znajdować się litera lub cyfra.
    
    b. Wybierz **Typ** dla udziału.  
    Dostępne są dwa typy — **SMB** i **NFS** — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux.  
    W zależności od tego, czy wybrano udział SMB, czy NFS, pozostałe opcje nieco się różnią. 

    d. Podaj konto magazynu, w którym będzie przechowywany udział.  
    Jeśli kontener jeszcze nie istnieje, zostanie on utworzony z nowo utworzoną nazwą udziału na koncie magazynu. Jeśli kontener już istnieje, zostanie on użyty. 
    
    d. Na liście rozwijanej **Usługa magazynu** wybierz pozycję **Blokowy obiekt blob**, **Stronicowy obiekt blob** lub **Pliki**.  
    Wybrany typ usługi zależy od tego, jaki format mają mieć dane do użycia na platformie Azure. Ponieważ w tym przykładzie chcemy przechowywać dane jako blokowe obiekty blob na platformie Azure, wybierzemy pozycję **Blokowy obiekt blob**. Jeśli wybierzesz pozycję Stronicowy obiekt blob, upewnij się, że dane są wyrównane o 512 bajtów. Na przykład dysk VHDX jest zawsze wyrównany o 512 bajtów.
   
    e. W zależności od tego, czy utworzono udział SMB, czy udział NFS, wykonaj jeden z następujących kroków: 
     
    - **Udział SMB**: W obszarze **Użytkownik lokalny ze wszystkimi uprawnieniami** wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. Jeśli tworzysz nowego użytkownika lokalnego, wprowadź nazwę użytkownika i hasło, a następnie potwierdź hasło. Ta akcja spowoduje przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz modyfikować je przy użyciu Eksploratora plików.

        Jeśli zaznaczysz pole wyboru **Zezwalaj tylko na operacje odczytu** dla tego udziału danych, możesz określić użytkowników z uprawnieniami tylko do odczytu.

        ![Dodawanie udziału SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Udział NFS**: Wprowadź adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału.

        ![Dodawanie udziału NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
1. Wybierz pozycję **Utwórz**, aby utworzyć udział. 
    
    Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami sekcja **Udziały** zostanie zaktualizowana o nowe informacje o udziale. 
    
    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Łączenie z udziałem

Teraz możesz połączyć się z jednym lub kilkoma udziałami utworzonymi w ostatnim kroku. W zależności od tego, czy masz udział SMB, czy NFS, kroki mogą się różnić. 

### <a name="connect-to-an-smb-share"></a>Łączenie z udziałem SMB

Na kliencie systemu Windows Server połączonym z urządzeniem Data Box Edge połącz się z udziałem SMB, wprowadzając polecenia:


1. W oknie polecenia wpisz:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

1. Po wyświetleniu monitu wprowadź hasło dla udziału.  
   Poniżej przedstawiono przykładowe dane wyjściowe dla tego polecenia.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 Microsoft Corporation. All rights reserved. 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


1. Na klawiaturze naciśnij klawisze Windows + R. 

1. W oknie **Uruchamianie** określ `\\<device IP address>`, a następnie wybierz pozycję **OK**.  
   Zostanie otwarty Eksplorator plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. W Eksploratorze plików kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
 
    ![Łączenie z udziałem SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Dane są zapisywane w tych udziałach podczas ich generowania, a urządzenie wypycha dane do chmury.

### <a name="connect-to-an-nfs-share"></a>Łączenie z udziałem NFS

Na kliencie systemu Linux połączonym z urządzeniem Data Box Edge wykonaj następująca procedurę:

1. Upewnij się, że na kliencie zainstalowano klienta NFSv4. Aby zainstalować klienta NFS, użyj następującego polecenia:

   `sudo apt-get install nfs-common`

    Aby uzyskać więcej informacji, przejdź do tematu [Instalowanie klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

1. Po zainstalowaniu klienta NFS zainstaluj utworzony przez Ciebie udział NFS na urządzeniu Data Box Edge, używając następującego polecenia:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Zanim zainstalujesz udział, upewnij się, że katalogi, które będą pełnić rolę punktów instalacji na komputerze lokalnym, zostały już utworzone. Te katalogi nie powinny zawierać żadnych plików ani podfolderów.

    Poniższy przykład pokazuje, jak nawiązać połączenie z udziałem na urządzeniu Data Box Edge za pośrednictwem systemu plików NFS. Adres IP urządzenia to `10.10.10.60`. Udział `mylinuxshare2` jest instalowany w systemie ubuntuVM. Punkt instalacji udziału to `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Do wersji zapoznawczej mają zastosowanie następujące zastrzeżenia:
> - Po utworzeniu pliku w udziale nie można zmienić nazwy pliku. 
> - Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące zagadnienia dotyczące usługi Data Box Edge:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem

Aby dowiedzieć się, jak przekształcać dane za pomocą usługi Data Box Edge, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Przekształcanie danych za pomocą usługi Data Box Edge](./data-box-edge-deploy-configure-compute.md)


