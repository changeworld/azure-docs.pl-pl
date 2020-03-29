---
title: Zarządzanie regułami zapory — Witryna Azure portal — Usługa Azure Database for PostgreSQL — single server
description: Tworzenie reguł zapory dla usługi Azure Database dla postgreSQL i zarządzanie nimi — pojedynczy serwer przy użyciu witryny Azure portal
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770309"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Tworzenie reguł zapory dla usługi Azure Database dla postgreSQL i zarządzanie nimi — pojedynczy serwer przy użyciu witryny Azure portal
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database for PostgreSQL Server z określonego adresu IP lub zakresu adresów IP.

Reguły sieci wirtualnej (VNet) mogą być również używane do bezpiecznego dostępu do serwera. Dowiedz się więcej o [tworzeniu punktów końcowych i regułach usługi sieci wirtualnej i zarządzaniu nimi za pomocą portalu Azure.](howto-manage-vnet-using-portal.md)

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- Serwer [Tworzenie bazy danych platformy Azure dla postgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal
1. Na stronie Serwera PostgreSQL w obszarze Ustawienia kliknij pozycję **Zabezpieczenia połączenia,** aby otworzyć stronę zabezpieczeń połączenia dla bazy danych Azure database for PostgreSQL.

   ![Portal Azure — kliknij pozycję Zabezpieczenia połączeń](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij **pozycję Dodaj mój adres IP** na pasku narzędzi. Spowoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, postrzegane przez system platformy Azure.

   ![Portal Azure — kliknij pozycję Dodaj mój adres IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Przed zapisaniem konfiguracji należy zweryfikować swój adres IP. W niektórych sytuacjach adres IP obserwowany przez witrynę Azure portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może być konieczna zmiana początkowego adresu IP i końcowego adresu IP, aby reguła działała zgodnie z oczekiwaniami.
   Użyj wyszukiwarki lub innego narzędzia online, aby sprawdzić swój własny adres IP. Na przykład wyszukaj hasło "jaki jest mój adres IP".

   ![Wyszukiwanie Bing dla Co to jest mój adres IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Dodaj dodatkowe zakresy adresów. W regułach zapory dla usługi Azure Database for PostgreSQL można określić jeden adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do jednego adresu IP, wpisz ten sam adres w polu Początkowego adresu IP i Końcowego ADRESU IP. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacjom dostęp do dowolnej bazy danych na serwerze PostgreSQL, do której mają prawidłowe poświadczenia.

   ![Portal Azure — reguły zapory](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kliknij **przycisk Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończyła się pomyślnie.

   ![Portal Azure — kliknij przycisk Zapisz](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure łączenie się z usługą Azure Database dla serwera PostgreSQL, należy włączyć połączenia platformy Azure. Na przykład do obsługi aplikacji usługi Azure Web Apps lub aplikacji, która działa w maszynie Wirtualnej platformy Azure lub połączyć się z bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (sieci wirtualnej) lub grupie zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć tego typu połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie można ustawić opcję **Zezwalaj na dostęp do usług platformy Azure** na **ON** w portalu z **okienka zabezpieczeń Połączenie** i naciśnij **klawisz Zapisz**. Jeśli próba połączenia jest niedozwolona, żądanie nie dociera do usługi Azure Database dla serwera PostgreSQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu witryny Azure Portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk + **Dodaj mój adres IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać kolejne adresy IP, wpisz nazwę reguły, początkowy adres IP i końcowy adres IP. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule i wprowadź zmiany. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij wielokropek [...] i kliknij przycisk **Usuń,** aby usunąć regułę. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki
- Podobnie można tworzyć skrypty, aby [utworzyć i zarządzać usługą Azure Database dla reguł zapory PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).
- Dalsze bezpieczne dostęp do serwera poprzez [tworzenie i zarządzanie punktami końcowymi usługi sieci wirtualnej i regułami przy użyciu portalu Azure](howto-manage-vnet-using-portal.md).
- Aby uzyskać pomoc dotyczącą łączenia się z usługą Azure Database dla serwera PostgreSQL, zobacz [Biblioteki połączeń dla usługi Azure Database for PostgreSQL](concepts-connection-libraries.md).
