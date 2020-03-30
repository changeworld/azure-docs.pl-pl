---
title: Zarządzanie regułami zapory — Witryna Azure portal — usługa Azure Database for MariaDB
description: Tworzenie reguł zapory usługi Azure Database dla mariadb i zarządzanie nimi przy użyciu portalu Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8be8e948595cfb93049c0d6c93f421e4902e771d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530669"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Tworzenie reguł zapory usługi Azure Database dla mariadb i zarządzanie nimi przy użyciu witryny Azure portal
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database for MariaDB Server z określonego adresu IP lub zakresu adresów IP.

Reguły sieci wirtualnej (VNet) mogą być również używane do bezpiecznego dostępu do serwera. Dowiedz się więcej o [tworzeniu punktów końcowych i regułach usługi sieci wirtualnej i zarządzaniu nimi za pomocą portalu Azure.](howto-manage-vnet-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

1. Na stronie serwera MariaDB w obszarze Nagłówek Ustawienia kliknij pozycję **Zabezpieczenia połączenia,** aby otworzyć stronę Zabezpieczenia połączeń dla bazy danych platformy Azure dla bazy danych MariaDB.

   ![Portal Azure — kliknij pozycję Zabezpieczenia połączeń](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Kliknij **pozycję Dodaj mój adres IP** na pasku narzędzi. Spowoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, postrzegane przez system platformy Azure.

   ![Portal Azure — kliknij pozycję Dodaj mój adres IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Przed zapisaniem konfiguracji należy zweryfikować swój adres IP. W niektórych sytuacjach adres IP obserwowany przez witrynę Azure portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może być konieczna zmiana początkowego adresu IP i końcowego adresu IP, aby reguła działała zgodnie z oczekiwaniami.

   Użyj wyszukiwarki lub innego narzędzia online, aby sprawdzić swój własny adres IP. Na przykład wyszukaj "jaki jest mój adres IP".

4. Dodaj dodatkowe zakresy adresów. W regułach zapory dla usługi Azure Database for MariaDB można określić jeden adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do jednego adresu IP, wpisz ten sam adres w polach Początkowa własność IP i Koniec ip. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacji dostęp do dowolnej bazy danych na serwerze MariaDB, do której mają prawidłowe poświadczenia.

   ![Portal Azure — reguły zapory](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Kliknij **przycisk Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończy się pomyślnie.

   ![Portal Azure — kliknij przycisk Zapisz](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure łączenie się z usługą Azure Database dla serwera MariaDB, należy włączyć połączenia platformy Azure. Na przykład do obsługi aplikacji usługi Azure Web Apps lub aplikacji, która działa w maszynie Wirtualnej platformy Azure lub połączyć się z bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (sieci wirtualnej) lub grupie zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć tego typu połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie można ustawić opcję **Zezwalaj na dostęp do usług platformy Azure** na **ON** w portalu z **okienka zabezpieczeń Połączenie** i kliknij przycisk **Zapisz**. Jeśli próba połączenia jest niedozwolona, żądanie nie dociera do usługi Azure Database dla serwera MariaDB.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory w witrynie Azure portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk **+ Dodaj mój adres IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać dodatkowe adresy IP, wpisz **nazwę reguły**, **START IP**i END **IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne z pól reguły, a następnie zmodyfikuj. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij wielokropek [...], a następnie kliknij przycisk **Usuń**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki
 - Podobnie można tworzyć skrypty, aby [utworzyć i zarządzać regułami zapory usługi Azure Database dla MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.](howto-manage-firewall-cli.md)
 - Dalsze bezpieczne dostęp do serwera poprzez [tworzenie i zarządzanie punktami końcowymi usługi sieci wirtualnej i regułami przy użyciu portalu Azure](howto-manage-vnet-portal.md).