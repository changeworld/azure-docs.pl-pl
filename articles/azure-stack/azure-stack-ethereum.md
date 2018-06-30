---
title: Azure szablon rozwiązania Ethereum stosu
description: Szablony niestandardowe rozwiązanie umożliwia wdrażanie i konfigurowanie sieci Ethereum konsorcjum na stosie Azure
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114735"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Szablony usługi Azure rozwiązania Ethereum stosu

Szablon rozwiązania Ethereum zaprojektowano w celu umożliwiają łatwiejsze i szybsze wdrażanie i konfigurowanie sieci Ethereum konsorcjum wiele elementów członkowskich przy minimalnej znajomości Azure i Ethereum.

Niewielki podzbiór danych wprowadzonych przez użytkownika i wdrożenia jednym kliknięciem za pośrednictwem portalu Azure stosu administratora każdy element członkowski mogą zapewnić obsługę administracyjną ich rozmiaru sieci. Każdy element członkowski rozmiaru sieci zawiera zestaw węzłów transakcji z równoważeniem obciążenia, z którego aplikacji lub użytkownika mogą współdziałać przesłać transakcje, zestaw węzłów wyszukiwania do transakcji rekordów i wirtualne urządzenia sieci (NVA). Krok kolejne połączenie łączy NVAs utworzyć sieć w pełni skonfigurowany blockchain wiele elementów członkowskich.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz następujące [z witryny Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS wersji 16.04.201802220
* Windows Server 2016 
* Skryptu niestandardowego dla systemu Linux 2.0 
* Rozszerzenie niestandardowego skryptu 

Aby uzyskać więcej informacji na temat scenariuszy blockchain na platformie Azure, zobacz [szablon rozwiązania konsorcjum dowód pracy Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

Subskrypcja platformy Azure, który może obsługiwać wdrażania wielu maszyn wirtualnych jest wymagany. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="deployment-architecture"></a>Architektura wdrażania

Ten szablon rozwiązania można wdrożyć na jednym lub wielu sieci konsorcjum Ethereum elementu członkowskiego. Sieć wirtualna jest połączony w topologii łańcucha przy użyciu zasobów sieciowych, urządzenie wirtualne i połączenia. 

## <a name="deployment-use-cases"></a>Przypadki użycia wdrożenia

Szablonu można wdrożyć konsorcjum Ethereum wiodące i Dołącz do elementu członkowskiego na różne sposoby, w tym miejscu są tymi, które przetestowano:
- Na stosie Azure wielowęzłowego z usługi Azure AD lub AD FS, należy wdrożyć potencjalnych klientów i elementu członkowskiego przy użyciu tej samej subskrypcji lub różnych subskrypcji.
- Na stosie Azure jednowęzłowej (z usługą Azure AD) wdrażanie potencjalnych klientów i elementu członkowskiego przy użyciu tej samej subskrypcji.

### <a name="standalone-and-consortium-leader-deployment"></a>Autonomiczne i konsorcjum wdrożenia wiodące

Szablon wiodące konsorcjum konfiguruje wpływ pierwszego elementu członkowskiego w sieci. 

1. Pobierz [wiodące szablonu z serwisu GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. W portalu administracyjnego platformy Azure stosu wybierz **nowy > wdrażania szablonu** do wdrożenia z szablonu niestandardowego.
3. Wybierz **Edytuj szablon** do edycji nowy szablon niestandardowy.
4. W okienku edycji po prawej stronie skopiuj i Wklej szablonu wiodące JSON, które zostały wcześniej pobrane.
    
    ![Edytuj szablon wiodące](media/azure-stack-ethereum/edit-leader-template.png)

5. Wybierz pozycję **Zapisz**.
6. Wybierz **Edytuj parametry** i ukończyć parametrów szablonu dla danego wdrożenia.
    
    ![Edytuj wiodące parametry szablonu](media/azure-stack-ethereum/edit-leader-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Parametry używane jako podstawa nazewnictwa wdrożonych zasobów. | Znaki alfanumeryczne o długości od 1 do 6 | ETH
    TYP | Metoda uwierzytelniania do maszyny wirtualnej. | Hasło lub SSH klucza publicznego | Hasło
    ADMINUSERNAME | Nazwa użytkownika administratora każdej wdrożonej maszyny wirtualnej | 1 - 64 znaków | gethadmin
    ADMINPASSWORD (typ uwierzytelniania = hasło)| Hasło dla konta administratora dla każdego z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 następujących wymagań: 1 wielką literą, 1 małe litery, liczby 1 i 1 znak specjalny. <br />Gdy wszystkie maszyny wirtualne mają początkowo to samo hasło, należy zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|
    ADMINSSHKEY (typ uwierzytelniania = parametry sshPublicKey) | Klucz bezpiecznego powłoki używane do logowania zdalnego. | |
    GENESISBLOCK | Ciąg JSON reprezentujący niestandardowe genesis bloku. | |
    ETHEREUMACCOUNTPSSWD | Hasło administratora, używany do zabezpieczania konta Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | Hasło używane do generowania klucza prywatnego skojarzonego z kontem Ethereum. | |
    ETHEREUMNETWORKID | Identyfikator sieci konsorcjum. | Użyj wartości od 5 do 999,999,999 | 72
    CONSORTIUMMEMBERID | Identyfikator skojarzony z każdego członka konsorcjum sieci.   | Ten identyfikator powinien być unikatowy w sieci. | 0
    NUMMININGNODES | Liczba węzłów wyszukiwania. | Pomiędzy 2 a 15. | 2
    MNNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach wyszukiwania. | | Standardowa_A1
    MNSTORAGEACCOUNTTYPE | Wydajność magazynu węzłami wyszukiwania. | | Standard_LRS
    NUMTXNODES | Liczba węzłów transakcji. | Od 1 do 5. | 1
    TXNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach transakcji. | | Standardowa_A1
    TXSTORAGEACCOUNTTYPE | Wydajność magazynu węzłami transakcji. | | Standard_LRS
    BASEURL | Bazowy adres URL do pobrania szablonów w zależności od. | Użyj wartości domyślnej, chyba że chcesz dostosować szablony wdrażania. | 

7. Kliknij przycisk **OK**.
8. W **wdrożenie niestandardowe**, określ **subskrypcji**, **grupy zasobów**, i **lokalizacja grupy zasobów**.
    
    ![Wiodące parametry wdrożenia](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    Subskrypcja | Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum | | Zużycie subskrypcji
    Grupa zasobów | Grupy zasobów, dla której chcesz wdrożyć konsorcjum sieci. | | EthereumResources
    Lokalizacja | Region platformy Azure dla grupy zasobów. | | lokalne

8. Wybierz pozycję **Utwórz**.

Wdrożenie może trwać 20 minut lub dłużej.

Po zakończeniu wdrażania możesz przejrzeć podsumowanie wdrożenia **firmy Microsoft. Szablon** w sekcji wdrożenia grupy zasobów. Podsumowanie zawiera wartości danych wyjściowych, które mogą służyć do dołączenia elementy członkowskie konsorcjum.

Aby sprawdzić jego wiodące wdrożenia, przejdź wiodące przez administratora lokacji. Adres witryny administracyjnego można znaleźć w sekcji Wyjście **Microsoft.Template** wdrożenia.  

![Wiodącymi wdrażania](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Sprzęganie konsorcjum wdrażania elementu członkowskiego

1. Pobierz [konsorcjum szablon elementu członkowskiego z usługi GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. W portalu administracyjnego platformy Azure stosu wybierz **nowy > wdrażania szablonu** do wdrożenia z szablonu niestandardowego.
3. Wybierz **Edytuj szablon** do edycji nowy szablon niestandardowy.
4. W okienku edycji po prawej stronie skopiuj i Wklej szablonu wiodące JSON pobrane wcześniej.
5. Wybierz pozycję **Zapisz**.
6. Wybierz **Edytuj parametry** i ukończyć parametrów szablonu dla danego wdrożenia.

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Parametry używane jako podstawa nazewnictwa wdrożonych zasobów. | Znaki alfanumeryczne o długości od 1 do 6 | ETH
    TYP | Metoda uwierzytelniania do maszyny wirtualnej. | Hasło lub SSH klucza publicznego | Hasło
    ADMINUSERNAME | Nazwa użytkownika administratora każdej wdrożonej maszyny wirtualnej | 1 - 64 znaków | gethadmin
    ADMINPASSWORD (typ uwierzytelniania = hasło)| Hasło dla konta administratora dla każdego z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 następujących wymagań: 1 wielką literą, 1 małe litery, liczby 1 i 1 znak specjalny. <br />Gdy wszystkie maszyny wirtualne mają początkowo to samo hasło, należy zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|
    ADMINSSHKEY (typ uwierzytelniania = parametry sshPublicKey) | Klucz bezpiecznego powłoki używane do logowania zdalnego. | |
    CONSORTIUMMEMBERID | Identyfikator skojarzony z każdego członka konsorcjum sieci.   | Ten identyfikator powinien być unikatowy w sieci. | 0
    NUMMININGNODES | Liczba węzłów wyszukiwania. | Pomiędzy 2 a 15. | 2
    MNNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach wyszukiwania. | | Standardowa_A1
    MNSTORAGEACCOUNTTYPE | Wydajność magazynu węzłami wyszukiwania. | | Standard_LRS
    NUMTXNODES | Liczba węzłów transakcji. | Od 1 do 5. | 1
    TXNODEVMSIZE | Rozmiar maszyny Wirtualnej w węzłach transakcji. | | Standardowa_A1
    TXSTORAGEACCOUNTTYPE | Wydajność magazynu węzłami transakcji. | | Standard_LRS
    CONSORTIUMDATA | Adres URL wskazujący konsorcjum odpowiednie dane konfiguracji udostępniane przez innego członka wdrożenia. Tę wartość można znaleźć w wiodące w danych wyjściowych z wdrożenia. | |
    REMOTEMEMBERVNETADDRESSSPACE | Adres NVA IP wiodące. Tę wartość można znaleźć w wiodące w danych wyjściowych z wdrożenia. | | 
    REMOTEMEMBERNVAPUBLICIP | Adres NVA IP wiodące. Tę wartość można znaleźć w wiodące w danych wyjściowych z wdrożenia. | | 
    CONNECTIONSHAREDKEY | Wstępnie ustalonych klucz tajny między elementami członkowskimi konsorcjum sieci, które są ustanawiania połączenia. | |
    BASEURL | Podstawowy adres URL dla szablonu. | Użyj wartości domyślnej, chyba że chcesz dostosować szablony wdrażania. | 

7. Kliknij przycisk **OK**.
8. W **wdrożenie niestandardowe**, określ **subskrypcji**, **grupy zasobów**, i **lokalizacja grupy zasobów**.

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    Subskrypcja | Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum | | Zużycie subskrypcji
    Grupa zasobów | Grupy zasobów, dla której chcesz wdrożyć konsorcjum sieci. | | MemberResources
    Lokalizacja | Region platformy Azure dla grupy zasobów. | | lokalne

8. Wybierz pozycję **Utwórz**.

Wdrożenie może trwać 20 minut lub dłużej.

Po zakończeniu wdrażania możesz przejrzeć podsumowanie wdrożenia **Microsoft.Template** w sekcji wdrożenia grupy zasobów. Podsumowanie zawiera wartości danych wyjściowych, które można połączyć z konsorcjum członków.

Aby zweryfikować wdrożenie członka, przeglądanie witryny administratora elementu członkowskiego. Adres witryny administracyjnego można znaleźć w sekcji Wyjście Microsoft.Template wdrożenia.

![Podsumowania wdrożenia elementu członkowskiego](media/azure-stack-ethereum/ethereum-node-status-2.png)

Jak pokazano na rysunku, stan węzły elementu członkowskiego jest **nieuruchomiona**. Jest to spowodowane nie zostanie nawiązane połączenie między elementy członkowskie i wiodące. Połączenie między elementy członkowskie i wiodące jest dwukierunkowe połączenie. Podczas wdrażania elementu członkowskiego szablonu automatycznie tworzy połączenie z elementu członkowskiego do wiodące. Aby utworzyć połączenie z wiodącymi do elementu członkowskiego, przejdź do następnego kroku.

### <a name="connect-member-and-leader"></a>Łączenie elementu członkowskiego i znaki wiodące

Ten szablon tworzy połączenie z kierujący do zdalnego elementu członkowskiego. 

1. Pobierz [połączyć szablonu elementu członkowskiego i wiodące z usługi GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. W portalu administracyjnego platformy Azure stosu wybierz **nowy > wdrażania szablonu** do wdrożenia z szablonu niestandardowego.
3. Wybierz **Edytuj szablon** do edycji nowy szablon niestandardowy.
4. W okienku edycji po prawej stronie skopiuj i Wklej szablonu wiodące JSON pobrane wcześniej.
    
    ![Edytuj połączenie szablonu](media/azure-stack-ethereum/edit-connect-template.png)

5. Wybierz pozycję **Zapisz**.
6. Wybierz **Edytuj parametry** i ukończyć parametrów szablonu dla danego wdrożenia.
    
    ![Edytuj połączenie parametrów szablonu](media/azure-stack-ethereum/edit-connect-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefiks nazwy firmy wiodące. Tę wartość można znaleźć w wiodące w danych wyjściowych z wdrożenia.  | Znaki alfanumeryczne o długości od 1 do 6 | |
    MEMBERROUTETABLENAME | Nazwa tabeli tras wiodące. Tę wartość można znaleźć w wiodące w danych wyjściowych z wdrożenia. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adres miejsca elementu członkowskiego. Tę wartość można znaleźć w danych wyjściowych wdrażania elementu członkowskiego. | |
    CONNECTIONSHAREDKEY | Wstępnie ustalonych klucz tajny między elementami członkowskimi konsorcjum sieci, które są ustanawiania połączenia.  | |
    REMOTEMEMBERNVAPUBLICIP | Adres NVA IP elementu członkowskiego. Tę wartość można znaleźć w danych wyjściowych wdrażania elementu członkowskiego. | |
    MEMBERNVAPRIVATEIP | Wiodącymi przez prywatnego adresu NVA IP. Tę wartość można znaleźć w wiodące w danych wyjściowych z wdrożenia. | |
    LOKALIZACJA | Lokalizacja środowiska Azure stosu. | | lokalne
    BASEURL | Podstawowy adres URL dla szablonu. | Użyj wartości domyślnej, chyba że chcesz dostosować szablony wdrażania. | 

7. Kliknij przycisk **OK**.
8. W **wdrożenie niestandardowe**, określ **subskrypcji**, **grupy zasobów**, i **lokalizacja grupy zasobów**.
    
    ![Połącz parametry wdrożenia](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nazwa parametru | Opis | Dozwolone wartości | Wartość przykładowa
    ---------------|-------------|----------------|-------------
    Subskrypcja | Wiodącymi subskrypcji. | | Zużycie subskrypcji
    Grupa zasobów | Wiodącymi grupy zasobów. | | EthereumResources
    Lokalizacja | Region platformy Azure dla grupy zasobów. | | lokalne

8. Wybierz pozycję **Utwórz**.

Po zakończeniu wdrażania zajmuje kilka minut, aż wiodące i element członkowski uruchomić komunikacji. Aby zweryfikować wdrożenie, Odśwież użytkownika administratora witryny. Stan elementu członkowskiego węzłów powinna być uruchomiona. 

![Weryfikacja wdrażania](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat Ethereum i Azure, zobacz [Blockchain technologii i aplikacji | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Aby uzyskać więcej informacji na temat scenariuszy blockchain na platformie Azure, zobacz [szablon rozwiązania konsorcjum dowód pracy Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).