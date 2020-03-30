---
title: Konfigurowanie grupy dostępności (szablon Szybki start platformy Azure)
description: Użyj szablonów szybki start platformy Azure, aby utworzyć klaster trybu failover systemu Windows, dołączyć maszyny wirtualne programu SQL Server do klastra, utworzyć odbiornik i skonfigurować wewnętrzny moduł równoważenia obciążenia na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022378"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Konfigurowanie grupy dostępności programu SQL Server na maszynie Wirtualnej platformy Azure za pomocą szablonów szybki startowych platformy Azure
W tym artykule opisano sposób używania szablonów szybkiego startu platformy Azure do częściowego automatyzacji wdrażania konfiguracji grupy zawsze włączone dla dostępności dla maszyn wirtualnych programu SQL Server na platformie Azure. W tym procesie są używane dwa szablony szybkiego startu platformy Azure: 

   | Szablon | Opis |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Tworzy klaster trybu failover systemu Windows i dołącza do niego maszyny wirtualne programu SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Tworzy odbiornik grupy dostępności i konfiguruje wewnętrzny moduł równoważenia obciążenia. Tego szablonu można używać tylko wtedy, gdy klaster trybu failover systemu Windows został utworzony za pomocą szablonu **101-sql-vm-ag-setup.** |
   | &nbsp; | &nbsp; |

Inne części konfiguracji grupy dostępności muszą być wykonywane ręcznie, takie jak tworzenie grupy dostępności i tworzenie wewnętrznego modułu równoważenia obciążenia. Ten artykuł zawiera sekwencję kroków automatycznych i ręcznych.
 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby zautomatyzować konfigurację grupy dostępności zawsze włączone przy użyciu szablonów szybkiego startu, należy mieć następujące wymagania wstępne: 
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jedna maszyna wirtualna przyłączona do domeny [na platformie Azure z systemem SQL Server 2016 (lub nowsza) Enterprise edition,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) które znajdują się w tej samej strefie zestawu dostępności lub dostępności i które zostały [zarejestrowane u dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Dwa dostępne (nie używane przez żadną jednostkę) adresy IP: jeden dla wewnętrznego modułu równoważenia obciążenia i jeden dla odbiornika grupy dostępności w tej samej podsieci co grupa dostępności. Jeśli używany jest istniejący moduł równoważenia obciążenia, potrzebny jest tylko jeden dostępny adres IP.  

## <a name="permissions"></a>Uprawnienia
Następujące uprawnienia są niezbędne do skonfigurowania grupy Dostępności zawsze przy użyciu szablonów szybki start platformy Azure przy użyciu szablonów szybki start platformy Azure: 

- Istniejące konto użytkownika domeny z **uprawnieniem Utwórz obiekt komputera** w domenie.  Na przykład konto administratora domeny zazwyczaj ma wystarczające account@domain.comuprawnienia (na przykład: ). _To konto powinno również być częścią lokalnej grupy administratorów na każdej maszynie wirtualnej, aby utworzyć klaster._
- Konto użytkownika domeny, które steruje usługą PROGRAMU SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Krok 1: Tworzenie klastra trybu failover i dołączanie maszyn wirtualnych programu SQL Server do klastra przy użyciu szablonu szybkiego startu 
Po zarejestrowaniu maszyn wirtualnych programu SQL Server u dostawcy zasobów maszyn wirtualnych SQL można dołączyć maszyny wirtualne programu SQL Server do *grup SQLVirtualMachineGroups*. Ten zasób definiuje metadane klastra trybu failover systemu Windows. Metadane obejmują wersję, wersję, w pełni kwalifikowaną nazwę domeny, konta usługi Active Directory do zarządzania zarówno klastrem, jak i usługą programu SQL Server, a także konto magazynu jako monitor w chmurze. 

Dodawanie maszyn wirtualnych programu SQL Server do grupy zasobów *SqlVirtualMachineGroups* uruchamia usługę klastrowania trybu failover systemu Windows w celu utworzenia klastra, a następnie dołącza do tych maszyn wirtualnych programu SQL Server do tego klastra. Ten krok jest zautomatyzowany za pomocą szablonu szybkiego startu **101-sql-vm-ag-setup.** Można go zaimplementować, wykonując następujące kroki:

1. Przejdź do szablonu szybkiego startu [**101-sql-vm-ag-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Następnie wybierz pozycję **Wdrażanie na platformie Azure,** aby otworzyć szablon przewodnika Szybki start w witrynie Azure portal.
1. Wypełnij pola wymagane do skonfigurowania metadanych dla klastra trybu failover systemu Windows. Pola opcjonalne można pozostawić puste.

   W poniższej tabeli przedstawiono wartości niezbędne dla szablonu: 

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której istnieją maszyny wirtualne programu SQL Server. |
   |**Grupa zasobów** | Grupa zasobów, w której znajdują się maszyny wirtualne programu SQL Server. | 
   |**Nazwa klastra trybu failover** | Nazwa nowego klastra trybu failover systemu Windows. |
   | **Istniejąca lista maszyn wirtualnych** | Maszyny wirtualne programu SQL Server, które chcesz uczestniczyć w grupie dostępności i być częścią tego nowego klastra. Oddziel te wartości przecinkiem i spacją (na przykład: *SQLVM1, SQLVM2*). |
   | **Wersja programu SQL Server** | Wersja sql server maszyn wirtualnych programu SQL Server. Wybierz go z listy rozwijanej. Obecnie obsługiwane są tylko obrazy programu SQL Server 2016 i SQL Server 2017. |
   | **Istniejąca w pełni kwalifikowana nazwa domeny** | Istniejąca nazwa FQDN dla domeny, w której znajdują się maszyny wirtualne programu SQL Server. |
   | **Istniejące konto domeny** | Istniejące konto użytkownika domeny, które ma **uprawnienie Utwórz obiekt komputera** w domenie podczas wdrażania [szablonu.](/windows-server/failover-clustering/prestage-cluster-adds) Na przykład konto administratora domeny zazwyczaj ma wystarczające account@domain.comuprawnienia (na przykład: ). *To konto powinno również być częścią lokalnej grupy administratorów na każdej maszynie wirtualnej, aby utworzyć klaster.*| 
   | **Hasło do konta domeny** | Hasło do wcześniej wspomnianego konta użytkownika domeny. | 
   | **Istniejące konto usługi SQL** | Konto użytkownika domeny, które steruje [usługą SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) account@domain.compodczas wdrażania grupy dostępności (na przykład: ). |
   | **Hasło usługi SQL** | Hasło używane przez konto użytkownika domeny, które kontroluje usługę PROGRAMU SQL Server. |
   | **Nazwa monitora w chmurze** | Nowe konto magazynu platformy Azure, które zostanie utworzone i używane dla monitora w chmurze. Można zmodyfikować tę nazwę. |
   | **\_lokalizacje artefaktów** | To pole jest ustawione domyślnie i nie powinno być modyfikowane. |
   | **\_Artefakty Lokalizacja Sas Token** | To pole jest celowo puste. |
   | &nbsp; | &nbsp; |

1. Jeśli zgadzasz się z warunkami, zaznacz pole wyboru **Zgadzam się z warunkami podanymi powyżej.** Następnie wybierz **pozycję Zakup,** aby zakończyć wdrażanie szablonu przewodnika Szybki start. 
1. Aby monitorować wdrożenie, wybierz wdrożenie z ikony dzwonka **powiadomień** w górnym banerze nawigacyjnym lub przejdź do **grupy zasobów** w witrynie Azure portal. Wybierz **pozycję Wdrożenia** w obszarze **Ustawienia**i wybierz wdrożenie **microsoft.template.** 

>[!NOTE]
> Poświadczenia podane podczas wdrażania szablonu są przechowywane tylko przez czas trwania wdrożenia. Po zakończeniu wdrażania te hasła są usuwane. Zostaniesz poproszony o podanie ich ponownie, jeśli dodasz więcej maszyn wirtualnych programu SQL Server do klastra. 


## <a name="step-2-manually-create-the-availability-group"></a>Krok 2: Ręczne tworzenie grupy dostępności 
Ręcznie utwórz grupę dostępności w zwykły sposób, używając [programu SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), Programu [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)lub [Transact-SQL.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> *Nie* należy tworzyć odbiornika w tej chwili, ponieważ szablon szybkiego startu **101-sql-vm-aglistener-setup** robi to automatycznie w kroku 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Krok 3: Ręczne utworzenie wewnętrznego modułu równoważenia obciążenia
Odbiornik grupy zawsze włączone dostępność wymaga wewnętrznego wystąpienia modułu Azure Load Balancer. Wewnętrzny moduł równoważenia obciążenia zapewnia "zmienny" adres IP dla odbiornika grupy dostępności, który umożliwia szybsze praca awaryjna i ponowne połączenie. Jeśli maszyny wirtualne programu SQL Server w grupie dostępności są częścią tego samego zestawu dostępności, można użyć modułu równoważenia obciążenia basic. W przeciwnym razie należy użyć standardowego modułu równoważenia obciążenia. 

> [!IMPORTANT]
> Wewnętrzny moduł równoważenia obciążenia powinien znajdować się w tej samej sieci wirtualnej, co wystąpienia maszyn wirtualnych programu SQL Server. 

Wystarczy utworzyć wewnętrzny moduł równoważenia obciążenia. W kroku 4 szablon szybki start **konfiguracji 101-sql-vm-aglistener** obsługuje pozostałą część konfiguracji (na przykład pulę wewnętrznej bazy danych, sondę kondycji i reguły równoważenia obciążenia). 

1. W witrynie Azure Portal otwórz grupę zasobów zawierającą maszyny wirtualne programu SQL Server. 
2. W grupie zasobów wybierz pozycję **Dodaj**.
3. Wyszukaj **moduł równoważenia obciążenia**. W wynikach wyszukiwania wybierz **pozycję Load Balancer**, który jest publikowany przez **firmę Microsoft**.
4. W bloku **Moduł równoważenia obciążenia** wybierz pozycję **Utwórz**.
5. W oknie dialogowym **Tworzenie modułu równoważenia obciążenia** skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Wprowadź nazwę tekstową reprezentującą moduł równoważenia obciążenia. Na przykład wprowadź **sqlLB**. |
   | **Typ** |**Wewnętrzna:** Większość implementacji używa wewnętrznego modułu równoważenia obciążenia, który umożliwia aplikacjom w tej samej sieci wirtualnej łączenie się z grupą dostępności.  </br> **Zewnętrzne**: Umożliwia aplikacjom łączenie się z grupą dostępności za pośrednictwem publicznego połączenia internetowego. |
   | **Sieć wirtualna** | Wybierz sieć wirtualną, w których znajdują się wystąpienia programu SQL Server. |
   | **Podsieci** | Wybierz podsieć, w których znajdują się wystąpienia programu SQL Server. |
   | **Przypisanie adresu IP** |**Statyczne** |
   | **Prywatny adres IP** | Określ dostępny adres IP z podsieci. |
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, to pole może się pojawić. Wybierz subskrypcję, którą chcesz skojarzyć z tym zasobem. Zwykle jest to ta sama subskrypcja, co wszystkie zasoby dla grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, w których znajdują się wystąpienia programu SQL Server. |
   | **Lokalizacja** |Wybierz lokalizację platformy Azure, w którym znajdują się wystąpienia programu SQL Server. |
   | &nbsp; | &nbsp; |

6. Wybierz **pozycję Utwórz**. 


>[!IMPORTANT]
> Publiczny zasób IP dla każdej maszyny Wirtualnej programu SQL Server powinien mieć standardową jednostkę SKU, która będzie zgodna z modułem równoważenia obciążenia Standard. Aby określić jednostkę SKU publicznego zasobu IP maszyny Wirtualnej, przejdź do **grupy zasobów**, wybierz zasób publiczny **adres IP** dla maszyny Wirtualnej programu SQL Server i znajdź wartość w obszarze **Jednostka SKU** w **okienku Przegląd.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Krok 4: Tworzenie odbiornika grupy dostępności i konfigurowanie wewnętrznego modułu równoważenia obciążenia przy użyciu szablonu szybkiego startu

Utwórz odbiornik grupy dostępności i skonfiguruj wewnętrzny moduł równoważenia obciążenia automatycznie za pomocą szablonu szybkiego startu **konfiguracji 101-sql-vm-aglistener.** Szablon udostępnia zasób Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Szablon szybkiego startu **konfiguracji 101-sql-vm-aglistener** za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL wykonuje następujące czynności:

- Tworzy nowy zasób IP frontendu (na podstawie wartości adresu IP podanej podczas wdrażania) dla odbiornika. 
- Konfiguruje ustawienia sieciowe klastra i wewnętrznego modułu równoważenia obciążenia. 
- Konfiguruje pulę wewnętrznej bazy danych dla wewnętrznego modułu równoważenia obciążenia, sondy kondycji i reguł równoważenia obciążenia.
- Tworzy odbiornik grupy dostępności z podanym adresem IP i nazwą.
 
>[!NOTE]
> **101-sql-vm-aglistener-setup** można używać tylko wtedy, gdy klaster trybu failover systemu Windows został utworzony za pomocą szablonu **101-sql-vm-ag-setup.**
   
   
Aby skonfigurować wewnętrzny moduł równoważenia obciążenia i utworzyć odbiornik grup dostępności, wykonaj następujące czynności:
1. Przejdź do szablonu szybkiego startu [konfiguracji 101-sql-vm-aglistener](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) i wybierz pozycję **Wdrażanie na platformie Azure,** aby uruchomić szablon przewodnika Szybki start w witrynie Azure portal.
1. Wypełnij pola wymagane do skonfigurowania wewnętrznego modułu równoważenia obciążenia i utworzenia odbiornika grupy dostępności. Pola opcjonalne można pozostawić puste. 

   W poniższej tabeli przedstawiono wartości niezbędne dla szablonu: 

   | **Pole** | Wartość |
   | --- | --- |
   |**Grupa zasobów** | Grupa zasobów, w której istnieją maszyny wirtualne i grupa dostępności programu SQL Server. | 
   |**Istniejąca nazwa klastra trybu failover** | Nazwa klastra, do których są przyłączane maszyny wirtualne programu SQL Server. |
   | **Istniejąca grupa dostępności sql**| Nazwa grupy dostępności, do których należą maszyny wirtualne programu SQL Server. |
   | **Istniejąca lista maszyn wirtualnych** | Nazwy maszyn wirtualnych programu SQL Server, które są częścią wcześniej wspomnianej grupy dostępności. Oddziel nazwy przecinkiem i spacją (na przykład: *SQLVM1, SQLVM2*). |
   | **Odbiornika** | Nazwa DNS, którą chcesz przypisać do odbiornika. Domyślnie ten szablon określa nazwę "aglistener", ale można ją zmienić. Nazwa nie powinna przekraczać 15 znaków. |
   | **Port odbiornika** | Port, którego odbiornik ma używać. Zazwyczaj ten port powinien być domyślny 1433. Jest to numer portu, który określa szablon. Ale jeśli domyślny port został zmieniony, port odbiornika należy użyć tej wartości zamiast. | 
   | **Adres IP odbiornika** | Adres IP, którego odbiornik ma używać. Ten adres zostanie utworzony podczas wdrażania szablonu, więc podaj adres, który nie jest jeszcze używany.  |
   | **Istniejąca podsieć** | Nazwa wewnętrznej podsieci maszyn wirtualnych programu SQL Server (na przykład: *domyślna).* Tę wartość można określić, przechodząc do **grupy zasobów**, wybierając sieć wirtualną, wybierając **podsieci** w okienku **Ustawienia** i kopiując wartość w obszarze **Nazwa**. |
   | **Istniejący wewnętrzny moduł równoważenia obciążenia** | Nazwa wewnętrznego modułu równoważenia obciążenia utworzonego w kroku 3. |
   | **Port sondy** | Port sondy, którego ma używać wewnętrzny moduł równoważenia obciążenia. Szablon używa 59999 domyślnie, ale można zmienić tę wartość. |
   | &nbsp; | &nbsp; |

1. Jeśli zgadzasz się z warunkami, zaznacz pole wyboru **Zgadzam się z warunkami podanymi powyżej.** Wybierz **pozycję Zakup,** aby zakończyć wdrażanie szablonu przewodnika Szybki start. 
1. Aby monitorować wdrożenie, wybierz wdrożenie z ikony dzwonka **powiadomień** w górnym banerze nawigacyjnym lub przejdź do **grupy zasobów** w witrynie Azure portal. Wybierz **pozycję Wdrożenia** w obszarze **Ustawienia**i wybierz wdrożenie **microsoft.template.** 

>[!NOTE]
>Jeśli wdrożenie zakończy się niepowodzeniem w połowie, należy ręcznie [usunąć nowo utworzony odbiornik](#remove-the-availability-group-listener) przy użyciu programu PowerShell przed ponowne wdrożenie szablonu szybkiego startu konfiguracji **101-sql-vm-aglistener.** 

## <a name="remove-the-availability-group-listener"></a>Usuwanie odbiornika grupy dostępności
Jeśli później trzeba usunąć odbiornik grupy dostępności, że szablon skonfigurowany, należy przejść przez dostawcę zasobów maszyny Wirtualnej SQL. Ponieważ odbiornik jest zarejestrowany za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL, po prostu usunięcie go za pośrednictwem programu SQL Server Management Studio jest niewystarczające. 

Najlepszą metodą jest usunięcie go za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL przy użyciu następującego fragmentu kodu w programie PowerShell. W ten sposób usuwa metadane odbiornika grupy dostępności z dostawcy zasobów maszyny Wirtualnej SQL. Również fizycznie usuwa odbiornik z grupy dostępności. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Typowe błędy
W tej sekcji omówiono niektóre znane problemy i ich możliwe rozwiązanie. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Odbiornik grupy dostępności dla grupy\<dostępności 'AG-Name>' już istnieje
Wybrana grupa dostępności używana w szablonie szybki start platformy Azure dla detektora grupy dostępności zawiera już odbiornik. Fizycznie znajduje się w grupie dostępności lub jej metadane pozostają w dostawcy zasobów maszyny Wirtualnej SQL. Usuń odbiornik przy użyciu programu [PowerShell](#remove-the-availability-group-listener) przed ponowne wdrożenie szablonu szybkiego startu **konfiguracji 101-sql-vm-aglistener.** 

### <a name="connection-only-works-from-primary-replica"></a>Połączenie działa tylko z repliki podstawowej
To zachowanie jest prawdopodobne z nieudanego wdrożenia **szablonu 101-sql-vm-aglistener-setup,** które opuściło konfigurację wewnętrznego modułu równoważenia obciążenia w niespójnym stanie. Sprawdź, czy pula wewnętrznej bazy danych zawiera listę zestawów dostępności i że istnieją reguły dla sondy kondycji i reguł równoważenia obciążenia. Jeśli czegoś brakuje, konfiguracja wewnętrznego modułu równoważenia obciążenia jest niespójnym stanem. 

Aby rozwiązać ten problem, usuń odbiornik przy użyciu [programu PowerShell](#remove-the-availability-group-listener), usuń wewnętrzny moduł równoważenia obciążenia za pośrednictwem witryny Azure portal i uruchom ponownie w kroku 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - można zaktualizować tylko listę maszyn wirtualnych SQL
Ten błąd może wystąpić podczas wdrażania szablonu **101-sql-vm-aglistener-setup,** jeśli odbiornik został usunięty za pośrednictwem programu SQL Server Management Studio (SSMS), ale nie został usunięty z dostawcy zasobów maszyny wirtualnej SQL. Usunięcie odbiornika za pośrednictwem usługi SSMS nie powoduje usunięcia metadanych odbiornika z dostawcy zasobów maszyny Wirtualnej SQL. Odbiornik musi zostać usunięty z dostawcy zasobów za pośrednictwem programu [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>Konto domeny nie istnieje
Ten błąd może mieć dwie przyczyny. Określone konto domeny nie istnieje lub brakuje danych [głównej nazwy użytkownika (UPN).](/windows/desktop/ad/naming-properties#userprincipalname) Szablon **101-sql-vm-ag-setup** oczekuje konta domeny w formularzu UPN *user@domain.com*(czyli ), ale niektóre konta domeny mogą go brakować. Zazwyczaj dzieje się tak, gdy użytkownik lokalny został zmigrowany jako pierwsze konto administratora domeny, gdy serwer został awansowany do kontrolera domeny lub gdy użytkownik został utworzony za pośrednictwem programu PowerShell. 

Sprawdź, czy konto istnieje. Jeśli tak, być może wpadasz w drugą sytuację. Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Na kontrolerze domeny otwórz okno **Użytkownicy i komputery usługi Active Directory** z opcji **Narzędzia** w **Menedżerze serwera**. 
2. Przejdź do konta, wybierając **pozycję Użytkownicy** w lewym okienku.
3. Kliknij prawym przyciskiem myszy konto, a następnie wybierz polecenie **Właściwości**.
4. Wybierz kartę **Konto.** Jeśli pole **nazwa logowania użytkownika** jest puste, jest to przyczyną błędu. 

    ![Puste konto użytkownika wskazuje brak nazwy UPN](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Wypełnij pole **Nazwa logowania użytkownika,** aby dopasować nazwę użytkownika, i wybierz odpowiednią domenę z listy rozwijanej. 
6. Wybierz **pozycję Zastosuj,** aby zapisać zmiany, i zamknij okno dialogowe, wybierając przycisk **OK**. 

Po wprowadzeniu tych zmian spróbuj ponownie wdrożyć szablon szybki start platformy Azure. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Przełączanie modeli licencjonowania dla maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-ahb.md)



