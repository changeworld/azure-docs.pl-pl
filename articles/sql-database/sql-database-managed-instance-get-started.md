---
title: 'Azure Portal: Tworzenie wystąpienia zarządzanego SQL Database | Microsoft Docs'
description: Utwórz SQL Database wystąpienie zarządzane, środowisko sieciowe i maszynę wirtualną klienta, aby uzyskać dostęp.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 5f37ca8e22b07e39eda87e11f52358e1d1497c60
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178513"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Szybki start: Tworzenie wystąpienia zarządzanego usługi Azure SQL Database

Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia Azure SQL Database [wystąpienia zarządzanego](sql-database-managed-instance.md) w Azure Portal.

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki przedstawiają sposób tworzenia wystąpienia zarządzanego.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma usługi Azure SQL, wybierz pozycję **wszystkie usługi**, a następnie wpisz *SQL Azure* w polu wyszukiwania.
2. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Można wyświetlić dodatkowe informacje o Azure SQL Database wystąpieniu zarządzanym, wybierając pozycję **Pokaż szczegóły** na kafelku **wystąpienia zarządzane** .
3. Wybierz pozycję **Utwórz**:

   ![Tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Wypełnij formularz **tworzenie SQL Database tworzenia wystąpienia zarządzanego** , wypełniając informacje wymagane na karcie podstawowa. Skonfiguruj dodatkowe ustawienia przy użyciu sieci i ustawień zaawansowanych.

### <a name="basic-tab"></a>Karta podstawowa

Wypełnij obowiązkowe informacje wymagane na karcie **podstawowe** , korzystając z poniższej tabeli. Jest to minimalny zestaw informacji umożliwiających zainicjowanie wystąpienia zarządzanego.

   ![Utwórz kartę podstawową dla wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Subskrypcja** | Twoja subskrypcja. | Subskrypcja, która zapewnia uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** | nowa lub istniejąca grupa zasobów.|Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa).|
   | **Nazwa wystąpienia zarządzanego** | Dowolna prawidłowa nazwa.|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Region** |Region, w którym chcesz utworzyć wystąpienie zarządzane.|Aby uzyskać informacje na temat regionów, zobacz [Regiony systemu Azure](https://azure.microsoft.com/regions/).|
| **Identyfikator logowania administratora wystąpienia zarządzanego** | Dowolna prawidłowa nazwa użytkownika. | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nie należy używać elementu "serveradmin", ponieważ jest to zastrzeżona rola na poziomie serwera.|
   | **Hasło** | Dowolne prawidłowe hasło.| Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Wybierz pozycję **Konfiguruj wystąpienie zarządzane** , aby zmienić rozmiar zasobów obliczeniowych i magazynu oraz przejrzeć opcje warstwy cenowej.
- Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych.
- Po zakończeniu wybierz pozycję **Zastosuj** , aby zapisać swój wybór. 

  ![Formularz wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

Możesz kliknąć pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje przed utworzeniem wystąpienia zarządzanego, lub skonfigurować sieć, klikając przycisk **dalej: Sieć**.

### <a name="networking-tab"></a>Karta Sieć

Wypełnij informacje opcjonalne na karcie Sieć, korzystając z poniższej tabeli. W przypadku pominięcia tych informacji zostaną zastosowane ustawienia domyślne.

   ![Karta Tworzenie sieci wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sieć wirtualna** | Wybierz opcję **Utwórz nową sieć wirtualną** lub prawidłową sieć wirtualną i podsieć.| Jeśli sieć lub podsieć jest niedostępna, należy ją [zmodyfikować, aby spełniała wymagania sieciowe](sql-database-managed-instance-configure-vnet-subnet.md) przed wybraniem jej jako docelowej dla nowego wystąpienia zarządzanego. Aby uzyskać informacje o wymaganiach dotyczących konfigurowania środowiska sieciowego dla wystąpienia zarządzanego, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md). |
   | **Typ połączenia** | Wybierz między serwerem proxy a typem połączenia przekierowania.|Aby uzyskać więcej informacji na temat typów połączeń, zobacz [Azure SQL Database Policy Connection](sql-database-connectivity-architecture.md#connection-policy).|
   | **Publiczny punkt końcowy**  | Wybierz tę opcję, aby włączyć publiczny punkt końcowy | Aby można było uzyskać dostęp do zarządzanego wystąpienia za pomocą publicznego punktu końcowego **danych, należy wybrać opcję publiczny** punkt końcowy. | 
   | **Zezwalaj na dostęp z** (w przypadku gdy publiczny punkt końcowy jest ustawiony na wartość włączone) | Wybierz jedną z opcji: <ul> <li>**Usługi platformy Azure**</li> <li>**Internet**</li> <li>**Brak dostępu**</li></ul>   |Środowisko portalu umożliwia skonfigurowanie grupy zabezpieczeń z publicznym punktem końcowym. </br> </br> Na podstawie Twojego scenariusza wybierz jedną z następujących opcji: </br> <ul> <li>Usługi platformy Azure — zalecane w przypadku nawiązywania połączenia z poziomu usługi Power BI lub innej dzierżawy. </li> <li> Używanie Internetu do celów testowych, gdy chcesz szybko utworzyć wystąpienie zarządzane. Nie jest to zalecane do użycia w środowiskach produkcyjnych. </li> <li> Brak dostępu — ta opcja tworzy regułę odmowy zabezpieczeń. Należy zmodyfikować tę regułę, aby umożliwić dostęp do wystąpienia zarządzanego za poorednictwem publicznego punktu końcowego. </li> </ul> </br> Aby uzyskać więcej informacji o zabezpieczeniach publicznego punktu końcowego, zobacz [bezpieczne używanie Azure SQL Database wystąpienia zarządzanego z publicznym punktem końcowym](sql-database-managed-instance-public-endpoint-securely.md).|

Możesz wybrać pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje przed utworzeniem wystąpienia zarządzanego, lub skonfigurować dodatkowe ustawienia, klikając przycisk **dalej: Ustawienia**dodatkowe.

### <a name="additional-settings-tab"></a>Karta Ustawienia dodatkowe

Wypełnij opcjonalne informacje na karcie **Ustawienia dodatkowe** , korzystając z poniższej tabeli. W przypadku pominięcia tych informacji zostaną zastosowane ustawienia domyślne.

   ![Karta Tworzenie dodatkowych ustawień wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sortowanie** | Sortowanie, które ma być używane dla wystąpienia zarządzanego.|W przypadku migrowania baz danych z SQL Server sprawdź sortowanie źródłowe za `SELECT SERVERPROPERTY(N'Collation')` pomocą i Użyj tej wartości. Aby uzyskać informacje o sortowaniu, zobacz [Ustawianie lub zmienianie sortowania serwera](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Strefa czasowa** | Strefa czasowa, która ma być zaobserwowana przez wystąpienie zarządzane.|Aby uzyskać więcej informacji, zobacz [strefy czasowe](sql-database-managed-instance-timezone.md).|
   | **Użyj jako dodatkowego trybu failover** | Aby użyć wystąpienia zarządzanego jako pomocniczej grupy trybu failover wystąpienia, wybierz pozycję tak. |
   | **Podstawowe wystąpienie zarządzane** (w przypadku użycia jako pomocniczy tryb failover ma ustawioną wartość tak) | Wybierz podstawowe wystąpienie zarządzane (musi już istnieć), z którym tworzone wystąpienie zarządzane zostanie dołączone w tej samej strefie DNS. Jest to krok wstępny służący do włączania tworzenia konfiguracji grupy trybu failover. Aby uzyskać szczegółowe [informacje, zobacz Samouczek: Dodaj wystąpienie zarządzane SQL Database do grupy](sql-database-managed-instance-failover-group-tutorial.md)trybu failover. |

### <a name="review--create-tab"></a>Przejrzyj i Utwórz kartę

1. Wybierz kartę **Recenzja + tworzenie** , aby przejrzeć wybrane opcje przed utworzeniem wystąpienia zarządzanego.

   ![Przegląd i tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

1. Wybierz pozycję **Utwórz** , aby rozpocząć Inicjowanie obsługi administracyjnej wystąpienia zarządzanego.

> [!IMPORTANT]
> Wdrożenie wystąpienia zarządzanego jest długotrwałą operacją. Wdrożenie pierwszego wystąpienia w podsieci zwykle trwa znacznie dłużej niż wdrażanie w podsieci z istniejącymi wystąpieniami zarządzanymi. Aby uzyskać średni czas udostępniania, zobacz [operacje zarządzania wystąpieniami zarządzanymi](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Monitorowanie postępu wdrażania

1. Wybierz ikonę **powiadomienia** , aby wyświetlić stan wdrożenia.

    ![Postęp wdrażania wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

1. Wybierz pozycję **wdrożenie w toku** dla powiadomienia, aby otworzyć okno wystąpienia zarządzane, aby dodatkowo monitorować postęp wdrażania. 

> [!TIP]
> W przypadku zamknięcia przeglądarki sieci Web lub przechodzenia poza ekran postępu wdrażania wykonaj następujące kroki, aby zlokalizować ekran postępu wdrażania:
> 1. W Azure Portal Otwórz grupę zasobów (wprowadzoną na karcie **podstawowa** ), w której wdrażasz wystąpienie zarządzane.
> 2. Wybierz pozycję **wdrożenia**.
> 3. Wybierz operację wdrażania wystąpienia zarządzanego w toku.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Przeglądanie zasobów i pobieranie w pełni kwalifikowanej nazwy serwera

Po pomyślnym wdrożeniu zapoznaj się z utworzonymi zasobami i Pobierz w pełni kwalifikowaną nazwę serwera do użycia w nowszych przewodnikach Szybki Start.

1. Otwórz grupę zasobów dla wystąpienia zarządzanego. Wyświetl zasoby, które zostały utworzone dla Ciebie, w oknie [Tworzenie wystąpienia zarządzanego](#create-a-managed-instance) — Szybki Start.

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/resources.png)

2. Wybierz tabelę tras, aby przejrzeć utworzoną przez użytkownika tabelę tras (UDR).

   ![Tabela tras](./media/sql-database-managed-instance-get-started/route-table.png)

3. W tabeli tras zapoznaj się z wpisami na potrzeby kierowania ruchu z i do sieci wirtualnej wystąpienia zarządzanego. W przypadku ręcznego tworzenia lub konfigurowania tabeli tras należy koniecznie utworzyć te wpisy w tabeli tras.

   ![Wpis dla podsieci wystąpienia zarządzanego do lokalnego](./media/sql-database-managed-instance-get-started/udr.png)

4. Wróć do grupy zasobów i wybierz grupę zabezpieczeń sieci, aby przejrzeć reguły zabezpieczeń.

   ![Sieciowa grupa zabezpieczeń](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Zapoznaj się z zasadami zabezpieczeń dla ruchu przychodzącego i wychodzącego. Jeśli skonfigurowano publiczne punkty końcowe dla wystąpienia zarządzanego, zapoznaj się z artykułem [Konfigurowanie publicznego punktu końcowego](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) , aby uzyskać więcej informacji.

   ![Reguły zabezpieczeń](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Wróć do grupy zasobów i wybierz swoje wystąpienie zarządzane.

   ![Wystąpienie zarządzane](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na karcie **Przegląd** Znajdź właściwość **host** . Skopiuj w pełni kwalifikowany adres hosta dla wystąpienia zarządzanego do użycia w następnym przewodniku Szybki Start.

   ![Nazwa hosta](./media/sql-database-managed-instance-get-started/host-name.png)

   Nazwa jest podobna do **nazwa_maszyny.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak nawiązać połączenie z wystąpieniem zarządzanym:
  - Aby zapoznać się z omówieniem opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
  - Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak nawiązać połączenie z wystąpieniem zarządzanym z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia z maszyną wirtualną platformy Azure](sql-database-managed-instance-configure-vm.md).
  - Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia punkt-lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby przywrócić istniejącą bazę danych SQL Server z lokalnego do wystąpienia zarządzanego: 
    - Aby przeprowadzić migrację z pliku kopii zapasowej bazy danych, użyj [Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) do przetworzenia. 
    - Użyj [polecenia Przywróć T-SQL](sql-database-managed-instance-get-started-restore.md) , aby przywrócić plik kopii zapasowej bazy danych.
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przy użyciu wbudowanej analizy rozwiązywania problemów, zobacz [monitorowanie Azure SQL Database przy użyciu Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
