---
title: Baza danych Azure SQL zarządzane wystąpienia konfigurację sieci wirtualnej | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla sieci wirtualnej (VNet) z wystąpienia zarządzane bazy danych SQL Azure.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: bccc2d52c592101a23338e57791a992441e80047
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurowanie sieci wirtualnej dla zarządzanego wystąpienia bazy danych Azure SQL

Azure wystąpienia bazy danych SQL zarządzane (wersja zapoznawcza) musi być wdrażana w obrębie platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). To wdrożenie umożliwia następujące scenariusze: 
- Nawiązywanie połączenia z wystąpieniem zarządzane bezpośrednio tworzą sieci lokalnej 
- Nawiązywanie połączenia z wystąpieniem zarządzane połączonego serwera lub innego lokalnego magazynu danych 
- Wystąpienie usługi zarządzania nawiązywania połączenia z zasobami Azure  

## <a name="plan"></a>Planowanie

Zaplanuj sposób wdrażania wystąpienia programu zarządzane w sieci wirtualnej przy użyciu odpowiedzi na następujące pytania: 
- Czy firma planuje wdrażanie jednego lub wielu wystąpień zarządzane? 

  Liczba wystąpień zarządzane Określa minimalny rozmiar podsieci do przydzielenia dla swoich wystąpień zarządzane. Aby uzyskać więcej informacji, zobacz [określić rozmiar podsieci dla wystąpienia zarządzane](#create-a-new-virtual-network-for-managed-instances). 
- Musi wdrażać wystąpienia zarządzane w ramach istniejącej sieci wirtualnej, czy tworzysz nową sieć? 

   Jeśli planujesz użyć istniejącej sieci wirtualnej, należy zmodyfikować tej konfiguracji sieci, aby pomieścić wystąpienia zarządzane. Aby uzyskać więcej informacji, zobacz [modyfikowania istniejącej sieci wirtualnej dla wystąpienia zarządzane](#modify-an-existing-virtual-network-for-managed-instances). 

   Jeśli planujesz tworzenie nowej sieci wirtualnej, zobacz [Utwórz nową sieć wirtualną dla wystąpienia zarządzane](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Wymagania

Do utworzenia wystąpienia zarządzane muszą przeznaczyć podsieci w sieci wirtualnej, która spełnia następujące wymagania:
- **Być pusty**: podsieci nie może zawierać inne usługi chmury, skojarzony, a nie może być podsieć bramy. Nie można utworzyć wystąpienia zarządzanego w podsieci, która zawiera zasobów innych niż zarządzane wystąpienia lub dodać inne zasoby w tej podsieci później.
- **Nie grupy NSG**: podsieci nie może mieć skojarzone z nim grupa zabezpieczeń sieci.
- **Tabela określoną trasę**: podsieć musi mieć użytkownika trasy tabeli przez z 0.0.0.0/0 następnego przeskoku Internetem jako trasy tylko przypisane do niej. Aby uzyskać więcej informacji, zobacz [Tworzenie tabeli tras wymagane i kojarzenie go](#create-the-required-route-table-and-associate-it)
3. **Opcjonalne niestandardowe DNS**: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, adres IP platformy Azure cyklicznego rozpoznawania nazw (na przykład 168.63.129.16) należy dodać do listy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowe DNS](sql-database-managed-instance-custom-dns.md).
4. **Brak punktu końcowego usługi**: podsieci nie może mieć punktu końcowego (magazynu lub Sql) skojarzony. Upewnij się, że opcja punktów końcowych usługi jest wyłączona podczas tworzenia sieci wirtualnej.
5. **Wystarczającą liczbą adresów IP**: podsieć musi mieć co najmniej 16 adresów IP. Aby uzyskać więcej informacji, zobacz [określić rozmiar podsieci dla zarządzanych wystąpień](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienie zarządzane, jeśli podsieci docelowej nie jest zgodny z wszystkich powyższych wymagań. Docelowy sieci wirtualnej i podsieci muszą być przechowywane zgodnie z wymaganiami te wystąpienia zarządzane (przed i po wdrożeniu), jak naruszenia może spowodować wystąpienie przejściu w stan uszkodzony i stać się niedostępne. Odzyskiwanie ze stanu wymaga do utworzenia nowego wystąpienia w sieci wirtualnej z zasadami zgodności sieci, ponownie danych na poziomie wystąpienia i przywracanie baz danych. Powstaje znaczne przestoju dla aplikacji.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Określ rozmiar podsieci dla zarządzanych wystąpień

Podczas tworzenia wystąpienia zarządzane Azure przydziela liczbę maszyn wirtualnych, w zależności od wielkości warstwy, którą wybierzesz podczas inicjowania obsługi. Ponieważ te maszyny wirtualne są skojarzone z podsieci, wymagają one adresów IP. Aby zapewnić wysoką dostępność w regularnych operacjach i obsłudze usługi, Azure mogą przydzielić dodatkowe maszyny wirtualne. W związku z tym liczby wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci. 

Zgodnie z projektem wystąpienie usługi zarządzania wymaga co najmniej 16 adresów IP w podsieci i może używać maksymalnie 256 adresów IP. W związku z tym można użyć maski podsieci /28 na prefiksie/24, definiując zakresy IP podsieci. 

Jeśli planujesz wdrożyć wiele wystąpień zarządzany wewnątrz podsieci i do zoptymalizowania na rozmiar podsieci, użyj tych parametrów do utworzenia obliczenia: 

- Platforma Azure korzysta pięć adresów IP w podsieci do własnych potrzeb 
- Każde wystąpienie ogólnego przeznaczenia musi dwa adresy 

**Przykład**: ma osiem zarządzanych wystąpień. Czy potrzebujesz 5 + 8 * 2 = 21 oznacza, że adresy IP. Określone zakresy adresów IP w potęgą liczby 2 należy zakres adresów IP 32 (2 ^ 5) adresów IP. W związku z tym należy zarezerwować podsieci o /27 maski podsieci. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Utwórz nową sieć wirtualną dla zarządzanych wystąpień 

Tworzenie sieci wirtualnej platformy Azure jest wymagane w przypadku utworzenia wystąpienia usługi zarządzania. Mogą korzystać z portalu Azure, [PowerShell](../virtual-network/quick-create-powershell.md), lub [interfejsu wiersza polecenia Azure](../virtual-network/quick-create-cli.md). Poniższa sekcja przedstawia kroki przy użyciu portalu Azure. Szczegóły omówione w tym miejscu mają zastosowanie do każdej z tych metod.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj i kliknij opcję **Sieć wirtualna**, upewnij się, że wybrano opcję **Resource Manager** jako tryb wdrożenia, a następnie kliknij opcję **Utwórz**.

   ![tworzenie sieci wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Wypełnij formularz sieci wirtualnej z żądane informacje w sposób podobny do następującego zrzutu ekranu:

   ![formularz tworzenia sieci wirtualnej](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Kliknij przycisk **Utwórz**.

   Przestrzeni adresowej i podsieci są określone w notacji CIDR. 

   > [!IMPORTANT]
   > Wartości domyślne, Utwórz podsieć, która przyjmuje całą przestrzeń adresową sieci wirtualnej. Jeśli wybierzesz tę opcję, nie można utworzyć żadnych innych zasobów w sieci wirtualnej innych niż zarządzane wystąpienia. 

   Zalecanym podejściem jest następujące: 
   - Oblicz rozmiar podsieci, wykonując [określić rozmiar podsieci dla wystąpienia zarządzane](#determine-the-size-of-subnet-for-managed-instances) sekcji  
   - Ocena potrzeb w pozostałej części sieci wirtualnej 
   - W związku z tym Wypełnij zakresów adresów sieci wirtualnej i podsieci 

   Upewnij się, że punkty końcowe usługi opcję pozostaje **wyłączone**. 

   ![formularz tworzenia sieci wirtualnej](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Utwórz tabelę wymagane trasy i powiązać ją

1. Logowanie się do witryny Azure Portal  
2. Zlokalizuj i kliknij opcję **Tabela tras**, a następnie kliknij opcję **Utwórz** na stronie tabeli tras.

   ![formularz tworzenia tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Utwórz trasę następnego przeskoku Internet 0.0.0.0/0 w sposób, jak poniższe zrzuty ekranu:

   ![dodawanie tabeli tras](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![trasa](./media/sql-database-managed-instance-tutorial/route.png)

4. Skojarz tej trasy dla podsieci dla wystąpienia zarządzane w sposób, jak poniższe zrzuty ekranu:

    ![podsieć](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![ustawianie tabeli tras](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![zapisywanie ustawionej tabeli tras](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Po utworzeniu sieci wirtualnej, możesz przystąpić do tworzenia wystąpienia zarządzane.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modyfikowanie istniejącej sieci wirtualnej dla zarządzanych wystąpień 

Pytania i odpowiedzi w tej sekcji opisano, jak dodać zarządzane wystąpienie do istniejącej sieci wirtualnej. 

**Jest używany model wdrażania klasycznego lub Menedżera zasobów dla istniejącej sieci wirtualnej?** 

Wystąpienie usługi zarządzania można tworzyć tylko w sieciach wirtualnych Menedżera zasobów. 

**Czy chcesz utworzyć nową podsieć dla wystąpienia SQL zarządzane lub użyć istniejącego?**

Jeśli chcesz utworzyć nowe: 

- Oblicz rozmiar podsieci przez zgodnie z wytycznymi w [określić rozmiar podsieci dla zarządzanych wystąpień](#determine-the-size-of-subnet-for-managed-instances) sekcji.
- Wykonaj czynności opisane w [Dodawanie, zmienianie lub usuwanie podsieć sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md). 
- Utwórz tabelę tras, która zawiera pojedynczy wpis **0.0.0.0/0**, zgodnie z następnego przeskoku Internet i skojarzyć go z podsiecią dla wystąpienia zarządzane.  

W przypadku, gdy chcesz utworzyć wystąpienia zarządzanego wewnątrz istniejącą podsieć: 
- Sprawdź, czy podsieć jest pusty — w podsieci, która zawiera inne zasoby, łącznie z podsieci bramy nie można utworzyć wystąpienia zarządzane 
- Oblicz rozmiar podsieci przez zgodnie z wytycznymi w [określić rozmiar podsieci dla zarządzanych wystąpień](#determine-the-size-of-subnet-for-managed-instances) sekcji i sprawdź, czy jest odpowiednio wielkości. 
- Sprawdź, czy punktów końcowych usługi nie są włączone w tej podsieci.
- Upewnij się, że nie ma żadnych grup zabezpieczeń sieci skojarzonych z podsiecią 

**Czy organizacja ma niestandardowy serwer DNS skonfigurowany?** 

Jeśli tak, zobacz [Konfigurowanie niestandardowe DNS](sql-database-managed-instance-custom-dns.md). 

- Utwórz tabelę wymagane trasy i powiązać ją: zobacz [Tworzenie tabeli tras wymagane i kojarzenie go](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne informacje, zobacz [co to jest wystąpieniem zarządzane](sql-database-managed-instance.md)
- Samouczek przedstawiający sposób utworzyć sieć wirtualną, Utwórz wystąpienie zarządzane i przywracanie bazy danych z kopii zapasowej bazy danych, zobacz [tworzenia wystąpienia zarządzane bazy danych SQL Azure](sql-database-managed-instance-create-tutorial-portal.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowe DNS](sql-database-managed-instance-custom-dns.md)
