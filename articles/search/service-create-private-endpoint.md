---
title: Utwórz prywatny punkt końcowy dla bezpiecznych połączeń
titleSuffix: Azure Cognitive Search
description: Obecnie w wersji zapoznawczej można ograniczyć dostęp do punktu końcowego usługi wyszukiwania przy użyciu prywatnego punktu końcowego i bezpiecznego połączenia sieci wirtualnej.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865014"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Ogranicz dostęp do usługi Azure Wyszukiwanie poznawcze przy użyciu prywatnego punktu końcowego i połączenia sieci wirtualnej.

> [!IMPORTANT] 
> Obsługa prywatnego punktu końcowego jest obecnie dostępna jako wersja zapoznawcza ograniczonego dostępu. Ta wersja zapoznawcza jest dostępna tylko dla usług wyszukiwania w **warstwie Podstawowa**.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-10-01 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

W tym artykule dowiesz się, jak utworzyć nową usługę wyszukiwania, która jest dostępna za pośrednictwem bezpiecznych połączeń, bez dostępu z publicznych adresów IP. Połączenia klientów są dozwolone z maszyn wirtualnych platformy Azure wdrożonych w tej samej sieci wirtualnej co usługa.

## <a name="about-private-endpoint-support"></a>Informacje o pomocy technicznej dotyczącej prywatnego punktu końcowego

[Prywatne punkty końcowe](../private-link/private-endpoint-overview.md) dla systemu Azure wyszukiwanie poznawcze umożliwiają klientowi w sieci wirtualnej bezpieczne uzyskiwanie dostępu do danych za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z [przestrzeni adresowej sieci wirtualnej](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania przechodzi przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft, eliminując narażenie na publiczny Internet. Aby zapoznać się z listą usług PaaS Services, które obsługują link prywatny, zapoznaj się z [sekcją dostępność](../private-link/private-link-overview.md#availability) w dokumentacji produktu.

Prywatny punkt końcowy usługi wyszukiwania umożliwia:

+ Zablokuj wszystkie połączenia w publicznym punkcie końcowym usługi wyszukiwania.
+ Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając zablokowanie eksfiltracji danych z sieci wirtualnej.
+ Bezpiecznie łącz się z usługą wyszukiwania z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

> [!NOTE]
> Gdy punkt końcowy usługi jest prywatny, niektóre funkcje portalu są wyłączone. Będziesz mieć możliwość wyświetlania informacji o poziomie usług i zarządzania nimi, ale dostęp portalu do danych indeksu i różne składniki usługi, takie jak indeks, indeksator i definicje zestawu umiejętności, są ograniczone ze względów bezpieczeństwa.

## <a name="request-access"></a>Żądanie dostępu 

Kliknij pozycję [Zażądaj dostępu](https://aka.ms/SearchPrivateLinkRequestAccess) , aby zarejestrować się w tej funkcji w wersji zapoznawczej. Formularz żąda informacji o użytkowniku, firmie i ogólnej topologii sieci. Po przejrzeniu Twojego żądania otrzymasz wiadomość e-mail z potwierdzeniem z dodatkowymi instrukcjami.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która zostanie użyta w celu uzyskania dostępu do prywatnego punktu końcowego usługi wyszukiwania.

### <a name="set-up-the-virtual-network"></a>Konfigurowanie sieci wirtualnej
1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. W lewym górnym rogu wybierz pozycję **Utwórz zasób** > **sieci** > **sieci wirtualnej**.
1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork* |
    | Przestrzeń adresowa | Wprowadź *10.1.0.0/16* |
    | Subskrypcja | Wybierz swoją subskrypcję|
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, wprowadź nazwę *zasobu*, a następnie wybierz przycisk **OK** . |
    | Lokalizacja | Wybierz **zachodnie stany USA** lub niezależnie od regionu, którego używasz|
    | Podsieć — nazwa | Wprowadź *maskę* |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24* |
    |||

1. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.


### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu strony głównej portalu wybierz pozycję **Utwórz zasób** > **COMPUTE** > **maszynie wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **zachodnie stany USA** lub region, którego używasz. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter**. |
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

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Utwórz maszynę wirtualną**, pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)** .|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Tworzenie usługi wyszukiwania za pomocą prywatnego punktu końcowego

W tej sekcji utworzysz nową usługę Wyszukiwanie poznawcze platformy Azure z prywatnym punktem końcowym. 

1. W lewym górnym rogu strony głównej portalu wybierz pozycję **Utwórz zasób** > **sieci Web** > **Azure wyszukiwanie poznawcze**.

1. W obszarze **nowe Search Service — podstawowe**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Adres URL | Wprowadź unikatową nazwę. |
    | Lokalizacja | Wybierz region określony podczas żądania dostępu do tej funkcji w wersji zapoznawczej. |
    | Warstwa cenowa | Wybierz pozycję **Zmień warstwę cenową** , a następnie wybierz pozycję **podstawowa**. Ta warstwa jest wymagana na potrzeby wersji zapoznawczej. |
    |||
  
1. Wybierz pozycję **Dalej: skala**.

1. Pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **nowy Search Service — sieć**wybierz pozycję **prywatne** dla **połączenia punktu końcowego (dane)** .

1. W obszarze **nowy Search Service — sieć**wybierz pozycję **+ Dodaj** w obszarze **prywatny punkt końcowy**. 

1. W obszarze **Utwórz prywatny punkt końcowy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | Lokalizacja | Wybierz pozycję **zachodnie stany USA**.|
    | Nazwa | Wprowadź *myPrivateEndpoint*.  |
    | Docelowy zasób podrzędny | Pozostaw wartość domyślną **searchService**. |
    | **SIECI** |  |
    | Sieć wirtualna  | Wybierz pozycję *MyVirtualNetwork* *z grupy zasobów*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    | **PRYWATNA INTEGRACJA Z USŁUGĄ DNS** |  |
    | Integracja z prywatną strefą DNS  | Pozostaw wartość domyślną **tak**. |
    | Prywatna strefa DNS  | Pozostaw wartość domyślną * * (New) privatelink.search.windows.net * *. |
    |||

1. Kliknij przycisk **OK**. 

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**. 
1. Po utworzeniu usługi przejdź do zasobu, który właśnie został utworzony.
1. Z menu po lewej stronie wybierz pozycję **klucze** .
1. Skopiuj **podstawowy klucz administratora** do użycia w następnym kroku.

 
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas logowania może zostać wyświetlone ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Dostęp do prywatnej usługi wyszukiwania z maszyny wirtualnej

W tej sekcji będziesz weryfikować dostęp do sieci prywatnej do usługi wyszukiwania i łączyć się prywatnie z kontem magazynu przy użyciu prywatnego punktu końcowego.

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.

1. Wprowadź ciąg "nslookup [nazwa usługi wyszukiwania]. Search. Windows. NET"

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. Postępuj zgodnie z tym [przewodnikiem Szybki Start](search-get-started-postman.md) z maszyny wirtualnej, aby utworzyć nowy indeks wyszukiwania w usłudze na stronie post przy użyciu interfejsu API REST.

1. Wypróbuj kilka z tych samych żądań w programie Poster na lokalnej stacji roboczej.

1. Jeśli masz możliwość ukończenia szybkiego startu z poziomu maszyny wirtualnej, ale otrzymasz błąd, że serwer zdalny nie istnieje na lokalnej stacji roboczej, pomyślnie skonfigurowano prywatny punkt końcowy dla usługi wyszukiwania.

1. Zamknij połączenie pulpitu zdalnego z *myVM*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Gdy skończysz korzystać z prywatnego punktu końcowego, konta usługi wyszukiwania i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 

1. Wprowadź * * w polu **wyszukiwania** w górnej części portalu, a następnie wybierz pozycję Lista *zasobów* z wyników wyszukiwania. 
1. Wybierz pozycję **Usuń grupę zasobów**. 
1. Wprowadź dla elementu *Webresources* , aby **wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono MASZYNę wirtualną w sieci wirtualnej i usługę wyszukiwania z prywatnym punktem końcowym. Nawiązano połączenie z maszyną wirtualną z Internetu i bezpiecznie komunikuje się z usługą wyszukiwania za pomocą linku prywatnego. 

> [!div class="nextstepaction"]
> [Co to jest prywatny punkt końcowy platformy Azure?](../private-link/private-endpoint-overview.md)
