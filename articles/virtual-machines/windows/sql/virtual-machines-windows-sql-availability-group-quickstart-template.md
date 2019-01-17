---
title: Tworzenie usługi WSFC, odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na maszynę Wirtualną programu SQL Server przy użyciu szablonu szybkiego startu platformy Azure
description: Szablony szybkiego startu platformy Azure Użyj w celu uproszczenia procesu tworzenia grup dostępności dla maszyn wirtualnych serwera SQL na platformie Azure przy użyciu szablonu, aby utworzyć klaster, Dołącz maszyny wirtualne SQL do klastra, utwórz odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360329"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>Tworzenie usługi WSFC, odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na maszynę Wirtualną programu SQL Server przy użyciu szablonu szybkiego startu platformy Azure
W tym artykule opisano, jak częściowo zautomatyzować wdrożenia zawsze włączonej konfiguracji grupy dostępności dla maszyn wirtualnych serwera SQL na platformie Azure za pomocą szablonów szybkiego startu platformy Azure. Istnieją dwa szablony szybkiego startu platformy Azure, które są używane w ramach tego procesu. 

   | Szablon | Opis |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Tworzy klaster pracy awaryjnej Windows i dołącza maszyny wirtualne programu SQL Server do niego. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Tworzy odbiornika grupy dostępności i konfiguruje wewnętrznego modułu równoważenia obciążenia. |
   | &nbsp; | &nbsp; |

Inne części konfiguracji grupy dostępności musi być wykonywane ręcznie, takie jak tworzenie grupy dostępności i tworzenia wewnętrznego modułu równoważenia obciążenia. Ten artykuł zawiera sekwencję kroków automatycznych i ręcznych.
 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby zautomatyzować konfiguracji zawsze włączonej grupy dostępności przy użyciu szablonów szybkiego startu, musi już mieć następujące wymagania wstępne: 
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów o [kontrolera domeny](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- Co najmniej jeden przyłączonych do domeny [maszyn wirtualnych na platformie Azure działającej wersji programu SQL Server 2016 (lub nowszego) przedsiębiorstwa](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) w ten sam zestaw lub dostępności strefę dostępności, które zostały [zarejestrowanego dostawcy zasobów maszyny Wirtualnej SQL](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Zarejestruj istniejącej maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów
Ponieważ te grupy dostępności, które szablony szybkiego startu platformy Azure zależą od dostawcy zasobów maszyny Wirtualnej SQL (Microsoft.SqlVirtualMachine) istniejące maszyny wirtualne programu SQL Server musi być zarejestrowany za pomocą dostawcy zasobów maszyny Wirtualnej SQL. Pomiń ten krok, jeśli został utworzony po grudnia 2018 r, jak wszystkie maszyny wirtualne SQL Server utworzonych po tej dacie są automatycznie rejestrowane do maszyny Wirtualnej z programu SQL Server. Ta sekcja zawiera kroki, aby zarejestrować dostawcę przy użyciu witryny Azure portal, ale można również użyć [PowerShell](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Jeśli usuniesz zasób maszynę Wirtualną programu SQL Server, będzie wrócisz do ustawienia zakodowany licencji obrazu. 

1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  
1. W **subskrypcje** bloku, przejdź do **dostawcy zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

  ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Krok 1 — Tworzenie usługi WSFC i dołączyć maszyny wirtualne SQL Server w klastrze za pomocą szablonu szybkiego startu 
Gdy maszyny wirtualne programu SQL Server zostały zarejestrowane przy użyciu nowego dostawcę zasobów maszyny Wirtualnej SQL, możesz dołączyć do maszyn wirtualnych programu SQL Server do *SqlVirtualMachineGroup*. Ten zasób definiuje metadanych klastra pracy awaryjnej Windows, w tym wersji, wersji, w pełni kwalifikowanej nazwy domeny, konta usługi AD do zarządzania klastrem i konta magazynu jako monitor w chmurze. Dodawanie maszyny Wirtualnej programu SQL Server, aby *SqlVirtualMachineGroup* usługę klastra pracy awaryjnej Windows używa do ładowania i dołączania maszyny wirtualne programu SQL Server do klastra. Ten krok jest automatycznym z funkcjami **101-sql-maszyny wirtualnej ag-setup** szablon szybkiego startu i może być implementowana w wykonaj następujące czynności:

1. Przejdź do [ **101-sql-maszyny wirtualnej ag-setup** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) szablon szybkiego startu i wybierz **Wdróż na platformie Azure** Aby uruchomić szablon szybkiego startu w witrynie Azure portal.
1. Wypełnij wymagane pola w celu skonfigurowania klastra pracy awaryjnej Windows metadanych. Opcjonalne pola może być puste.

    W poniższej tabeli przedstawiono wartości niezbędne do szablonu: 

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której maszyny wirtualne programu SQL Server istnieje. |
   |**Grupa zasobów** | Grupa zasobów, w którym znajdują się Twoje maszyny wirtualne SQL Server. | 
   |**Nazwa klastra trybu failover** | Nazwa nowego klastra pracy awaryjnej Windows. |
   | **Istniejące listy maszyn wirtualnych** | Maszyny wirtualne SQL Server potrzebny do wzięcia udziału w grupie dostępności, a w efekcie należeć do tego nowego klastra. Te wartości należy oddzielać przecinkiem i spacją (np: SQLVM1, SQLVM2). |
   | **Wersja programu SQL Server** | Wybierz maszyny wirtualne programu SQL Server w wersji programu SQL Server z listy rozwijanej. Obecnie tylko obrazy SQL 2016 oraz SQL 2017 są obsługiwane. |
   | **Istniejące w pełni kwalifikowanej nazwy domeny** | Istniejące nazwę FQDN domeny, w którym znajdują się Twoje maszyny wirtualne SQL Server. |
   | **Istniejące konto domeny** | Istniejące konto domeny, które ma dostęp administratora systemu do programu SQL Server. | 
   | **Hasło konta domeny** | Hasło dla konta domeny wymienionych wcześniej. | 
   | **Istniejące konto usługi programu Sql** | Konto użytkownika domeny, który jest używany do kontrolowania usługi SQL Server. Te informacje można znaleźć, używając [ **Menedżera konfiguracji SQL Server**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Hasło usługi SQL** | Hasło używane przez konto użytkownika domeny, który kontroluje usługi SQL Server. |
   | &nbsp; | &nbsp; |

1. Jeśli wyrażasz zgodę na warunki i postanowienia, zaznacz pole wyboru obok pozycji **zgodę na warunki i postanowienia, o których wspomniano** i wybierz **zakupu** aby zakończyć wdrożenie szablonu szybkiego startu. 
1. Aby monitorować wdrożenie, wybierz wdrożenie z **powiadomienia** dzwonka ikony w górnym menu nawigacyjnym transparencie fizyczną lub przejdź do swojej **grupy zasobów** w witrynie Azure portal wybierz  **Wdrożenia** w **ustawienia** pola, a następnie wybierz opcję wdrażania "Microsoft.Template". 

## <a name="step-2---manually-create-the-availability-group"></a>Krok 2 — ręcznie utworzyć grupy dostępności 
Ręcznie utwórz grupę dostępności, tak jak zwykle, za pomocą [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) lub [języka Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > Czy **nie** utworzyć odbiornik, w tym momencie, ponieważ to jest zautomatyzowane przez **101-sql-vm-aglistener — setup** szablon szybkiego startu w kroku 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Krok 3 — ręczne tworzenie wewnętrznego modułu równoważenia obciążenia (ILB)
Zawsze włączone odbiornika grupy dostępności (grupy dostępności) wymaga wewnętrznego modułu równoważenia obciążenia platformy Azure (ILB). Wewnętrznego modułu równoważenia obciążenia zapewnia "pływający" adres IP dla odbiornika grupy dostępności, która umożliwia szybsze trybu failover i ponowne nawiązanie połączenia. Jeśli maszyny wirtualne SQL Server w grupie dostępności są częścią tego samego zestawu dostępności, wówczas można użyć podstawowego modułu równoważenia obciążenia; w przeciwnym razie należy użyć standardowego modułu równoważenia obciążenia.  **Wewnętrznego modułu równoważenia obciążenia musi należeć do tej samej sieci wirtualnej jako wystąpienia maszyny Wirtualnej programu SQL Server.** Wewnętrznego modułu równoważenia obciążenia tylko musi zostać utworzona pozostałą część konfiguracji (np. puli zaplecza, sondy kondycji i równoważenie obciążenia reguły) jest obsługiwany przez **101-sql-vm-aglistener — setup** szablon szybkiego startu w kroku 4. 

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
   | **Prywatny adres IP** |Podaj dostępny adres IP z podsieci. Podczas tworzenia odbiornika w klastrze, należy użyć tego adresu IP.|
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, może pojawić się w tym polu. Wybierz subskrypcję, która ma być skojarzony z tym zasobem. Zwykle jest tej samej subskrypcji co wszystkie zasoby dla grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, które należą do wystąpienia programu SQL Server. |
   | **Lokalizacja** |Wybierz wystąpienia programu SQL Server znajdują się w lokalizacji platformy Azure. |
   | &nbsp; | &nbsp; |

6. Wybierz pozycję **Utwórz**. 


  >[!NOTE]
  > Publiczny zasób adresu IP dla każdej maszyny Wirtualnej serwera SQL powinien mieć standardowej jednostki SKU, aby zapewnić ich zgodność przy użyciu standardowego modułu równoważenia obciążenia. Aby określić jednostki SKU publicznego zasobu adresu IP maszyny Wirtualnej, przejdź do usługi **grupy zasobów**, wybierz opcję usługi **publiczny adres IP** zasób żądanego programu SQL Server maszyny Wirtualnej i Znajdź wartość w obszarze **jednostki SKU**  z **Przegląd** okienka. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Krok 4 — tworzenie odbiornika grupy dostępności i konfigurowanie wewnętrznego modułu równoważenia obciążenia z szablonu szybkiego startu

Tworzenie odbiornika grupy dostępności i konfigurowanie wewnętrznego modułu równoważenia obciążenia (ILB) automatycznie za pomocą **101-sql-vm-aglistener — setup** szablon szybkiego startu, ponieważ obsługuje wirtualne Microsoft.SqlVirtualMachine/Sql Maszyny odbiornika grupy dostępności/grupy zasobów. **101-sql-vm-aglistener — setup** szablon szybkiego startu, za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL zapewnia następujące funkcje:

 - Konfiguruje ustawienia sieciowe dla klastra i wewnętrznego modułu równoważenia obciążenia. 
 - Umożliwia skonfigurowanie puli zaplecza wewnętrznego modułu równoważenia obciążenia, sondy kondycji i równoważenie obciążenia reguły.
 - Umożliwia utworzenie odbiornika grupy dostępności z danego adresu IP i nazwy.

Konfigurowanie wewnętrznego modułu równoważenia obciążenia i tworzenia odbiornika grupy dostępności, wykonaj następujące czynności:
1. Przejdź do [ **101-sql-vm-aglistener — setup** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) szablon szybkiego startu i wybierz **Wdróż na platformie Azure** Aby uruchomić szablon szybkiego startu w witrynie Azure portal.
1. Wypełnij wymagane pola do konfigurowania wewnętrznego modułu równoważenia obciążenia, a następnie utwórz odbiornik grupy dostępności. Opcjonalne pola może być puste. 

    W poniższej tabeli przedstawiono wartości niezbędne do szablonu: 

   | **Pole** | Wartość |
   | --- | --- |
   |**Grupa zasobów** | Grupa zasobów, jeśli Twoje maszyny wirtualne SQL Server i grupy dostępności istnieje. | 
   |**Nazwę istniejącego klastra trybu Failover** | Nazwa maszyny wirtualne programu SQL Server są przyłączone do klastra. |
   | **Istniejące grupy dostępności Sql**| Nazwa grupy dostępności, która maszyn wirtualnych serwera SQL są częścią. |
   | **Istniejące listy maszyn wirtualnych** | Nazwy maszyn wirtualnych serwera SQL, które są częścią grupy dostępności wymienionych wcześniej. Nazwy powinny być oddzielone przecinkiem i spacją (np: SQLVM1, SQLVM2). |
   | **Istniejące w pełni kwalifikowanej nazwy domeny** | Istniejące nazwę FQDN domeny, w którym znajdują się Twoje maszyny wirtualne SQL Server. |
   | **Odbiornik** | Nazwa DNS, którą chcesz przypisać do odbiornika. Domyślnie ten szablon Określa nazwę aglistener, ale można go zmienić. |
   | **Port odbiornika** | Portu używanego przez odbiornik do użycia. Zazwyczaj ten port musi być domyślnego portu 1433, a w efekcie jest to numer portu, określony przez ten szablon. Jednak jeśli została zmieniona z domyślnego portu, następnie port odbiornika należy użyć tej wartości. | 
   | **Istniejącej sieci wirtualnej** | Nazwa sieci wirtualnej, w którym znajdują się Twoje maszyny wirtualne SQL Server i wewnętrznego modułu równoważenia obciążenia. |
   | **Istniejącej podsieci** | *Nazwa* wewnętrznej podsieci maszyn wirtualnych programu SQL Server (np: domyślna). Tę wartość można określić, przechodząc do Twojej **grupy zasobów**, wybierając swoje **sieci wirtualnej**, wybierając opcję **podsieci** w obszarze **ustawienia**okienko i kopiowanie wartości w obszarze **nazwa**. |
   | **Istniejące wewnętrznego modułu równoważenia obciążenia** | Nazwa wewnętrznego modułu równoważenia obciążenia, który został utworzony w kroku 3. |
   | **Port sondy** | Port sondy, który będzie wewnętrznego modułu równoważenia obciążenia do użycia. Szablon używa 59999 domyślnie, ale można ją zmienić tę wartość. |
   | &nbsp; | &nbsp; |

1. Jeśli wyrażasz zgodę na warunki i postanowienia, zaznacz pole wyboru obok pozycji **zgodę na warunki i postanowienia, o których wspomniano** i wybierz **zakupu** aby zakończyć wdrożenie szablonu szybkiego startu. 
1. Aby monitorować wdrożenie, wybierz wdrożenie z **powiadomienia** dzwonka ikony w górnym menu nawigacyjnym transparencie fizyczną lub przejdź do swojej **grupy zasobów** w witrynie Azure portal wybierz  **Wdrożenia** w **ustawienia** pola, a następnie wybierz opcję wdrażania "Microsoft.Template". 

  >[!NOTE]
  >Jeśli wdrożenie nie powiedzie się połowy za pośrednictwem, konieczne będzie ręczne [usunąć nowo utworzony odbiornik](#remove-availability-group-listener) przy użyciu programu PowerShell przed ponownego wdrażania **101-sql-vm-aglistener — setup** szablon szybkiego startu. 

## <a name="remove-availability-group-listener"></a>Usuwanie odbiornika grupy dostępności
Jeśli zechcesz później usunąć odbiornika grupy dostępności, które są skonfigurowane w szablonie, należy przejść przez dostawcę zasobów maszyny Wirtualnej SQL. Ponieważ odbiornika jest zarejestrowany za pośrednictwem dostawcy zasobów maszyny Wirtualnej SQL, po prostu usunięcie go za pomocą programu SQL Server Management Studio jest niewystarczająca. Którą faktycznie należy usunąć za pomocą dostawcy zasobów maszyny Wirtualnej SQL przy użyciu programu PowerShell. Ten sposób usuwa metadanych odbiornika grupy dostępności od dostawcy zasobów maszyny Wirtualnej SQL i fizycznie usuwa odbiornik z grupy dostępności. 

Poniższy fragment kodu usuwa odbiornika grupy dostępności SQL z obu dostawcy zasobu języka SQL, a z grupy dostępności: 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Znane problemy i błędy
W tej sekcji omówiono niektóre znane problemy i ich możliwe rozwiązanie. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Odbiornik grupy dostępności dla grupy dostępności "\<AG-Name >' już istnieje
Grupa dostępności wybranego już użyty w odbiornika grupy dostępności szablon szybkiego startu platformy Azure zawiera odbiornik fizycznie w obrębie grupy dostępności lub jako metadane w ramach dostawcy zasobów maszyny Wirtualnej SQL.  Usuwanie odbiornika przy użyciu [PowerShell](#remove-availability-group-listener) przed ponownego wdrażania **101-sql-vm-aglistener — setup** szablon szybkiego startu. 

### <a name="connection-only-works-from-primary-replica"></a>Połączenie działa tylko z repliki podstawowej
To zachowanie jest prawdopodobnie po nieudanej **101-sql-vm-aglistener — setup** wdrożenia szablonu, pozostawiając konfiguracji wewnętrznego modułu równoważenia obciążenia w niespójnym stanie. Sprawdź, czy puli wewnętrznej bazy danych zawiera zestaw dostępności oraz istnieją reguły dla sondy kondycji i reguł równoważenia obciążenia. Jeśli brakuje niektórych elementów, konfiguracja wewnętrznego modułu równoważenia obciążenia jest niespójna. 

Aby rozwiązać ten problem, Usuń odbiornika przy użyciu [PowerShell](#remove-availability-group-listener), usunąć wewnętrznego modułu równoważenia obciążenia w witrynie Azure portal i rozpocznij ponownie od [kroku 3](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Element BadRequest — można aktualizować tylko listy maszyn wirtualnych SQL
Ten błąd może wystąpić podczas wdrażania **101-sql-vm-aglistener — setup** szablonu, jeśli odbiornik został usunięty za pośrednictwem programu SQL Server Management Studio (SSMS), ale nie został usunięty z maszyny Wirtualnej SQL dostawcy zasobów. Usuwanie odbiornik za pomocą programu SSMS nie powoduje usunięcia metadanych odbiornika od dostawcy zasobów maszyny Wirtualnej SQL; odbiornik muszą zostać usunięte od dostawcy zasobów za pomocą [PowerShell](#remove-availability-group-listener). 


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Program SQL Server w maszynie Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen maszyn wirtualnych serwera SQL](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji maszyny Wirtualnej programu SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Przełączanie Modele licencjonowania maszynę Wirtualną programu SQL Server](virtual-machines-windows-sql-ahb.md)



