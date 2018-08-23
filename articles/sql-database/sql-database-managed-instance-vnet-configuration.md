---
title: Usługa Azure SQL Database Managed konfiguracja sieci wirtualnej wystąpienia | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla sieci wirtualnej (VNet) z wystąpienia zarządzanego bazy danych SQL Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f634167f24c221e702696174ea86a212c535695b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057402"
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

Tworzenie wystąpienia zarządzanego należy przeznaczyć podsieci w sieci wirtualnej, który spełnia następujące wymagania:
- **Być puste**: podsieci nie może zawierać innych usług cloud, powiązany i nie może być podsieć bramy. Nie można utworzyć wystąpienie zarządzane usługi w podsieci, która zawiera zasobów innych niż wystąpienia zarządzanego lub dodanie innych zasobów w obrębie podsieci później.
- **Brak sieciowej grupy zabezpieczeń**: podsieci nie może mieć skojarzonych z nią sieciową grupę zabezpieczeń.
- **Tabela jest określoną trasę**: podsieć musi mieć użytkownika tabeli (UDR) za pomocą internetowej następnego przeskoku 0.0.0.0/0 jako tylko trasy do niej przypisany. Aby uzyskać więcej informacji, zobacz [Tworzenie tabeli tras wymagane i skojarz go](#create-the-required-route-table-and-associate-it)
3. **Opcjonalne niestandardowe DNS**: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, należy dodać adres IP platformy Azure rekursywnego rozpoznawania nazw (na przykład 168.63.129.16) do listy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md).
4. **Nie punktu końcowego usługi**: podsieci nie może mieć punktu końcowego usługi (magazyn lub Sql) powiązany. Upewnij się, że punkty końcowe usługi opcja jest wyłączona, podczas tworzenia sieci wirtualnej.
5. **Wystarczającą liczbą adresów IP**: podsieć musi mieć co najmniej 16 adresów IP. Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienie zarządzane, jeśli podsieci docelowej nie jest zgodny z wszystkich poprzednich wymagań. Docelowej sieci wirtualnej i podsieci muszą być przechowywane zgodnie z wymaganiami te wystąpienia zarządzanego (przed i po wdrożeniu), zgodnie z naruszeniem może spowodować wystąpienie wejść w stan uszkodzony i staną się niedostępne. Odzyskiwanie z, że stan wymaga utworzenia nowego wystąpienia w sieci wirtualnej przy użyciu zgodnymi zasadami sieci, ponownie Utwórz dane na poziomie wystąpienia i przywrócenia baz danych. Wprowadza znaczących przestojów w działaniu aplikacji.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Określ rozmiar podsieci wystąpienia zarządzanego

Podczas tworzenia wystąpienia zarządzanego Azure przydziela liczbę maszyn wirtualnych, w zależności od warstwy o rozmiarze wybranych podczas inicjowania obsługi administracyjnej. Ponieważ te maszyny wirtualne są skojarzone z podsieci, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacjach i obsłudze usługi, platformy Azure może przydzielić dodatkowe maszyny wirtualne. W wyniku liczbę wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci. 

Zgodnie z projektem wystąpienie zarządzane wymaga co najmniej 16 adresów IP w podsieci i może używać maksymalnie 256 adresów IP. W rezultacie można użyć maski podsieci /28 na prefiksie/24, podczas definiowania zakresów IP podsieci. 

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych w tej podsieci i należy zoptymalizować rozmiar podsieci, użyj tych parametrów w celu utworzenia obliczeń: 

- Platforma Azure używa pięciu adresów IP w podsieci dla własnych potrzeb 
- Każde wystąpienie ogólnego przeznaczenia należy dwa adresy 
- Każde wystąpienie krytyczne dla działania firmy wymaga czterech adresów

**Przykład**: ma trzy ogólnego przeznaczenia i dwa biznesowe krytyczne wystąpienia zarządzane przez usługę. Czy potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 oznacza, że adresy IP. Zakresy adresów IP określonych w potęgą liczby 2 należy zakresu adresów IP 32 (2 ^ 5) adresy IP. W związku z tym należy zarezerwować podsieć z maską podsieci/27. 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Utwórz nową sieć wirtualną dla wystąpienia zarządzanego przy użyciu wdrożenia usługi Azure Resource Manager

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrożenia usługi Azure Resource Manager.

1. Zaloguj się do Portalu Azure.

2. Użyj **Wdróż na platformie Azure** przycisk, aby wdrożyć sieć wirtualną w chmurze platformy Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Kliknięcie tego przycisku zostanie otwarty formularz, który można użyć do konfigurowania środowisko sieciowe, w którym można wdrożyć wystąpienie zarządzane usługi.

  > [!Note]
  > Ten szablon usługi Azure Resource Manager umożliwia wdrożenie sieci wirtualnej z dwiema podsieciami. Jedną podsieć o nazwie **ManagedInstances** jest zarezerwowany dla wystąpienia zarządzanego i ma wstępnie skonfigurowane na tabelę tras, podczas gdy inne podsieci o nazwie **domyślne** służy do innych zasobów, które powinien uzyskiwać dostęp do zarządzanego Wystąpienie (na przykład maszyny wirtualne platformy Azure). Możesz usunąć **domyślne** podsieci, o ile nie są potrzebne.

3. Konfigurowanie środowiska sieciowego. W następującej postaci można skonfigurować parametrów środowiska sieciowego:

![Skonfiguruj sieć platformy azure](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

Może zmieniać nazwy sieci wirtualnej i podsieci i dostosować zakresów adresów IP skojarzonych z zasobami w sieci. Po naciśnięciu przycisku "Kup", ta forma będzie tworzyć i konfigurować środowiska. Jeśli dwie podsieci nie są potrzebne, można usunąć domyślny. 

## <a name="create-a-new-virtual-network-for-managed-instances-using-portal"></a>Utwórz nową sieć wirtualną dla wystąpienia zarządzane przy użyciu portalu

Tworzenie sieci wirtualnej platformy Azure jest wymaganiem wstępnym dla tworzenia wystąpienia zarządzanego. Można użyć witryny Azure portal [PowerShell](../virtual-network/quick-create-powershell.md), lub [wiersza polecenia platformy Azure](../virtual-network/quick-create-cli.md). W poniższej sekcji pokazano kroki w witrynie Azure portal. Szczegóły omówionych w tym miejscu Zastosuj do każdego z tych metod.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj i kliknij opcję **Sieć wirtualna**, upewnij się, że wybrano opcję **Resource Manager** jako tryb wdrożenia, a następnie kliknij opcję **Utwórz**.

   ![tworzenie sieci wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Wypełnij formularz sieci wirtualnej, podając wymagane informacje, w sposób, podobnie jak na poniższym zrzucie ekranu:

   ![formularz tworzenia sieci wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Kliknij pozycję **Utwórz**.

   Przestrzeni adresowej i podsieci są określone w notacji CIDR. 

   > [!IMPORTANT]
   > Wartości domyślne Utwórz podsieć, który pobiera wszystkie przestrzeni adresowej sieci wirtualnej. Jeśli ta opcja jest wybrana, nie można utworzyć żadnych innych zasobów w sieci wirtualnej niż wystąpienia zarządzanego. 

   Zalecanym podejściem będzie następująca: 
   - Oblicz rozmiar podsieci, postępując zgodnie z [określi rozmiar podsieci dla wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances) sekcji  
   - Ocena potrzeb dotyczących w pozostałej części sieci wirtualnej 
   - W związku z tym Wypełnij zakresów adresów sieci wirtualnej i podsieci 

   Upewnij się, że punkty końcowe usługi opcji pozostaje **wyłączone**. 

   ![formularz tworzenia sieci wirtualnej](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

### <a name="create-the-required-route-table-and-associate-it"></a>Tworzenie tabeli tras wymagane i skojarz go

1. Logowanie się do witryny Azure Portal  
2. Zlokalizuj i kliknij opcję **Tabela tras**, a następnie kliknij opcję **Utwórz** na stronie tabeli tras.

   ![formularz tworzenia tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Utwórz trasy internetowej następnego przeskoku 0.0.0.0/0, w sposób, np. poniższych zrzutach ekranu:

   ![dodawanie tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![trasa](./media/sql-database-managed-instance-tutorial/route.png)

4. Skojarz tę trasę dla podsieci dla wystąpienia zarządzanego w sposób, np. poniższych zrzutach ekranu:

    ![podsieć](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![ustawianie tabeli tras](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![zapisywanie ustawionej tabeli tras](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Po utworzeniu sieci wirtualnej, możesz przystąpić do tworzenia wystąpienia zarządzanego.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modyfikuj istniejącą sieć wirtualną dla wystąpienia zarządzanego 

Pytania i odpowiedzi w tej sekcji pokazano, jak dodać wystąpienia zarządzanego do istniejącej sieci wirtualnej. 

**Czy używasz modelu wdrażania klasycznego lub usługi Resource Manager dla istniejącej sieci wirtualnej?** 

Wystąpienie zarządzane można utworzyć tylko w sieci wirtualnej usługi Resource Manager. 

**Czy chcesz utworzyć nową podsieć dla wystąpienia zarządzanego SQL lub użyć istniejącego?**

Jeśli chcesz utworzyć nowy: 

- Oblicz rozmiar podsieci, postępując zgodnie z wytycznymi podanymi w [określi rozmiar podsieci wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances) sekcji.
- Wykonaj czynności opisane w [Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md). 
- Utwórz tabelę tras, która zawiera pojedynczy wpis **0.0.0.0/0**, jak następnego przeskoku Internet i skojarzyć ją z podsiecią dla wystąpienia zarządzanego.  

W przypadku, gdy chcesz utworzyć wystąpienie zarządzane wewnątrz istniejącej podsieci: 
- Sprawdź, jeśli podsieć jest pusty — wystąpienia zarządzanego nie można utworzyć w podsieci, która zawiera inne zasoby, w tym podsieć bramy 
- Oblicz rozmiar podsieci, postępując zgodnie z wytycznymi podanymi w [określi rozmiar podsieci wystąpienia zarządzanego](#determine-the-size-of-subnet-for-managed-instances) sekcji i sprawdź, czy ma odpowiedni rozmiar. 
- Sprawdź, czy punkty końcowe usługi nie są włączone w tej podsieci.
- Upewnij się, że nie istnieją żadne grupy zabezpieczeń sieci skojarzoną z podsiecią 

**Czy masz niestandardowego serwera DNS skonfigurowanego?** 

Jeśli tak, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md). 

- Tworzenie tabeli tras wymagane i skojarz go: zobacz [Tworzenie tabeli tras wymagane i skojarz go](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby uzyskać samouczek, w którym pokazano, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-create-tutorial-portal.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md)
