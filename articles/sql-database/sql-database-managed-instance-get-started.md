---
title: 'Witryna Azure portal: tworzenie wystąpienia zarządzanego'
description: Utwórz wystąpienie zarządzane bazy danych SQL, środowisko sieciowe i maszynę wirtualną klienta w celu uzyskania dostępu.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 25128442cd922f6b9130586e245695b6880f661c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257618"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Szybki start: tworzenie wystąpienia zarządzanego usługi Azure SQL Database

Ten przewodnik Szybki start przeprowadzi Cię przez sposób tworzenia [wystąpienia zarządzanego](sql-database-managed-instance.md) usługi Azure SQL Database w witrynie Azure portal.

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki pokazują, jak utworzyć wystąpienie zarządzane:

1. Wybierz **sql platformy Azure** w lewym menu witryny Azure portal. Jeśli **usługi Azure SQL** nie ma na liście, wybierz pozycję Wszystkie **usługi**, a następnie wprowadź usługę **Azure SQL** w polu wyszukiwania.
2. Wybierz **+Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Można wyświetlić dodatkowe informacje o wystąpieniu zarządzanym usługi Azure SQL Database, wybierając **pozycję Pokaż szczegóły** na kafelku Wystąpienia **zarządzane.**
3. Wybierz **pozycję Utwórz**.

   ![Tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Użyj kart w formularzu **inicjowania** obsługi administracyjnej wystąpienia zarządzanego usługi Azure SQL Database, aby dodać wymagane i opcjonalne informacje. W poniższych sekcjach opisano te karty.

### <a name="basics"></a>Podstawy

- Wypełnij obowiązkowe informacje wymagane na karcie **Podstawy.** Jest to minimalny zestaw informacji wymaganych do aprowizowania wystąpienia zarządzanego.

   ![Zakładka "Podstawy" do tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Użyj poniższej tabeli jako informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Subskrypcja** | Twoja subskrypcja. | Subskrypcja, która daje uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** | nowa lub istniejąca grupa zasobów.|Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa).|
   | **Nazwa wystąpienia zarządzanego** | Każda prawidłowa nazwa.|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   | **Region** |Region, w którym chcesz utworzyć wystąpienie zarządzane.|Aby uzyskać informacje o regionach, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/).|
   | **Identyfikator logowania administratora wystąpienia zarządzanego** | Każda prawidłowa nazwa użytkownika. | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming). Nie używaj "serveradmin", ponieważ jest to rola na poziomie serwera zastrzeżonego.|
   | **Hasło** | Wszelkie prawidłowe hasło.| Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Wybierz **pozycję Konfiguruj wystąpienie zarządzane,** aby uzyskać rozmiar zasobów obliczeniowych i magazynowych oraz przejrzeć warstwy cenowe. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych. Po zakończeniu wybierz pozycję **Zastosuj,** aby zapisać zaznaczenie. 

   ![Formularz wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Aby przejrzeć wybory przed utworzeniem wystąpienia zarządzanego, można wybrać **opcję Przejrzyj + utwórz**. Możesz też skonfigurować opcje sieci, wybierając **pozycję Dalej: Sieć**.

### <a name="networking"></a>Obsługa sieci

- Wypełnij opcjonalne informacje na karcie **Sieć.** Jeśli te informacje zostaną pominięte, portal zastosuje ustawienia domyślne.

   ![Zakładka "Sieć" do tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Użyj poniższej tabeli jako informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sieć wirtualna** | Wybierz opcję **Utwórz nową sieć wirtualną** lub prawidłową sieć wirtualną i podsieć.| Jeśli sieć lub podsieć jest niedostępna, musi zostać zmodyfikowana w [celu spełnienia wymagań sieciowych](sql-database-managed-instance-configure-vnet-subnet.md) przed wybraniem jej jako obiektu docelowego dla nowego wystąpienia zarządzanego. Aby uzyskać informacje na temat wymagań dotyczących konfigurowania środowiska sieciowego dla wystąpienia zarządzanego, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md). |
   | **Typ połączenia** | Wybierz między serwerem proxy a typem połączenia przekierowania.|Aby uzyskać więcej informacji na temat typów połączeń, zobacz [zasady połączenia usługi Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
   | **Publiczny punkt końcowy**  | Wybierz pozycję **Włącz**. | Aby wystąpienie zarządzane było dostępne za pośrednictwem publicznego punktu końcowego danych, należy włączyć tę opcję. | 
   | **Zezwalaj na dostęp z** (jeśli **publiczny punkt końcowy** jest włączony) | Wybierz jedną z opcji.   |Środowisko portalu umożliwia konfigurowanie grupy zabezpieczeń z publicznym punktem końcowym. </br> </br> Na podstawie scenariusza wybierz jedną z następujących opcji: </br> <ul> <li>**Usługi platformy Azure:** Zaleca się tę opcję podczas nawiązywania połączenia z usługi Power BI lub innej usługi wielodostępnej. </li> <li> **Internet:** Służy do celów testowych, gdy chcesz szybko rozkręcić wystąpienie zarządzane. Nie zaleca się go dla środowisk produkcyjnych. </li> <li> **Brak dostępu:** Ta opcja tworzy regułę zabezpieczeń **Odmów.** Zmodyfikuj tę regułę, aby wystąpienie zarządzane było dostępne za pośrednictwem publicznego punktu końcowego. </li> </ul> </br> Aby uzyskać więcej informacji na temat zabezpieczeń publicznych punktów końcowych, zobacz [Bezpieczne korzystanie z wystąpienia zarządzanego usługi Azure SQL Database z publicznym punktem końcowym.](sql-database-managed-instance-public-endpoint-securely.md)|

- Wybierz **opcję Przejrzyj + utwórz,** aby przejrzeć swoje wybory przed utworzeniem wystąpienia zarządzanego. Możesz też skonfigurować więcej ustawień niestandardowych, wybierając **pozycję Dalej: Dodatkowe ustawienia**.

### <a name="additional-settings"></a>Dodatkowe ustawienia

- Wypełnij opcjonalne informacje na karcie **Ustawienia dodatkowe.** Jeśli te informacje zostaną pominięte, portal zastosuje ustawienia domyślne.

   ![Zakładka "Dodatkowe ustawienia" do tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Użyj poniższej tabeli jako informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sortowanie** | Wybierz sortowanie, którego chcesz użyć dla wystąpienia zarządzanego. Jeśli przeprowadzasz migrację baz danych z programu SQL `SELECT SERVERPROPERTY(N'Collation')` Server, sprawdź sortowanie źródłowe przy użyciu tej wartości i użyj jej.| Aby uzyskać informacje o sortowaniach, zobacz [Ustawianie lub zmienianie sortowania serwera](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Strefa czasowa** | Wybierz strefę czasową, którą będzie obserwować zarządzane wystąpienie.|Aby uzyskać więcej informacji, zobacz [Strefy czasowe](sql-database-managed-instance-timezone.md).|
   | **Użyj jako trybu pomocniczego trybu failover** | Wybierz **pozycję Tak**. | Włącz tę opcję, aby użyć wystąpienia zarządzanego jako pomocniczej grupy trybu failover.|
   | **Podstawowe wystąpienie zarządzane** (jeśli **użyj jako pomocniczego trybu failover** jest ustawiona na **Tak)** | Wybierz istniejące podstawowe wystąpienie zarządzane, które zostanie połączone w tej samej strefie DNS z wywoływanym wystąpieniem zarządzanym. | Ten krok umożliwi konfigurację po utworzeniu grupy trybu failover. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie wystąpienia zarządzanego bazy danych SQL do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Recenzja + tworzenie

5. Wybierz **pozycję Przejrzyj + utwórz** kartę, aby przejrzeć wybory przed utworzeniem wystąpienia zarządzanego.

   ![Karta do przeglądania i tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Wybierz **pozycję Utwórz,** aby rozpocząć inicjowanie obsługi administracyjnej wystąpienia zarządzanego.

> [!IMPORTANT]
> Wdrażanie wystąpienia zarządzanego jest długotrwałą operacją. Wdrożenie pierwszego wystąpienia w podsieci zwykle trwa znacznie dłużej niż wdrażanie w podsieci z istniejącymi wystąpieniami zarządzanymi. Aby uzyskać średni czas inicjowania obsługi administracyjnej, zobacz [Operacje zarządzania wystąpieniami zarządzanymi](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Monitorowanie postępu wdrażania

7. Wybierz ikonę **Powiadomienia,** aby wyświetlić stan wdrożenia.

   ![Postęp wdrażania wdrożenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Wybierz **wdrożenie w toku** w powiadomieniu, aby otworzyć okno wystąpienia zarządzanego i dalej monitorować postęp wdrażania. 

> [!TIP]
> Jeśli przeglądarka internetowa została zamknięta lub odsunięto od ekranu postępu wdrażania, wykonaj następujące kroki, aby zlokalizować z powrotem ekran postępu wdrażania:
> 1. W witrynie Azure portal otwórz grupę zasobów (na karcie **Podstawy),** do której wdrażasz wystąpienie zarządzane.
> 2. Wybierz **opcję Wdrożenia**.
> 3. Wybierz operację wdrażania wystąpienia zarządzanego w toku.

> [!IMPORTANT]
> Aby uzyskać stan tworzenia wystąpienia zarządzanego, musisz mieć uprawnienia do **odczytu** w grupie zasobów. Jeśli nie masz tego uprawnienia lub odwołać go, gdy wystąpienie zarządzane jest w procesie tworzenia, może to spowodować, że wystąpienie zarządzane nie jest widoczne na liście wdrożeń grupy zasobów.
>

## <a name="post-deployment-operations"></a>Operacje po wdrożeniu

Aby przejrzeć utworzone zasoby, dostosuj ustawienia sieciowe i pobierz szczegóły połączenia hosta (FQDN) wykonaj kroki opisane w tej sekcji.

### <a name="view-resources-created"></a>Wyświetlanie utworzonych zasobów

Po pomyślnym wdrożeniu wystąpienia zarządzanego, aby wyświetlić utworzone zasoby:

1. Otwórz grupę zasobów dla wystąpienia zarządzanego. Służy do wyświetlania zasobów utworzonych dla Ciebie w przewodniku Szybki start [tworzenia wystąpienia zarządzanego.](#create-a-managed-instance)

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Wyświetlanie i dostosowywanie ustawień sieciowych

Aby opcjonalnie dostosować ustawienia sieci, sprawdź następujące kwestie:

1. Wybierz tabelę tras, aby przejrzeć trasę zdefiniowaną przez użytkownika (UDR), która została utworzona dla Ciebie.

   ![Tabela tras](./media/sql-database-managed-instance-get-started/route-table.png)

2. W tabeli trasy przejrzyj wpisy, aby kierować ruch z sieci wirtualnej i w sieci wirtualnej wystąpienia zarządzanego. Jeśli tabela marszruty zostanie utworzona lub skonfigurowana ręcznie, upewnij się, że zostaną utworzone te wpisy w tabeli trasy wystąpienia zarządzanego.

   ![Wpis dla podsieci wystąpienia zarządzanego do lokalnego](./media/sql-database-managed-instance-get-started/udr.png)

3. Wróć do grupy zasobów i wybierz sieciową grupę zabezpieczeń.

   ![Sieciowa grupa zabezpieczeń](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Zapoznaj się z zasadami zabezpieczeń dla ruchu przychodzącego i wychodzącego. 

   ![Reguły zabezpieczeń](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Jeśli skonfigurowano publiczny punkt końcowy dla wystąpienia zarządzanego, należy otworzyć porty, aby umożliwić ruch sieciowy umożliwiający połączenia do wystąpienia zarządzanego z publicznego Internetu, zobacz [Konfigurowanie publicznego punktu końcowego dla wystąpienia zarządzanego,](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) aby uzyskać więcej informacji.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Pobieranie szczegółów połączenia do wystąpienia zarządzanego

Aby połączyć się z wystąpieniem zarządzanym, wykonaj następujące kroki, aby pobrać nazwę hosta i w pełni kwalifikowaną nazwę domeny (FQDN):

1. Wróć do grupy zasobów i wybierz swoje wystąpienie zarządzane.

   ![Wystąpienie zarządzane w grupie zasobów](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Na **karcie Przegląd** znajdź właściwość **Host.** Skopiuj nazwę hosta dla wystąpienia zarządzanego do użycia w następnym przewodniku Szybki start.

   ![Nazwa hosta](./media/sql-database-managed-instance-get-started/host-name.png)

   Skopiowana wartość reprezentuje w pełni kwalifikowaną nazwę domeny (FQDN), która może służyć do łączenia się z wystąpieniem zarządzanym. Jest podobny do następującego adresu: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak połączyć się z wystąpieniem zarządzanym:
- Aby uzyskać przegląd opcji połączeń dla aplikacji, zobacz [Łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby uzyskać przewodnik Szybki start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia maszyny wirtualnej platformy Azure.](sql-database-managed-instance-configure-vm.md)
- Aby uzyskać przewodnik Szybki start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia typu "punkt-lokacja".](sql-database-managed-instance-configure-p2s.md)

Aby przywrócić istniejącą bazę danych programu SQL Server z lokalnego do wystąpienia zarządzanego: 
- Użyj [usługi migracji bazy danych azure do migracji,](../dms/tutorial-sql-server-to-managed-instance.md) aby przywrócić z pliku kopii zapasowej bazy danych. 
- Użyj [polecenia T-SQL RESTORE,](sql-database-managed-instance-get-started-restore.md) aby przywrócić z pliku kopii zapasowej bazy danych.

Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpienia zarządzanego za pomocą wbudowanej analizy rozwiązywania problemów, zobacz [Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
