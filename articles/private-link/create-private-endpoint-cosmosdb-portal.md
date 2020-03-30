---
title: Łączenie się z kontem usługi Azure Cosmos za pomocą łącza prywatnego usługi Azure
description: Dowiedz się, jak bezpiecznie uzyskać dostęp do konta usługi Azure Cosmos z maszyny Wirtualnej, tworząc prywatny punkt końcowy.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252600"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Łączenie się prywatnie z kontem usługi Azure Cosmos przy użyciu łącza prywatnego usługi Azure

Prywatny punkt końcowy platformy Azure jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobami łącza prywatnego.

W tym artykule dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure i konto usługi Azure Cosmos z prywatnym punktem końcowym przy użyciu witryny Azure portal. Następnie można bezpiecznie uzyskać dostęp do konta usługi Azure Cosmos z maszyny Wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego (konto usługi Azure Cosmos w tym przykładzie).

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroup|
| **\<>nazwa sieci wirtualnej** | myVirtualNetwork         |
| **\<nazwa regionu>**          | Zachodnio-środkowe stany USA     |
| **\<>przestrzeni adresowej IPv4**   | 10.1.0.0\16          |
| **\<>nazwa podsieci**          | mySubnet        |
| **\<>zakresu adresu podsieci** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

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
    | Potwierdź hasło | Ponownie wejdź pod hasło. |
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

## <a name="create-an-azure-cosmos-account"></a>Tworzenie konta usługi Azure Cosmos

Utwórz [konto interfejsu API sql usługi Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Dla uproszczenia można utworzyć konto usługi Azure Cosmos w tym samym regionie co inne zasoby (czyli "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Tworzenie prywatnego punktu końcowego dla konta usługi Azure Cosmos

Utwórz łącze prywatne dla konta usługi Azure Cosmos zgodnie z opisem w [sekcji Utwórz łącze prywatne przy użyciu](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) sekcji portalu Azure w połączonym artykule.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. W pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik *rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

        > [!NOTE]
        > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Dostęp do konta usługi Azure Cosmos prywatnie z maszyny Wirtualnej

W tej sekcji połączysz się prywatnie z kontem usługi Azure Cosmos przy użyciu prywatnego punktu końcowego. 

1. Aby uwzględnić adres IP i mapowanie DNS, zaloguj się `c:\Windows\System32\Drivers\etc\hosts` do maszyny wirtualnej *myVM*, otwórz plik i dołącz informacje DNS z poprzedniego kroku w następującym formacie:

   [Prywatny adres IP] [Punkt końcowy konta].documents.azure.com

   **Przykład:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. W pulpicie zdalnym *myVM*zainstaluj [Eksploratora magazynu platformy Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Wybierz **pozycję Konta usługi Cosmos DB (wersja zapoznawcza)** po kliknięciu prawym przyciskiem myszy.

1. Wybierz **pozycję Połącz z bazą danych usługi Cosmos**.

1. Wybierz pozycję **Interfejs API**.

1. Wprowadź parametry połączenia, wklejając wcześniej skopiowane informacje.

1. Wybierz **pozycję Dalej**.

1. Wybierz przycisk **Połącz**.

1. Przeglądaj bazy danych i kontenery usługi Azure Cosmos z *konta mycosmosaccount*.

1. (Opcjonalnie) dodaj nowe elementy do *mycosmosaccount*.

1. Zamknij połączenie pulpitu zdalnego do *myVM*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z prywatnego punktu końcowego, konta usługi Azure Cosmos i maszyny Wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera: 

1. Wprowadź *myResourceGroup* w polu **wyszukiwania** u góry portalu i wybierz *myResourceGroup* z wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono maszynę wirtualną w sieci wirtualnej, konto usługi Azure Cosmos i prywatny punkt końcowy. Połączenie z maszyną wirtualną z Internetu i bezpiecznie przekazane do konta usługi Azure Cosmos przy użyciu łącza prywatnego.

* Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [Co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).

* Aby dowiedzieć się więcej na temat ograniczenia prywatnego punktu końcowego podczas korzystania z usługi Azure Cosmos DB, zobacz [Azure Private Link z usługą Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artykuł.
