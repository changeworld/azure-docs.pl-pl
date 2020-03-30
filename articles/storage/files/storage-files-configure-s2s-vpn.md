---
title: Konfigurowanie sieci VPN typu lokacja lokacja (S2S) do użytku z usługą Azure Files | Dokumenty firmy Microsoft
description: Jak skonfigurować sieć VPN lokacja lokacja (S2S) do użytku z usługą Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae3d38d92990d7a1af4146c25b017286ebd29352
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061041"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu lokacja lokacja do użytku z plikami platformy Azure
Za pomocą połączenia sieci VPN lokacja lokacja (S2S) można zainstalować udziały plików platformy Azure za pośrednictwem protokołu SMB z sieci lokalnej bez otwierania portu 445. Sieć VPN typu lokacja lokacja lokacja umożliwia korzystanie z [usługi Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), która jest zasobem platformy Azure oferującym usługi sieci VPN i jest wdrażana w grupie zasobów wraz z kontami magazynu lub innymi zasobami platformy Azure.

![Wykres topologii ilustrujący topologię bramy sieci VPN platformy Azure łączącej udział plików platformy Azure z witryną lokalną przy użyciu sieci VPN S2S](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Zdecydowanie zaleca się [przeczytanie przeglądu sieci usługi Azure Files](storage-files-networking-overview.md) przed kontynuowaniem tego artykułu, aby uzyskać pełną dyskusję na temat opcji sieci dostępnych dla usług Azure Files.

W artykule opisano kroki konfigurowania sieci VPN lokacja lokacji do instalowania udziałów plików platformy Azure bezpośrednio lokalnie. Jeśli chcesz przekierować ruch synchronizacji dla usługi Azure File Sync za pośrednictwem sieci VPN typu lokacja lokacja, zobacz [konfigurowanie ustawień serwera proxy i zapory usługi Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzania reprezentującymi współużytkową pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Więcej informacji na temat wdrażania udziałów plików i kont magazynu platformy Azure można dowiedzieć się więcej w obszarze [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. Aby dowiedzieć się więcej o tworzeniu prywatnego punktu końcowego, zobacz [Konfigurowanie sieciowych punktów końcowych usługi Azure Files](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Urządzenie sieciowe lub serwer w lokalnym centrum danych, które jest zgodne z usługą Azure VPN Gateway. Usługa Azure Files jest niezależna od wybranego lokalnego urządzenia sieciowego, ale usługa Azure VPN Gateway przechowuje [listę przetestowanych urządzeń.](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) Różne urządzenia sieciowe oferują różne funkcje, charakterystykę wydajności i funkcje zarządzania, dlatego należy wziąć je pod uwagę przy wyborze urządzenia sieciowego.

    Jeśli nie masz istniejącego urządzenia sieciowego, system Windows Server zawiera wbudowaną funkcję serwera, routing i dostęp zdalny (RRAS), który może być używany jako lokalne urządzenie sieciowe. Aby dowiedzieć się więcej o konfigurowaniu usługi Routing i dostępu zdalnego w systemie Windows Server, zobacz [Brama RAS](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Dodawanie konta magazynu do sieci wirtualnej
W witrynie Azure portal przejdź do konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. W spisie treści dla konta magazynu wybierz wpis **Zapory i sieci wirtualne.** Jeśli nie dodano sieci wirtualnej do konta magazynu podczas jej tworzenia, okienko wynikowe powinno mieć wybrany przycisk **Opcji Zezwalaj na dostęp z** wszystkich **sieci.**

Aby dodać konto magazynu do żądanej sieci wirtualnej, wybierz pozycję **Wybrane sieci**. W podpozycji **Sieci wirtualne** kliknij opcję **+ Dodaj istniejącą sieć wirtualną** lub **+Dodaj nową sieć wirtualną** w zależności od żądanego stanu. Utworzenie nowej sieci wirtualnej spowoduje utworzenie nowego zasobu platformy Azure. Nowy lub istniejący zasób sieci wirtualnej nie musi znajdować się w tej samej grupie zasobów lub subskrypcji co konto magazynu, jednak musi znajdować się w tym samym regionie co konto magazynu, a grupa zasobów i subskrypcja, w której wdrażasz swoją witrynę wirtualną, muszą być zgodne z tym, w jakim będzie wdrożyć bramę sieci VPN. 

![Zrzut ekranu przedstawiający witrynę Azure portal, w ramach usługi Dodawania istniejącej lub nowej sieci wirtualnej do konta magazynu](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Jeśli dodasz istniejącą sieć wirtualną, zostaniesz poproszony o wybranie jednej lub więcej podsieci tej sieci wirtualnej, do której konto magazynu powinno zostać dodane. Jeśli wybierzesz nową sieć wirtualną, utworzysz podsieć jako część tworzenia sieci wirtualnej i możesz dodać więcej później za pośrednictwem wynikowego zasobu platformy Azure dla sieci wirtualnej.

Jeśli konto magazynu nie zostało wcześniej dodane do subskrypcji, punkt końcowy usługi Microsoft.Storage będzie musiał zostać dodany do sieci wirtualnej. Może to zająć trochę czasu, a dopóki ta operacja nie zostanie zakończona, nie będzie można uzyskać dostępu do udziałów plików platformy Azure w ramach tego konta magazynu, w tym za pośrednictwem połączenia sieci VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Wdrażanie bramy sieci VPN platformy Azure
W spisie treści witryny Azure Portal wybierz pozycję **Utwórz nowy zasób** i wyszukaj *bramę sieci wirtualnej*. Brama sieci wirtualnej musi znajdować się w tej samej subskrypcji, regionie platformy Azure i grupie zasobów, co sieć wirtualna wdrożona w poprzednim kroku (zwróć uwagę, że grupa zasobów jest automatycznie wybierana podczas wybierania sieci wirtualnej). 

Na potrzeby wdrażania bramy sieci VPN platformy Azure należy wypełnić następujące pola:

- **Nazwa**: Nazwa zasobu platformy Azure dla bramy sieci VPN. Ta nazwa może być dowolną nazwą, która jest przydatna dla twojego kierownictwa.
- **Region:** region, w którym zostanie wdrożona brama sieci VPN.
- **Typ bramy:** W celu wdrożenia sieci VPN lokacja lokacja należy wybrać **sieć VPN**.
- **Typ SIECI VPN:** możesz wybrać trasę *** lub **zasadę opartą** na twoim urządzeniu VPN. Sieci VPN oparte na trasach obsługują IKEv2, podczas gdy sieci VPN oparte na zasadach obsługują tylko łańców IKEv1. Aby dowiedzieć się więcej o dwóch typach bram sieci VPN, zobacz [Bramy sieci VPN oparte na zasadach i trasach](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **Jednostka SKU:** Jednostka SKU kontroluje liczbę dozwolonych tuneli lokacja-lokacja i żądaną wydajność sieci VPN. Aby wybrać odpowiednią jednostkę SKU dla danego przypadku użycia, zapoznaj się z listą [jednostek SKU bramy.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) Jednostka SKU bramy sieci VPN może zostać później zmieniona, jeśli to konieczne.
- **Sieć wirtualna:** sieć wirtualna utworzona w poprzednim kroku.
- **Publiczny adres IP:** Adres IP bramy sieci VPN, który będzie narażony na działanie Internetu. Prawdopodobnie trzeba będzie utworzyć nowy adres IP, jednak można również użyć istniejącego nieużytego adresu IP, jeśli jest to właściwe. Jeśli wybierzesz **pozycję Utwórz nowy,** nowy zasób platformy Azure adresu IP zostanie utworzony w tej samej grupie zasobów co brama sieci VPN, a **nazwa publicznego adresu IP** będzie nazwą nowo utworzonego adresu IP. W przypadku wybrania opcji **Użyj istniejącego**należy wybrać istniejący nieużyny adres IP.
- **Włącz tryb aktywny-aktywny:** Tylko wybrać **Opcję Włącz,** jeśli tworzysz konfigurację bramy aktywny-aktywny, w przeciwnym razie pozostaw **zaznaczone Wyłączone.** Aby dowiedzieć się więcej o trybie aktywnym i aktywnym, zobacz [Wysoce dostępne połączenia międzylokacyjnymi i sieci wirtualnej z siecią wirtualną](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Konfigurowanie asn BGP:** Wybierz **opcję Włączone** tylko wtedy, gdy konfiguracja wymaga tego ustawienia. Aby dowiedzieć się więcej o tym ustawieniu, zobacz [BGP z usługą Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)— informacje .

Wybierz **pozycję Recenzja + utwórz,** aby utworzyć bramę sieci VPN. Pełne utworzenie i wdrożenie bramy sieci VPN może potrwać do 45 minut.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Tworzenie bramy sieci lokalnej dla bramy lokalnej 
Brama sieci lokalnej to zasób platformy Azure reprezentujący lokalne urządzenie sieciowe. W spisie treści witryny Azure Portal wybierz pozycję **Utwórz nowy zasób** i wyszukaj *bramę sieci lokalnej*. Brama sieci lokalnej to zasób platformy Azure, który zostanie wdrożony wraz z kontem magazynu, siecią wirtualną i bramą sieci VPN, ale nie musi znajdować się w tej samej grupie zasobów lub subskrypcji co konto magazynu. 

Na potrzeby wdrażania zasobu bramy sieci lokalnej należy wypełnić następujące pola:

- **Nazwa:** Nazwa zasobu platformy Azure dla bramy sieci lokalnej. Ta nazwa może być dowolną nazwą, która jest przydatna dla twojego kierownictwa.
- **Adres IP:** publiczny adres IP bramy lokalnej.
- **Przestrzeń adresowa:** zakresy adresów dla sieci, które reprezentuje brama sieci lokalnej. Można dodać wiele zakresów przestrzeni adresowej, ale upewnij się, że zakresy, które określisz w tym miejscu, nie pokrywają się z zakresami innych sieci, z którymi chcesz się połączyć. 
- **Konfigurowanie ustawień protokołu BGP:** Ustawienia protokołu BGP należy skonfigurować tylko wtedy, gdy konfiguracja wymaga tego ustawienia. Aby dowiedzieć się więcej o tym ustawieniu, zobacz [BGP z usługą Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)— informacje .
- **Subskrypcja**: Żądana subskrypcja. Nie musi to być zgodne z subskrypcją używaną dla bramy sieci VPN lub konta magazynu.
- **Grupa zasobów**: Żądana grupa zasobów. Nie musi to być zgodne z grupą zasobów używaną dla bramy sieci VPN lub konta magazynu.
- **Lokalizacja:** Region platformy Azure, w którym należy utworzyć zasób bramy sieci lokalnej. Powinno to odpowiadać regionowi wybranej dla bramy sieci VPN i konta magazynu.

Wybierz **pozycję Utwórz,** aby utworzyć zasób bramy sieci lokalnej.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurowanie lokalnego urządzenia sieciowego
Określone kroki konfigurowania lokalnego urządzenia sieciowego zależą od wybranego przez organizację urządzenia sieciowego. W zależności od wybranego przez organizację urządzenia [lista przetestowanych urządzeń](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) może zawierać łącze do instrukcji dostawcy urządzenia do konfigurowania za pomocą usługi Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Tworzenie połączenia lokacja lokacja
Aby zakończyć wdrażanie sieci VPN S2S, należy utworzyć połączenie między lokalnym urządzeniem sieciowym (reprezentowanym przez zasób bramy sieciowej) a bramą sieci VPN. Aby to zrobić, przejdź do bramy sieci VPN utworzonej powyżej. W spisie treści bramy sieci VPN wybierz pozycję **Połączenia**i kliknij przycisk **Dodaj**. Powstałe okienko **połączenia Dodaj** wymaga następujących pól:

- **Nazwa**: Nazwa połączenia. Brama sieci VPN może obsługiwać wiele połączeń, więc wybierz nazwę pomocną dla zarządzania, która rozróżnia to konkretne połączenie.
- **Typ połączenia:** Od tego połączenia S2S wybierz **pozycję Lokacja lokacja (IPSec)** na liście rozwijanej.
- **Brama sieci wirtualnej:** to pole jest wybierane automatycznie do bramy sieci VPN, do której nawiązujesz połączenie i którego nie można zmienić.
- **Brama sieci lokalnej:** Jest to brama sieci lokalnej, którą chcesz połączyć z bramą sieci VPN. Wynikowe okienko wyboru powinno mieć nazwę bramy sieci lokalnej utworzonej powyżej.
- **Klucz udostępniony (PSK)**: Mieszanina liter i cyfr, używana do ustanawiania szyfrowania połączenia. Ten sam klucz udostępniony musi być używany zarówno w sieci wirtualnej, jak i w bramach sieci lokalnej. Jeśli urządzenie bramy go nie udostępnia, możesz je zrobić tutaj i przekazać je urządzeniu.

Wybierz **przycisk OK,** aby utworzyć połączenie. Połączenie zostało pomyślnie nawiązane za pomocą strony **Połączenia.**

## <a name="mount-azure-file-share"></a>Udostępnianie plików platformy Azure 
Ostatnim krokiem w konfigurowaniu sieci VPN S2S jest sprawdzenie, czy działa dla usługi Azure Files. Można to zrobić, montując udział plików platformy Azure lokalnie z preferowanym os. Zobacz instrukcje montażu przez system operacyjny tutaj:

- [Windows](storage-how-to-use-files-windows.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Zobacz też
- [Omówienie sieci usług Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie sieci VPN typu "punkt-lokacja) w systemie Windows do użytku z plikami platformy Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurowanie sieci VPN typu "point-to-site) w systemie Linux do użytku z plikami azure](storage-files-configure-p2s-vpn-linux.md)