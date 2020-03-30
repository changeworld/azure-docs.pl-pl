---
title: Konfigurowanie detektora grupy dostępności dla programu SQL Server na maszynach wirtualnych RHEL na platformie Azure — maszyny wirtualne systemu Linux | Dokumenty firmy Microsoft
description: Dowiedz się więcej o konfigurowaniu detektora grupy dostępności w programie SQL Server na maszynach wirtualnych RHEL na platformie Azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096599"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Samouczek: Konfigurowanie detektora grupy dostępności dla programu SQL Server na maszynach wirtualnych RHEL na platformie Azure

> [!NOTE]
> Przedstawiony samouczek znajduje się w **publicznej wersji zapoznawczej**. 
>
> Używamy programu SQL Server 2017 z RHEL 7.6 w tym samouczku, ale jest możliwe użycie programu SQL Server 2019 w RHEL 7 lub RHEL 8 do skonfigurowania ha. Polecenia do konfigurowania zasobów grupy dostępności uległy zmianie w programie RHEL 8 i warto przyjrzeć się temu artykułowi [Utwórz zasoby grupy dostępności](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) i Zasoby RHEL 8, aby uzyskać więcej informacji na temat prawidłowych poleceń.

W tym samouczku zostaną opisane kroki dotyczące tworzenia odbiornika grupy dostępności dla serwerów SQL na maszynach wirtualnych RHEL na platformie Azure. Poznasz następujące czynności:

> [!div class="checklist"]
> - Tworzenie modułu równoważenia obciążenia w witrynie Azure portal
> - Konfigurowanie puli zaplecza dla modułu równoważenia obciążenia
> - Tworzenie sondy dla modułu równoważenia obciążenia
> - Ustawianie reguł równoważenia obciążenia
> - Tworzenie zasobu modułu równoważenia obciążenia w klastrze
> - Tworzenie odbiornika grupy dostępności
> - Test podłączenia do odbiornika
> - Testowanie pracy awaryjnej

## <a name="prerequisite"></a>Wymagania wstępne

Ukończony [ **samouczek: Konfigurowanie grup dostępności dla programu SQL Server na maszynach wirtualnych RHEL na platformie Azure**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Tworzenie modułu równoważenia obciążenia w witrynie Azure portal

Poniższe instrukcje można wykonać kroki od 1 do 4 z [tworzenie i konfigurowanie modułu równoważenia obciążenia w](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) sekcji Azure portal [modułu równoważenia obciążenia — artykuł azure portal.](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

1. W witrynie Azure Portal otwórz grupę zasobów zawierającą maszyny wirtualne programu SQL Server. 

2. W grupie zasobów kliknij pozycję **Dodaj**.

3. Wyszukaj **moduł równoważenia obciążenia,** a następnie w wynikach wyszukiwania wybierz pozycję **Load Balancer**, który jest publikowany przez **firmę Microsoft**.

4. W bloku **Moduł równoważenia obciążenia** kliknij przycisk **Utwórz**.

5. W oknie dialogowym **Tworzenie modułu równoważenia obciążenia** skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca moduł równoważenia obciążenia. Na przykład **sqlLB**. |
   | **Typ** |**Wewnętrznego** |
   | **Sieć wirtualna** |Domyślna utworzona sieci wirtualna powinna mieć na imię **VM1VNET**. |
   | **Podsieci** |Wybierz podsieć, w których znajdują się wystąpienia programu SQL Server. Wartość domyślna powinna to być **VM1Subnet**.|
   | **Przypisanie adresu IP** |**Statyczne** |
   | **Prywatny adres IP** |Użyj `virtualip` adresu IP utworzonego w klastrze. |
   | **Subskrypcja** |Użyj subskrypcji, która została użyta dla grupy zasobów. |
   | **Grupa zasobów** |Wybierz grupę zasobów, w których znajdują się wystąpienia programu SQL Server. |
   | **Lokalizacja** |Wybierz lokalizację platformy Azure, w którym znajdują się wystąpienia programu SQL Server. |

### <a name="configure-the-back-end-pool"></a>Konfigurowanie puli zaplecza
Platforma Azure wywołuje *pulę zaplecza*puli adresów zaplecza. W takim przypadku pula zaplecza jest adresy trzech wystąpień programu SQL Server w grupie dostępności. 

1. W grupie zasobów kliknij utworzony moduł równoważenia obciążenia. 

2. W **obszarze Ustawienia**kliknij pozycję **Pule wewnętrznej bazy**.

3. W **pulach wewnętrznej bazy**danych kliknij przycisk **Dodaj,** aby utworzyć pulę adresów zaplecza. 

4. W **obszarze Dodaj pulę zaplecza**w obszarze **Nazwa**wpisz nazwę puli zaplecza.

5. W obszarze **Skojarzone**z wybierz pozycję **Maszyna wirtualna**. 

6. Wybierz każdą maszynę wirtualną w środowisku i skojarz odpowiedni adres IP z każdym wyborem.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Dodaj pulę zaplecza":::

7. Kliknij przycisk **Dodaj**. 

### <a name="create-a-probe"></a>Tworzenie sondy

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

5. Zaloguj się do wszystkich maszyn wirtualnych i otwórz port sondy za pomocą następujących poleceń:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Platforma Azure tworzy sondę, a następnie używa jej do testowania, które wystąpienie programu SQL Server ma odbiornik dla grupy dostępności.

### <a name="set-the-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

Reguły równoważenia obciążenia konfigurują sposób, w jaki moduł równoważenia obciążenia kieruje ruch do wystąpień programu SQL Server. W przypadku tego modułu równoważenia obciążenia można włączyć bezpośredni zwrot serwera, ponieważ tylko jedno z trzech wystąpień programu SQL Server jest właścicielem zasobu odbiornika grupy dostępności naraz.

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

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Dodaj regułę równoważenia obciążenia":::

4. Kliknij przycisk **OK**. 
5. Platforma Azure konfiguruje regułę równoważenia obciążenia. Teraz moduł równoważenia obciążenia jest skonfigurowany do kierowania ruchu do wystąpienia programu SQL Server, w którym znajduje się odbiornik dla grupy dostępności. 

W tym momencie grupa zasobów ma moduł równoważenia obciążenia, który łączy się ze wszystkimi komputerami programu SQL Server. Moduł równoważenia obciążenia zawiera również adres IP dla odbiornika grupy dostępności programu SQL Server Always On, dzięki czemu każdy komputer może odpowiadać na żądania dotyczące grup dostępności.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Tworzenie zasobu modułu równoważenia obciążenia w klastrze

1. Zaloguj się do podstawowej maszyny wirtualnej. Musimy utworzyć zasób, aby włączyć port sondy modułu równoważenia obciążenia platformy Azure (59999 jest używany w naszym przykładzie). Uruchom następujące polecenie:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Utwórz grupę `virtualip` zawierającą i `azure_load_balancer` zasób:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Dodawanie ograniczeń

1. Ograniczenie kolokacji musi być skonfigurowane w taki sposób, aby zapewnić, że adres IP modułu równoważenia obciążenia platformy Azure i zasób AG są uruchomione w tym samym węźle. Uruchom następujące polecenie:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Utwórz ograniczenie zamawiania, aby upewnić się, że zasób AG jest uruchomiony przed adresem IP modułu równoważenia obciążenia platformy Azure. Podczas gdy ograniczenie kolokacji implikuje ograniczenie porządkowania, wymusza je.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Aby zweryfikować ograniczenia, uruchom następujące polecenie:

    ```bash
    sudo pcs constraint list --full
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

1. W węźle podstawowym uruchom następujące polecenie w języku SQLCMD lub SSMS:

    - Zastąp adres IP `virtualip` użyty poniżej adresem IP.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Zaloguj się do każdego węzła maszyny Wirtualnej. Użyj następującego polecenia, aby otworzyć plik hosta `ag1-listener` i skonfigurować rozpoznawanie nazw hostów dla każdego komputera.

    ```
    sudo vi /etc/hosts
    ```

    W **vi** edytorze vi `i` wprowadź, aby wstawić tekst, a w `ag1-listener`pustym wierszu dodaj adres IP pliku . Następnie `ag1-listener` dodaj po spacji obok adresu IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Aby zamknąć edytor **vi,** najpierw naciśnij klawisz **Esc,** a następnie wprowadź polecenie, `:wq` aby napisać plik i zamknąć. Zrób to w każdym węźle.

## <a name="test-the-listener-and-a-failover"></a>Testowanie odbiornika i pracy awaryjnej

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Testowanie logowania do programu SQL Server przy użyciu detektora grupy dostępności

1. Użyj sqlcmd, aby zalogować się do węzła podstawowego programu SQL Server przy użyciu nazwy odbiornika grupy dostępności:

    - Użyj loginu, który został `<YourPassword>` wcześniej utworzony i zastąp poprawnym hasłem. Poniższy przykład używa `sa` logowania, który został utworzony za pomocą programu SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Sprawdź nazwę serwera, z którego masz połączenie. Uruchom następujące polecenie w języku SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Dane wyjściowe powinny być wyświetlane bieżącego węzła podstawowego. Powinno to `VM1` być, jeśli nigdy nie testował pracy awaryjnej.

    Zamknij sesję SQL, wpisując `exit` polecenie.

### <a name="test-a-failover"></a>Testowanie pracy awaryjnej

1. Uruchom następujące polecenie, aby ręcznie przemorować replikę podstawową do `<VM2>` repliki podstawowej lub innej repliki. Zamień `<VM2>` na wartość nazwy serwera.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Jeśli sprawdzisz swoje ograniczenia, zobaczysz, że inne ograniczenie zostało dodane z powodu ręcznego trybu failover:

    ```bash
    sudo pcs constraint list --full
    ```

    Zobaczysz, że dodano ograniczenie `cli-prefer-ag_cluster-master` o identyfikatorze.

1. Usuń ograniczenie z `cli-prefer-ag_cluster-master` identyfikatorem za pomocą następującego polecenia:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Sprawdź zasoby klastra `sudo pcs resource`za pomocą polecenia i powinieneś `<VM2>`zobaczyć, że wystąpienie podstawowe jest teraz .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Użyj SQLCMD, aby zalogować się do repliki podstawowej przy użyciu nazwy odbiornika:

    - Użyj loginu, który został `<YourPassword>` wcześniej utworzony i zastąp poprawnym hasłem. Poniższy przykład używa `sa` logowania, który został utworzony za pomocą programu SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Sprawdź serwer, z którego masz połączenie. Uruchom następujące polecenie w języku SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Powinieneś zobaczyć, że jesteś teraz połączony z maszyną wirtualną, do której powiodłeś się po awarii.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułów równoważenia obciążenia na platformie Azure, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie modułu równoważenia obciążenia dla grupy dostępności na maszynach wirtualnych programu Azure SQL Server](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
