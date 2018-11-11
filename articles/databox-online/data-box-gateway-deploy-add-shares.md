---
title: Transfer danych za pomocą usługi Azure Data Box Gateway | Microsoft Docs
description: Dowiedz się, jak dodawać udziały i łączyć się z nimi na urządzeniu usługi Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: fa31397e0ecffbd245557a824bdd770724bbc91c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249884"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Samouczek: transfer danych za pomocą usługi Azure Data Box Gateway (wersja zapoznawcza)


## <a name="introduction"></a>Wprowadzenie

W tym artykule opisano sposób dodawania udziałów i łączenia się z nimi na urządzeniu usługi Data Box Gateway. Po dodaniu udziałów urządzenie usługi Data Box Gateway może przesłać dane na platformę Azure.

Wykonanie tej procedury może zająć około 10 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem

> [!IMPORTANT]
> - Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem udziałów do urządzenia usługi Data Box Gateway upewnij się, że spełniono następujące warunki:

* Aprowizowano urządzenie wirtualne i nawiązano z nim połączenie zgodnie z procedurą opisaną w temacie [Aprowizowanie urządzenia usługi Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Aprowizowanie urządzenia usługi Data Box Gateway w środowisku VMware](data-box-gateway-deploy-provision-vmware.md). 

    Urządzenie wirtualne zostało aktywowane zgodnie z procedurą opisaną w temacie [Łączenie i aktywowanie usługi Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) i jest gotowe do utworzenia udziałów i transferu danych.


## <a name="add-a-share"></a>Dodawanie udziału

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/), aby utworzyć udział.

1. Wróć do witryny Azure Portal. Przejdź do obszaru **Wszystkie zasoby** i wyszukaj zasób usługi Data Box Gateway.
    
2. Na filtrowanej liście zasobów wybierz zasób usługi Data Box Gateway, a następnie przejdź do sekcji **Omówienie**. Kliknij przycisk **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału. 

   Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.
    
5. Wybierz **Typ** dla udziału. Dostępne są dwa typy — SMB i NFS — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne. 

6. Należy podać konto magazynu, w którym zostanie umieszczony udział. Na koncie magazynu zostanie utworzony kontener z nazwą udziału (jeśli jeszcze nie istnieje). Jeśli kontener już istnieje, zostanie użyty istniejący kontener. 
    
7. W obszarze **Usługa magazynu** wybierz blokowy obiekt blob, stronicowy obiekt blob lub pliki. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład w tym wypadku chcemy, aby dane zostały umieszczone na platformie Azure jako blokowe obiekty blob, dlatego wybierzemy opcję Blokowy obiekt blob. Jeśli wybierzesz opcję Stronicowy obiekt blob, upewnij się, że dane są wyrównane o 512 bajtów. Należy pamiętać, że dysk VHDX jest zawsze wyrównany o 512 bajtów.
   
8. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS. 
     
    - **W przypadku tworzenia udziału SMB** — W polu Użytkownik lokalny ze wszystkimi uprawnieniami wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. W przypadku tworzenia nowego użytkownika lokalnego, podaj **nazwę użytkownika**, **hasło**, a następnie **potwierdź hasło**. Spowoduje to przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
    
        ![Dodawanie udziału SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Jeśli zaznaczysz opcję **Zezwalaj tylko na operacje odczytu** dla tego udziału danych, będziesz mieć możliwość określenia użytkowników z uprawnieniami tylko do odczytu.
        
    - **W przypadku tworzenia udziału NFS** — Należy podać adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału.

        ![Dodawanie udziału NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Kliknij pozycję **Utwórz**, aby utworzyć udział. 
    
    Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział. 
    
    ![Zaktualizowana lista udziałów](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Łączenie z udziałem

Aby połączyć się z udziałami, wykonaj następujące kroki na kliencie systemu Windows Server połączonym z urządzeniem usługi Data Box Gateway.


1. Otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po wyświetleniu monitu wprowadź hasło dla udziału. Poniżej przedstawiono przykładowe dane wyjściowe dla tego polecenia.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Kliknij przycisk **OK**. Spowoduje to otworzenie Eksploratora plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. Wybierz i kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
 
    ![Łączenie z udziałem SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Dane są zapisywane w tych udziałach podczas ich generowania, a urządzenie wypycha dane do chmury.

### <a name="connect-to-an-nfs-share"></a>Łączenie z udziałem NFS

Wykonaj poniższe kroki na kliencie systemu Linux połączonym z urządzeniem usługi Data Box Edge.

1. Upewnij się, że na kliencie zainstalowano klienta NFSv4. Aby zainstalować klienta NFS, użyj następującego polecenia:

   `sudo apt-get install nfs-common`

    Aby uzyskać więcej informacji, przejdź do tematu [Instalowanie klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po zainstalowaniu klienta NFS użyj następującego polecenia, aby zainstalować utworzony przez Ciebie udział NFS na urządzeniu usługi Data Box Gateway:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Przed skonfigurowaniem instalacji upewnij się, że katalogi, które będą pełnić rolę punktów instalacji na komputerze lokalnym, zostały już utworzone i że nie zawierają żadnych plików ani podfolderów.

    Poniższy przykład pokazuje, jak nawiązać połączenie z udziałem na urządzeniu usługi Gateway za pośrednictwem systemu plików NFS. Adres IP urządzenia wirtualnego: `10.10.10.60`. Udział `mylinuxshare2` jest instalowany na maszynie wirtualnej ubuntuVM. Punkt instalacji: `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Do wersji zapoznawczej mają zastosowanie następujące zastrzeżenia:
> - Po utworzeniu pliku w udziałach nie można zmienić nazwy pliku. 
> - Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem


Przejdź do następnego samouczka, aby dowiedzieć się, jak administrować usługą Data Box Gateway.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Gateway za pomocą lokalnego internetowego interfejsu użytkownika](https://aka.ms/dbg-docs)


