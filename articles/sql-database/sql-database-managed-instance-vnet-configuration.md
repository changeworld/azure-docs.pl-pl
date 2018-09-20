---
title: Usługa Azure SQL Database Managed konfiguracja sieci wirtualnej wystąpienia | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla sieci wirtualnej (VNet) z wystąpienia zarządzanego bazy danych SQL Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: c2caa9ebbc3a4e6b9180466950775524986fd244
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364571"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurowanie sieci wirtualnej dla wystąpienie zarządzane usługi Azure SQL Database

Wystąpienie usługi Azure SQL Database Managed (wersja zapoznawcza) musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). To wdrożenie umożliwia następujące scenariusze: 
- Połączenie do wystąpienia zarządzanego bezpośrednio z siecią lokalną 
- Nawiązywanie połączenia z wystąpienia zarządzanego połączonego serwera lub innego środowiska lokalnego magazynu danych 
- Wystąpienie zarządzane nawiązywania połączenia z zasobami platformy Azure  

## <a name="plan"></a>Planowanie

Zaplanuj, jak wdrożyć wystąpienie zarządzane w sieci wirtualnej przy użyciu odpowiedzi na następujące pytania: 
- Czy planowane jest wdrażanie jednego lub wielu wystąpień zarządzanych? 

  Liczba wystąpień zarządzanych Określa minimalny rozmiar podsieci, aby przydzielić dla swoich wystąpień zarządzanych. Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci dla wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances). 
- Muszą wdrożenia wystąpienia zarządzanego w istniejącej sieci wirtualnej, czy tworzysz nową sieć? 

   Jeśli planujesz użyć istniejącej sieci wirtualnej, należy zmodyfikować tej konfiguracji sieci, aby pomieścić wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](#modify-an-existing-virtual-network-for-managed-instances). 

   Jeśli zamierzasz utworzyć nową sieć wirtualną, zobacz [Utwórz nową sieć wirtualną dla wystąpienia zarządzanego](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Wymagania

Aby utworzyć wystąpienie zarządzane, należy utworzyć dedykowaną podsieć (podsieci wystąpienia zarządzanego) wewnątrz sieci wirtualnej, która spełnia następujące wymagania:
- **W wersji dedykowanej podsieci**: podsieci wystąpienia zarządzanego nie może zawierać wszystkie inne usługi w chmurze skojarzone z nim, a nie może być podsieć bramy. Nie można utworzyć wystąpienie zarządzane w podsieci, która zawiera zasoby innych niż wystąpienia zarządzanego, a nie można później dodać innych zasobów w podsieci.
- **Niezgodne grupy zabezpieczeń sieci (NSG)**: sieciowej grupy zabezpieczeń, które są skojarzone z podsieci wystąpienia zarządzanego musi zawierać reguły pokazano w poniższych tabelach (reguł zabezpieczeń ruchu przychodzącego obowiązkowe i reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe) przed jakiekolwiek inne reguły. Sieciowa grupa zabezpieczeń można użyć w pełni kontrolować dostęp do endpoint danych wystąpienia zarządzanego, filtrując ruch na porcie 1433. 
- **Tabeli zgodnych tras zdefiniowanych przez użytkownika (UDR)**: podsieci wystąpienia zarządzanego musi mieć tabelę tras użytkownika za pomocą **internetowej następnego przeskoku 0.0.0.0/0** jako obowiązkowe trasy zdefiniowanej przez użytkownika do niej przypisany. Ponadto możesz dodać trasy zdefiniowanej przez użytkownika tego kieruje ruch z zakresów IP prywatnych w środowisku lokalnym jako miejsce docelowe za pośrednictwem bramy sieci wirtualnej lub sieci wirtualne urządzenie sieciowe. 
- **Opcjonalne niestandardowe DNS**: Jeżeli niestandardowe DNS jest określony w wirtualnej sieciowy, adres IP platformy Azure cyklicznego programu rozpoznawania nazw (na przykład 168.63.129.16) musi być dodana do listy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md). Niestandardowego serwera DNS musi być w stanie rozpoznać nazwy hostów w następujących domen i poddomen ich: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, i *microsoftonline-p.com*. 
- **Brak punktów końcowych usługi**: podsieci wystąpienia zarządzanego nie może mieć powiązany punktu końcowego usługi. Upewnij się, że opcji punktów końcowych usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- **Wystarczającą liczbą adresów IP**: podsieci wystąpienia zarządzanego jest posiadanie absolutnego minimum 16 adresów IP (zalecane są co najmniej 32 adresów IP). Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienie zarządzane, jeśli podsieci docelowej nie jest zgodny ze wszystkimi te wymagania. Podczas tworzenia wystąpienia zarządzanego *sieciowe przeznaczenie zasady* została zastosowana w tej podsieci, aby uniemożliwić niezgodnych zmian do konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci, *sieciowe przeznaczenie zasady* zostanie także usunięta

### <a name="mandatory-inbound-security-rules"></a>Reguły zabezpieczeń ruchu przychodzącego obowiązkowe 

| NAZWA       |PORT                        |PROTOKÓŁ|Źródło           |MIEJSCE DOCELOWE|AKCJA|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438 1440, 1452|TCP     |Dowolne              |Dowolne        |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |Dowolne        |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|Dowolne        |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe 

| NAZWA       |PORT          |PROTOKÓŁ|Źródło           |MIEJSCE DOCELOWE|AKCJA|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |80, 443, 12000|TCP     |Dowolne              |Dowolne        |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |Dowolne              |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO  |Zezwalaj |

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Określ rozmiar podsieci wystąpienia zarządzanego

Podczas tworzenia wystąpienia zarządzanego Azure przydziela liczbę maszyn wirtualnych, w zależności od warstwy wybranej podczas inicjowania obsługi. Ponieważ te maszyny wirtualne są skojarzone z podsieci, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacjach i obsłudze usługi, platformy Azure może przydzielić dodatkowe maszyny wirtualne. W wyniku liczbę wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci. 

Zgodnie z projektem wystąpienie zarządzane wymaga co najmniej 16 adresów IP w podsieci i może używać maksymalnie 256 adresów IP. W rezultacie można użyć maski podsieci /28 na prefiksie/24, podczas definiowania zakresów IP podsieci. 

> [!IMPORTANT]
> Rozmiar podsieci o 16 adresów IP jest absolutnego minimum przy użyciu ograniczonych możliwości dalsze skalowanie wystąpienia zarządzanego w poziomie. Zdecydowanie zaleca się wybranie pozycji podsieci z prefiksem wartość/27 lub poniżej. 

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych w tej podsieci i należy zoptymalizować rozmiar podsieci, użyj tych parametrów w celu utworzenia obliczeń: 

- Platforma Azure używa pięciu adresów IP w podsieci dla własnych potrzeb 
- Każde wystąpienie ogólnego przeznaczenia należy dwa adresy 
- Każde wystąpienie krytyczne dla działania firmy wymaga czterech adresów

**Przykład**: ma trzy ogólnego przeznaczenia i dwa biznesowe krytyczne wystąpienia zarządzane przez usługę. Czy potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 oznacza, że adresy IP. Zakresy adresów IP określonych w potęgą liczby 2 należy zakresu adresów IP 32 (2 ^ 5) adresy IP. W związku z tym należy zarezerwować podsieć z maską podsieci/27. 

> [!IMPORTANT]
> Obliczenie wyświetlane powyżej staną się nieaktualne z dalsze ulepszenia. 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Utwórz nową sieć wirtualną dla wystąpienia zarządzanego przy użyciu wdrożenia usługi Azure Resource Manager

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrożenia usługi Azure Resource Manager.

1. Zaloguj się do Portalu Azure.

2. Użyj **Wdróż na platformie Azure** przycisk, aby wdrożyć sieć wirtualną w chmurze platformy Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Kliknięcie tego przycisku zostanie otwarty formularz, który można użyć do konfigurowania środowisko sieciowe, w którym można wdrożyć wystąpienie zarządzane usługi.

  > [!Note]
  > Ten szablon usługi Azure Resource Manager umożliwia wdrożenie sieci wirtualnej z dwiema podsieciami. Jedną podsieć o nazwie **ManagedInstances** jest zarezerwowany dla wystąpienia zarządzanego i ma wstępnie skonfigurowane na tabelę tras, podczas gdy inne podsieci o nazwie **domyślne** służy do innych zasobów, które powinien uzyskiwać dostęp do zarządzanego Wystąpienie (na przykład maszyny wirtualne platformy Azure). Możesz usunąć **domyślne** podsieci, o ile nie są potrzebne.

3. Konfigurowanie środowiska sieciowego. W następującej postaci można skonfigurować parametrów środowiska sieciowego:

![Skonfiguruj sieć platformy azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Może zmieniać nazwy sieci wirtualnej i podsieci i dostosować zakresów adresów IP skojarzonych z zasobami w sieci. Po naciśnięciu przycisku "Kup", ta forma będzie tworzyć i konfigurować środowiska. Jeśli dwie podsieci nie są potrzebne, można usunąć domyślny. 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modyfikuj istniejącą sieć wirtualną dla wystąpienia zarządzanego 

Pytania i odpowiedzi w tej sekcji pokazano, jak dodać wystąpienia zarządzanego do istniejącej sieci wirtualnej. 

**Czy używasz modelu wdrażania klasycznego lub usługi Resource Manager dla istniejącej sieci wirtualnej?** 

Wystąpienie zarządzane można utworzyć tylko w sieci wirtualnej usługi Resource Manager. 

**Czy chcesz utworzyć nową podsieć dla wystąpienia zarządzanego SQL lub użyć istniejącego?**

Jeśli chcesz utworzyć nowy: 

- Oblicz rozmiar podsieci, postępując zgodnie z wytycznymi podanymi w [określi rozmiar podsieci wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances) sekcji.
- Postępuj zgodnie z instrukcjami w [Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md). 
- Utwórz tabelę tras, która zawiera pojedynczy wpis **0.0.0.0/0**, jak następnego przeskoku Internet i skojarzyć ją z podsiecią dla wystąpienia zarządzanego.  

Jeśli chcesz utworzyć wystąpienie zarządzane wewnątrz istniejącej podsieci, zaleca się poniższy skrypt programu PowerShell, aby przygotować się do podsieci.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
Przygotowanie podsieci odbywa się w trzech prostych krokach:

- Sprawdź poprawność — wybrane netwok wirtualnych i podsieci są prawidłowe dla zarządzanego wystąpienia wymagania sieciowe
- Upewnij się, — użytkownik jest wyświetlany zestaw zmian, które muszą zostać wprowadzone do przygotowania podsieci do wdrożenia wystąpienia zarządzanego i monit o zgodę
- Przygotowanie — sieci wirtualnej i podsieci są prawidłowo skonfigurowane

**Czy masz niestandardowego serwera DNS skonfigurowanego?** 

Jeśli tak, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md). 

- Tworzenie tabeli tras wymagane i skojarz go: zobacz [Tworzenie tabeli tras wymagane i skojarz go](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby uzyskać samouczek, w którym pokazano, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md)
