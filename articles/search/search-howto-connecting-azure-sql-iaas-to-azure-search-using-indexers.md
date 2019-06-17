---
title: Wyszukaj Azure połączenia maszyny Wirtualnej maszyny wirtualnej SQL Azure dla indeksowania wyszukiwania —
description: Włączanie połączeń szyfrowanych i Skonfiguruj zaporę, aby umożliwić połączenia do programu SQL Server na maszynie wirtualnej (VM) platformy Azure z indeksatora w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 90e5a133bac519cbc5ab2d7b112d51a019e8f698
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60871284"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurowanie połączenia z indeksator usługi Azure Search do programu SQL Server na Maszynie wirtualnej platformy Azure
Jak wspomniano w [łączenie usługi Azure SQL Database do usługi Azure Search przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), tworzenia indeksatorów względem **programu SQL Server na maszynach wirtualnych Azure** (lub **maszyn wirtualnych platformy Azure SQL** w skrócie) jest obsługiwana przez usługę Azure Search ale istnieje kilka powiązanych z zabezpieczeniami wymagania wstępne dotyczące zajmie się w pierwszej kolejności. 

Połączenia z usługi Azure Search do programu SQL Server na maszynie Wirtualnej jest publicznego połączenia internetowego. Wszystkie środki ochrony, które zwykle należy wykonać w przypadku tych połączeń zgłosić się tutaj również:

+ Uzyskiwanie certyfikatu z [dostawcy urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority#Providers) dla w pełni kwalifikowana nazwa domeny wystąpienia programu SQL Server na maszynie Wirtualnej platformy Azure.
+ Zainstaluj certyfikat na maszynie Wirtualnej, a następnie, Włącz i skonfiguruj połączeń szyfrowanych na maszynie Wirtualnej, korzystając z instrukcji w tym artykule.

## <a name="enable-encrypted-connections"></a>Włączanie połączeń szyfrowanych
Usługa Azure Search wymaga szyfrowanego kanału dla wszystkich żądań indeksatora za pośrednictwem publicznego połączenia internetowego. W tej sekcji przedstawiono kroki, aby umożliwić.

1. Sprawdź właściwości certyfikatu, aby sprawdzić, czy nazwa podmiotu jest w pełni kwalifikowana nazwa domeny (FQDN) dla maszyny Wirtualnej platformy Azure. Aby wyświetlić właściwości, można użyć narzędzia, takiego jak CertUtils lub przystawki Certyfikaty. Nazwa FQDN z sekcji podstawowe elementy bloku usługi maszyny Wirtualnej, można uzyskać w **publiczny adres IP adres/etykieta nazwy DNS** polu [witryny Azure portal](https://portal.azure.com/).
   
   * W przypadku maszyn wirtualnych utworzonych przy użyciu nowszego **usługi Resource Manager** szablonu, nazwy FQDN jest w formacie `<your-VM-name>.<region>.cloudapp.azure.com`
   * Dla starsze maszyny wirtualne utworzone jako **klasycznego** maszyny Wirtualnej, nazwy FQDN jest w formacie `<your-cloud-service-name.cloudapp.net>`.

2. Konfigurowanie programu SQL Server do używania certyfikatu za pomocą Edytora rejestru (regedit). 
   
    Mimo że Menedżera konfiguracji SQL Server jest często używana dla tego zadania, nie można użyć go w tym scenariuszu. Go nie odnajdzie zaimportowany certyfikat, ponieważ nazwa FQDN maszyny Wirtualnej na platformie Azure nie pasuje do nazwy FQDN, zgodnie z ustaleniami maszyny Wirtualnej (identyfikuje domeny komputera lokalnego lub domeny sieciowej, do której jest dołączony). Gdy nazwy nie są zgodne, aby określić certyfikat należy użyć programu regedit.
   
   * W edytorze regedit przejdź do tego klucza rejestru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     `[MSSQL13.MSSQLSERVER]` Część zależy od wersji i nazwę wystąpienia. 
   * Ustaw wartość **certyfikatu** klucza **odcisk palca** certyfikatu SSL, które zostały zaimportowane do maszyny Wirtualnej.
     
     Istnieje kilka sposobów, aby uzyskać odcisk palca, niektóre lepiej niż inne. W przypadku kopiowania z **certyfikaty** przystawkę programu MMC, należy prawdopodobnie przejmą wiodący znak niewidoczne [zgodnie z opisem w tym artykule pomocy technicznej](https://support.microsoft.com/kb/2023869/), które powoduje błąd przy próbie połączenia . Istnieje kilka obejścia dla rozwiązania tego problemu. Najprostsza jest backspace ciągu, a następnie ponownie wpisz pierwszy znak odcisku palca, aby usunąć wiodący znak w polu wartość klucza w programie regedit. Alternatywnie można użyć innego narzędzia do skopiuj odcisk palca.

3. Udziel uprawnień do konta usługi. 
   
    Upewnij się, że konto usługi programu SQL Server jest uprawnienie odpowiedniego klucza prywatnego certyfikatu SSL. Jeśli ten krok jest pomijane, programu SQL Server nie zostanie uruchomiona. Możesz użyć **certyfikaty** przystawki lub **CertUtils** dla tego zadania.
    
4. Uruchom ponownie usługę SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurowanie połączeń programu SQL Server na maszynie wirtualnej
Po skonfigurowaniu szyfrowanego połączenia wymagane przez usługę Azure Search, istnieją dodatkowe czynności konfiguracyjne wewnętrznej do programu SQL Server na maszynach wirtualnych platformy Azure. Jeśli użytkownik jeszcze tego nie zrobiono, następnym krokiem jest zakończenie konfiguracji za pomocą jednego z następujących artykułów:

* Dla **usługi Resource Manager** maszyny Wirtualnej, zobacz [Connect do maszyny wirtualnej SQL Server na platformie Azure przy użyciu usługi Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Dla **klasycznego** maszyny Wirtualnej, zobacz [Connect do programu SQL Server maszynę wirtualną w klasycznym portalu Azure](../virtual-machines/windows/classic/sql-connect.md).

W szczególności zapoznaj się z sekcją w każdym artykule "łączenia za pośrednictwem Internetu".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurowanie sieciowej grupy zabezpieczeń (NSG)
Nie jest niczym niezwykłym skonfigurować sieciowej grupy zabezpieczeń i odpowiedni punkt końcowy platformy Azure lub listy kontroli dostępu (ACL) udostępnić maszynie Wirtualnej platformy Azure dla innych stron. Jest szansa, że wykonano to przed umożliwia własną logikę aplikacji, nawiązywania połączenia z maszyną wirtualną Azure SQL. Go nie różni się dla usługi Azure Search połączenia z maszyną wirtualną Azure SQL. 

Poniższe linki zawierają instrukcje dotyczące konfiguracji sieciowej grupy zabezpieczeń dla wdrożeń maszyn wirtualnych. Użyj tych instrukcji do listy ACL punktu końcowego usługi Azure Search na podstawie jego adresu IP.

> [!NOTE]
> Aby uzyskać ogólne, zobacz [co to jest sieciowa grupa zabezpieczeń?](../virtual-network/security-overview.md)
> 
> 

* Dla **usługi Resource Manager** maszyny Wirtualnej, zobacz [tworzenie sieciowych grup zabezpieczeń dla wdrożeń ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Dla **klasycznego** maszyny Wirtualnej, zobacz [tworzenie sieciowych grup zabezpieczeń dla wdrożeń Classic](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Adresowanie IP może stanowić kilka wyzwania, które są łatwe przezwyciężyć, jeśli masz świadomość problemu i potencjalnych rozwiązań. W pozostałych sekcjach przedstawiono zalecenia dotyczące obsługi problemów związanych z adresów IP na liście ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Ograniczanie dostępu do adresu IP usługi wyszukiwania
Zdecydowanie zaleca się ograniczyć dostęp do adresu IP usługi wyszukiwania na liście ACL dokonywane maszynach wirtualnych platformy Azure SQL w pełni otwarta do wszystkich żądań połączenia. Można łatwo znaleźć adres IP, wysyłając polecenie ping nazwa FQDN (na przykład `<your-search-service-name>.search.windows.net`) usługi wyszukiwania.

#### <a name="managing-ip-address-fluctuations"></a>Zarządzanie zmianami adresu IP
Jeśli usługa wyszukiwania ma tylko jedną jednostkę wyszukiwania (oznacza to jedną replikę i jednej partycji), adres IP ulegnie zmianie podczas ponownego uruchamiania komputera usługa rutynowych unieważnienia istniejących listy ACL z adresu IP usługi wyszukiwania.

Jednym ze sposobów, aby uniknąć tego błędu kolejne połączenie jest używać więcej niż jednej repliki i jedną partycję w usłudze Azure Search. To zwiększa koszty, ale także rozwiązuje problem adresu IP. W usłudze Azure Search nie zmieniaj adresów IP, jeśli masz więcej niż jednej jednostki wyszukiwania.

Druga metoda jest zezwalają na połączenie zakończyć się niepowodzeniem, a następnie ponownie skonfiguruj listy ACL w sieciowej grupie zabezpieczeń. Zazwyczaj można oczekiwać, że adresy IP, aby zmienić co kilka tygodni. Dla klientów, którzy wykonują indeksowania kontrolowane na podstawie rzadkie takie podejście może być możliwego do użycia.

Trzeci podejście możliwego do użycia (ale szczególnie niezabezpieczony) jest Określ zakres adresów IP regionu Azure, w którym zainicjowano usługi wyszukiwania. Lista zakresów adresów IP, z której publiczne adresy IP są przydzielane do zasobów platformy Azure, jest opublikowana w [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Zawierać adresy IP z portalu usługi Azure Search
Korzystania z witryny Azure portal do utworzenia indeksatora logiki portalu usługi Azure Search również musi mieć dostęp do maszyny Wirtualnej SQL Azure podczas tworzenia. Adresy IP z portalu usługi Azure search można znaleźć, wysyłając polecenie ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Kolejne kroki
Za pomocą konfiguracji na bok można teraz określić programu SQL Server na maszynie Wirtualnej platformy Azure jako źródło danych dla indeksatora usługi Azure Search. Zobacz [łączenie usługi Azure SQL Database do usługi Azure Search przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) Aby uzyskać więcej informacji.

