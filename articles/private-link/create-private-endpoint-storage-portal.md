---
title: Nawiązywanie prywatnego połączenia z kontem magazynu za pomocą prywatnego punktu końcowego platformy Azure
description: Dowiedz się, jak połączyć się prywatnie z kontem magazynu na platformie Azure przy użyciu prywatnego punktu końcowego.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8c76333d5a2be8a2c589dbe54389b023fef34854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252529"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Nawiązywanie prywatnego połączenia z kontem magazynu za pomocą prywatnego punktu końcowego platformy Azure
Prywatny punkt końcowy platformy Azure jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobami łącza prywatnego.

W tym przewodniku Szybki start dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure, konto magazynu z prywatnym punktem końcowym przy użyciu witryny Azure portal. Następnie można bezpiecznie uzyskać dostęp do konta magazynu z maszyny Wirtualnej.

> [!NOTE]
> Prywatne punkty końcowe nie są dozwolone w połączeniu z punktami końcowymi usługi w tej samej podsieci!

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego (konto magazynu w tym przykładzie).

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego.

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroup |
| **\<>nazwa sieci wirtualnej** | myVirtualNetwork          |
| **\<nazwa regionu>**          | Zachodnio-środkowe stany USA      |
| **\<>przestrzeni adresowej IPv4**   | 10.1.0.0\16          |
| **\<>nazwa podsieci**          | mySubnet        |
| **\<>zakresu adresu podsieci** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję Utwórz**maszynę wirtualną****obliczeniową** >  **zasobów** > .

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wpisz *myVm*. |
    | Region | Wybierz **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image (Obraz) | Wybierz **pozycję Centrum danych systemu Windows Server 2019**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz **dalej: Dyski**.

1. W **obszarze Tworzenie maszyny wirtualnej — dyski**pozostaw ustawienia domyślne i wybierz pozycję **Dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw domyślną **myvirtualnetwork**.  |
    | Przestrzeń adresowa | Pozostaw domyślny **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną **mySubnet (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji.

1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

## <a name="create-your-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
W tej sekcji utworzysz konto magazynu prywatnego przy użyciu prywatnego punktu końcowego do niego. 

1. W lewym górnym rogu ekranu w witrynie Azure portal wybierz pozycję **Utwórz** > **konto****magazynu** > zasobów .

1. W **obszarze Tworzenie konta magazynu — podstawy**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa konta magazynu  | Wprowadź *mystorageaccount*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę. |
    | Region | Wybierz **WestCentralUS**. |
    | Wydajność| Pozostaw wartość domyślną **Standardowa**. |
    | Rodzaj konta | Pozostaw domyślny **magazyn (ogólnego przeznaczenia w wersji 2)**. |
    | Replikacja | Wybierz **opcję Magazyn geograficzny dostępu do odczytu (RA-GRS)**. |
    |||
  
3. Wybierz **dalej: Sieć**.
4. W **obszarze Tworzenie konta magazynu — Sieć**, metoda łączności wybierz pozycję Prywatny punkt **końcowy**.
5. W **obszarze Tworzenie konta magazynu — Sieć**wybierz pozycję Dodaj prywatny punkt **końcowy**. 
6. W **obszarze Tworzenie prywatnego punktu końcowego**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    |Lokalizacja|Wybierz **WestCentralUS**.|
    |Nazwa|Wprowadź *myPrivateEndpoint*.  |
    |Podsób magazynu|Pozostaw domyślny **obiekt blob**. |
    | **Sieci** |  |
    | Sieć wirtualna  | Wybierz *MyVirtualNetwork* z grupy zasobów *myResourceGroup*. |
    | Podsieć | Wybierz *mySubnet*. |
    | **PRYWATNA INTEGRACJA Z SYSTEMEM DNS**|  |
    | Integracja z prywatną strefą DNS  | Pozostaw domyślny **Tak**. |
    | Prywatna strefa DNS  | Pozostaw domyślną **privatelink.blob.core.windows.net (Nowy)**. |
    |||
7. Kliknij przycisk **OK**. 
8. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 
9. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**. 
10. Przejdź do zasobu konta magazynu, który właśnie utworzono.
11. Z menu zawartości po lewej stronie wybierz **pozycję Klawisze dostępu.**
12. Wybierz **polecenie Kopiuj** na ciągu połączenia dla key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. W pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz *pobrany plik rdp.*

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

        > [!NOTE]
        > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-storage-account-privately-from-the-vm"></a>Dostęp do konta magazynu prywatnego z maszyny Wirtualnej

W tej sekcji połączysz się prywatnie z kontem magazynu przy użyciu prywatnego punktu końcowego.

1. W pulpicie zdalnym *myVM*otwórz program PowerShell.
2. Wprowadź `nslookup mystorageaccount.blob.core.windows.net` Otrzymasz wiadomość podobną do tej:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Zainstaluj [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Wybierz **pozycję Konta magazynu** po kliknięciu prawym przyciskiem myszy.
5. Wybierz **pozycję Połącz z magazynem na platformie Azure**.
6. Wybierz **pozycję Użyj ciągu połączenia**.
7. Wybierz **pozycję Dalej**.
8. Wprowadź parametry połączenia, wklejając wcześniej skopiowane informacje.
9. Wybierz **pozycję Dalej**.
10. Wybierz przycisk **Połącz**.
11. Przeglądaj kontenery obiektów Blob z konta mystorageaccount 
12. (Opcjonalnie) Twórz foldery i/lub przesyłaj pliki na *konto mystorageaccount*. 
13. Zamknij połączenie pulpitu zdalnego do *myVM*. 

Dodatkowe opcje dostępu do konta magazynu:
- Microsoft Azure Storage Explorer to samodzielna bezpłatna aplikacja firmy Microsoft, która umożliwia wizualną pracę z danymi magazynu platformy Azure w systemach Windows, macOS i Linux. Aplikację można zainstalować w celu prywatnego przeglądania zawartości konta magazynu. 
 
- Narzędzie AzCopy to kolejna opcja dla szybkiego transferu danych skryptowych dla magazynu platformy Azure. Narzędzie AzCopy służy do transferowania danych do i z usług Blob, File i Table Storage. 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Po zakończeniu korzystania z prywatnego punktu końcowego, konta magazynu i maszyny Wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera: 
1. Wprowadź *myResourceGroup* w polu **wyszukiwania** u góry portalu i wybierz *myResourceGroup* z wyników wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono maszynę wirtualną na koncie sieci wirtualnej i magazynu oraz w prywatnym punkcie końcowym. Połączenie z jedną maszyną wirtualną z Internetu i bezpieczne komunikowanie się z kontem magazynu przy użyciu łącza prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [Co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).
