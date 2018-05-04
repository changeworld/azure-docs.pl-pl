---
title: Maszyna wirtualna SQL połączenie do usługi Azure Search | Dokumentacja firmy Microsoft
description: Włączanie połączeń szyfrowanych i skonfigurować zaporę tak, aby zezwolić na połączenia z programem SQL Server na maszynie wirtualnej platformy Azure (VM) z indeksatora w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: heidist
ms.openlocfilehash: 34c5d1999625d1728e884adb794af235ba415c26
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Skonfiguruj połączenie z indeksator usługi Azure Search do programu SQL Server na maszynie Wirtualnej platformy Azure
Zgodnie z opisem w [połączenie bazy danych SQL Azure do usługi Azure Search przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), tworzenie indeksatory przed **programu SQL Server na maszynach wirtualnych Azure** (lub **maszynach wirtualnych Azure SQL** skrócie) jest obsługiwana przez usługi Azure Search, ale istnieje kilka związanych z zabezpieczeniami wymagania wstępne dotyczące zajmie się pierwszy. 

**Czas trwania:** około 30 minut, zakładając, że możesz już zainstalowany certyfikat na maszynie Wirtualnej.

## <a name="enable-encrypted-connections"></a>Włączanie połączeń szyfrowanych
Usługa Azure Search wymaga zaszyfrowanego kanału dla wszystkich żądań indeksatora za pośrednictwem publicznego połączenia internetowego. W tej sekcji wymieniono kroki, aby umożliwić użycie tych wartości.

1. Sprawdź właściwości certyfikatu, aby sprawdzić, czy nazwa podmiotu jest w pełni kwalifikowaną nazwę (FQDN) maszyny wirtualnej Azure. Aby wyświetlić właściwości, można użyć narzędzia, takiego jak CertUtils lub przystawki Certyfikaty. Nazwa FQDN z sekcji Essentials bloku usługi maszyny Wirtualnej, można uzyskać w **publiczny adres IP na adres/etykieta nazwy DNS** w [portalu Azure](https://portal.azure.com/).
   
   * Dla maszyn wirtualnych utworzonych przy użyciu nowszej **Resource Manager** szablonu, nazwy FQDN jest w formacie `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * Dla starszych maszyny wirtualne utworzone jako **klasycznego** maszyny Wirtualnej, nazwy FQDN jest w formacie `<your-cloud-service-name.cloudapp.net>`. 
2. Skonfiguruj program SQL Server do używania certyfikatu za pomocą Edytora rejestru (regedit). 
   
    Chociaż Menedżer konfiguracji programu SQL Server jest często używany do tego zadania, nie można używać go w tym scenariuszu. Nie będzie go odnaleźć zaimportowanego certyfikatu, ponieważ nazwa FQDN maszyny wirtualnej na platformie Azure nie jest zgodna nazwę FQDN określoną przez maszynę Wirtualną (identyfikuje domena komputera lokalnego lub domeny sieciowej, do której jest dołączony). Gdy nazwy nie są zgodne, użyj regedit, aby określić certyfikat.
   
   * W programie regedit, przejdź do tego klucza rejestru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     `[MSSQL13.MSSQLSERVER]` Części w zależności od wersji i nazwę wystąpienia. 
   * Ustaw wartość **certyfikatu** klucza **odcisk palca** certyfikatu SSL zaimportowane do maszyny Wirtualnej.
     
     Istnieje kilka sposobów, aby uzyskać odcisk palca niektóre lepiej od innych. W przypadku kopiowania z **certyfikaty** przystawkę programu MMC, użytkownik prawdopodobnie przejmą niewidoczne wiodących znaków [zgodnie z opisem w tym artykule pomocy technicznej](https://support.microsoft.com/kb/2023869/), które powoduje błąd podczas próby połączenia. Istnieje kilka obejścia dla rozwiązania tego problemu. Najprostsza jest backspace za pośrednictwem, a następnie ponownie wpisz pierwszy znak odcisku palca w celu usunięcia wiodących znaków w polu wartość klucza w programie regedit. Alternatywnie można użyć innego narzędzia do skopiuj odcisk palca.
3. Udziel uprawnień do konta usługi. 
   
    Upewnij się, że konto usługi programu SQL Server jest uprawnienie odpowiedniego klucza prywatnego certyfikatu protokołu SSL. Jeśli pomijane ten krok, programu SQL Server nie zostanie uruchomiona. Można użyć **certyfikaty** przystawki lub **CertUtils** dla tego zadania.
4. Uruchom ponownie usługę SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Skonfiguruj połączenie programu SQL Server w maszynie Wirtualnej
Po skonfigurowaniu zaszyfrowanego połączenia wymagane przez usługę Azure Search są dodatkowe czynności konfiguracyjne wewnętrznej do programu SQL Server na maszynach wirtualnych Azure. Jeśli jeszcze tego nie zrobiono tego wcześniej, następnym krokiem jest zakończenie konfiguracji przy użyciu jednej z tych artykułach:

* Dla **Resource Manager** maszyny Wirtualnej, zobacz [Connect do programu SQL Server maszynę wirtualną na platformie Azure przy użyciu usługi Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Dla **klasycznego** maszyny Wirtualnej, zobacz [Connect do SQL Server maszynę wirtualną w klasycznym Azure](../virtual-machines/windows/classic/sql-connect.md).

W szczególności zapoznaj się z sekcją w każdym artykule "połączenie za pośrednictwem Internetu".

## <a name="configure-the-network-security-group-nsg"></a>Skonfiguruj grupy zabezpieczeń sieci (NSG)
Nie jest nietypowe do skonfigurowania udostępnić maszynie Wirtualnej platformy Azure dla innych stron NSG i odpowiedniego punktu końcowego platformy Azure lub listy kontroli dostępu (ACL). Prawdopodobnie przycisk przed zezwalająca na własną logikę aplikacji, nawiązywania połączenia z maszyną Wirtualną programu SQL Azure. Nie różni się dla połączenia usługi Azure Search do maszyny Wirtualnej Azure SQL się go. 

Poniższe linki zawierają instrukcje konfiguracji wdrażania maszyny Wirtualnej w konfiguracji grupy NSG. Użyj tych instrukcji do listy ACL punktu końcowego usługi Azure SEarch, na podstawie jego adresu IP.

> [!NOTE]
> W tle, zobacz [co to jest grupa zabezpieczeń sieci?](../virtual-network/virtual-networks-nsg.md)
> 
> 

* Dla **Resource Manager** maszyny Wirtualnej, zobacz [sposób tworzenia grup NSG wdrożeń ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 
* Dla **klasycznego** maszyny Wirtualnej, zobacz [sposób tworzenia grup NSG wdrożeń Classic](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Adresy IP mogą wiązać się z kilku wyzwania, które są łatwo rozwiązać, jeśli masz świadomość problem i potencjalne rozwiązania problemu. W pozostałych sekcjach przedstawiono zalecenia dotyczące obsługi problemów związanych z adresami IP na liście ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Ograniczanie dostępu do adresu IP usługi wyszukiwania
Zdecydowanie zaleca się ograniczyć dostęp do adresu IP z usługą wyszukiwania na liście ACL zamiast wprowadzania otwartej maszynach wirtualnych platformy SQL Azure żądań połączenia. Adres IP można łatwo sprawdzić, wysyłając polecenie ping nazwę FQDN (na przykład `<your-search-service-name>.search.windows.net`) usługi wyszukiwania.

#### <a name="managing-ip-address-fluctuations"></a>Zarządzanie zmianami adresu IP
Jeśli usługi wyszukiwania ma tylko jedną jednostkę wyszukiwania (czyli jedna replika i jednej partycji), adres IP zostanie zmieniona podczas ponownego uruchamiania komputera usługa rutynowych unieważnia istniejące listy ACL z adresu IP usługi wyszukiwania.

Jednym ze sposobów uniknąć tego błędu, kolejne łączności jest użycie więcej niż jednej repliki i jedną partycję w usłudze Azure Search. Dzięki temu zwiększa koszt, ale również rozwiązuje problem adresu IP. W usłudze Azure Search nie zmian adresów IP, jeśli masz więcej niż jednej jednostki wyszukiwania.

Drugi podejście jest zezwalały na połączenie zakończyć się niepowodzeniem, a następnie ponownie skonfigurować listy kontroli dostępu w grupie NSG. Średnia można oczekiwać, że adresy IP, aby zmienić co kilka tygodni. Dla klientów, którzy są kontrolowane indeksowania na podstawie rzadkim ta metoda może być działało.

Określ zakres adresów IP regionu Azure, gdzie zainicjowaniu obsługi usługi wyszukiwania jest podejście trzeci działało (lecz nie szczególnie bezpieczny). Lista zakresów adresów IP, z których publiczne adresy IP są przydzielane zasoby platformy Azure jest opublikowana w [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Zawierać adresy IP z portalu usługi Azure Search
Jeśli używasz portalu Azure do utworzenia indeksatora, logiki portalu usługi Azure Search również musi mieć dostęp do maszyny Wirtualnej Azure SQL podczas tworzenia. Usługa Azure search portalu adresów IP można znaleźć wysyłając polecenie ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Kolejne kroki
Sposób konfiguracji można teraz wskazać serwer SQL na maszynie Wirtualnej Azure, jako źródło danych dla indeksatora usługi Azure Search. Zobacz [połączenie bazy danych SQL Azure do usługi Azure Search przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) Aby uzyskać więcej informacji.

