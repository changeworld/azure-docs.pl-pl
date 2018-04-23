---
title: Tworzenie i zarządzanie nimi MySQL reguły zapory w bazie danych Azure dla programu MySQL
description: Tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu portalu Azure
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6475c5c3ecb43352a8ef8db8fe1c023a16cd8a3d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu portalu Azure
Reguły zapory poziomu serwera umożliwiają administratorom dostęp do bazy danych Azure MySQL serwera z określonego adresu IP lub zakresu adresów IP. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

1. Na stronie serwer MySQL, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć stronę połączenia zabezpieczeń bazy danych Azure dla programu MySQL.

   ![Portal Azure — kliknij przycisk Zabezpieczenia połączeń](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij przycisk **dodać Moje IP** na pasku narzędzi. Powoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, jako widocznego w systemie Azure.

   ![Portal Azure — kliknij przycisk Dodaj mój adres IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Zweryfikuj swój adres IP przed zapisaniem konfiguracji. W niektórych sytuacjach adres IP uwzględniony przez Azure portal różni się od adresu IP, używana podczas uzyskiwania dostępu do Internetu i serwery usługi Azure. Dlatego może być konieczne zmiany Start IP i końcowemu adresowi IP, aby funkcja reguły zgodnie z oczekiwaniami.

   Użyj aparatu wyszukiwania lub innego narzędzia online, aby sprawdzić adres IP. Wyszukaj na przykład "co to jest adresu IP". 

   ![Bing w celu przedstawienia Moje IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Dodaj dodatkowy adres zakresów. W regułach zapory dla bazy danych Azure dla programu MySQL można określić pojedynczy adres IP lub zakresu adresów. Jeśli chcesz ograniczyć regułę do pojedynczego adresu IP, wpisz ten sam adres w polach Start IP i końcowy adres IP. Otwarcie zapory umożliwia Administratorzy, użytkownicy i aplikacji dostępu do dowolnej bazy danych na serwerze MySQL, do których mają prawidłowe poświadczenia.

   ![Portal Azure — reguły zapory ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kliknij przycisk **zapisać** na pasku narzędzi, aby zapisać tę regułę zapory poziomu serwera. Poczekaj na potwierdzenie, że aktualizacja reguły zapory zakończyła się powodzeniem.

   ![Portal Azure — kliknij przycisk Zapisz](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom połączenia z bazą danych Azure dla serwera MySQL na platformie Azure, można włączyć połączenia platformy Azure. Na przykład do obsługi aplikacji Azure aplikacje sieci Web lub aplikacji, która działa w maszynie Wirtualnej platformy Azure lub nawiązywania połączenia z bramą zarządzania danymi fabryki danych Azure. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcji w celu **ON** w portalu z **zabezpieczenia połączeń** okienko i naciśnij klawisz **zapisać**. Jeśli próba połączenia nie jest dozwolone, żądanie nie osiąga Azure bazy danych dla serwera MySQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Zarządzanie istniejących reguł zapory na poziomie serwera przy użyciu portalu Azure
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk **+ Dodaj adres IP Moje**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać dodatkowe adresy IP, wpisz w **Nazwa reguły**, **START IP**, i **KOŃCOWEMU adresowi IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij każdego pola w regule, a następnie zmodyfikować. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **usunąć**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.


## <a name="next-steps"></a>Kolejne kroki
- Podobnie można utworzyć skrypty do [tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu interfejsu wiersza polecenia Azure](howto-manage-firewall-using-cli.md).
- Aby uzyskać pomoc w nawiązywania połączenia z bazą danych Azure dla serwera MySQL, [biblioteki połączeń dla bazy danych Azure dla programu MySQL](./concepts-connection-libraries.md)
