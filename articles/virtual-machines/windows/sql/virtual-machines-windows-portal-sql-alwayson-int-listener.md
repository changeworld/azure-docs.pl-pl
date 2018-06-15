---
title: Utwórz odbiornik grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Instrukcje krok po kroku dotyczące tworzenia odbiornika dla grupy dostępności AlwaysOn dla programu SQL Server na maszynach wirtualnych Azure
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
ms.openlocfilehash: 0399f9ef969098216e080140a67f81725b670115
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400634"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurowanie usługi równoważenia obciążenia zawsze włączonej grupy dostępności na platformie Azure
W tym artykule opisano sposób tworzenia modułu równoważenia obciążenia dla grupy dostępności programu SQL Server zawsze włączony w maszynach wirtualnych platformy Azure, które działają z usługą Azure Resource Manager. Grupa dostępności wymaga modułu równoważenia obciążenia w przypadku wystąpienia programu SQL Server na maszynach wirtualnych Azure. Moduł równoważenia obciążenia przechowuje adres IP dla odbiornika grupy dostępności. Jeśli grupy dostępności obejmuje wielu regionach, każdy region musi modułu równoważenia obciążenia.

Do zakończenia tego zadania, należy mieć wdrożonych na maszynach wirtualnych Azure, których są uruchomione za pomocą Menedżera zasobów grupy dostępności programu SQL Server. Maszyny wirtualne zarówno programu SQL Server musi należeć do tego samego zestawu dostępności. Można użyć [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) można automatycznie utworzyć grupy dostępności w Menedżerze zasobów. Ten szablon automatycznie utworzy wewnętrznego modułu równoważenia obciążenia. 

Jeśli wolisz, możesz [ręcznie skonfigurować grupę dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

W tym artykule wymaga już skonfigurowania grup dostępności.  

Tematy pokrewne obejmują:

* [Konfigurowanie zawsze włączonych grup dostępności maszyny Wirtualnej Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurowanie połączenia do wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Instruktaż w tym artykule, możesz utworzyć i skonfigurowania funkcji równoważenia obciążenia w portalu Azure. Po zakończeniu procesu konfigurowania klastra do korzystania z adresu IP z usługi równoważenia obciążenia dla odbiornika grupy dostępności.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Tworzenie i konfigurowanie usługi równoważenia obciążenia w portalu Azure
W tej części zadanie wykonaj następujące czynności:

1. W portalu Azure Utwórz moduł równoważenia obciążenia i skonfiguruj adres IP.
2. Skonfiguruj pulę zaplecza.
3. Utwórz sondy. 
4. Ustaw reguły równoważenia obciążenia.

> [!NOTE]
> W przypadku wystąpienia programu SQL Server w wielu grup zasobów i regionów, wykonać każdy krok dwa razy, raz w każdej grupie zasobów.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Tworzenie usługi równoważenia obciążenia i skonfiguruj adres IP
Najpierw utwórz moduł równoważenia obciążenia. 

1. W portalu Azure Otwórz grupę zasobów, która zawiera maszyny wirtualnej programu SQL Server. 

2. W tej grupie zasobów, kliknij przycisk **Dodaj**.

3. Wyszukaj **modułu równoważenia obciążenia** , a następnie w wynikach wyszukiwania wybierz **moduł równoważenia obciążenia**, opublikowana przez **Microsoft**.

4. Na **modułu równoważenia obciążenia** bloku, kliknij przycisk **Utwórz**.

5. W **modułu równoważenia obciążenia Utwórz** okna dialogowego należy skonfigurować usługę równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekst reprezentujący usługi równoważenia obciążenia. Na przykład **sqlLB**. |
   | **Typ** |**Wewnętrzny**: większości wdrożeń używać wewnętrznego modułu równoważenia obciążenia, które umożliwia aplikacji łączenie się z grupą dostępności w ramach tej samej sieci wirtualnej.  </br> **Zewnętrzne**: umożliwia aplikacji łączenie się z grupą dostępności za pośrednictwem publicznego połączenia internetowego. |
   | **Sieć wirtualna** |Wybierz czy intances programu SQL Server znajdują się w sieci wirtualnej. |
   | **Podsieć** |Wybierz podsieć, będące w wystąpieniach programu SQL Server. |
   | Przypisywanie adresów IP |**Statyczne** |
   | **Prywatny adres IP** |Podaj dostępny adres IP z podsieci. Użyj tego adresu IP, tworząc odbiornik w klastrze. W skrypcie programu PowerShell w dalszej części tego artykułu, użyj tego samego adresu dla `$ILBIP` zmiennej. |
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, w tym polu może być wyświetlany. Wybierz subskrypcję, którą chcesz skojarzyć z tym zasobem. Zwykle jest tej samej subskrypcji co wszystkie zasoby grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, będące w wystąpieniach programu SQL Server. |
   | **Lokalizacja** |Wybierz wystąpienia programu SQL Server znajdują się w lokalizacji platformy Azure. |

6. Kliknij przycisk **Utwórz**. 

Platforma Azure tworzy moduł równoważenia obciążenia. Moduł równoważenia obciążenia należy do określonej sieci, podsieci, grupy zasobów i lokalizacji. Po zakończeniu zadania Azure Sprawdź ustawienia usługi równoważenia obciążenia na platformie Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurowanie puli zaplecza
Azure wymaga puli adresów zaplecza *puli zaplecza*. W takim przypadku pula zaplecza to adresy dwa wystąpienia programu SQL Server w grupie dostępności. 

1. W grupie zasobów kliknij przycisk równoważenia obciążenia, który został utworzony. 

2. Na **ustawienia**, kliknij przycisk **pul zaplecza**.

3. Na **pul zaplecza**, kliknij przycisk **Dodaj** do tworzenia puli adresów zaplecza. 

4. Na **Dodaj pulę zaplecza**w obszarze **nazwa**, wpisz nazwę puli zaplecza.

5. W obszarze **maszyn wirtualnych**, kliknij przycisk **Dodaj maszynę wirtualną**. 

6. W obszarze **wybierz maszyny wirtualne**, kliknij przycisk **wybierz zestaw dostępności**, a następnie określ, że maszyny wirtualne programu SQL Server należy do zestawu dostępności.

7. Po wybraniu zestawu dostępności, kliknij przycisk **wybierz maszyny wirtualne**wybierz dwóch maszyn wirtualnych hostujących wystąpienie programu SQL Server w grupie dostępności, a następnie kliknij przycisk **wybierz**. 

8. Kliknij przycisk **OK** zamknąć bloków dla **wybierz maszyny wirtualne**, i **Dodaj pulę zaplecza**. 

Azure aktualizuje ustawienia dla puli adresów zaplecza. Zestaw dostępności ma teraz puli dwa wystąpienia programu SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: Tworzenie sondę
Sonda definiuje sposób Azure sprawdza, które z wystąpień programu SQL Server jest bieżącym właścicielem odbiornika grupy dostępności. Azure sondy usługi na podstawie adresu IP na porcie definiowany podczas tworzenia sondy.

1. W module równoważenia obciążenia **ustawienia** bloku, kliknij przycisk **sondy kondycji**. 

2. Na **sondy kondycji** bloku, kliknij przycisk **Dodaj**.

3. Skonfiguruj sondy na **sondowania Dodaj** bloku. Aby skonfigurować sondy, użyj następujących wartości:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekst reprezentujący sondy. Na przykład **SQLAlwaysOnEndPointProbe**. |
   | **Protokół** |**TCP** |
   | **Port** |Umożliwia dowolny dostępny port. Na przykład *59999*. |
   | **Interwał** |*5* |
   | **Próg złej kondycji** |*2* |

4.  Kliknij przycisk **OK**. 

> [!NOTE]
> Upewnij się, że port, który określisz jest otwarty na zaporze obu wystąpień programu SQL Server. Oba wystąpienia wymaga regułę ruchu przychodzącego dla portu TCP, którego używasz. Aby uzyskać więcej informacji, zobacz [Dodawanie lub edytowanie reguły zapory](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure tworzy badanie i używa ich do testowania wystąpieniu programu SQL Server, z którym ma odbiornika dla grupy dostępności.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: Ustaw reguły równoważenia obciążenia
Reguły równoważenia obciążenia skonfigurować sposób równoważenia obciążenia kieruje ruchem do wystąpień programu SQL Server. Dla tej usługi równoważenia obciążenia ponieważ tylko jeden z dwóch wystąpień programu SQL Server jest właścicielem zasobu odbiornika grupy dostępności w czasie włączyć się bezpośredni zwrot serwera.

1. W module równoważenia obciążenia **ustawienia** bloku, kliknij przycisk **reguły równoważenia obciążenia**. 

2. Na **reguły równoważenia obciążenia** bloku, kliknij przycisk **Dodaj**.

3. Na **reguły równoważenia obciążenia Dodaj** bloku, skonfiguruj regułę równoważenia obciążenia. Użyj następujących ustawień: 

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekst reprezentujący reguły równoważenia obciążenia. Na przykład **SQLAlwaysOnEndPointListener**. |
   | **Protokół** |**TCP** |
   | **Port** |*1433* |
   | **Port zaplecza** |*1433*. Ta wartość jest ignorowana, ponieważ ta zasada używa **pływającego adresu IP (bezpośredni zwrot serwera)**. |
   | **Sondy** |Użyj nazwy sondy utworzonego dla tej usługi równoważenia obciążenia. |
   | **Trwałość sesji** |Brak |
   | Limit czasu bezczynności (w minutach) |*4* |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** |**włączone** |

   > [!NOTE]
   > Może być konieczne przewiń w dół bloku, aby wyświetlić wszystkie ustawienia.
   > 

4. Kliknij przycisk **OK**. 
5. Azure konfiguruje reguły równoważenia obciążenia. Moduł równoważenia obciążenia jest skonfigurowany kierowanie ruchem do wystąpienia programu SQL Server, który jest hostem odbiornika dla grupy dostępności. 

W tym momencie grupa zasobów ma równoważenia obciążenia, który łączy na obu komputerach programu SQL Server. Usługi równoważenia obciążenia zawiera także adresu IP dla programu SQL Server AlwaysOn odbiornika grupy dostępności, dzięki czemu maszyna może odpowiadać na żądania dla grupy dostępności.

> [!NOTE]
> W przypadku wystąpienia programu SQL Server w dwóch oddzielnych regionach, powtórz kroki w innym regionie. Każdy region wymaga usługi równoważenia obciążenia. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurowanie klastra do używania adresu IP usługi równoważenia obciążenia
Następnym krokiem jest Konfigurowanie odbiornika w klastrze, a następnie przełącz odbiornika w trybie online. Wykonaj następujące czynności: 

1. Tworzenie odbiornika grupy dostępności w klastrze trybu failover. 

2. Przełącz odbiornika w trybie online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5: Tworzenie odbiornika grupy dostępności w klastrze trybu failover
W tym kroku ręcznie utworzyć odbiornika grupy dostępności w Menedżerze klastra trybu Failover i SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Sprawdź konfigurację odbiornika

Jeśli zasoby klastra i zależności są poprawnie skonfigurowane, należy wyświetlić odbiornika w programie SQL Server Management Studio. Aby ustawić port odbiornika, wykonaj następujące czynności:

1. Uruchom program SQL Server Management Studio i Połącz z repliką podstawową.

2. Przejdź do **AlwaysOn wysokiej dostępności** > **grup dostępności** > **odbiorniki grupy dostępności**.  
    Powinna zostać wyświetlona nazwa odbiornika utworzonego w Menedżerze klastra trybu Failover. 

3. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij przycisk **właściwości**.

4. W **portu** polu Określ numer portu dla odbiornika grupy dostępności, używając $EndpointPort użyto wcześniej (1433 jest wartość domyślna), a następnie kliknij przycisk **OK**.

Masz teraz grupy dostępności na maszynach wirtualnych Azure działających w trybie Menedżera zasobów. 

## <a name="test-the-connection-to-the-listener"></a>Testuj połączenie odbiornika
Testuj połączenie, wykonując następujące czynności:

1. RDP do wystąpienia programu SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Ten serwer może być inne wystąpienie programu SQL Server w klastrze.

2. Użyj **sqlcmd** narzędzia do testowania połączenia. Na przykład poniższy skrypt ustanawia **sqlcmd** połączenie z repliką podstawową za pośrednictwem odbiornika z uwierzytelnianiem systemu Windows:
   
        sqlcmd -S <listenerName> -E

Połączenia narzędzia SQLCMD automatycznie łączy się z wystąpienia programu SQL Server, który obsługuje replikę podstawową. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Utwórz adres IP dla grupy dostępności dodatkowe

Każda grupa dostępności używa oddzielnych odbiornika. Każdy odbiornika ma swój własny adres IP. Użyj tego samego modułu równoważenia obciążenia do przechowywania adresów IP dla dodatkowych odbiorników. Po utworzeniu grupy dostępności, Dodaj adres IP do modułu równoważenia obciążenia, a następnie skonfiguruj odbiornik.

Aby dodać adres IP do modułu równoważenia obciążenia z portalu Azure, wykonaj następujące czynności:

1. W portalu Azure Otwórz grupę zasobów, która zawiera usługi równoważenia obciążenia, a następnie kliknij przycisk usługi równoważenia obciążenia. 

2. W obszarze **ustawienia**, kliknij przycisk **puli adresów IP frontonu**, a następnie kliknij przycisk **Dodaj**. 

3. W obszarze **Dodaj adres IP frontonu**, Przypisz nazwę frontonu. 

4. Sprawdź, czy **sieci wirtualnej** i **podsieci** są takie same jak wystąpień programu SQL Server.

5. Określ adres IP dla odbiornika. 
   
   >[!TIP]
   >Można wartość statyczny adres IP, a następnie wpisz adres, który nie jest obecnie używany w podsieci. Można ustawić na dynamiczny adres IP i zapisać nowej puli adresów IP frontonu. Po wykonaniu tej czynności w portalu Azure automatycznie przypisuje dostępnego adresu IP do puli. Można ponownie otworzyć puli adresów IP frontonu i zmienić przypisanie statyczne. 

6. Zapisz adres IP dla odbiornika. 

7. Dodaj sondy kondycji przy użyciu następujących ustawień:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwę identyfikującą sondy.
   |**Protokół** |TCP
   |**Port** |Nieużywany port TCP, które muszą być dostępne na wszystkich maszynach wirtualnych. Nie można używać do innych celów. Nie dwa odbiorniki można używać tego samego portu sondowania. 
   |**Interwał** |Ilość czasu między próbami sondy. Użyj domyślnej (5).
   |**Próg złej kondycji** |Liczba kolejnych progów, które powinno zakończyć się niepowodzeniem przed maszyny wirtualnej jest określana jako zła.

8. Kliknij przycisk **OK** można zapisać sondy. 

9. Utwórz regułę równoważenia obciążenia. Kliknij przycisk **reguły równoważenia obciążenia**, a następnie kliknij przycisk **Dodaj**.

10. Konfigurowanie nowego obciążenia regułę równoważenia przy użyciu następujących ustawień:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa do identyfikowania reguły równoważenia obciążenia. 
   |**Adres IP frontonu** |Wybierz adres IP, który został utworzony. 
   |**Protokół** |TCP
   |**Port** |Użyj port, którego używasz wystąpienia programu SQL Server. Domyślne wystąpienie korzysta z portu 1433, chyba że zostało zmienione. 
   |**Port zaplecza** |Użyj taką samą wartość jak **portu**.
   |**Puli wewnętrznej bazy danych** |Puli zawierającej maszyn wirtualnych o wystąpieniach programu SQL Server. 
   |**Badania kondycji** |Wybierz sondowania, który został utworzony.
   |**Trwałość sesji** |None
   |Limit czasu bezczynności (w minutach) |Domyślne (4)
   |**Zmienny adres IP (bezpośredni zwrot serwera)** | Enabled (Włączony)

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Skonfiguruj grupę dostępności, aby użyć nowego adresu IP

Aby ukończyć konfigurowanie klastra, powtórz kroki, które należy przestrzegać podczas wprowadzone pierwszej grupy dostępności. Oznacza to, skonfiguruj [klaster, aby użyć nowego adresu IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po dodaniu adresu IP dla odbiornika, należy skonfigurować grupę dostępności dodatkowych, wykonując następujące czynności: 

1. Sprawdź, czy port sondy nowy adres IP jest otwarty na obu maszynach wirtualnych programu SQL Server. 

2. [W Menedżerze klastra Dodaj punkt dostępu klienta](#addcap).

3. [Konfigurowanie zasobów adresu IP dla grupy dostępności](#congroup).

   >[!IMPORTANT]
   >Podczas tworzenia adresu IP, należy użyć adresu IP, który został dodany do modułu równoważenia obciążenia.  

4. [Skonfiguruj zasób grupy dostępności programu SQL Server zależał od punktu dostępu klienta](#dependencyGroup).

5. [Sprawdź zależne od adresu IP zasobu punktu dostępu klienta](#listname).
 
6. [Ustaw parametry klastra w programie PowerShell](#setparam).

Po skonfigurowaniu grupy dostępności, aby użyć nowego adresu IP, należy skonfigurować połączenie odbiornika. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Dodaj regułę dla rozproszonej grupy dostępności równoważenia obciążenia

Jeśli grupa dostępności uczestniczy w rozproszonej grupy dostępności, usługi równoważenia obciążenia musi dodatkowe reguły. Ta reguła zapisuje port używany przez odbiornik grupy dostępności rozproszonych.

>[!IMPORTANT]
>Ten krok ma zastosowanie tylko, gdy grupa dostępności uczestniczy w [rozproszonej grupy dostępności](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na każdym serwerze, który uczestniczy w rozproszonej grupie dostępności Utwórz regułę ruchu przychodzącego na odbiornika grupy dostępności rozproszonej TCP port. W przykładach wiele dokumentacji używa 5022. 

1. W portalu Azure kliknij modułu równoważenia obciążenia, a następnie kliknij przycisk **reguły równoważenia obciążenia**, a następnie kliknij przycisk **+ Dodaj**. 

1. Utwórz regułę równoważenia z następującymi ustawieniami obciążenia:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa do identyfikowania reguły dla grupy dostępności rozproszonej równoważenia obciążenia. 
   |**Adres IP frontonu** |Użyj tego samego adresu IP frontonu jako grupy dostępności.
   |**Protokół** |TCP
   |**Port** |5022 — numer portu [odbiornika punktu końcowego grupy dostępności rozproszonej](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Może być dowolny dostępny port.  
   |**Port zaplecza** | 5022 - Użyj tę samą wartość jak **portu**.
   |**Puli wewnętrznej bazy danych** |Puli zawierającej maszyn wirtualnych o wystąpieniach programu SQL Server. 
   |**Badania kondycji** |Wybierz sondowania, który został utworzony.
   |**Trwałość sesji** |None
   |Limit czasu bezczynności (w minutach) |Domyślne (4)
   |**Zmienny adres IP (bezpośredni zwrot serwera)** | Enabled (Włączony)

Powtórz te kroki dla usługi równoważenia obciążenia na inne uczestniczących w grupach rozproszonych dostępności grupy dostępności.

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie programu SQL Server zawsze włączonej grupy dostępności na maszynach wirtualnych Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md)
