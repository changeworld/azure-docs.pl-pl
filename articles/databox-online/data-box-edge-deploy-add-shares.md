---
title: Transfer danych za pomocą usługi Azure Data Box Edge | Microsoft Docs
description: Dowiedz się, jak dodawać udziały i łączyć się z nimi na urządzeniu Data Box Edge.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: b5c63a255547bae03acbec771593eac97d474003
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48833104"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Samouczek: transfer danych za pomocą usługi Azure Data Box Edge (wersja zapoznawcza)

W tym samouczku opisano sposób dodawania udziałów i łączenia się z nimi na urządzeniu Data Box Edge. Po dodaniu udziałów urządzenie Data Box Edge może przesłać dane na platformę Azure.

Wykonanie tej procedury może zająć około 10 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem udziałów do urządzenia Data Box Edge upewnij się, że spełniono następujące warunki:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Instalowanie urządzenia Data Box Edge](data-box-edge-deploy-install.md). 

    Urządzenie fizyczne zostało aktywowane zgodnie z opisem w temacie [Podłączanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). Urządzenie jest gotowe do tworzenia udziałów i przesyłania danych.


## <a name="add-a-share"></a>Dodawanie udziału

Wykonaj następujące czynności w witrynie [Azure Portal](https://portal.azure.com/), aby utworzyć udział.

1. Wróć do witryny Azure Portal. Przejdź do obszaru **Wszystkie zasoby** i wyszukaj zasób usługi Data Box Edge.
    
2. Na filtrowanej liście zasobów wybierz zasób usługi Data Box Edge, a następnie przejdź do sekcji **Omówienie**. Kliknij przycisk **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/data-box-edge-deploy-add-shares/click-add-share.png)

3. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału. 

   Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.
    
    1. Wybierz **Typ** dla udziału. Dostępne są dwa typy — SMB i NFS — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. 

    2. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne. 

    3. Należy podać konto magazynu, w którym zostanie umieszczony udział. Na koncie magazynu zostanie utworzony kontener z nazwą udziału (jeśli jeszcze nie istnieje). Jeśli kontener już istnieje, zostanie użyty istniejący kontener. 
    
    4. W obszarze **Usługa magazynu** wybierz blokowy obiekt blob, stronicowy obiekt blob lub pliki. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład w tym przypadku chcemy, aby dane zostały umieszczone na platformie Azure jako blokowe obiekty blob, dlatego wybierzemy opcję Blokowy obiekt blob. Jeśli wybierzesz opcję Stronicowy obiekt blob, upewnij się, że dane są wyrównane o 512 bajtów. Na przykład dysk VHDX jest zawsze wyrównany o 512 bajtów.
   
    5. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS. 
     
        - **W przypadku tworzenia udziału SMB** — W polu Użytkownik lokalny ze wszystkimi uprawnieniami wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. W przypadku tworzenia nowego użytkownika lokalnego, podaj **nazwę użytkownika**, **hasło**, a następnie **potwierdź hasło**. Spowoduje to przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.

            Jeśli zaznaczysz opcję **Zezwalaj tylko na operacje odczytu** dla tego udziału danych, będziesz mieć możliwość określenia użytkowników z uprawnieniami tylko do odczytu.

            ![Dodawanie udziału SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
        - **W przypadku tworzenia udziału NFS** — Należy podać adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału.

            ![Dodawanie udziału NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Kliknij pozycję **Utwórz**, aby utworzyć udział. 
    
    Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział. 
    
    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Łączenie z udziałem

Teraz możesz połączyć się z jednym lub kilkoma udziałami utworzonymi w poprzednim kroku. W zależności od tego, czy masz udział SMB, czy NFS, kroki mogą być różne. 

### <a name="connect-to-an-smb-share"></a>Łączenie z udziałem SMB

Aby połączyć się z udziałami, wykonaj następujące kroki na kliencie systemu Windows Server połączonym z urządzeniem usługi Data Box Edge.


1. Otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po wyświetleniu monitu wprowadź hasło dla udziału. Poniżej przedstawiono przykładowe dane wyjściowe dla tego polecenia.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


2. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Kliknij przycisk **OK**. Spowoduje to otworzenie Eksploratora plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. Wybierz i kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
 
    ![Łączenie z udziałem SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Dane są zapisywane w tych udziałach podczas ich generowania, a urządzenie wypycha dane do chmury.

### <a name="connect-to-an-nfs-share"></a>Łączenie z udziałem NFS

Wykonaj poniższe kroki na kliencie systemu Linux połączonym z urządzeniem usługi Data Box Edge.

1. Upewnij się, że na kliencie zainstalowano klienta NFSv4. Aby zainstalować klienta NFS, użyj następującego polecenia:

   `sudo apt-get install nfs-common`

    Aby uzyskać więcej informacji, przejdź do tematu [Instalowanie klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po zainstalowaniu klienta NFS użyj następującego polecenia, aby zainstalować utworzony przez Ciebie udział NFS na urządzeniu Data Box Edge:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Zanim zainstalujesz udziały, upewnij się, że katalogi, które będą pełnić rolę punktów instalacji na komputerze lokalnym, zostały już utworzone. Te katalogi nie powinny zawierać żadnych plików ani podfolderów.

    Poniższy przykład pokazuje, jak nawiązać połączenie z udziałem na urządzeniu Data Box Edge za pośrednictwem systemu plików NFS. Adres IP urządzenia to `10.10.10.60`. Udział `mylinuxshare2` jest instalowany w systemie ubuntuVM. Punkt instalacji udziału to `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Do wersji zapoznawczej mają zastosowanie następujące zastrzeżenia:
> - Po utworzeniu pliku w udziałach nie można zmienić nazwy pliku. 
> - Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem


Przejdź do następnego samouczka, aby dowiedzieć się, jak przekształcać dane za pomocą usługi Data Box Edge.

> [!div class="nextstepaction"]
> [Przekształcanie danych za pomocą usługi Data Box Edge](./data-box-edge-deploy-configure-compute.md)


