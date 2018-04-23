---
title: Tworzenie i zarządzanie reguły zapory w bazie danych Azure dla PostgreSQL
description: Tworzenie i zarządzanie bazą danych Azure dla reguł zapory PostgreSQL przy użyciu portalu Azure
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bef927cff49d957728a2a12362786d48d60e61b7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Tworzenie i zarządzanie bazą danych Azure dla reguł zapory PostgreSQL przy użyciu portalu Azure
Reguły zapory poziomu serwera umożliwiają administratorom dostęp do bazy danych Azure PostgreSQL serwera z określonego adresu IP lub zakresu adresów IP. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- Serwer [utworzenia bazy danych Azure dla PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal
1. Na stronie serwer PostgreSQL w sekcji Ustawienia kliknij pozycję **zabezpieczenia połączeń** otworzyć strona połączenia zabezpieczeń bazy danych Azure do PostgreSQL.

  ![Portal Azure — kliknij przycisk Zabezpieczenia połączeń](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij przycisk **dodać Moje IP** na pasku narzędzi. Powoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, jako widocznego w systemie Azure.

  ![Portal Azure — kliknij przycisk Dodaj mój adres IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Zweryfikuj swój adres IP przed zapisaniem konfiguracji. W niektórych sytuacjach adres IP uwzględniony przez Azure portal różni się od adresu IP, używana podczas uzyskiwania dostępu do Internetu i serwery usługi Azure. Dlatego może być konieczne zmiany Start IP i końcowemu adresowi IP, aby funkcja reguły zgodnie z oczekiwaniami.
Użyj aparatu wyszukiwania lub innego narzędzia online, aby sprawdzić adres IP. Na przykład, wyszukaj "co to jest Mój IP".

  ![Co to jest Mój IP wyszukiwania usługi Bing](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Dodaj dodatkowy adres zakresów. W regułach zapory dla bazy danych Azure dla PostgreSQL można określić pojedynczy adres IP lub zakresu adresów. Jeśli chcesz ograniczyć regułę do pojedynczego adresu IP, wpisz ten sam adres w polu Start IP i końcowy adres IP. Otwarcie zapory umożliwia Administratorzy, użytkownicy i aplikacje do logowania do dowolnej bazy danych na serwerze PostgreSQL, do którego mają prawidłowe poświadczenia.

  ![Portal Azure — reguły zapory ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kliknij przycisk **zapisać** na pasku narzędzi, aby zapisać tę regułę zapory poziomu serwera. Poczekaj na potwierdzenie pomyślnego aktualizacji reguł zapory.

  ![Portal Azure — kliknij przycisk Zapisz](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom połączenia z bazą danych Azure PostgreSQL serwera z platformy Azure, można włączyć połączenia platformy Azure. Na przykład do obsługi aplikacji Azure aplikacje sieci Web lub aplikacji, która działa w maszynie Wirtualnej platformy Azure lub nawiązywania połączenia z bramą zarządzania danymi fabryki danych Azure. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcji w celu **ON** w portalu z **zabezpieczenia połączeń** okienko i naciśnij klawisz **zapisać**. Jeśli próba połączenia nie jest dozwolone, żądanie nie osiąga PostgreSQL serwera bazy danych Azure.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu witryny Azure Portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk + **dodać Moje IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać kolejne adresy IP, wpisz nazwę reguły, początkowy adres IP i końcowy adres IP. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule i wprowadź zmiany. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **usunąć** Aby usunąć regułę. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Kolejne kroki
- Podobnie można utworzyć skrypty do [tworzenie i zarządzanie bazą danych Azure PostgreSQL reguł zapory przy użyciu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).
- Aby uzyskać pomoc w nawiązywania połączenia z bazą danych Azure dla serwera PostgreSQL, [biblioteki połączeń dla bazy danych Azure dla PostgreSQL](concepts-connection-libraries.md).
