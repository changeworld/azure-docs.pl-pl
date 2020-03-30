---
title: Tworzenie prywatnego punktu końcowego dla bezpiecznego połączenia
titleSuffix: Azure Cognitive Search
description: Konfigurowanie prywatnego punktu końcowego w sieci wirtualnej w celu bezpiecznego połączenia z usługą Azure Cognitive Search
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945821"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Tworzenie prywatnego punktu końcowego dla bezpiecznego połączenia z usługą Azure Cognitive Search (Wersja zapoznawcza)

W tym artykule użyj portalu, aby utworzyć nowe wystąpienie usługi Azure Cognitive Search, do którego nie można uzyskać dostępu za pośrednictwem publicznego adresu IP. Następnie skonfiguruj maszynę wirtualną platformy Azure w tej samej sieci wirtualnej i użyj jej do uzyskania dostępu do usługi wyszukiwania za pośrednictwem prywatnego punktu końcowego.

> [!Important]
> Prywatna obsługa punktów końcowych dla usługi Azure Cognitive Search jest dostępna [na żądanie](https://aka.ms/SearchPrivateLinkRequestAccess) jako wersja zapoznawcza z ograniczonym dostępem. Funkcje w wersji zapoznawczej są dostarczane bez umowy dotyczącej poziomu usług i nie są zalecane dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Po udzieleniu dostępu do wersji zapoznawczej będzie można skonfigurować prywatne punkty końcowe dla usługi za pomocą portalu Azure lub [interfejsu API Management REST w wersji 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Dlaczego warto korzystać z prywatnego punktu końcowego dla bezpiecznego dostępu?

[Prywatne punkty końcowe](../private-link/private-endpoint-overview.md) dla usługi Azure Cognitive Search umożliwiają klientowi w sieci wirtualnej bezpieczny dostęp do danych w indeksie wyszukiwania za pośrednictwem [łącza prywatnego.](../private-link/private-link-overview.md) Prywatny punkt końcowy używa adresu IP z [przestrzeni adresowej sieci wirtualnej](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania przechodzi przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu. Aby uzyskać listę innych usług PaaS obsługujących łącze prywatne, zapoznaj się z [sekcją dostępności](../private-link/private-link-overview.md#availability) w dokumentacji produktu.

Prywatne punkty końcowe usługi wyszukiwania umożliwiają:

- Blokuj wszystkie połączenia w publicznym punkcie końcowym usługi wyszukiwania.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej.
- Bezpiecznie połącz się z usługą wyszukiwania z sieci lokalnych, które łączą się z siecią wirtualną za pomocą [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoutes](../expressroute/expressroute-locations.md) za pomocą prywatnej komunikacji równorzędnej.

> [!NOTE]
> Obecnie istnieją pewne ograniczenia w podglądzie, o których należy pamiętać:
> * Dostępne tylko dla usług wyszukiwania w warstwie **Podstawowa.** 
> * Dostępne w regionach Zachodnie stany USA 2, Zachodnie środkowe stany USA, Wschodnie stany USA, Południowo-środkowe stany USA, Australia Wschodnia i Australia Południowo-Wschodnie.
> * Gdy punkt końcowy usługi jest prywatny, niektóre funkcje portalu są wyłączone. Będziesz mógł wyświetlać informacje o poziomie usług i zarządzać nimi, ale dostęp do danych indeksu i różnych składników w usłudze, takich jak definicje indeksu, indeksatora i zestawów umiejętności, jest ograniczony ze względów bezpieczeństwa.
> * Gdy punkt końcowy usługi jest prywatny, należy użyć [interfejsu API REST wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) do przekazywania dokumentów do indeksu.
> * Aby wyświetlić opcję obsługi prywatnego punktu końcowego w witrynie Azure portal, należy użyć następującego łącza:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Żądanie dostępu 

Kliknij przycisk Uzyskaj dostęp do [żądania,](https://aka.ms/SearchPrivateLinkRequestAccess) aby zarejestrować się w tej funkcji podglądu. Formularz żąda informacji o Tobie, Firmie i ogólnej topologii sieci. Gdy rozpatrzymy Twoją prośbę, otrzymasz wiadomość e-mail z potwierdzeniem wraz z dodatkowymi instrukcjami.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która będzie używana do uzyskiwania dostępu do prywatnego punktu końcowego usługi wyszukiwania.

1. Na karcie Narzędzia główne portalu platformy Azure wybierz pozycję **Utwórz zasób** > **Sieć wirtualna sieci** > **Virtual network**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork* |
    | Przestrzeń adresowa | Wprowadź *10.1.0.0/16* |
    | Subskrypcja | Wybierz swoją subskrypcję|
    | Grupa zasobów | Wybierz **pozycję Utwórz nowy**, wprowadź *myResourceGroup*, a następnie wybierz przycisk **OK** |
    | Lokalizacja | Wybierz **zachodnie stany USA** lub region, z którego korzystasz|
    | Podsieć — nazwa | Wprowadź *mySubnet* |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24* |
    |||

1. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Tworzenie usługi wyszukiwania z prywatnym punktem końcowym

W tej sekcji utworzysz nową usługę Azure Cognitive Search z prywatnym punktem końcowym. 

1. W lewym górnym rogu ekranu w witrynie Azure portal wybierz pozycję **Utwórz zasób** > **Web** > **Azure Cognitive Search**.

1. W **nowej usłudze wyszukiwania — podstawy**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Adres URL | Wprowadź unikatową nazwę. |
    | Lokalizacja | Wybierz region określony podczas żądania dostępu do tej funkcji w wersji zapoznawczej. |
    | Warstwa cenowa | Wybierz **pozycję Zmień warstwę cenową** i wybierz pozycję **Podstawowe**. Ta warstwa jest wymagana dla wersji zapoznawczej. |
    |||
  
1. Wybierz **dalej: Skaluj**.

1. Pozostaw wartości jako domyślne i wybierz **dalej: Sieć**.

1. W **obszarze Nowa usługa wyszukiwania — sieć**wybierz pozycję **Private** for **Endpoint connectivity(data)**.

1. W **obszarze Nowa usługa wyszukiwania — sieć**wybierz pozycję + **Dodaj** w obszarze Prywatny **punkt końcowy**. 

1. W **obszarze Tworzenie prywatnego punktu końcowego**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | Lokalizacja | wybierz pozycję **Zachodnie stany USA**.|
    | Nazwa | Wprowadź *myPrivateEndpoint*.  |
    | Docelowy podsób | Pozostaw **domyślne wyszukiwanieSługa**. |
    | **Sieci** |  |
    | Sieć wirtualna  | Wybierz *MyVirtualNetwork* z grupy zasobów *myResourceGroup*. |
    | Podsieć | Wybierz *mySubnet*. |
    | **PRYWATNA INTEGRACJA Z SYSTEMEM DNS** |  |
    | Integracja z prywatną strefą DNS  | Pozostaw domyślny **Tak**. |
    | Prywatna strefa DNS  | Pozostaw domyślną wartość ** (Nowy) privatelink.search.windows.net**. |
    |||

1. Kliknij przycisk **OK**. 

1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 

1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**. 

1. Po zakończeniu inicjowania obsługi administracyjnej nowej usługi przejdź do zasobu, który właśnie został utworzony.

1. Wybierz **klawisze** z lewego menu zawartości.

1. Skopiuj **podstawowy klucz administracyjny** na później, podczas łączenia się z usługą.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję Utwórz**maszynę wirtualną****obliczeniową** >  **zasobów** > .

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wpisz *myVm*. |
    | Region | Wybierz **zachodnie stany USA** lub dowolny region, którego używasz. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image (Obraz) | Wybierz **pozycję Centrum danych systemu Windows Server 2019**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw domyślny **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Pozostaw domyślny **prow (3389)**. |
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


## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Pobierz, a następnie połącz się z maszyną wirtualną *myVm* w następujący sposób:

1. W pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik.rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

        > [!NOTE]
        > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  


## <a name="test-connections"></a>Połączenia testowe

W tej sekcji zweryfikujesz dostęp do sieci prywatnej do usługi wyszukiwania i połączysz się prywatnie z za pomocą prywatnego punktu końcowego.

Przypomnijmy, że wszystkie interakcje z usługą wyszukiwania wymagają [interfejsu API REST wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/). Portal i plik .NET SDK nie są obsługiwane w tym podglądzie.

1. W pulpicie zdalnym *myVM*otwórz program PowerShell.

1. Wprowadź 'nslookup [nazwa usługi wyszukiwania].search.windows.net'

    Otrzymasz wiadomość podobną do tej:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Z maszyny Wirtualnej, połączyć się z usługą wyszukiwania i utworzyć indeks. Możesz wykonać ten [szybki start,](search-get-started-postman.md) aby utworzyć nowy indeks wyszukiwania w usłudze w postman przy użyciu interfejsu API REST. Konfigurowanie żądań z postmana wymaga punktu końcowego usługi wyszukiwania (https://[nazwa usługi wyszukiwania].search.windows.net) i klucza interfejsu api administratora skopiowanego w poprzednim kroku.

1. Ukończenie szybkiego startu z maszyny Wirtualnej jest potwierdzeniem, że usługa jest w pełni operacyjna.

1. Zamknij połączenie pulpitu zdalnego do *myVM*. 

1. Aby sprawdzić, czy usługa nie jest dostępna w publicznym punkcie końcowym, otwórz listonosz na lokalnej stacji roboczej i spróbuj pierwszych kilku zadań w przewodniku Szybki start. Jeśli zostanie wyświetlony błąd, że serwer zdalny nie istnieje, pomyślnie skonfigurowano prywatny punkt końcowy dla usługi wyszukiwania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Po zakończeniu korzystania z prywatnego punktu końcowego, usługi wyszukiwania i maszyny Wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera:
1. Wprowadź *myResourceGroup* w polu **wyszukiwania** u góry portalu i wybierz *myResourceGroup* z wyników wyszukiwania. 
1. Wybierz pozycję **Usuń grupę zasobów**. 
1. Wprowadź *myResourceGroup* dla **TYPU NAZWA GRUPY ZASOBÓW** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono maszynę wirtualną w sieci wirtualnej i usługę wyszukiwania z prywatnym punktem końcowym. Połączenie z maszyną wirtualną z Internetu i bezpieczne komunikowanie się z usługą wyszukiwania przy użyciu łącza prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [Co to jest prywatny punkt końcowy platformy Azure?](../private-link/private-endpoint-overview.md).
