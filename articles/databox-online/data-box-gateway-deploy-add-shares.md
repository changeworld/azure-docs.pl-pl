---
title: Transfer danych za pomocą usługi Azure Data Box Gateway | Microsoft Docs
description: Dowiedz się, jak dodawać udziały i łączyć się z nimi na urządzeniu usługi Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 32466cc0a1ab9b86fc2fb8eb791c232ae13f1c01
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79213569"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Samouczek: Przenoszenie danych za pomocą bramy pola danych platformy Azure


## <a name="introduction"></a>Wprowadzenie

W tym artykule opisano sposób dodawania udziałów i łączenia się z nim w bramie pola danych. Po dodaniu udziałów urządzenie bramy pola danych może przesyłać dane na platformę Azure.

Wykonanie tej procedury może zająć około 10 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Dodawanie udziału
> * Łączenie się z udziałem

## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem udziałów do urządzenia usługi Data Box Gateway upewnij się, że spełniono następujące warunki:

- Udostępniono urządzenie wirtualne i połączono z nim, jak opisano w [programie A Provision a Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Aprowizuj bramę pola danych w VMware](data-box-gateway-deploy-provision-vmware.md).

- Aktywowano urządzenie wirtualne opisane w [aplikacji Connect i aktywowano bramę usługi Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Urządzenie jest gotowe do tworzenia udziałów i przesyłania danych.

## <a name="add-a-share"></a>Dodawanie udziału

Aby utworzyć udział, wykonaj następującą procedurę:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz zasób bramy pola danych, a następnie przejdź do **przeglądu**. Urządzenie powinno być w trybie online. Wybierz **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. W **programie Dodaj udział**wykonaj następującą procedurę:

    1. Podaj unikatową nazwę udziału. Nazwy udziałów mogą zawierać tylko małe litery, cyfry i łączniki. Nazwa udziału musi mieć od 3 do 63 znaków i zaczynać się od litery lub liczby. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.
    
    2. Wybierz **Typ** dla udziału. Dostępne są dwa typy — SMB i NFS — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

    3. Podaj konto magazynu, na którym będzie mieszkał udział. Jeśli kontener jeszcze nie istnieje, zostanie on utworzony z nowo utworzoną nazwą udziału na koncie magazynu. Jeśli kontener już istnieje, zostanie on użyty.
       > [!IMPORTANT]
       > Upewnij się, że konto usługi Azure Storage, którego używasz, nie ma zasad niezmienności ustawionych na nim, jeśli używasz go z urządzeniem usługi Azure Stack Edge lub Data Box Gateway. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad niezmienności i zarządzanie nimi dla magazynu obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).
    
    4. W obszarze **Usługa magazynu** wybierz blokowy obiekt blob, stronicowy obiekt blob lub pliki. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład w tym wypadku chcemy, aby dane zostały umieszczone na platformie Azure jako blokowe obiekty blob, dlatego wybierzemy opcję Blokowy obiekt blob. Jeśli wybierzesz opcję Stronicowy obiekt blob, upewnij się, że dane są wyrównane o 512 bajtów. Na przykład dysk VHDX jest zawsze wyrównany o 512 bajtów.
   
    5. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS.
     
    - **Udział SMB** — w obszarze **Wszystkie uprawnienia użytkownik lokalny**wybierz pozycję **Utwórz nowy** lub Użyj **istniejącego**. Jeśli utworzysz nowego użytkownika lokalnego, wprowadź **nazwę użytkownika** i **hasło**, a następnie **potwierdź hasło**. Ta akcja przypisuje uprawnienia do użytkownika lokalnego. Modyfikacja uprawnień na poziomie udziału nie jest obecnie obsługiwana.
    
        ![Dodawanie udziału SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Jeśli dla tych danych udziału zostanie zaznaczone pole wyboru **Zezwalaj tylko** na operacje odczytu, można określić użytkowników tylko do odczytu.
        
    - **Udział NFS** — wprowadź adresy IP dozwolonych klientów, którzy mogą uzyskać dostęp do udziału.

        ![Dodawanie udziału NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Wybierz pozycję **Utwórz**, aby utworzyć udział.
    
    Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami kafelka **Udostępnień** jest aktualizowana w celu odzwierciedlenia nowego udziału.
    
    ![Kafelek Zaktualizowane udziały](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Łączenie z udziałem

Teraz możesz połączyć się z jednym lub kilkoma udziałami utworzonymi w ostatnim kroku. W zależności od tego, czy masz udział SMB, czy NFS, kroki mogą się różnić.

### <a name="connect-to-an-smb-share"></a>Łączenie z udziałem SMB

Na kliencie systemu Windows Server połączonym z bramą pola danych połącz się z udziałem SMB, wprowadzając polecenia:


1. W oknie polecenia wpisz:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po wyświetleniu monitu wprowadź hasło dla udziału. Poniżej przedstawiono przykładowe dane wyjściowe dla tego polecenia.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Na klawiaturze wybierz pozycję Windows + R. 
3. W oknie **Uruchom** określ `\\<device IP address>` przycisk **OK**. Zostanie otwarty Eksplorator plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. W Eksploratorze plików kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
 
    ![Łączenie z udziałem SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Dane są zapisywane w tych udziałach podczas ich generowania, a urządzenie wypycha dane do chmury.

### <a name="connect-to-an-nfs-share"></a>Łączenie z udziałem NFS

Na kliencie systemu Linux połączonym z urządzeniem Data Box Edge wykonaj następująca procedurę:

1. Upewnij się, że na kliencie zainstalowano klienta NFSv4. Aby zainstalować klienta NFS, użyj następującego polecenia:

   `sudo apt-get install nfs-common`

    Aby uzyskać więcej informacji, przejdź do tematu [Instalowanie klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po zainstalowaniu klienta NFS użyj następującego polecenia, aby zainstalować utworzony przez Ciebie udział NFS na urządzeniu usługi Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Przed skonfigurowaniem instalacji upewnij się, że katalogi, które będą pełnić rolę punktów instalacji na komputerze lokalnym, zostały już utworzone i że nie zawierają żadnych plików ani podfolderów.

    Poniższy przykład pokazuje, jak nawiązać połączenie z udziałem na urządzeniu usługi Gateway za pośrednictwem systemu plików NFS. Adres IP urządzenia wirtualnego: `10.10.10.60`. Udział `mylinuxshare2` jest instalowany na maszynie wirtualnej ubuntuVM. Punkt instalacji: `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Następujące zastrzeżenia mają zastosowanie do tej wersji:
> - Po utworzeniu pliku w udziałach zmiana nazwy pliku nie jest obsługiwana.
> - Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.
> - W przypadku kopiowania danych za pomocą narzędzia `rsync` opcja `rsync -a` nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem


Przejdź do następnego samouczka, aby dowiedzieć się, jak administrować usługą Data Box Gateway.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Gateway za pomocą lokalnego internetowego interfejsu użytkownika](https://aka.ms/dbg-docs)


