---
title: 'Samouczek: Dodawanie SQL Database wystąpienia zarządzanego do grupy trybu failover'
description: Dowiedz się, jak skonfigurować grupę trybu failover dla Azure SQL Database wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 059a614dff7fc0eab5419e3e2ffdeaeecb79ad99
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981383"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Samouczek: Dodawanie SQL Database wystąpienia zarządzanego do grupy trybu failover

Dodaj wystąpienie zarządzane SQL Database do grupy trybu failover. W tym artykule dowiesz się jak:

> [!div class="checklist"]
> - Tworzenie podstawowego wystąpienia zarządzanego
> - Utwórz dodatkowe wystąpienie zarządzane jako część [grupy trybu failover](sql-database-auto-failover-group.md). 
> - Testowe przełączenie w tryb failover

  > [!NOTE]
  > Tworzenie wystąpienia zarządzanego może zająć dużo czasu. W związku z tym ten samouczek może trwać kilka godzin. Aby uzyskać więcej informacji o czasach udostępniania, zobacz [operacje zarządzania wystąpieniami zarządzanymi](sql-database-managed-instance.md#managed-instance-management-operations). Korzystanie z grup trybu failover z wystąpieniami zarządzanymi jest obecnie dostępne w wersji zapoznawczej. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcję platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz. 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1 — Tworzenie grupy zasobów i podstawowego wystąpienia zarządzanego
W tym kroku utworzysz grupę zasobów i podstawowe wystąpienie zarządzane dla grupy trybu failover przy użyciu Azure Portal. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Wybierz, aby **utworzyć zasób** w lewym górnym rogu Azure Portal. 
1. Wpisz `managed instance` w polu wyszukiwania i wybierz opcję wystąpienia zarządzanego Azure SQL. 
1. Wybierz pozycję **Utwórz** , aby uruchomić stronę tworzenia **wystąpienia zarządzanego SQL** . 
1. Na stronie **tworzenie Azure SQL Database wystąpienia zarządzanego** na karcie **podstawowe**
    1. W obszarze **szczegóły projektu**wybierz **subskrypcję** z listy rozwijanej, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz nazwę grupy zasobów, `myResourceGroup`na przykład. 
    1. W obszarze **szczegóły wystąpienia zarządzanego**Podaj nazwę wystąpienia zarządzanego oraz region, w którym chcesz wdrożyć wystąpienie zarządzane. Pozostaw wartości domyślne w obszarze **obliczenia i magazyn** . 
    1. W obszarze **konto administratora**Podaj nazwę logowania administratora, `azureuser`na przykład i złożone hasło administratora. 

    ![Utwórz podstawowe MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Pozostaw pozostałe ustawienia w wartości domyślne, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia wystąpienia zarządzanego. 
1. Wybierz pozycję **Utwórz** , aby utworzyć podstawowe wystąpienie zarządzane. 


## <a name="2---create-a-virtual-network"></a>2 — Tworzenie sieci wirtualnej
W tym kroku utworzysz sieć wirtualną dla pomocniczego wystąpienia zarządzanego. Ten krok jest niezbędny, ponieważ istnieje wymóg, aby podsieć podstawowych i pomocniczych wystąpień zarządzanych miała nienakładające się zakresy adresów. 

Aby sprawdzić zakres podsieci podstawowej sieci wirtualnej, wykonaj następujące kroki:
1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów i wybierz sieć wirtualną dla wystąpienia podstawowego. 
1. W obszarze **Ustawienia** wybierz pozycję **podsieci** i zanotuj **zakres adresów**. Zakres adresów podsieci sieci wirtualnej dla pomocniczego wystąpienia zarządzanego nie może się nakładać. 


   ![Podsieć podstawowa](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Aby utworzyć sieć wirtualną, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** i Wyszukaj *sieć wirtualną*. 
1. Wybierz opcję **Virtual Network** publikowaną przez firmę Microsoft, a następnie wybierz pozycję **Utwórz** na następnej stronie. 
1. Wypełnij pola wymagane, aby skonfigurować sieć wirtualną dla pomocniczego wystąpienia zarządzanego, a następnie wybierz pozycję **Utwórz**. 

   W poniższej tabeli przedstawiono wartości niezbędne do zapewnienia dodatkowej sieci wirtualnej:

    | **Pole** | Value |
    | --- | --- |
    | **Nazwa** |  Nazwa sieci wirtualnej, która ma być używana przez pomocnicze wystąpienie zarządzane, na przykład `vnet-sql-mi-secondary`. |
    | **Przestrzeń adresowa** | Przestrzeń adresowa sieci wirtualnej, `10.128.0.0/16`na przykład. | 
    | **Subskrypcja** | Subskrypcja, w której znajduje się główne wystąpienie zarządzane i Grupa zasobów. |
    | **Region** | Lokalizacja, w której zostanie wdrożone pomocnicze wystąpienie zarządzane. |
    | **Podsieć** | Nazwa podsieci. `default`jest dostarczany domyślnie. |
    | **Zakres adresów**| Zakres adresów dla podsieci. Ta wartość musi być inna niż zakres adresów podsieci używany przez sieć wirtualną głównego wystąpienia zarządzanego, `10.128.0.0/24`na przykład.  |
    | &nbsp; | &nbsp; |

    ![Dodatkowe wartości sieci wirtualnej](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3 — Tworzenie pomocniczego wystąpienia zarządzanego
W tym kroku utworzysz dodatkowe wystąpienie zarządzane w Azure Portal, które również skonfiguruje sieć między dwoma wystąpieniami zarządzanymi. 

Drugie wystąpienie zarządzane musi:
- Być pusty. 
- Ma inną podsieć i zakres adresów IP niż podstawowe wystąpienie zarządzane. 

Aby utworzyć dodatkowe wystąpienie zarządzane, wykonaj następujące kroki: 

1. W [Azure Portal](http://portal.azure.com)wybierz pozycję **Utwórz zasób** i Wyszukaj *wystąpienie zarządzane Azure SQL*. 
1. Wybierz opcję **wystąpienia zarządzanego Azure SQL** opublikowaną przez firmę Microsoft, a następnie wybierz pozycję **Utwórz** na następnej stronie.
1. Na karcie **podstawy** na stronie **Tworzenie Azure SQL Database wystąpienia zarządzanego** wprowadź wymagane pola, aby skonfigurować dodatkowe wystąpienie zarządzane. 

   W poniższej tabeli przedstawiono wartości niezbędne do pomocniczego wystąpienia zarządzanego:
 
    | **Pole** | Value |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Grupa zasobów**| Grupa zasobów, w której znajduje się podstawowe wystąpienie zarządzane. |
    | **Nazwa wystąpienia zarządzanego** | Nazwa nowego pomocniczego wystąpienia zarządzanego, takiego jak`sql-mi-secondary`  | 
    | **Region**| Lokalizacja pomocniczego wystąpienia zarządzanego.  |
    | **Identyfikator logowania administratora wystąpienia zarządzanego** | Nazwa logowania, która ma być używana dla nowego pomocniczego wystąpienia zarządzanego, na `azureuser`przykład. |
    | **Hasło** | Złożone hasło, które będzie używane przez nazwę logowania administratora dla nowego wystąpienia zarządzanego.  |
    | &nbsp; | &nbsp; |

1. Na karcie **Sieć** dla **Virtual Network**wybierz sieć wirtualną utworzoną dla pomocniczego wystąpienia zarządzanego z listy rozwijanej.

   ![Pomocnicza sieć MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na karcie **Ustawienia dodatkowe** na potrzeby **replikacji**geograficznej wybierz opcję **tak** , aby _użyć jej jako pomocniczej pracy w trybie failover_. Wybierz z listy rozwijanej podstawowe wystąpienie zarządzane. 
    1. Upewnij się, że sortowanie i strefa czasowa są zgodne z podstawowym wystąpieniem zarządzanym. Główne wystąpienie zarządzane utworzone w tym samouczku użyło domyślnego `SQL_Latin1_General_CP1_CI_AS` sortowania `(UTC) Coordinated Universal Time` i strefy czasowej. 

   ![Pomocnicza sieć MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia dla pomocniczego wystąpienia zarządzanego. 
1. Wybierz pozycję **Utwórz** , aby utworzyć pomocnicze wystąpienie zarządzane. 


## <a name="4---create-primary-virtual-network-gateway"></a>4 — Tworzenie bramy podstawowej sieci wirtualnej 

W przypadku dwóch wystąpień zarządzanych, które mają być uwzględnione w grupie trybu failover, musi istnieć Brama skonfigurowana między sieciami wirtualnymi dwóch wystąpień zarządzanych, aby umożliwić komunikację sieciową. Bramę dla podstawowego wystąpienia zarządzanego można utworzyć przy użyciu Azure Portal:

1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, a następnie wybierz zasób **sieci wirtualnej** dla podstawowego wystąpienia zarządzanego. 
1. W obszarze **Ustawienia** wybierz pozycję **podsieci** , a następnie wybierz opcję, aby dodać nową **podsieć bramy**. Pozostaw wartości domyślne. 

   ![Dodawanie bramy dla podstawowego wystąpienia zarządzanego](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po utworzeniu bramy podsieci wybierz pozycję **Utwórz zasób** w lewym okienku nawigacji, a następnie wpisz `Virtual network gateway` w polu wyszukiwania. Wybierz zasób **bramy sieci wirtualnej** Opublikowany przez **firmę Microsoft**. 

   ![Utwórz nową bramę sieci wirtualnej](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Wypełnij pola wymagane, aby skonfigurować bramę jako podstawowe wystąpienie zarządzane. 

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla podstawowego wystąpienia zarządzanego:
 
    | **Pole** | Value |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Nazwa** | Nazwa bramy sieci wirtualnej, `primary-mi-gateway`na przykład. | 
    | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
    | **Typ bramy** | wybierz pozycję **VPN**. |
    | **Typ sieci VPN** | Wybierz pozycję **oparta na trasach** |
    | **SKU**| Pozostaw wartość domyślną `VpnGw1`. |
    | **Location**| Lokalizacja, w której znajduje się podstawowe wystąpienie zarządzane i podstawowa Sieć wirtualna.   |
    | **Sieć wirtualna**| Wybierz sieć wirtualną, która została utworzona w sekcji 2, na przykład `vnet-sql-mi-primary`. |
    | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
    | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, `primary-gateway-IP`na przykład. |
    | &nbsp; | &nbsp; |
1. Pozostaw pozostałe wartości jako domyślne, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia bramy sieci wirtualnej.

   ![Ustawienia bramy podstawowej](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć nową bramę sieci wirtualnej. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 — Konfigurowanie bramy dodatkowej sieci wirtualnej 

Powtórz kroki opisane w poprzedniej sekcji, aby utworzyć podsieć i bramę sieci wirtualnej dla pomocniczego wystąpienia zarządzanego. Wypełnij pola wymagane, aby skonfigurować bramę dla pomocniczego wystąpienia zarządzanego. 

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla pomocniczego wystąpienia zarządzanego:

   | **Pole** | Value |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której znajduje się pomocnicze wystąpienie zarządzane. |
   | **Nazwa** | Nazwa bramy sieci wirtualnej, `secondary-mi-gateway`na przykład. | 
   | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
   | **Typ bramy** | wybierz pozycję **VPN**. |
   | **Typ sieci VPN** | Wybierz pozycję **oparta na trasach** |
   | **SKU**| Pozostaw wartość domyślną `VpnGw1`. |
   | **Location**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i pomocnicza Sieć wirtualna.   |
   | **Sieć wirtualna**| Wybierz sieć wirtualną, która została utworzona w sekcji 2, na przykład `vnet-sql-mi-secondary`. |
   | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
   | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, `secondary-gateway-IP`na przykład. |
   | &nbsp; | &nbsp; |

   ![Ustawienia bramy dodatkowej](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6 — łączenie bram
W tym kroku utworzysz połączenie między bramami. Połączenie musi zostać ustanowione z poziomu podstawowego do bramy pomocniczej, a następnie należy nawiązać oddzielne połączenie między lokacją pomocniczą a bramą podstawową. Pamiętaj, aby podczas konfigurowania łączności między bramami korzystać z tego samego **klucza** współużytkowanego. 

Aby skonfigurować połączenie, wykonaj następujące kroki:

1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com) i wybierz bramę podstawową utworzoną w kroku 4. 
1. Wybierz pozycję **połączenia** w obszarze **Ustawienia** , a następnie wybierz pozycję **Dodaj** , aby utworzyć nowe połączenie. 

   ![Dodaj połączenie z bramą podstawową](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Wprowadź nazwę połączenia, na przykład `Primary-connection`, i wpisz wartość dla `mi1mi2psk`klucza współużytkowanego, takiego jak. 
1. Wybierz **drugą bramę sieci wirtualnej** , a następnie wybierz bramę dla pomocniczego wystąpienia zarządzanego, na `secondary-mi-gateway`przykład. 

   ![Utwórz połączenie podstawowe z serwerem pomocniczym](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Wybierz **przycisk OK** , aby dodać nowe połączenie bramy "podstawowe-to-pomocnicze".
1. Powtórz te kroki, aby utworzyć połączenie z bramy pomocniczego wystąpienia zarządzanego do bramy głównego wystąpienia zarządzanego. 

   ![Utwórz połączenie pomocnicze z połączeniem podstawowym](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7\. Tworzenie grupy trybu failover
W tym kroku utworzysz grupę trybu failover i dodasz do niej oba wystąpienia zarządzane. 

1. W [Azure Portal](https://portal.azure.com)przejdź do pozycji **wszystkie usługi** , `managed instance` a następnie wpisz w polu wyszukiwania. 
1. Obowiązkowe Wybierz gwiazdkę obok pozycji **wystąpienia zarządzane SQL** , aby dodać wystąpienia zarządzane jako skrót do paska nawigacyjnego po lewej stronie. 
1. Wybierz pozycję **wystąpienia zarządzane SQL** i wybierz swoje podstawowe wystąpienie zarządzane, na `sql-mi-primary`przykład. 
1. W obszarze **Ustawienia**przejdź do pozycji **grupy trybu failover wystąpienia** , a następnie wybierz pozycję **Dodaj grupę** , aby otworzyć stronę **Grupa trybu failover wystąpienia** . 

   ![Dodawanie grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na stronie **Grupa trybu failover wystąpienia** wpisz nazwę grupy trybu failover, na przykład, `failovergrouptutorial` a następnie wybierz pomocnicze wystąpienie zarządzane, takie jak `sql-mi-secondary` z listy rozwijanej. Wybierz pozycję **Utwórz** , aby utworzyć grupę trybu failover. 

   ![Utwórz grupę trybu failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Po zakończeniu wdrażania grupy trybu failover nastąpi powrót do strony **grupy trybu failover** . 

## <a name="8---test-failover"></a>8 — testowanie pracy w trybie failover
W tym kroku nastąpi niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal. 

1. Przejdź do wystąpienia zarządzanego w [Azure Portal](https://portal.azure.com) a następnie wybierz pozycję **grupy trybu failover dla wystąpienia** w obszarze Ustawienia. 
1. Sprawdź, które wystąpienie zarządzane jest podstawowym, a którym wystąpieniem zarządzanym jest pomocniczy. 
1. Wybierz pozycję **tryb failover** , a następnie wybierz pozycję **tak** na ostrzeżenie o rozłączeniu sesji tds. 

   ![Praca awaryjna grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Sprawdź, które zarządzane wystąpienie jest podstawowym i jakie wystąpienie jest serwerem pomocniczym. Jeśli przełączenie w tryb failover powiodło się, dwa wystąpienia powinny mieć przypisane role. 

   ![Wystąpienia zarządzane mają przełączane role po zakończeniu pracy w trybie failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Po ponownym uruchomieniu wystąpienia podstawowego z powrotem do roli głównej należy ponownie wybrać **tryb failover** . 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Wyczyść zasoby, usuwając najpierw wystąpienie zarządzane, klaster wirtualny, wszystkie pozostałe zasoby i wreszcie grupę zasobów. 

1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com). 
1. Wybierz wystąpienie zarządzane, a następnie wybierz pozycję **Usuń**. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. Ten proces może potrwać trochę czasu w tle, a do momentu ukończenia nie będzie można usunąć *klastra wirtualnego* ani żadnych innych zasobów zależnych. Monitoruj pozycję Usuń na karcie działanie, aby potwierdzić, że zarządzane wystąpienie zostało usunięte. 
1. Po usunięciu wystąpienia zarządzanego Usuń *klaster wirtualny* , wybierając go w grupie zasobów, a następnie wybierając pozycję **Usuń**. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Usuń wszystkie pozostałe zasoby. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Aby usunąć grupę zasobów, wybierz pozycję **Usuń grupę zasobów**, wpisz nazwę grupy zasobów, `myResourceGroup`a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano grupę trybu failover między dwoma wystąpieniami zarządzanymi. W tym samouczku omówiono:

> [!div class="checklist"]
> - Tworzenie podstawowego wystąpienia zarządzanego
> - Utwórz dodatkowe wystąpienie zarządzane jako część [grupy trybu failover](sql-database-auto-failover-group.md). 
> - Testowe przełączenie w tryb failover

Przejdź do następnego przewodnika Szybki Start dotyczącego sposobu nawiązywania połączenia z wystąpieniem zarządzanym i sposobu przywracania bazy danych do wystąpienia zarządzanego: 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia](sql-database-managed-instance-configure-vm.md)
> z wystąpieniem zarządzanym[przywracanie bazy danych do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md)


