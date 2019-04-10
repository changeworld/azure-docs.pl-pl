---
title: 'Azure Portal: Tworzenie wystąpienia zarządzanego SQL | Microsoft Docs'
description: Tworzenie wystąpienia zarządzanego SQL, środowiska sieciowego i udostępnionej maszyny wirtualnej klienta.
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
ms.date: 03/13/2019
ms.openlocfilehash: c6a6612390676de26f2f14e23b8040f983493970
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361382"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Szybki start: Tworzenie wystąpienia zarządzanego usługi Azure SQL Database

Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia [wystąpienia zarządzanego](sql-database-managed-instance.md) usługi Azure SQL Database w witrynie Azure Portal.

> [!IMPORTANT]
> Aby uzyskać informacje o ograniczeniach, zobacz [obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki przedstawiają sposób tworzenia wystąpienia zarządzanego.

1. W lewym górnym rogu okna witryny Azure Portal wybierz pozycję **Utwórz zasób**.
2. Zlokalizuj pozycję **Wystąpienie zarządzane**, a następnie wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL**.
3. Wybierz pozycję **Utwórz**.

   ![Tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Wypełnij formularz **wystąpienia zarządzanego SQL**, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w której masz uprawnienie do tworzenia nowych zasobów |
   |**Nazwa wystąpienia zarządzanego**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Identyfikator logowania administratora wystąpienia zarządzanego**|Dowolna prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nie używaj nazwy „serveradmin”, gdyż jest ona zarezerwowana dla roli poziomu serwera.|
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Sortowanie**|Sortowanie, którego chcesz użyć dla wystąpienia zarządzanego|W przypadku migrowania baz danych z programu SQL Server sprawdź sortowanie źródła przy użyciu elementu `SELECT SERVERPROPERTY(N'Collation')` i użyj tej wartości. Aby uzyskać informacje na temat sortowań, zobacz [Server-level collations (Sortowania na poziomie serwera)](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Lokalizacja**|Lokalizacja, w której chcesz utworzyć wystąpienie zarządzane|Aby uzyskać informacje na temat regionów, zobacz [Regiony systemu Azure](https://azure.microsoft.com/regions/).|
   |**Sieć wirtualna**|Wybierz opcję **Utwórz nową sieć wirtualną** lub prawidłową sieć wirtualną i podsieć.| Jeśli sieć/podsieć jest niedostępny jest musi być [zmodyfikowana, aby spełnić wymagania dotyczące sieci](sql-database-managed-instance-configure-vnet-subnet.md) można wybrać jako miejsce docelowe dla nowego wystąpienia zarządzanego. Aby uzyskać informacje o wymaganiach dotyczących konfigurowania środowiska sieci dla wystąpienia zarządzanego, zobacz [Configure a VNet for a managed instance (Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego)](sql-database-managed-instance-connectivity-architecture.md). |
   |**Grupa zasobów**|Nowa lub istniejąca grupa zasobów|Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa).|

   ![formularz wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Aby użyć wystąpienia zarządzanego jako pomocniczej grupy trybu failover wystąpienia, zaznacz pole wyboru i określ wystąpienie zarządzane DnsAzurePartner. Ta funkcja jest dostępna w wersji zapoznawczej i nie jest pokazana na towarzyszącym zrzucie ekranu.
6. Wybierz pozycję **Warstwa cenowa**, aby ustalić rozmiar zasobów obliczeniowych i magazynu, a także sprawdzić opcje warstw cenowych. Wartość domyślna to warstwa cenowa Ogólnego przeznaczenia z 32 GB pamięci i 16 rdzeniami wirtualnymi.
7. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych.
8. Po zakończeniu wybierz pozycję **Zastosuj**, aby zapisać wybór.  
9. Wybierz pozycję **Utwórz**, aby wdrożyć wystąpienie zarządzane.
10. Wybierz ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.

    ![postęp wdrożenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Wybierz pozycję **Wdrażanie jest w toku**, aby otworzyć okno wystąpienia zarządzanego i dokładniej monitorować postęp wdrażania.

> [!IMPORTANT]
> W przypadku pierwszego wystąpienia w podsieci czas wdrożenia jest zazwyczaj znacznie dłuższy niż w kolejnych wystąpieniach. Nie anuluj operacji wdrażania, ponieważ proces trwa dłużej niż oczekiwano. Utworzenie drugiego wystąpienia zarządzanego w podsieci trwa tylko kilka minut.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Przeglądanie zasobów i pobieranie w pełni kwalifikowanej nazwy serwera

Po pomyślnym ukończeniu wdrażania przejrzyj utworzone zasoby i pobierz w pełni kwalifikowaną nazwę serwera do użycia w kolejnych przewodnikach Szybki start.

1. Otwórz grupę zasobów wystąpienia zarządzanego i wyświetl jej zasoby utworzone dla Ciebie w przewodniku Szybki start [Tworzenie wystąpienia zarządzanego](#create-a-managed-instance).

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/resources.png)

2. Wybierz tabelę tras, aby zapoznać się z utworzoną na Twoje potrzeby tabelą tras zdefiniowanych przez użytkownika.

   ![Tabela tras](./media/sql-database-managed-instance-get-started/route-table.png)

3. W tabeli tras zapoznaj się z wpisami na potrzeby kierowania ruchu z i do sieci wirtualnej wystąpienia zarządzanego. Jeśli tworzysz lub konfigurujesz tabelę tras ręcznie, należy koniecznie utworzyć te wpisy w tabeli tras.

   ![Wpis na potrzeby podsieci wystąpienia zarządzanego dla środowiska lokalnego](./media/sql-database-managed-instance-get-started/udr.png)

4. Wróć do grupy zasobów i wybierz sieciową grupę zabezpieczeń, aby zapoznać się z zasadami zabezpieczeń.

   ![Sieciowa grupa zabezpieczeń](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Zapoznaj się z zasadami zabezpieczeń dla ruchu przychodzącego i wychodzącego.

   ![Reguły zabezpieczeń](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Wróć do grupy zasobów i wybierz swoje wystąpienie zarządzane.

   ![Wystąpienie zarządzane](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na karcie **Przegląd** znajdź właściwość **Host** i skopiuj w pełni kwalifikowany adres hosta dla wystąpienia zarządzanego. Zostanie on użyty w następnym przewodniku Szybki start.

   ![Nazwa hosta](./media/sql-database-managed-instance-get-started/host-name.png)

   Nazwa jest podobna do **nazwa_maszyny.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat nawiązywania połączenia z wystąpieniem zarządzanym, zobacz:
  - Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz [Connect your applications to a managed instance (Łączenie aplikacji z wystąpieniem zarządzanym)](sql-database-managed-instance-connect-app.md).
  - Aby skorzystać z samouczka przedstawiającego sposób łączenia wystąpienia zarządzanego z maszyny wirtualnej platformy Azure, zobacz [Configure an Azure virtual machine connection (Konfigurowanie połączenia maszyny wirtualnej platformy Azure)](sql-database-managed-instance-configure-vm.md).
  - Aby skorzystać z przewodnika Szybki start przedstawiającego sposób łączenia wystąpienia zarządzanego z poziomu lokalnego komputera klienckiego za pomocą połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia punkt-lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby przywrócić istniejącą bazę danych programu SQL ze środowiska lokalnego do wystąpienia zarządzanego, możesz użyć [usługi Azure Database Migration Service (DMS) na potrzeby migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) w celu przywrócenia z pliku kopii zapasowej bazy danych.
- Aby uzyskać informacje na temat zaawansowanego monitorowania wydajności bazy danych wystąpienia zarządzanego z wbudowaną analizą rozwiązywania problemów, zobacz [Monitor Azure SQL Database using Azure SQL Analytics (Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics)](../azure-monitor/insights/azure-sql.md)
