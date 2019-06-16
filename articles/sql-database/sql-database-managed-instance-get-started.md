---
title: 'Witryna Azure Portal: Tworzenie wystąpienia zarządzanego SQL Database | Dokumentacja firmy Microsoft'
description: Utwórz wystąpienie zarządzane bazy danych SQL, środowiska sieciowego i uzyskać dostęp do maszyny Wirtualnej klienta.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 27439758c9e2b909bb7179b69c7aade81429f502
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070492"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Szybki start: Tworzenie wystąpienia zarządzanego usługi Azure SQL Database

Ten przewodnik Szybki Start przeprowadzi Cię przez tworzenie usługi Azure SQL Database [wystąpienia zarządzanego](sql-database-managed-instance.md) w witrynie Azure portal.

> [!IMPORTANT]
> Aby uzyskać informacje o ograniczeniach, zobacz [obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki przedstawiają sposób tworzenia wystąpienia zarządzanego.

1. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. Znajdź **wystąpienia zarządzanego**, a następnie wybierz pozycję **wystąpienia zarządzanego Azure SQL**.
3. Wybierz pozycję **Utwórz**.

   ![Tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Wypełnij **wystąpienie zarządzane SQL** formularz, podając wymagane informacje, korzystając z informacji podanych w poniższej tabeli.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Subskrypcja** | Twoja subskrypcja. | Subskrypcja, która przyzna Ci odpowiednie uprawnienia do tworzenia nowych zasobów. |
   |**Nazwa wystąpienia zarządzanego**|Dowolna prawidłowa nazwa.|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Identyfikator logowania administratora wystąpienia zarządzanego**|Dowolną prawidłową nazwę użytkownika.|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nie należy używać "serveradmin", ponieważ jest to zastrzeżony roli poziomu serwera.|
   |**Hasło**|Dowolne prawidłowe hasło.|Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Strefa czasowa**|Strefa czasowa muszą spełniać Twoje wystąpienie zarządzane.|Aby uzyskać więcej informacji, zobacz [stref czasowych](sql-database-managed-instance-timezone.md).|
   |**Sortowanie**|Sortowanie, które chcesz użyć dla Twojego wystąpienia zarządzanego.|W przypadku migrowania baz danych z programu SQL Server, należy sprawdzić sortowania źródła za pomocą `SELECT SERVERPROPERTY(N'Collation')` i użyć tej wartości. Aby uzyskać informacje na temat sortowań, zobacz [Ustawianie lub zmienianie sortowania serwera](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Lokalizacja**|Lokalizacja, w której chcesz utworzyć wystąpienie zarządzane.|Aby uzyskać informacje na temat regionów, zobacz [Regiony systemu Azure](https://azure.microsoft.com/regions/).|
   |**Sieć wirtualna**|Wybierz opcję **Utwórz nową sieć wirtualną** lub prawidłową sieć wirtualną i podsieć.| Jeśli sieć lub podsieć jest niedostępny, musi on być [zmodyfikowana, aby spełnić wymagania dotyczące sieci](sql-database-managed-instance-configure-vnet-subnet.md) można wybrać jako miejsce docelowe dla nowego wystąpienia zarządzanego. Aby uzyskać informacje o wymaganiach dotyczących konfigurowania środowisko sieciowe do wystąpienia zarządzanego, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md). |
   |**Włączanie publicznego punktu końcowego**   |Zaznacz tę opcję, aby włączyć publiczny punkt końcowy   |Dla wystąpienia zarządzanego, dostępne za pośrednictwem punktu końcowego dane publiczne **włączyć publiczny punkt końcowy** musi zostać sprawdzony.| 
   |**Zezwalaj na dostęp z**   |Wybierz jedną z opcji: <ul> <li>**Usługi platformy Azure**</li> <li>**Internet**</li> <li>**Brak dostępu**</li></ul>   |Środowisko portalu umożliwia skonfigurowanie grupy zabezpieczeń z publicznym punktem końcowym. </br> </br> Oparte na danego scenariusza, wybierz jedną z następujących opcji: </br> <ul> <li>Usługi platformy Azure — zalecane w przypadku nawiązywania połączenia z usługi Power BI lub innej usługi wielodostępnej. </li> <li> Internet — używany do celów testowych, gdy użytkownik chce szybkie zwiększanie możliwości wystąpienia zarządzanego. Nie jest zalecane do użytku w środowiskach produkcyjnych. </li> <li> Brak dostępu — ta opcja tworzy regułę Odmów zabezpieczeń. Należy zmodyfikować tę regułę, aby udostępnić wystąpienia zarządzanego za pośrednictwem publicznego punktu końcowego. </li> </ul> </br> Aby uzyskać więcej informacji na temat bezpieczeństwa publicznego punktu końcowego, zobacz [za pomocą usługi Azure SQL Database managed wystąpienie bezpiecznie z publicznym punktem końcowym](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Typ połączenia**|Wybierz między serwera Proxy i typ połączenia przekierowania.|Aby uzyskać więcej informacji na temat typów połączeń, zobacz [zasady połączenia usługi Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
   |**Grupa zasobów**|nowa lub istniejąca grupa zasobów.|Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa).|

   ![Formularz wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Aby użyć wystąpienia zarządzanego jako pomocniczej grupy trybu failover wystąpienia, zaznacz pole wyboru i określ wystąpienie zarządzane DnsAzurePartner. Ta funkcja jest dostępna w wersji zapoznawczej i nie jest widoczne na następującym zrzucie ekranu.
6. Wybierz **warstwa cenowa** zasoby obliczeniowe i magazynowe rozmiar i przejrzyj opcje warstw cenowych. Wartość domyślna to warstwa cenowa Ogólnego przeznaczenia z 32 GB pamięci i 16 rdzeniami wirtualnymi.
7. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych.
8. Gdy skończysz, wybierz pozycję **Zastosuj** Aby zapisać wybrane opcje. 
9. Wybierz pozycję **Utwórz**, aby wdrożyć wystąpienie zarządzane.
10. Wybierz **powiadomienia** ikonę, aby wyświetlić stan wdrożenia.

    ![postęp wdrożenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Wybierz pozycję **Wdrażanie jest w toku**, aby otworzyć okno wystąpienia zarządzanego i dokładniej monitorować postęp wdrażania. 

> [!IMPORTANT]
> W przypadku pierwszego wystąpienia w podsieci czas wdrożenia jest zazwyczaj znacznie dłuższy niż w kolejnych wystąpieniach. Nie anuluj operacji wdrażania, ponieważ proces trwa dłużej niż oczekiwano.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Przeglądanie zasobów i pobieranie w pełni kwalifikowanej nazwy serwera

Po pomyślnym wdrożeniu Przejrzyj zasoby, które zostały utworzone i pobrać w pełni kwalifikowaną nazwę serwera do użycia w późniejszym przewodników Szybki Start.

1. Otwórz grupę zasobów dla Twojego wystąpienia zarządzanego. Wyświetl jej zasobów, które zostały utworzone dla Ciebie w [Tworzenie wystąpienia zarządzanego](#create-a-managed-instance) Szybki Start.

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/resources.png)

2. Wybierz tabelę tras, aby zapoznać się z tabeli tras zdefiniowanych przez użytkownika (UDR), która została utworzona automatycznie.

   ![Tabela tras](./media/sql-database-managed-instance-get-started/route-table.png)

3. W tabeli tras zapoznaj się z wpisami na potrzeby kierowania ruchu z i do sieci wirtualnej wystąpienia zarządzanego. Jeśli tworzysz lub ręcznie skonfigurować tabeli tras, należy koniecznie utworzyć te wpisy w tabeli tras.

   ![Wpis w podsieci wystąpienia zarządzanego do lokalnego](./media/sql-database-managed-instance-get-started/udr.png)

4. Wróć do tej grupy zasobów, a następnie wybierz sieciowej grupy zabezpieczeń, zapoznaj się z zasadami zabezpieczeń.

   ![Sieciowa grupa zabezpieczeń](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Zapoznaj się z zasadami zabezpieczeń dla ruchu przychodzącego i wychodzącego. Jeśli skonfigurowano publicznych punktów końcowych dla wystąpienia zarządzanego, zobacz artykuł [skonfigurować publiczny punkt końcowy](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) Aby uzyskać więcej informacji.

   ![Reguły zabezpieczeń](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Wróć do tej grupy zasobów, a następnie wybierz swoje wystąpienie zarządzane.

   ![Wystąpienie zarządzane](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na **Przegląd** kartę, odszukaj **hosta** właściwości. Skopiuj adres FQDN hosta dla wystąpienia zarządzanego do użycia w kolejnym przewodniku Szybki Start.

   ![Nazwa hosta](./media/sql-database-managed-instance-get-started/host-name.png)

   Nazwa jest podobna do **nazwa_maszyny.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o tym, jak połączyć się z wystąpienia zarządzanego:
  - Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpienia zarządzanego](sql-database-managed-instance-connect-app.md).
  - Aby uzyskać szybki Start, który pokazuje, jak nawiązać połączenie do wystąpienia zarządzanego z maszyną wirtualną platformy Azure, zobacz [Konfigurowanie połączenia maszyny wirtualnej platformy Azure](sql-database-managed-instance-configure-vm.md).
  - Aby uzyskać szybki Start, który pokazuje, jak połączyć się z wystąpienia zarządzanego z poziomu komputera klienckiego w środowisku lokalnym za pomocą połączenia punkt lokacja, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby przywrócić istniejącą bazę danych programu SQL Server ze środowiska lokalnego do wystąpienia zarządzanego: 
    - Użyj [usługi Azure Database Migration Service (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) do przywrócenia z pliku kopii zapasowej bazy danych. 
    - Użyj [polecenia języka T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) do przywrócenia z pliku kopii zapasowej bazy danych.
- Zaawansowane monitorowanie wydajności bazy danych wystąpienia zarządzanego dzięki wbudowanym funkcjom analizy dotyczące rozwiązywania problemów, zobacz [monitora usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
