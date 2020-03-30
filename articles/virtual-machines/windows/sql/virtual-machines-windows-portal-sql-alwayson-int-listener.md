---
title: Konfigurowanie detektorów grup dostępności & modułu równoważenia obciążenia (Azure portal)
description: Instrukcje krok po kroku dotyczące tworzenia odbiornika dla grupy dostępności zawsze włączone dla programu SQL Server na maszynach wirtualnych platformy Azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096328"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Konfigurowanie modułu równoważenia obciążenia dla grupy dostępności na maszynach wirtualnych programu Azure SQL Server
W tym artykule wyjaśniono, jak utworzyć moduł równoważenia obciążenia dla grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure, które są uruchomione z usługą Azure Resource Manager. Grupa dostępności wymaga modułu równoważenia obciążenia, gdy wystąpienia programu SQL Server znajdują się na maszynach wirtualnych platformy Azure. Moduł równoważenia obciążenia przechowuje adres IP dla odbiornika grupy dostępności. Jeśli grupa dostępności obejmuje wiele regionów, każdy region potrzebuje modułu równoważenia obciążenia.

Aby wykonać to zadanie, musisz wdrożyć grupę dostępności programu SQL Server na maszynach wirtualnych platformy Azure, które są uruchomione z Menedżerem zasobów. Obie maszyny wirtualne programu SQL Server muszą należeć do tego samego zestawu dostępności. Za pomocą [szablonu firmy Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) można automatycznie utworzyć grupę dostępności w Menedżerze zasobów. Ten szablon automatycznie tworzy wewnętrzny moduł równoważenia obciążenia. 

Jeśli wolisz, możesz [ręcznie skonfigurować grupę dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Ten artykuł wymaga, aby grupy dostępności były już skonfigurowane.  

Tematy pokrewne obejmują:

* [Konfigurowanie grup dostępności zawsze włączone w usłudze Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurowanie połączenia sieci wirtualnych przy użyciu usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Przechodząc przez ten artykuł, można utworzyć i skonfigurować moduł równoważenia obciążenia w witrynie Azure portal. Po zakończeniu procesu należy skonfigurować klaster do używania adresu IP z modułu równoważenia obciążenia dla odbiornika grupy dostępności.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Tworzenie i konfigurowanie modułu równoważenia obciążenia w witrynie Azure portal
W tej części zadania wykonaj następujące czynności:

1. W witrynie Azure portal utwórz moduł równoważenia obciążenia i skonfiguruj adres IP.
2. Skonfiguruj pulę zaplecza.
3. Utwórz sondę. 
4. Ustaw reguły równoważenia obciążenia.

> [!NOTE]
> Jeśli wystąpienia programu SQL Server znajdują się w wielu grupach i regionach zasobów, wykonaj każdy krok dwa razy, raz w każdej grupie zasobów.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Utwórz moduł równoważenia obciążenia i skonfiguruj adres IP
Najpierw utwórz moduł równoważenia obciążenia. 

1. W witrynie Azure Portal otwórz grupę zasobów zawierającą maszyny wirtualne programu SQL Server. 

2. W grupie zasobów kliknij pozycję **Dodaj**.

3. Wyszukaj **moduł równoważenia obciążenia,** a następnie w wynikach wyszukiwania wybierz pozycję **Load Balancer**, który jest publikowany przez **firmę Microsoft**.

4. W bloku **Moduł równoważenia obciążenia** kliknij przycisk **Utwórz**.

5. W oknie dialogowym **Tworzenie modułu równoważenia obciążenia** skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca moduł równoważenia obciążenia. Na przykład **sqlLB**. |
   | **Typ** |**Wewnętrzna:** Większość implementacji używa wewnętrznego modułu równoważenia obciążenia, który umożliwia aplikacjom w tej samej sieci wirtualnej łączenie się z grupą dostępności.  </br> **Zewnętrzne**: Umożliwia aplikacjom łączenie się z grupą dostępności za pośrednictwem publicznego połączenia internetowego. |
   | **Sieć wirtualna** |Wybierz sieć wirtualną, w których znajdują się wystąpienia programu SQL Server. |
   | **Podsieci** |Wybierz podsieć, w których znajdują się wystąpienia programu SQL Server. |
   | **Przypisanie adresu IP** |**Statyczne** |
   | **Prywatny adres IP** |Określ dostępny adres IP z podsieci. Ten adres IP należy używać podczas tworzenia odbiornika w klastrze. W skrypcie programu PowerShell w dalszej `$ILBIP` części tego artykułu użyj tego adresu dla zmiennej. |
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, to pole może się pojawić. Wybierz subskrypcję, którą chcesz skojarzyć z tym zasobem. Zwykle jest to ta sama subskrypcja, co wszystkie zasoby dla grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, w których znajdują się wystąpienia programu SQL Server. |
   | **Lokalizacja** |Wybierz lokalizację platformy Azure, w którym znajdują się wystąpienia programu SQL Server. |

6. Kliknij przycisk **Utwórz**. 

Platforma Azure tworzy moduł równoważenia obciążenia. Moduł równoważenia obciążenia należy do określonej sieci, podsieci, grupy zasobów i lokalizacji. Po zakończeniu zadania przez platformę Azure sprawdź ustawienia modułu równoważenia obciążenia na platformie Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurowanie puli zaplecza
Platforma Azure wywołuje *pulę zaplecza*puli adresów zaplecza. W takim przypadku pula zaplecza jest adresy dwóch wystąpień programu SQL Server w grupie dostępności. 

1. W grupie zasobów kliknij utworzony moduł równoważenia obciążenia. 

2. W **obszarze Ustawienia**kliknij pozycję **Pule wewnętrznej bazy**.

3. W **pulach wewnętrznej bazy**danych kliknij przycisk **Dodaj,** aby utworzyć pulę adresów zaplecza. 

4. W **obszarze Dodaj pulę zaplecza**w obszarze **Nazwa**wpisz nazwę puli zaplecza.

5. W **obszarze Maszyny wirtualne**kliknij pozycję **Dodaj maszynę wirtualną**. 

6. W obszarze **Wybierz maszyny wirtualne**kliknij pozycję **Wybierz zestaw dostępności**, a następnie określ zestaw dostępności, do którego należą maszyny wirtualne programu SQL Server.

7. Po wybraniu zestawu dostępności kliknij pozycję **Wybierz maszyny wirtualne**, wybierz dwie maszyny wirtualne obsługujące wystąpienia programu SQL Server w grupie dostępności, a następnie kliknij przycisk **Wybierz**. 

8. Kliknij **przycisk OK,** aby zamknąć bloky dla **wybierz maszyny wirtualne,** a następnie **dodaj pulę zaplecza**. 

Platforma Azure aktualizuje ustawienia puli adresów zaplecza. Teraz zestaw dostępności ma pulę dwóch wystąpień programu SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: Tworzenie sondy
Sonda definiuje, jak platforma Azure weryfikuje, które wystąpienia programu SQL Server są obecnie właścicielami odbiornika grupy dostępności. Platforma Azure sonduje usługę na podstawie adresu IP na porcie, który można zdefiniować podczas tworzenia sondy.

1. W bloku **Ustawienia** modułu równoważenia obciążenia kliknij pozycję **Sondy kondycji**. 

2. Na **bloku Sondy kondycji** kliknij przycisk **Dodaj**.

3. Skonfiguruj sondę na bloku **Dodaj sondę.** Użyj następujących wartości, aby skonfigurować sondę:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca sondę. Na przykład **SQLAlwaysOnEndPointProbe**. |
   | **Protokół** |**TCP** |
   | **Portu** |Można użyć dowolnego dostępnego portu. Na przykład *59999*. |
   | **Interwał** |*5* |
   | **Niezdrowy próg** |*2* |

4.  Kliknij przycisk **OK**. 

> [!NOTE]
> Upewnij się, że określony port jest otwarty na zaporze obu wystąpień programu SQL Server. Oba wystąpienia wymagają reguły przychodzącej dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub edytowanie reguły zapory](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Platforma Azure tworzy sondę, a następnie używa jej do testowania, które wystąpienie programu SQL Server ma odbiornik dla grupy dostępności.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: Ustawianie reguł równoważenia obciążenia
Reguły równoważenia obciążenia konfigurują sposób, w jaki moduł równoważenia obciążenia kieruje ruch do wystąpień programu SQL Server. W przypadku tego modułu równoważenia obciążenia można włączyć bezpośredni zwrot serwera, ponieważ tylko jedno z dwóch wystąpień programu SQL Server jest właścicielem zasobu odbiornika grupy dostępności naraz.

1. W bloku **Ustawienia** modułu równoważenia obciążenia kliknij pozycję **Reguły równoważenia obciążenia**. 

2. W bloku **Reguły równoważenia obciążenia** kliknij przycisk **Dodaj**.

3. W bloku **Dodaj reguły równoważenia obciążenia** skonfiguruj regułę równoważenia obciążenia. Użyj następujących ustawień: 

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca reguły równoważenia obciążenia. Na przykład **SQLAlwaysOnEndPointListener**. |
   | **Protokół** |**TCP** |
   | **Portu** |*1433* |
   | **Port zaplecza** |*1433*. Ta wartość jest ignorowana, ponieważ ta reguła używa **przestawnego adresu IP (bezpośredniego zwrotu serwera)**. |
   | **Sondy** |Użyj nazwy sondy utworzonej dla tego modułu równoważenia obciążenia. |
   | **Trwałość sesji** |**Brak** |
   | **Limit czasu bezczynnego (minuty)** |*4* |
   | **Przestawny adres IP (bezpośredni powrót serwera)** |**Enabled (Włączony)** |

   > [!NOTE]
   > Może być trzeba przewinąć w dół bloku, aby wyświetlić wszystkie ustawienia.
   > 

4. Kliknij przycisk **OK**. 
5. Platforma Azure konfiguruje regułę równoważenia obciążenia. Teraz moduł równoważenia obciążenia jest skonfigurowany do kierowania ruchu do wystąpienia programu SQL Server, w którym znajduje się odbiornik dla grupy dostępności. 

W tym momencie grupa zasobów ma moduł równoważenia obciążenia, który łączy się z obu komputerów programu SQL Server. Moduł równoważenia obciążenia zawiera również adres IP dla odbiornika grupy dostępności programu SQL Server Always On, dzięki czemu każda z maszyn może odpowiadać na żądania dotyczące grup dostępności.

> [!NOTE]
> Jeśli wystąpienia programu SQL Server znajdują się w dwóch oddzielnych regionach, powtórz kroki w innym regionie. Każdy region wymaga modułu równoważenia obciążenia. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurowanie klastra do używania adresu IP modułu równoważenia obciążenia
Następnym krokiem jest skonfigurowanie odbiornika w klastrze i przesuń odbiornika do trybu online. Wykonaj następujące czynności: 

1. Utwórz odbiornik grupy dostępności w klastrze trybu failover. 

2. Przewiń słuchacza do trybu online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5: Tworzenie odbiornika grupy dostępności w klastrze trybu failover
W tym kroku ręcznie utworzysz odbiornik grupy dostępności w Menedżerze klastrów trybu failover i programie SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Sprawdź konfigurację odbiornika

Jeśli zasoby klastra i zależności są poprawnie skonfigurowane, powinno być możliwe wyświetlanie odbiornika w programie SQL Server Management Studio. Aby ustawić port odbiornika, wykonaj następujące czynności:

1. Uruchom program SQL Server Management Studio, a następnie połącz się z repliką podstawową.

2. Przejdź do **funkcji AlwaysOn High Availability** > **Availability Grupowanie** > **grup odbiorników**.  
    Nazwa odbiornika utworzona w Menedżerze klastrów trybu failover powinna być teraz widoczna. 

3. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Właściwości**.

4. W polu **Port** określ numer portu dla odbiornika grupy dostępności, używając $EndpointPort używanej wcześniej (wartość domyślna 1433 była wartością domyślną), a następnie kliknij przycisk **OK**.

Masz teraz grupę dostępności w maszynach wirtualnych platformy Azure uruchomionych w trybie Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testowanie połączenia ze odbiornikiem
Przetestuj połączenie, wykonując następujące czynności:

1. RDP do wystąpienia programu SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Ten serwer może być innym wystąpieniem programu SQL Server w klastrze.

2. Użyj narzędzia **sqlcmd,** aby przetestować połączenie. Na przykład następujący skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pośrednictwem odbiornika za pomocą uwierzytelniania systemu Windows:
   
        sqlcmd -S <listenerName> -E

Połączenie SQLCMD automatycznie łączy się z wystąpieniem programu SQL Server, w którym znajduje się replika podstawowa. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Tworzenie adresu IP dla dodatkowej grupy dostępności

Każda grupa dostępności używa oddzielnego odbiornika. Każdy odbiornik ma swój własny adres IP. Użyj tego samego modułu równoważenia obciążenia, aby przytrzymać adres IP dla dodatkowych odbiorników. Po utworzeniu grupy dostępności dodaj adres IP do modułu równoważenia obciążenia, a następnie skonfiguruj odbiornik.

Aby dodać adres IP do modułu równoważenia obciążenia za pomocą witryny Azure portal, wykonaj następujące czynności:

1. W witrynie Azure portal otwórz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie kliknij moduł równoważenia obciążenia. 

2. W obszarze **USTAWIENIA**kliknij pozycję **Pula adresów IP zaplecza**, a następnie kliknij pozycję **Dodaj**. 

3. W obszarze **Dodaj adres IP frontendu**przypisz nazwę frontokalnej. 

4. Sprawdź, czy **sieć wirtualna** i **podsieć** są takie same jak wystąpienia programu SQL Server.

5. Ustaw adres IP odbiornika. 
   
   >[!TIP]
   >Adres IP można ustawić na statyczny i wpisać adres, który nie jest obecnie używany w podsieci. Alternatywnie można ustawić adres IP jako dynamiczny i zapisać nową pulę adresów IP front-end. Po wykonaniu tej tej usługi witryny portal Azure automatycznie przypisuje dostępny adres IP do puli. Następnie można ponownie otworzyć pulę adresów IP front-end i zmienić przypisanie na statyczne. 

6. Zapisz adres IP odbiornika. 

7. Dodaj sondę kondycji przy użyciu następujących ustawień:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa identyfikująca sondę.
   |**Protokół** |TCP
   |**Portu** |Nieużywane port TCP, który musi być dostępny na wszystkich maszynach wirtualnych. Nie może być używany do żadnych innych celów. Nie dwóch odbiorników można użyć tego samego portu sondy. 
   |**Interwał** |Czas między próbami sondy. Użyj wartości domyślnej (5).
   |**Niezdrowy próg** |Liczba kolejnych progów, które powinny zakończyć się niepowodzeniem, zanim maszyna wirtualna jest uważana za złą w złą kondycję.

8. Kliknij **przycisk OK,** aby zapisać sondę. 

9. Utwórz regułę równoważenia obciążenia. Kliknij **pozycję Wczytywanie reguł równoważenia**, a następnie kliknij przycisk **Dodaj**.

10. Skonfiguruj nową regułę równoważenia obciążenia przy użyciu następujących ustawień:

    |Ustawienie |Wartość
    |:-----|:----
    |**Nazwa** |Nazwa identyfikujący regułę równoważenia obciążenia. 
    |**Adres IP frontu** |Wybierz utworzony adres IP. 
    |**Protokół** |TCP
    |**Portu** |Użyj portu, którego używają wystąpienia programu SQL Server. Wystąpienie domyślne używa portu 1433, chyba że został zmieniony. 
    |**Port zaplecza** |Użyj tej samej wartości co **Port**.
    |**Pula zaplecza** |Pula zawierająca maszyny wirtualne z wystąpieniami programu SQL Server. 
    |**Sonda kondycji** |Wybierz utworzoną sondę.
    |**Trwałość sesji** |Brak
    |**Limit czasu bezczynnego (minuty)** |Domyślnie (4)
    |**Przestawny adres IP (bezpośredni powrót serwera)** | Enabled (Włączony)

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurowanie grupy dostępności do używania nowego adresu IP

Aby zakończyć konfigurowanie klastra, powtórz kroki, które zostały wykonane podczas tworzenia pierwszej grupy dostępności. Oznacza to, że [skonfiguruj klaster tak, aby używał nowego adresu IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po dodaniu adresu IP odbiornika skonfiguruj dodatkową grupę dostępności, wykonując następujące czynności: 

1. Sprawdź, czy port sondy dla nowego adresu IP jest otwarty na obu maszynach wirtualnych programu SQL Server. 

2. [W Menedżerze klastrów dodaj punkt dostępu klienta](#addcap).

3. [Skonfiguruj zasób IP dla grupy dostępności](#congroup).

   >[!IMPORTANT]
   >Podczas tworzenia adresu IP należy użyć adresu IP dodanego do modułu równoważenia obciążenia.  

4. [Uzależnij zasób grupy dostępności programu SQL Server od punktu dostępu klienta](#dependencyGroup).

5. [Uzależnij zasób punktu dostępu klienta od adresu IP](#listname).
 
6. [Ustawianie parametrów klastra w programie PowerShell](#setparam).

Po skonfigurowaniu grupy dostępności do używania nowego adresu IP należy skonfigurować połączenie z odbiornikiem. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Dodawanie reguły równoważenia obciążenia dla rozproszonej grupy dostępności

Jeśli grupa dostępności uczestniczy w rozproszonej grupie dostępności, moduł równoważenia obciążenia wymaga dodatkowej reguły. Ta reguła przechowuje port używany przez odbiornik rozproszonej grupy dostępności.

>[!IMPORTANT]
>Ten krok ma zastosowanie tylko wtedy, gdy grupa dostępności uczestniczy w [rozproszonej grupie dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na każdym serwerze, który uczestniczy w rozproszonej grupie dostępności, utwórz regułę przychodzącą dla portu TCP odbiornika rozproszonej grupy dostępności. W wielu przykładach w dokumentacji użyto 5022. 

1. W witrynie Azure portal kliknij moduł równoważenia obciążenia i kliknij pozycję **Reguły równoważenia obciążenia,** a następnie kliknij pozycję **+Dodaj**. 

1. Utwórz regułę równoważenia obciążenia z następującymi ustawieniami:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa identyfikująca regułę równoważenia obciążenia dla rozproszonej grupy dostępności. 
   |**Adres IP frontu** |Użyj tego samego adresu IP frontendu co grupa dostępności.
   |**Protokół** |TCP
   |**Portu** |5022 - Port dla [rozproszonego odbiornika punktów końcowych grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Może być dowolny dostępny port.  
   |**Port zaplecza** | 5022 - Użyj tej samej wartości co **Port**.
   |**Pula zaplecza** |Pula zawierająca maszyny wirtualne z wystąpieniami programu SQL Server. 
   |**Sonda kondycji** |Wybierz utworzoną sondę.
   |**Trwałość sesji** |Brak
   |**Limit czasu bezczynnego (minuty)** |Domyślnie (4)
   |**Przestawny adres IP (bezpośredni powrót serwera)** | Enabled (Włączony)

Powtórz te kroki dla modułu równoważenia obciążenia w innych grupach dostępności, które uczestniczą w rozproszonych grupach dostępności.

Jeśli ograniczasz dostęp za pomocą grupy zabezpieczeń sieciowej platformy Azure, upewnij się, że reguły zezwalania obejmują adresy IP maszyny Wirtualnej programu SQL Server zaplecza i zmienne adresy IP modułu równoważenia obciążenia dla odbiornika AG i podstawowy adres IP klastra, jeśli ma to zastosowanie.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md)
