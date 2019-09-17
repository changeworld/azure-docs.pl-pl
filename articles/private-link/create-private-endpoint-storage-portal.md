---
title: Tworzenie prywatnego punktu końcowego platformy Azure przy użyciu Azure Portal | Microsoft Docs
description: Informacje o prywatnym punkcie końcowym platformy Azure
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 8a569f91a27884c320f4af5b1282bfe4dd9a5a99
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018941"
---
# <a name="create-a-private-endpoint-using-the-azure-portal"></a>Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal
Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie z zasobami łączy prywatnych.

W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure, konto magazynu z prywatnym punktem końcowym przy użyciu Azure Portal. Następnie możesz bezpiecznie uzyskać dostęp do konta magazynu z maszyny wirtualnej.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (konta magazynu w tym przykładzie).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subscription | Wybierz subskrypcję.|
    | Resource group | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Location | Wybierz pozycję **WestCentralUS**.|
    | Podsieć — nazwa | Wprowadź nazwę moja *podsieć*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    |||
1. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.


### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **obliczeniowy** > **maszyny wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subscription | Wybierz subskrypcję. |
    | Resource group | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Size | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz opcję **Dalej: Dyski**.

1. W obszarze **Tworzenie maszyny wirtualnej — dyski**pozostaw wartości domyślne i wybierz pozycję **dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Subnet | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)** .|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

## <a name="create-your-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
W tej sekcji utworzysz prywatne konto magazynu przy użyciu prywatnego punktu końcowego. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** >  > magazyn**konto magazynu**.

1. W obszarze **Tworzenie konta magazynu — podstawowe**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subscription | Wybierz subskrypcję. |
    | Resource group | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa konta magazynu  | Wprowadź *mojekontomagazynu*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    | Region | Wybierz pozycję **WestCentralUS**. |
    | Wydajność| Pozostaw wartość domyślną **Standardowa**. |
    | Rodzaj konta | Pozostaw domyślny **Magazyn (ogólnego przeznaczenia w wersji 2)** . |
    | Replikacja | Wybierz **Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)** . |
    |||
  
3. Wybierz **pozycję Dalej: Sieć**.
4. W obszarze **Tworzenie konta magazynu — sieć**, metoda połączenia wybierz pozycję **prywatny punkt końcowy**.
5. W obszarze **Tworzenie konta magazynu — sieć**wybierz pozycję **Dodaj prywatny punkt końcowy**. 
6. W obszarze **Utwórz prywatny punkt końcowy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subscription | Wybierz subskrypcję. |
    | Resource group | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    |Location|Wybierz pozycję **WestCentralUS**.|
    |Name|Wprowadź *myPrivateEndpoint*.  |
    |Zasób podrzędny magazynu|Pozostaw domyślny **obiekt BLOB**. |
    | **SIECI** |  |
    | Sieć wirtualna  | Wybierz pozycję *MyVirtualNetwork* *z grupy zasobów*. |
    | Subnet | Wybierz pozycję Moja *podsieć*. |
    | **PRYWATNA INTEGRACJA Z USŁUGĄ DNS**|  |
    | Integracja z prywatną strefą DNS  | Pozostaw wartość domyślną **tak**. |
    | Strefa Prywatna strefa DNS  | Pozostaw wartość domyślną  ** (New) privatelink.blob.Core.Windows.NET**. |
    |||
7. Wybierz **przycisk OK**. 
8. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 
9. Gdy zobaczysz komunikat o  **przekazaniu walidacji**, wybierz pozycję **Utwórz**. 
10. Przejdź do zasobu konta magazynu, który właśnie został utworzony.
11. Z menu po lewej stronie wybierz pozycję **klucze dostępu** .
12. Wybierz pozycję **Kopiuj** w parametrach połączenia dla Klucz1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Plik Remote Desktop Protocol ( *.rdp*) zostanie utworzony na platformie Azure, a następnie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-storage-account-privately-from-the-vm"></a>Dostęp do konta magazynu prywatnego z maszyny wirtualnej

W tej sekcji nastąpi połączenie prywatne z kontem magazynu przy użyciu prywatnego punktu końcowego.

> [!IMPORTANT]
> Konfiguracja usługi DNS dla magazynu wymaga ręcznej modyfikacji pliku hosts w celu uwzględnienia nazwy FQDN określonego konta. Zmodyfikuj następujący plik, korzystając z uprawnień administratora w systemie Windows: c:\Windows\System32\Drivers\etc\hosts lub Linux/etc/hosts Dołącz informacje DNS dla konta z poprzedniego kroku w następującym formacie [prywatny adres IP] myaccount.blob.core.windows.net

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.
2. Wprowadź `nslookup mystorageaccount.blob.core.windows.net` komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
