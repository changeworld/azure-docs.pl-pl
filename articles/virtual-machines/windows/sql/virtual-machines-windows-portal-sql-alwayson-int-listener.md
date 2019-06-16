---
title: Utwórz odbiornik grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Instrukcje krok po kroku dotyczące tworzenia odbiornika dla zawsze włączonej grupy dostępności dla programu SQL Server w maszynach wirtualnych platformy Azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: 3b90ae3e9808b22b6d6c41e3ac11bec0293bd4bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326154"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurowanie modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure
W tym artykule opisano sposób tworzenia modułu równoważenia obciążenia dla grupy dostępności programu SQL Server Always On w maszynach wirtualnych platformy Azure, które działają z usługą Azure Resource Manager. Grupy dostępności wymaga modułu równoważenia obciążenia w przypadku wystąpienia programu SQL Server na maszynach wirtualnych platformy Azure. Moduł równoważenia obciążenia przechowuje adres IP dla odbiornika grupy dostępności. Jeśli grupy dostępności obejmuje wiele regionów, każdy region wymaga modułu równoważenia obciążenia.

Aby wykonać to zadanie, musisz mieć wdrożony na maszynach wirtualnych platformy Azure, które działają z usługą Resource Manager grupy dostępności programu SQL Server. Obie maszyny wirtualne programu SQL Server musi należeć do tego samego zestawu dostępności. Możesz użyć [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) można automatycznie utworzyć grupy dostępności w usłudze Resource Manager. Ten szablon automatycznie tworzy wewnętrznego modułu równoważenia obciążenia. 

Jeśli wolisz, możesz to zrobić [ręcznie skonfigurować grupę dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Ten artykuł wymaga grupy dostępności zostały już skonfigurowane.  

Powiązane tematy obejmują:

* [Konfigurowanie zawsze włączonych grup dostępności maszyny Wirtualnej platformy Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna za pomocą usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Przy zapoznaniu się z tego artykułu, możesz tworzyć i konfigurować modułu równoważenia obciążenia w witrynie Azure portal. Po zakończeniu procesu konfigurowania klastra na użycie adresu IP z modułu równoważenia obciążenia dla odbiornika grupy dostępności.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Tworzenie i Konfigurowanie modułu równoważenia obciążenia w witrynie Azure portal
W tej części zadanie wykonaj następujące czynności:

1. W witrynie Azure portal utworzyć moduł równoważenia obciążenia, a następnie skonfiguruj adres IP.
2. Konfigurowanie puli zaplecza.
3. Utwórz sondę. 
4. Ustaw reguły równoważenia obciążenia.

> [!NOTE]
> W przypadku wystąpienia programu SQL Server w wiele grup zasobów i regiony, wykonać każdy krok dwa razy, jeden raz w każdej grupie zasobów.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Tworzenie modułu równoważenia obciążenia, a następnie skonfiguruj adres IP
Najpierw utwórz moduł równoważenia obciążenia. 

1. W witrynie Azure portal Otwórz grupę zasobów, która zawiera maszyny wirtualne programu SQL Server. 

2. W grupie zasobów, kliknij przycisk **Dodaj**.

3. Wyszukaj **moduł równoważenia obciążenia** , a następnie w wynikach wyszukiwania wybierz **usługi Load Balancer**, opublikowana przez **Microsoft**.

4. Na **modułu równoważenia obciążenia** bloku kliknij **Utwórz**.

5. W **Tworzenie modułu równoważenia obciążenia** okna dialogowego Skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekst reprezentujący modułu równoważenia obciążenia. Na przykład **sqlLB**. |
   | **Typ** |**Wewnętrzny**: Większość implementacji użyć wewnętrznego modułu równoważenia obciążenia, który umożliwia aplikacjom w ramach tej samej sieci wirtualnej do grupy dostępności.  </br> **Zewnętrzne**: Umożliwia aplikacjom połączyć się z grupy dostępności za pomocą publicznego połączenia internetowego. |
   | **Sieć wirtualna** |Wybierz sieć wirtualną, które należą do wystąpienia programu SQL Server. |
   | **Podsieć** |Wybierz podsieć, którą wystąpienia programu SQL Server znajdują się w. |
   | **Przypisywanie adresów IP** |**Statyczne** |
   | **Prywatny adres IP** |Podaj dostępny adres IP z podsieci. Podczas tworzenia odbiornika w klastrze, należy użyć tego adresu IP. W skrypcie programu PowerShell w dalszej części tego artykułu Użyj tego adresu `$ILBIP` zmiennej. |
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, może pojawić się w tym polu. Wybierz subskrypcję, która ma być skojarzony z tym zasobem. Zwykle jest tej samej subskrypcji co wszystkie zasoby dla grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, które należą do wystąpienia programu SQL Server. |
   | **Lokalizacja** |Wybierz wystąpienia programu SQL Server znajdują się w lokalizacji platformy Azure. |

6. Kliknij pozycję **Utwórz**. 

Platforma Azure tworzy moduł równoważenia obciążenia. Moduł równoważenia obciążenia należy do określonej sieci, podsieci, grupy zasobów i lokalizacji. Sprawdź ustawienia usługi równoważenia obciążenia na platformie Azure, po ukończeniu zadania usługi Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurowanie puli zaplecza
Platforma Azure wywołuje puli adresów zaplecza *puli zaplecza*. W tym przypadku puli zaplecza jest adresy dwa wystąpienia programu SQL Server w grupie dostępności. 

1. W grupie zasobów kliknij przycisk modułu równoważenia obciążenia, który został utworzony. 

2. Na **ustawienia**, kliknij przycisk **pule zaplecza**.

3. Na **pule zaplecza**, kliknij przycisk **Dodaj** tworzenie puli adresów zaplecza. 

4. Na **Dodawanie puli zaplecza**w obszarze **nazwa**, wpisz nazwę puli zaplecza.

5. W obszarze **maszyn wirtualnych**, kliknij przycisk **Dodaj maszynę wirtualną**. 

6. W obszarze **wybierz maszyny wirtualne**, kliknij przycisk **wybierz zestaw dostępności**, a następnie określ, czy maszyny wirtualne programu SQL Server należy do zestawu dostępności.

7. Po wybraniu zestawu dostępności, kliknij przycisk **wybierz maszyny wirtualne**wybierz dwóch maszyn wirtualnych obsługujących wystąpienia programu SQL Server w grupie dostępności, a następnie kliknij przycisk **wybierz**. 

8. Kliknij przycisk **OK** aby zamknąć bloki dla **wybierz maszyny wirtualne**, i **Dodawanie puli zaplecza**. 

Azure aktualizuje ustawienia dla puli adresów zaplecza. Zestaw dostępności ma teraz pulę dwóch wystąpień programu SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: Tworzenie sondy
Sonda definiuje, jak platforma Azure sprawdzi, której wystąpienia programu SQL Server jest bieżącym właścicielem odbiornika grupy dostępności. Azure sondy usługi na podstawie adresu IP na port, który można zdefiniować podczas tworzenia sondy.

1. W module równoważenia obciążenia **ustawienia** bloku kliknij **sondy kondycji**. 

2. Na **sondy kondycji** bloku kliknij **Dodaj**.

3. Konfigurowanie sondy na **Dodawanie sondy** bloku. Konfigurowanie sondy, użyj następujących wartości:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekst reprezentujący sondy. Na przykład **SQLAlwaysOnEndPointProbe**. |
   | **Protokół** |**TCP** |
   | **Port** |Można użyć dowolnego dostępnego portu. Na przykład *59999*. |
   | **Interval** |*5* |
   | **Próg złej kondycji** |*2* |

4.  Kliknij przycisk **OK**. 

> [!NOTE]
> Upewnij się, że port, który określisz został otwarty na zaporze oba wystąpienia programu SQL Server. Oba wystąpienia wymagają regułę ruchu przychodzącego dla portu TCP, którego używasz. Aby uzyskać więcej informacji, zobacz [apletu Dodaj lub Edytuj regułę zapory](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Platforma Azure tworzy sondę i następnie używa go do testowania, którego wystąpienia programu SQL Server ma odbiornika dla grupy dostępności.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: Ustaw reguły równoważenia obciążenia
Reguły równoważenia obciążenia skonfiguruj, jak moduł równoważenia obciążenia kieruje ruch do wystąpienia programu SQL Server. Dla tego modułu równoważenia obciążenia możesz włączyć bezpośredni zwrot serwera ponieważ tylko jeden z dwóch wystąpień programu SQL Server jest właścicielem zasób odbiornika grupy dostępności w danym momencie.

1. W module równoważenia obciążenia **ustawienia** bloku kliknij **reguły równoważenia obciążenia**. 

2. Na **reguły równoważenia obciążenia** bloku kliknij **Dodaj**.

3. Na **reguły równoważenia obciążenia Dodaj** bloku, skonfiguruj regułę równoważenia obciążenia. Użyj następujących ustawień: 

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekst reprezentujący reguły równoważenia obciążenia. Na przykład **SQLAlwaysOnEndPointListener**. |
   | **Protokół** |**TCP** |
   | **Port** |*1433* |
   | **Port zaplecza** |*1433*. Ta wartość jest ignorowana, ponieważ ta reguła używa **pływającego adresu IP (bezpośredni zwrot serwera)** . |
   | **Probe** |Użyj nazwy sondy, który został utworzony dla tego modułu równoważenia obciążenia. |
   | **Trwałość sesji** |**Brak** |
   | **Limit czasu bezczynności (minuty)** |*4* |
   | **Pływający adres IP (bezpośredni zwrot serwera)** |**Enabled (Włączone)** |

   > [!NOTE]
   > Masz może być przewinięcie w dół bloku Aby wyświetlić wszystkie ustawienia.
   > 

4. Kliknij przycisk **OK**. 
5. Platforma Azure konfiguruje reguły równoważenia obciążenia. Moduł równoważenia obciążenia jest skonfigurowany na potrzeby kierowania ruchu do wystąpienia programu SQL Server, który jest hostem odbiornika dla grupy dostępności. 

W tym momencie grupa zasobów ma modułu równoważenia obciążenia, który nawiązuje połączenie z obu komputerach programu SQL Server. Moduł równoważenia obciążenia zawiera również adresu IP programu SQL Server Always On odbiornika grupy dostępności, dzięki czemu maszynowo mogą odpowiadać na żądania dotyczące grup dostępności.

> [!NOTE]
> Jeśli Twojego wystąpienia programu SQL Server znajdują się w dwóch oddzielnych regionach, powtórz kroki opisane w innym regionie. Każdy region wymaga modułu równoważenia obciążenia. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Skonfiguruj klaster pod kątem używania adresu IP modułu równoważenia obciążenia
Następnym krokiem jest Konfigurowanie odbiornika w klastrze, a następnie przełącz odbiornika w trybie online. Wykonaj następujące czynności: 

1. Utwórz odbiornik grupy dostępności w klastrze trybu failover. 

2. Przenieś odbiornika w trybie online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5. Utwórz odbiornik grupy dostępności w klastrze trybu failover
W tym kroku ręcznie utworzyć odbiornik grupy dostępności w Menedżerze klastra trybu Failover i SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Weryfikowanie konfiguracji odbiornika

Jeśli zasobów klastra i zależności są poprawnie skonfigurowane, należy wyświetlić odbiornika w programie SQL Server Management Studio. Aby ustawić port odbiornika, wykonaj następujące czynności:

1. Uruchom program SQL Server Management Studio, a następnie połącz się z repliką podstawową.

2. Przejdź do **AlwaysOn wysokiej dostępności** > **grup dostępności** > **odbiorników grup dostępności**.  
    Powinna zostać wyświetlona nazwa odbiornika, który został utworzony w Menedżerze klastra trybu Failover. 

3. Kliknij prawym przyciskiem myszy odbiornik o nazwie, a następnie kliknij przycisk **właściwości**.

4. W **portu** polu Określ numer portu odbiornika grupy dostępności przy użyciu $EndpointPort było wcześniej używane (1433 była wartość domyślna), a następnie kliknij przycisk **OK**.

Masz teraz do grupy dostępności, maszynach wirtualnych platformy Azure działa w trybie usługi Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testuj połączenie z odbiornikiem
Przetestuj połączenie, wykonując następujące czynności:

1. Protokół RDP do wystąpienia programu SQL Server, która znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Ten serwer może być inne wystąpienie programu SQL Server w klastrze.

2. Użyj **sqlcmd** narzędzie, aby przetestować połączenie. Na przykład, poniższy skrypt ustanawia **sqlcmd** połączenie z repliką podstawową, za pośrednictwem odbiornika z uwierzytelnianiem Windows:
   
        sqlcmd -S <listenerName> -E

Połączenie SQLCMD automatycznie nawiązuje połączenie z wystąpieniem programu SQL Server, które obsługuje replikę podstawową. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Utwórz adres IP dla grupy dostępności dodatkowe

Każda grupa dostępności używa oddzielnych odbiornika. Każdy odbiornik ma swój własny adres IP. Użyj tego samego modułu równoważenia obciążenia do przechowywania adresu IP dla dodatkowych odbiorników. Po utworzeniu grupy dostępności, Dodaj adres IP do modułu równoważenia obciążenia, a następnie skonfiguruj odbiornik.

Aby dodać adres IP do modułu równoważenia obciążenia za pomocą witryny Azure portal, wykonaj następujące czynności:

1. W witrynie Azure portal Otwórz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie kliknij przycisk modułu równoważenia obciążenia. 

2. W obszarze **ustawienia**, kliknij przycisk **puli adresów IP frontonu**, a następnie kliknij przycisk **Dodaj**. 

3. W obszarze **Dodaj adres IP frontonu**, przypisać nazwy dla frontonu. 

4. Upewnij się, że **sieć wirtualna** i **podsieci** są takie same jak wystąpienia programu SQL Server.

5. Ustaw adres IP dla odbiornika. 
   
   >[!TIP]
   >Można ustawić statyczny adres IP, a następnie wpisz adres który nie jest obecnie używany w podsieci. Alternatywnie możesz ustawić dynamiczny adres IP i zapisanie nowej puli adresów IP frontonu. Po wykonaniu tej czynności w witrynie Azure portal automatycznie przypisuje dostępny adres IP do puli. Można ponownie otworzyć puli adresów IP frontonu i zmianie przypisania na statyczną. 

6. Zapisz adres IP dla odbiornika. 

7. Dodaj sondę kondycji za pomocą następujących ustawień:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa do identyfikacji sondy.
   |**Protokół** |TCP
   |**Port** |Nieużywany port TCP, które muszą być dostępne na wszystkich maszynach wirtualnych. Nie można używać do innych celów. Nie dwóch detektorów można użyć tego samego portu sondowania. 
   |**Interval** |Ilość czasu między próbami sondy. Użyj wartości domyślnej (5).
   |**Próg złej kondycji** |Liczba kolejnych progów, które powinna zakończyć się niepowodzeniem przed utworzeniem maszyny wirtualnej jest uznawana za złą.

8. Kliknij przycisk **OK** można zapisać sondy. 

9. Utwórz reguły równoważenia obciążenia. Kliknij przycisk **reguły równoważenia obciążenia**, a następnie kliknij przycisk **Dodaj**.

10. Skonfiguruj nowy obciążenia, reguły równoważenia przy użyciu następujących ustawień:

    |Ustawienie |Wartość
    |:-----|:----
    |**Nazwa** |Nazwa do identyfikacji reguły równoważenia obciążenia. 
    |**Adres IP frontonu** |Wybierz adres IP, który został utworzony. 
    |**Protokół** |TCP
    |**Port** |Użyj portu, którego wystąpienia programu SQL Server. Domyślne wystąpienie korzysta z portu 1433, o ile nie został zmieniony. 
    |**Port zaplecza** |Użyj taką samą wartość jak **portu**.
    |**Pula zaplecza** |Puli, która zawiera maszyny wirtualne z wystąpienia programu SQL Server. 
    |**Sonda kondycji** |Wybierz utworzoną przez sondy.
    |**Trwałość sesji** |Brak
    |**Limit czasu bezczynności (minuty)** |Domyślne (4)
    |**Pływający adres IP (bezpośredni zwrot serwera)** | Enabled (Włączony)

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurowanie grupy dostępności do korzystania z nowego adresu IP

Aby ukończyć konfigurowanie klastra, powtórz kroki, które zostały wykonane po wykonaniu pierwszej grupy dostępności. Oznacza to, skonfiguruj [klaster pod kątem używania nowego adresu IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po dodaniu adresu IP dla odbiornika należy skonfigurować grupę wyższą dostępność, wykonując następujące czynności: 

1. Sprawdź, czy port sondy dla nowego adresu IP jest otwarty na obu maszynach wirtualnych programu SQL Server. 

2. [W Menedżerze klastra Dodaj punkt dostępu klienta](#addcap).

3. [Skonfiguruj zasób adresu IP dla grupy dostępności](#congroup).

   >[!IMPORTANT]
   >Podczas tworzenia adresu IP, należy użyć adresu IP, który został dodany do modułu równoważenia obciążenia.  

4. [Zasób grupy dostępności programu SQL Server, od Określ punkt dostępu klienta](#dependencyGroup).

5. [Marka zależne od adresu IP zasobu punktu dostępu klienta](#listname).
 
6. [Ustawianie parametrów klastra w programie PowerShell](#setparam).

Po skonfigurowaniu grupy dostępności do korzystania z nowego adresu IP, należy skonfigurować połączenie z odbiornikiem. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Dodaj regułę dla rozproszonej grupy dostępności równoważenia obciążenia

Jeśli grupa dostępności uczestniczy w rozproszonej grupy dostępności, modułu równoważenia obciążenia musi dodatkowa reguła. Ta reguła zapisuje port używany przez odbiornik grupy dostępności rozproszonych.

>[!IMPORTANT]
>Ten krok ma zastosowanie tylko wtedy, gdy grupa dostępności uczestniczy w [rozproszonej grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na każdym serwerze, który uczestniczy w rozproszonej grupie dostępności należy utworzyć regułę ruchu przychodzącego na odbiornika grupy dostępności rozproszonych portu TCP. W wielu przykładach dokumentacja używa 5022. 

1. W witrynie Azure portal kliknij w module równoważenia obciążenia, a następnie kliknij przycisk **reguły równoważenia obciążenia**, a następnie kliknij przycisk **+ Dodaj**. 

1. Utwórz równoważenia reguły z następującymi ustawieniami:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa do identyfikacji regułę dla rozproszonej grupy dostępności równoważenia obciążenia. 
   |**Adres IP frontonu** |Użyj tego samego adresu IP frontonu jako grupy dostępności.
   |**Protokół** |TCP
   |**Port** |5022 — port [odbiornika punktu końcowego grupy dostępności rozproszonych](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Może być dowolny dostępny port.  
   |**Port zaplecza** | 5022 — Użyj taką samą wartość jak **portu**.
   |**Pula zaplecza** |Puli, która zawiera maszyny wirtualne z wystąpienia programu SQL Server. 
   |**Sonda kondycji** |Wybierz utworzoną przez sondy.
   |**Trwałość sesji** |Brak
   |**Limit czasu bezczynności (minuty)** |Domyślne (4)
   |**Pływający adres IP (bezpośredni zwrot serwera)** | Enabled (Włączony)

Powtórz te czynności dla modułu równoważenia obciążenia na inne, które uczestniczą w grupach rozproszonych dostępności grupy dostępności.

Jeśli jesteś ograniczanie dostępu przy użyciu grupy zabezpieczeń sieci platformy Azure, upewnij się, że reguły Zezwalaj obejmują adresy IP maszyn wirtualnych serwera SQL w wewnętrznej bazie danych, pływający adres IP modułu równoważenia obciążenia adresów dla odbiornika grupy dostępności i adres IP podstawowe klastra, jeśli ma to zastosowanie.

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md)
