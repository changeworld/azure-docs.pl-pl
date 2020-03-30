---
title: Połączenie maszyny Wirtualnej sql platformy Azure do indeksowania wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz połączenia szyfrowane i skonfiguruj zaporę, aby zezwolić na połączenia z programem SQL Server na maszynie wirtualnej platformy Azure (VM) z indeksatora w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256967"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurowanie połączenia z indeksatora usługi Azure Cognitive Search do programu SQL Server na maszynie Wirtualnej platformy Azure

Jak wspomniano w [connecting azure sql database do usługi Azure Cognitive Search przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), tworzenie indeksatorów **dla programu SQL Server na maszynach wirtualnych platformy Azure** (lub SQL Azure VMs w **skrócie)** jest obsługiwany przez usługę Azure Cognitive Search, ale istnieje kilka wymagań wstępnych związanych z zabezpieczeniami, aby najpierw zająć się. 

Połączenia z usługi Azure Cognitive Search do PROGRAMU SQL Server na maszynie Wirtualnej to publiczne połączenie internetowe. Wszystkie środki bezpieczeństwa, które normalnie należy stosować dla tych połączeń stosuje się również tutaj:

+ Uzyskaj certyfikat od [dostawcy urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority#Providers) dla w pełni kwalifikowanej nazwy domeny wystąpienia programu SQL Server na maszynie Wirtualnej platformy Azure.
+ Zainstaluj certyfikat na maszynie wirtualnej, a następnie włącz i skonfiguruj połączenia szyfrowane na maszynie Wirtualnej, korzystając z instrukcji zawartych w tym artykule.

## <a name="enable-encrypted-connections"></a>Włączanie połączeń szyfrowanych
Usługa Azure Cognitive Search wymaga zaszyfrowanego kanału dla wszystkich żądań indeksatora za pośrednictwem publicznego połączenia internetowego. W tej sekcji wymieniono kroki, aby to działało.

1. Sprawdź właściwości certyfikatu, aby sprawdzić, czy nazwa podmiotu jest w pełni kwalifikowaną nazwą domeny (FQDN) maszyny Wirtualnej platformy Azure. Do wyświetlenia właściwości można użyć narzędzia, takiego jak CertUtils lub przystawki Certyfikaty. Nazwę FQDN można uzyskać z sekcji Essentials programu Usługi VM w polu **Publiczny adres IP/Etykieta nazwy DNS** w [witrynie Azure portal](https://portal.azure.com/).
   
   * W przypadku maszyn wirtualnych utworzonych przy użyciu nowszego szablonu **Menedżera zasobów** nazwa FQDN jest`<your-VM-name>.<region>.cloudapp.azure.com`
   * W przypadku starszych maszyn wirtualnych utworzonych jako **klasyczna** maszyna `<your-cloud-service-name.cloudapp.net>`wirtualna plik FQDN jest formatowany jako .

2. Skonfiguruj program SQL Server do używania certyfikatu przy użyciu Edytora rejestru (regedit). 
   
    Chociaż program SQL Server Configuration Manager jest często używany do tego zadania, nie można go użyć w tym scenariuszu. Nie znajdzie zaimportowanego certyfikatu, ponieważ nazwa FQDN maszyny Wirtualnej na platformie Azure nie jest zgodna z nazwaiem FQDN określonym przez maszynę wirtualną (identyfikuje domenę jako komputer lokalny lub domenę sieciową, do której jest przyłączona). Jeśli nazwy nie są zgodne, użyj regedit, aby określić certyfikat.
   
   * W regedit, przejdź do tego `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`klucza rejestru: .
     
     Część `[MSSQL13.MSSQLSERVER]` różni się w zależności od wersji i nazwy wystąpienia. 
   * Ustaw wartość **klucza certyfikatu** na **odcisk palca** certyfikatu TLS/SSL zaimportowanego do maszyny Wirtualnej.
     
     Istnieje kilka sposobów, aby uzyskać odcisk palca, niektóre lepsze niż inne. Jeśli skopiujesz go z **przystawki Certyfikaty** w programie MMC, prawdopodobnie wybierzesz niewidoczny znak wiodący [zgodnie z opisem w tym artykule pomocy technicznej,](https://support.microsoft.com/kb/2023869/)co powoduje błąd podczas próby połączenia. Istnieje kilka obejść w celu rozwiązania tego problemu. Najłatwiej jest backspace nad, a następnie ponownie wpisz pierwszy znak odcisku palca, aby usunąć znak wiodący w polu wartości klucza w regedit. Alternatywnie można użyć innego narzędzia do kopiowania odcisku palca.

3. Udziel uprawnień do konta usługi. 
   
    Upewnij się, że konto usługi PROGRAMU SQL Server uzyskało odpowiednie uprawnienia do klucza prywatnego certyfikatu TLS/SSL. Jeśli przeoczysz ten krok, sql server nie zostanie uruchomiony. W tym zadaniu można użyć przystawki **Certyfikaty** lub **Certyfikaty.**
    
4. Uruchom ponownie usługę SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurowanie łączności programu SQL Server na maszynie Wirtualnej
Po skonfigurowaniu szyfrowanego połączenia wymaganego przez usługę Azure Cognitive Search istnieją dodatkowe kroki konfiguracji wewnętrzne dla programu SQL Server na maszynach wirtualnych platformy Azure. Jeśli jeszcze tego nie zrobiono, następnym krokiem jest zakończenie konfiguracji przy użyciu jednego z następujących artykułów:

* Aby uzyskać maszynę wirtualną **Menedżera zasobów,** zobacz [Łączenie się z maszyną wirtualną programu SQL Server na platformie Azure przy użyciu Menedżera zasobów](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Aby uzyskać **klasyczną** maszynę wirtualną, zobacz [Łączenie się z maszyną wirtualną programu SQL Server na platformie Azure Classic](../virtual-machines/windows/classic/sql-connect.md).

W szczególności zapoznaj się z sekcją w każdym artykule dotyczącą "łączenia się przez Internet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurowanie sieciowej grupy zabezpieczeń (NSG)
Nie jest niczym niezwykłym, aby skonfigurować sieciowej sieciowej sieciowej i odpowiadające jej punktu końcowego platformy Azure lub listy kontroli dostępu (ACL), aby twoja maszyna wirtualna platformy Azure była dostępna dla innych stron. Prawdopodobnie zrobiłeś to wcześniej, aby umożliwić własną logikę aplikacji, aby połączyć się z maszyną wirtualną sql azure. Nie inaczej jest w przypadku połączenia usługi Azure Cognitive Search z maszyną wirtualną platformy SQL Azure. 

Poniższe łącza zawierają instrukcje dotyczące konfiguracji sieciowej grupy zabezpieczeń dla wdrożeń maszyn wirtualnych. Użyj tych instrukcji do listy ACL punktu końcowego usługi Azure Cognitive Search na podstawie jego adresu IP.

> [!NOTE]
> Aby uzyskać informacje na temat tła, zobacz [Co to jest grupa zabezpieczeń sieci?](../virtual-network/security-overview.md)
> 
> 

* Aby uzyskać maszynę wirtualną **Menedżera zasobów,** zobacz [Jak utworzyć sieciowe grupy zabezpieczeń dla wdrożeń ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Aby uzyskać **klasyczną** maszynę wirtualną, zobacz [Jak utworzyć sieciowe sieciowe dla wdrożeń klasycznych](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Adresowanie ip może stanowić kilka wyzwań, które można łatwo przezwyciężyć, jeśli jesteś świadomy problemu i potencjalnych rozwiązań. Pozostałe sekcje zawierają zalecenia dotyczące obsługi problemów związanych z adresami IP w acl.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Ograniczanie dostępu do usługi Azure Cognitive Search
Zdecydowanie zaleca się ograniczenie dostępu do adresu IP usługi wyszukiwania i `AzureCognitiveSearch` zakresu adresów IP [tagu usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) na listy ACL zamiast udostępniania maszyn wirtualnych sql azure dla wszystkich żądań połączeń.

Adres IP można znaleźć, wysyłając ping do sieci FQDN (na `<your-search-service-name>.search.windows.net`przykład) usługi wyszukiwania.

Zakres adresów IP `AzureCognitiveSearch` [tagu usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) można znaleźć za pomocą [plików JSON do pobrania](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) lub interfejsu API [odnajdowania tagów usługi.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) Zakres adresów IP jest aktualizowany co tydzień.

#### <a name="managing-ip-address-fluctuations"></a>Zarządzanie wahaniami adresów IP
Jeśli usługa wyszukiwania ma tylko jedną jednostkę wyszukiwania (czyli jedną replikę i jedną partycję), adres IP zmieni się podczas rutynowego ponownego uruchamiania usługi, unieważniając istniejącą acl z adresem IP usługi wyszukiwania.

Jednym ze sposobów uniknięcia późniejszego błędu łączności jest użycie więcej niż jednej repliki i jednej partycji w usłudze Azure Cognitive Search. Zwiększa to koszty, ale także rozwiązuje problem z adresem IP. W usłudze Azure Cognitive Search adresy IP nie zmieniają się, gdy masz więcej niż jedną jednostkę wyszukiwania.

Drugie podejście jest umożliwienie połączenia zakończyć się niepowodzeniem, a następnie ponownie skonfigurować listy kontroli dostępu w sieciowej listy sieciowej. Średnio można oczekiwać, że adresy IP będą się zmieniać co kilka tygodni. Dla klientów, którzy kontrolują indeksowanie rzadko, takie podejście może być opłacalne.

Trzeci opłacalne (ale nie szczególnie bezpieczne) podejście jest określenie zakresu adresów IP w regionie platformy Azure, w którym usługa wyszukiwania jest aprowizowana. Lista zakresów adresów IP, z których publiczne adresy IP są przydzielane do zasobów platformy Azure, jest publikowana w [zakresach adresów IP centrum danych platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653) 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Uwzględnianie adresów IP portalu usługi Azure Cognitive Search
Jeśli używasz witryny Azure portal do tworzenia indeksatora, logika portalu usługi Azure Cognitive Search również wymaga dostępu do maszyny Wirtualnej platformy SQL Azure w czasie tworzenia. Adresy IP portalu usługi Azure Cognitive `stamp2.search.ext.azure.com`Search można znaleźć za pomocą pingowania .

## <a name="next-steps"></a>Następne kroki
Z konfiguracji na uboczu, można teraz określić SQL Server na maszynie Wirtualnej platformy Azure jako źródło danych dla indeksatora usługi Azure Cognitive Search. Zobacz [Łączenie usługi Azure SQL Database do usługi Azure Cognitive Search przy użyciu indeksatorów, aby](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) uzyskać więcej informacji.

