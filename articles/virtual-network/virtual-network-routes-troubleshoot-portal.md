---
title: Rozwiązywanie problemów z tras - Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z trasy w modelu wdrażania usługi Azure Resource Manager przy użyciu portalu Azure.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 7c9d49a4135860bce317cd5808d3430af6b49fbd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Rozwiązywanie problemów z tras przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [Program PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

Jeśli występują problemy z połączeniem sieciowym do lub z sieci maszyny wirtualnej Azure (VM), tras może mieć wpływ na Twoje ruch maszyny Wirtualnej. Ten artykuł zawiera omówienie funkcji diagnostyki dla tras do dalszego rozwiązywania.

Tabele tras są skojarzone z podsieciami i obowiązują na wszystkich interfejsach sieciowych (NIC) w tej podsieci. Następujące typy tras może odnosić się do każdego interfejsu sieciowego:

* **Trasy systemowe:** Domyślnie każda podsieć utworzona w sieci wirtualnej platformy Azure (VNet) ma tabel tras systemu, które umożliwiają lokalnej sieci wirtualnej ruch, ruch lokalnej za pośrednictwem bramy sieci VPN i ruchu internetowego. Istnieją również tras systemowych dla połączyć za pomocą sieci wirtualnych.
* **Trasy protokołu BGP:** propagowane do interfejsów sieciowych za pośrednictwem usługi ExpressRoute lub połączeń VPN lokacja lokacja. Dowiedz się więcej o routingu BGP, odczytując [protokołu BGP z bramy sieci VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) i [omówienie ExpressRoute](../expressroute/expressroute-introduction.md) artykułów.
* **Trasy zdefiniowane przez użytkownika (przez):** korzystając z wirtualnych urządzeń sieciowych lub są wymuszone tunelowanie ruch do sieci lokalnej za pośrednictwem sieci VPN lokacja lokacja, może być zdefiniowana przez użytkownika tras (Udr) skojarzone z tabeli tras podsieci. Jeśli nie masz doświadczenia w obsłudze Udr, przeczytaj [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md#user-defined) artykułu.

Przy użyciu różnych tras, które można zastosować do interfejsu sieciowego może być trudne do ustalenia, obowiązują agregacji trasy. Do rozwiązywania problemów łączności sieciowej maszyny Wirtualnej, można wyświetlić wszystkie skuteczne trasy dla interfejsu sieciowego w modelu wdrażania usługi Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Rozwiązywanie problemów z przepływem ruchu maszyny Wirtualnej za pomocą skuteczne tras
W tym artykule używa następujący scenariusz jako przykład ilustrujący sposób rozwiązywania skuteczne trasy dla interfejsu sieciowego:

Maszyna wirtualna (*VM1*) podłączone do sieci wirtualnej (*VNet1*, prefiks: 10.9.0.0/16) nie może połączyć się VM(VM3) w nowo peered sieci wirtualnej (*VNet3*, prefiks 10.10.0.0/16). Brak Udr lub BGP kieruje stosowane do interfejsu sieciowego VM1 NIC1 podłączony do maszyny Wirtualnej, są stosowane tylko tras systemowych.

W tym artykule opisano sposób ustalić przyczynę niepowodzenia połączenia przy użyciu możliwości wprowadzenia trasy w modelu wdrażania zarządzania zasobami Azure.
Podczas w przykładzie użyto tylko tras systemowych, te same kroki może służyć do określenia błędów połączenia przychodzącego i wychodzącego przez dowolnego typu trasy.

> [!NOTE]
> Jeśli maszyna wirtualna ma więcej niż jedną kartę Sieciową podłączoną, sprawdź skuteczne trasy dla każdej z kart sieciowych do diagnozowania problemów z łącznością sieciową z maszyny Wirtualnej i.
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>Widok skuteczne trasy dla maszyny wirtualnej
Aby wyświetlić trasy agregacji, które są stosowane do maszyny Wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com. Konto musi mieć przypisaną *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* operacji interfejsu sieciowego. Aby dowiedzieć się, jak przypisać operacje do kont, zobacz [Tworzenie niestandardowych ról dla kontroli dostępu](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Kliknij przycisk **wszystkie usługi**, następnie kliknij przycisk **maszyn wirtualnych** na liście.
3. Wybierz maszynę Wirtualną, aby rozwiązać problemy z listy, która pojawia się i zostanie wyświetlony blok maszyny Wirtualnej, z opcjami.
4. Kliknij przycisk **Diagnozuj & rozwiązywania problemów** , a następnie wybierz powszechny problem. Na przykład **nie mogę połączyć się z maszyną Wirtualną z systemem Windows** jest zaznaczone.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Kroki są wyświetlane w obszarze ten problem, jak pokazano na poniższej ilustracji:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Kliknij przycisk *skuteczne tras* na liście zalecanych kroków.
6. **Skuteczne tras** zostanie wyświetlony blok, jak pokazano na poniższej ilustracji:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Jeśli maszyna wirtualna ma tylko jedną kartę Sieciową, jest wybrany domyślnie. Jeśli masz więcej niż jedną kartę Sieciową, wybierz karty interfejsu Sieciowego, dla którego chcesz wyświetlić skuteczne trasy.

   > [!NOTE]
   > Jeśli skojarzone z karty Sieciowej maszyny Wirtualnej nie jest w stanie uruchomienia, nie można wyświetlić skuteczne trasy. Tylko pierwszych 200 skuteczne trasy są wyświetlane w portalu. Aby uzyskać pełną listę, kliknij **Pobierz**. Dodatkowo można filtrować wyniki z pliku CSV pobrany.
   >
   >

    Zwróć uwagę następujące opcje w danych wyjściowych:

   * **Źródło**: Określa typ trasy. Trasy systemowe są wyświetlane jako *domyślne*, Udr są wyświetlane jako *użytkownika* i tras bramy (statyczny lub BGP) są wyświetlane jako *właściwość VPNGateway*.
   * **Stan**: wskazuje stan skuteczne trasy. Możliwe wartości to *Active* lub *nieprawidłowy*.
   * **AddressPrefixes**: Określa prefiks adresu skuteczne trasy w notacji CIDR.
   * **Typ następnego przeskoku**: wskazuje następnego skoku dla danej trasy. Możliwe wartości to *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, lub *Null*. Wartość *Null* dla **Typ następnego przeskoku** przez może oznaczać nieprawidłową trasy. Na przykład jeśli **Typ następnego przeskoku** jest *VirtualAppliance* i urządzenie wirtualne sieci maszyny Wirtualnej nie jest w stanie zainicjowano obsługę administracyjną uruchomiona. Jeśli **Typ następnego przeskoku** jest *właściwość VPNGateway* i nie ma żadnych bramy elastycznie/uruchomiony w danej sieci wirtualnej, trasa mogą być nieprawidłowe.
7. Brak trasy do *WestUS VNET3* sieciami wirtualnymi (prefiks 10.10.0.0/16) z *WestUS VNet1* (prefiksu 10.9.0.0/16) na obrazie w poprzednim kroku. Na poniższej ilustracji komunikacji równorzędnej łącze znajduje się w *Rozłączono* stanu:

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Łącze dwukierunkowe dla komunikacji równorzędnej jest uszkodzona, który wyjaśnia dlaczego VM1 nie może połączyć się VM3 w *WestUS VNet3* sieci wirtualnej.
8. Na poniższej ilustracji przedstawiono trasy po ustanowieniu łącza komunikacji równorzędnej dwukierunkowe:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Więcej rozwiązywania scenariusze w przypadku oceny tunelowanie wymuszone i tras, przeczytaj [zagadnienia](virtual-network-routes-troubleshoot-portal.md#considerations) sekcji tego artykułu.

### <a name="view-effective-routes-for-a-network-interface"></a>Widok skuteczne trasy dla interfejsu sieciowego
Jeśli jest w pełni funkcjonalne przepływ ruchu sieciowego dla konkretnego interfejsu sieciowego (NIC), można wyświetlić pełną listę tras skuteczne karty sieciowej bezpośrednio. Aby wyświetlić trasy agregacji, które są stosowane do karty Sieciowej, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com.
2. Kliknij przycisk **wszystkie usługi**, następnie kliknij przycisk **interfejsy sieciowe**
3. Wyszukaj liście Nazwa karty sieciowej, lub wybierz ją z listy. W tym przykładzie **VM1 NIC1** jest zaznaczone.
4. Wybierz **skuteczne tras** w **interfejsu sieciowego** bloku, jak pokazano na poniższej ilustracji:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    **Zakres** domyślnie wybrany interfejs sieciowy.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Widok skuteczne trasy dla tabeli tras
Podczas modyfikowania trasy zdefiniowane przez użytkownika (Udr) w tabeli tras, można przejrzeć wpływ tras dodawany na określonej maszynie Wirtualnej. Tabeli tras można skojarzyć z dowolną liczbą podsieci. Możesz teraz przeglądać skuteczne trasy dla wszystkich kart zastosowane tabelę tras danego, bez konieczności przełączania kontekstu za pomocą bloku tabeli daną trasą.

Na przykład przez (*UDRoute*) jest określona w tabeli tras (*UDRouteTable*). Ta trasa wysyła wszystkie ruch internetowy pochodzący od *podsieć1* w *WestUS VNet1* sieci wirtualnej przez urządzenie wirtualne sieci (NVA) w *podsieć2* z tej samej sieci wirtualnej. Trasy pokazano na poniższej ilustracji:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Aby wyświetlić łączny trasy dla tabeli tras, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com.
2. Kliknij przycisk **wszystkie usługi**, następnie kliknij przycisk **tabel tras**
3. Wyszukaj listę do tabeli tras, którą chcesz znajduje łączny trasy dla i zaznacz go. W tym przykładzie **UDRouteTable** jest zaznaczone. Zostanie wyświetlony blok dla tabeli wybranej trasy, jak pokazano na poniższej ilustracji:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Wybierz **skuteczne tras** w **tabeli tras** bloku. **Zakres** ma ustawioną wartość wybranego tabeli tras.
5. Tabela tras może odnosić się do wielu podsieci. Wybierz **podsieci** chcesz przejrzeć na liście. W tym przykładzie **podsieć1** jest zaznaczone.
6. Wybierz **interfejs sieciowy**. Wszystkie karty sieciowe podłączone do wybranej podsieci są wyświetlane. W tym przykładzie **VM1 NIC1** jest zaznaczone.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Jeśli karta sieciowa nie jest skojarzony z uruchomionej maszyny Wirtualnej, są wyświetlane nie skuteczne trasy.
   >
   >

## <a name="considerations"></a>Zagadnienia do rozważenia
Zwrócono kilka rzeczy, które należy wziąć pod uwagę podczas przeglądania listy tras:

* Routing jest oparta na Najdłuższy prefiks dopasowania (LPM) Wybierane spośród Udr, trasy protokołu BGP i systemu. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, następnie wybór trasy odbywa się w następującej kolejności:

  * Trasa zdefiniowana przez użytkownika
  * Trasa protokołu BGP
  * Trasa systemowa (ustawienie domyślne)

    Skuteczne trasy może zobaczyć tylko skuteczne ścieżek, które są dopasowaniem LPM oparte na trasach są one dostępne. Przez pokazanie, jak trasy faktycznie są oceniane pod kątem danej karty Sieciowej, dzięki temu znacznie łatwiejsze Rozwiązywanie problemów z określonej trasy, które mogą mieć wpływ na łączność z maszyny Wirtualnej.
* Jeśli masz Udr i wysyłania ruchu do sieci (NVA), urządzenie wirtualne z *VirtualAppliance* jako **Typ następnego przeskoku**, upewnij się, że przekazywanie IP jest włączona na NVA odbieranie ruchu lub pakietów porzucone.
* Jeśli włączono wymuszony tunelowania cały ruch wychodzący z Internetem będą kierowane do lokalnego. Protokołu RDP/SSH z Internetu do maszyny Wirtualnej może nie działać z tym ustawieniem, w zależności od tego, jak lokalną obsługuje ten ruch.
  Tunelowanie wymuszone można włączyć:
  * Jeśli przy użyciu sieci VPN typu lokacja lokacja, ustawiając trasy zdefiniowane przez użytkownika (przez) z Typ następnego przeskoku jako brama sieci VPN
  * Jeśli trasa domyślna jest anonsowana za pośrednictwem protokołu BGP
* Dla sieci wirtualnej komunikacji równorzędnej ruchu działał prawidłowo, trasa systemu o **Typ następnego przeskoku** *VNetPeering* musi istnieć dla zakresu prefiksu peered sieci wirtualnej. Jeśli taka trasa nie istnieje, a łącze sieci wirtualnej komunikacji równorzędnej wygląda OK:
  * Poczekaj kilka sekund, a następnie spróbuj ponownie, jeśli jest to nowo utworzonego łącze komunikacji równorzędnej. Od czasu do czasu zajmuje więcej czasu propagowania tras dla wszystkich interfejsów sieciowych w podsieci.
  * Zasady grupy zabezpieczeń sieci (NSG) może mieć wpływ na przepływów ruchu sieciowego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z grup zabezpieczeń sieci](virtual-network-nsg-troubleshoot-portal.md) artykułu.
