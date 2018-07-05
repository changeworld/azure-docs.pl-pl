---
title: Szablon rozwiązania łańcucha bloków w usłudze Azure Ethereum stosu
description: Szablony niestandardowe rozwiązanie umożliwiają wdrażanie i konfigurowanie sieci konsorcjum Ethereum łańcucha bloków w usłudze Azure Stack
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 07/03/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 0e03b524834f528ddb7555a344fbebe720b4d9ff
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446979"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Szablony rozwiązań łańcucha bloków w usłudze Azure Ethereum stosu

Szablon rozwiązania Ethereum jest przeznaczona do umożliwiają łatwiejsze i szybsze wdrażanie i konfigurowanie sieci konsorcjum zawierającym wiele elementów członkowskich Ethereum łańcucha bloków przy minimalnej znajomości platformy Azure i Ethereum.

Za pomocą kliku dane wejściowe użytkownika i jednym kliknięciem za pośrednictwem portalu dzierżawcy usługi Azure Stack każdy element członkowski można udostępnić swoje sieciowych zużycia. Każdy element członkowski sieciowych zużycia składa się z zestawu węzłów ze zrównoważonym obciążeniem transakcji za pomocą której aplikacji lub użytkownika mogą wchodzić w interakcje przesłać transakcje, zestaw węzłów wyszukiwania rekordu transakcji i wirtualnego urządzenia sieciowego (WUS). Krok kolejne połączenie nawiązuje połączenie z urządzenia WUS, aby utworzyć sieć pełni skonfigurowanej zawierającym wiele elementów członkowskich łańcucha bloków.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz następujące [z witryny Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS wersji 16.04.201802220
* Windows Server 2016 
* Niestandardowego skryptu dla systemu Linux w wersji 2.0 
* Rozszerzenie niestandardowego skryptu 

Aby uzyskać więcej informacji o scenariuszach łańcucha bloków na platformie Azure, zobacz [szablon rozwiązania konsorcjum dowód pracy Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

Wymagana jest subskrypcja platformy Azure, obsługująca wdrażanie kilka maszyn wirtualnych. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="deployment-architecture"></a>Architektura wdrożenia

Ten szablon rozwiązania można wdrożyć jedno- lub wielowarstwowe sieci konsorcjum Ethereum elementu członkowskiego. Sieć wirtualna jest połączona w łańcuch topologii przy użyciu wirtualnego urządzenia sieciowego i połączenia zasobów. 

## <a name="deployment-use-cases"></a>Przypadki użycia wdrożenia

Szablon można wdrożyć konsorcjum Ethereum lidera i element członkowski sprzężenia w na różne sposoby, w tym miejscu są tymi, które przetestowaliśmy:
- W wielu węzłach usłudze Azure Stack przy użyciu usługi Azure AD lub AD FS, wdrażanie potencjalnych klientów i elementów członkowskich przy użyciu tej samej subskrypcji lub z różnych subskrypcji.
- Na jednym węźle usługi Azure Stack (z usługą Azure AD) wdrażanie potencjalnych klientów i składową za pomocą tej samej subskrypcji.

### <a name="standalone-and-consortium-leader-deployment"></a>Autonomiczne, jak i konsorcjum wdrożenia lidera

Szablon lidera konsorcjum konfiguruje zużycie pierwszego elementu członkowskiego w sieci. 

1. Pobierz [lidera szablon z serwisu GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. W portalu usługi Azure Stack Administracja wybierz **nowy > wdrożenie szablonu** do wdrożenia szablonu niestandardowego.
3. Wybierz **Edytuj szablon** edytować nowy szablon niestandardowy.
4. W okienku edytowania, po prawej stronie skopiuj i Wklej lidera szablon JSON został wcześniej pobrany.
    
    ![Edytuj szablon lidera](media/azure-stack-ethereum/edit-leader-template.png)

5. Wybierz pozycję **Zapisz**.
6. Wybierz **Edytuj parametry** i ukończyć parametrów szablonu dla danego wdrożenia.
    
    ![Edytuj parametry szablonu lidera](media/azure-stack-ethereum/edit-leader-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Ciąg używany jako podstawa nazewnictwa wdrożonych zasobów. | Alfanumerycznym o długości od 1 do 6 | ETH
    WARTOŚĆ AUTHTYPE | Metoda uwierzytelniania do maszyny wirtualnej. | Klucz publiczny hasła lub protokołu SSH | Hasło
    ADMINUSERNAME | Nazwa użytkownika administratora w każdej wdrożonej maszyny wirtualnej | 1 - 64 znaki | gethadmin
    ADMINPASSWORD (typ uwierzytelniania = hasło)| Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 z następujących warunków: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny. <br />Gdy wszystkie maszyny wirtualne początkowo miały to samo hasło, możesz zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|
    ADMINSSHKEY (typ uwierzytelniania = sshPublicKey) | Klucz protokołu secure shell, używany do logowania zdalnego. | |
    GENESISBLOCK | Ciąg JSON reprezentujący niestandardowe genesis bloku.  Określenie wartości tego parametru jest opcjonalne. | |
    ETHEREUMACCOUNTPSSWD | Hasło administratora, używany do zabezpieczania konta Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | Hasło używane do generowania klucza prywatnego skojarzonego z kontem Ethereum. | |
    ETHEREUMNETWORKID | Identyfikator sieci konsorcjum. | Używać dowolnej wartości z zakresu od 5 do 999,999,999 | 72
    CONSORTIUMMEMBERID | Identyfikator skojarzony z każdego elementu członkowskiego sieci konsorcjum.   | Ten identyfikator powinny być unikatowe w sieci. | 0
    NUMMININGNODES | Liczba węzłów wyszukiwania. | Pomiędzy 2 a 15. | 2
    MNNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach wyszukiwania. | | Standardowa_A1
    MNSTORAGEACCOUNTTYPE | Wydajność magazynu węzłów wyszukiwania. | | Standard_LRS
    NUMTXNODES | Liczba węzłów transakcji. | Od 1 do 5. | 1
    TXNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach transakcji. | | Standardowa_A1
    TXSTORAGEACCOUNTTYPE | Wydajność magazynu węzłów transakcji. | | Standard_LRS
    BASEURL | Podstawowy adres URL do pobrania szablonów zależności z. | Użyj wartości domyślnej, chyba że chcesz dostosować szablony wdrażania. | 

7. Kliknij przycisk **OK**.
8. W **wdrożenie niestandardowe**, określ **subskrypcji**, **grupy zasobów**, i **lokalizację grupy zasobów**.
    
    ![Lider parametrów wdrożenia](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    Subskrypcja | Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum | | Użycie subskrypcji
    Grupa zasobów | Grupa zasobów, dla której chcesz wdrożyć sieci konsorcjum. | | EthereumResources
    Lokalizacja | Region platformy Azure dla grupy zasobów. | | lokalne

8. Wybierz pozycję **Utwórz**.

Wdrożenie może trwać 20 minut lub dłużej.

Po zakończeniu wdrażania możesz przejrzeć podsumowanie wdrożenia **firmy Microsoft. Szablon** w sekcji Wdrażanie grupy zasobów. Podsumowanie zawiera wartości danych wyjściowych, które może służyć do dołączenia do elementów członkowskich konsorcjum.

Aby zweryfikować wdrożenie liderów, Przeglądaj liderów administratora witryny. Adres witryny administracyjnej można znaleźć w sekcji danych wyjściowych **Microsoft.Template** wdrożenia.  

![Podsumowanie wdrożenia lidera](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Dołączenie do wdrożenia elementu członkowskiego konsorcjum

1. Pobierz [konsorcjum składowej szablonu z serwisu GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. W portalu usługi Azure Stack Administracja wybierz **nowy > wdrożenie szablonu** do wdrożenia szablonu niestandardowego.
3. Wybierz **Edytuj szablon** edytować nowy szablon niestandardowy.
4. W okienku edytowania, po prawej stronie skopiuj i Wklej lidera szablon JSON został wcześniej pobrany.
5. Wybierz pozycję **Zapisz**.
6. Wybierz **Edytuj parametry** i ukończyć parametrów szablonu dla danego wdrożenia.

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Ciąg używany jako podstawa nazewnictwa wdrożonych zasobów. | Alfanumerycznym o długości od 1 do 6 | ETH
    WARTOŚĆ AUTHTYPE | Metoda uwierzytelniania do maszyny wirtualnej. | Klucz publiczny hasła lub protokołu SSH | Hasło
    ADMINUSERNAME | Nazwa użytkownika administratora w każdej wdrożonej maszyny wirtualnej | 1 - 64 znaki | gethadmin
    ADMINPASSWORD (typ uwierzytelniania = hasło)| Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 z następujących warunków: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny. <br />Gdy wszystkie maszyny wirtualne początkowo miały to samo hasło, możesz zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|
    ADMINSSHKEY (typ uwierzytelniania = sshPublicKey) | Klucz protokołu secure shell, używany do logowania zdalnego. | |
    CONSORTIUMMEMBERID | Identyfikator skojarzony z każdego elementu członkowskiego sieci konsorcjum.   | Ten identyfikator powinny być unikatowe w sieci. | 0
    NUMMININGNODES | Liczba węzłów wyszukiwania. | Pomiędzy 2 a 15. | 2
    MNNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach wyszukiwania. | | Standardowa_A1
    MNSTORAGEACCOUNTTYPE | Wydajność magazynu węzłów wyszukiwania. | | Standard_LRS
    NUMTXNODES | Liczba węzłów transakcji. | Od 1 do 5. | 1
    TXNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach transakcji. | | Standardowa_A1
    TXSTORAGEACCOUNTTYPE | Wydajność magazynu węzłów transakcji. | | Standard_LRS
    CONSORTIUMDATA | Adres URL wskazujący konsorcjum odpowiednie dane konfiguracji, dostarczone przez innego użytkownika wdrożenia. Tę wartość można znaleźć w danych wyjściowych wdrożenia lidera. | |
    REMOTEMEMBERVNETADDRESSSPACE | Adres IP urządzenia NVA lidera. Tę wartość można znaleźć w danych wyjściowych wdrożenia lidera. | | 
    REMOTEMEMBERNVAPUBLICIP | Adres IP urządzenia NVA lidera. Tę wartość można znaleźć w danych wyjściowych wdrożenia lidera. | | 
    CONNECTIONSHAREDKEY | Wstępnie ustaloną klucz tajny między elementy członkowskie sieci konsorcjum, które są nawiązywania połączenia. | |
    BASEURL | Podstawowy adres URL dla szablonu. | Użyj wartości domyślnej, chyba że chcesz dostosować szablony wdrażania. | 

7. Kliknij przycisk **OK**.
8. W **wdrożenie niestandardowe**, określ **subskrypcji**, **grupy zasobów**, i **lokalizację grupy zasobów**.

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    Subskrypcja | Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum | | Użycie subskrypcji
    Grupa zasobów | Grupa zasobów, dla której chcesz wdrożyć sieci konsorcjum. | | MemberResources
    Lokalizacja | Region platformy Azure dla grupy zasobów. | | lokalne

8. Wybierz pozycję **Utwórz**.

Wdrożenie może trwać 20 minut lub dłużej.

Po zakończeniu wdrażania możesz przejrzeć podsumowanie wdrożenia **Microsoft.Template** w sekcji Wdrażanie grupy zasobów. Podsumowanie zawiera wartości danych wyjściowych, które może służyć do konsorcjum członkowie witryny connect.

Aby zweryfikować wdrożenie elementu członkowskiego, przeglądania witryny administracyjnej elementu członkowskiego. Adres witryny administracyjnej można znaleźć w sekcji danych wyjściowych Microsoft.Template wdrożenia.

![Podsumowanie wdrożenia elementu członkowskiego](media/azure-stack-ethereum/ethereum-node-status-2.png)

Jak pokazano na ilustracji, jest w stanie węzłów członka **nieuruchomiona**. Jest to spowodowane połączenie między elementu członkowskiego i lider nie zostanie nawiązane. Połączenie między elementu członkowskiego i lider jest dwukierunkowe połączenie. Podczas wdrażania elementu członkowskiego szablonu automatycznie tworzy połączenia od elementu członkowskiego do lidera. Aby utworzyć połączenie z liderem do elementu członkowskiego, przejdź do następnego kroku.

### <a name="connect-member-and-leader"></a>Łączenie elementu członkowskiego i lidera

Ten szablon tworzy połączenie z liderem do zdalnego elementu członkowskiego. 

1. Pobierz [połączyć elementu członkowskiego i lider szablonu z serwisu GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. W portalu usługi Azure Stack Administracja wybierz **nowy > wdrożenie szablonu** do wdrożenia szablonu niestandardowego.
3. Wybierz **Edytuj szablon** edytować nowy szablon niestandardowy.
4. W okienku edytowania, po prawej stronie skopiuj i Wklej lidera szablon JSON został wcześniej pobrany.
    
    ![Edytowanie połączenia szablonu](media/azure-stack-ethereum/edit-connect-template.png)

5. Wybierz pozycję **Zapisz**.
6. Wybierz **Edytuj parametry** i ukończyć parametrów szablonu dla danego wdrożenia.
    
    ![Edytuj Połącz parametry szablonu](media/azure-stack-ethereum/edit-connect-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefiks nazwy lidera. Tę wartość można znaleźć w danych wyjściowych wdrożenia lidera.  | Alfanumerycznym o długości od 1 do 6 | |
    MEMBERROUTETABLENAME | Nazwa tabeli tras lidera. Tę wartość można znaleźć w danych wyjściowych wdrożenia lidera. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Przestrzeń adresowa elementu członkowskiego. Tę wartość można znaleźć w danych wyjściowych wdrożenia elementu członkowskiego. | |
    CONNECTIONSHAREDKEY | Wstępnie ustaloną klucz tajny między elementy członkowskie sieci konsorcjum, które są nawiązywania połączenia.  | |
    REMOTEMEMBERNVAPUBLICIP | Adres IP urządzenia NVA elementu członkowskiego. Tę wartość można znaleźć w danych wyjściowych wdrożenia elementu członkowskiego. | |
    MEMBERNVAPRIVATEIP | Liderów prywatny adres IP urządzenia NVA. Tę wartość można znaleźć w danych wyjściowych wdrożenia lidera. | |
    LOKALIZACJA | Lokalizacja środowiska Azure Stack. | | lokalne
    BASEURL | Podstawowy adres URL dla szablonu. | Użyj wartości domyślnej, chyba że chcesz dostosować szablony wdrażania. | 

7. Kliknij przycisk **OK**.
8. W **wdrożenie niestandardowe**, określ **subskrypcji**, **grupy zasobów**, i **lokalizację grupy zasobów**.
    
    ![Łączenie parametrów wdrożenia](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    Subskrypcja | Subskrypcja lidera. | | Użycie subskrypcji
    Grupa zasobów | Grupa zasobów lidera. | | EthereumResources
    Lokalizacja | Region platformy Azure dla grupy zasobów. | | lokalne

8. Wybierz pozycję **Utwórz**.

Po zakończeniu wdrożenia zajmuje kilka minut, zanim lidera i elementów członkowskich rozpocząć komunikacji. Aby zweryfikować wdrożenie, Odśwież witrynę administratora elementu członkowskiego. Stan elementu członkowskiego węzłów powinna być uruchomiona. 

![Weryfikacja wdrożenia](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat Ethereum i na platformie Azure, zobacz [technologia łańcucha bloków i aplikacje | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Aby uzyskać więcej informacji o scenariuszach łańcucha bloków na platformie Azure, zobacz [szablon rozwiązania konsorcjum dowód pracy Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).