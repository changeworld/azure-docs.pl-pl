---
title: Zarządzanie regułami zapory — Witryna Azure portal — usługa Azure Database for MySQL
description: Tworzenie reguł zapory usługi Azure Database dla mysql i zarządzanie nimi przy użyciu witryny Azure portal
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: edd6403ed3d7607eb96bc7c6a603c3fef8a4f99e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063543"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Tworzenie reguł zapory usługi Azure Database dla mysql i zarządzanie nimi przy użyciu witryny Azure portal
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database for MySQL Server z określonego adresu IP lub zakresu adresów IP. 

Reguły sieci wirtualnej (VNet) mogą być również używane do bezpiecznego dostępu do serwera. Dowiedz się więcej o [tworzeniu punktów końcowych i regułach usługi sieci wirtualnej i zarządzaniu nimi za pomocą portalu Azure.](howto-manage-vnet-using-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

1. Na stronie serwera MySQL w obszarze Nagłówek Ustawienia kliknij pozycję **Zabezpieczenia połączeń,** aby otworzyć stronę Zabezpieczenia połączeń dla bazy danych platformy Azure dla mysql.

   ![Portal Azure — kliknij pozycję Zabezpieczenia połączeń](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij **pozycję Dodaj mój adres IP** na pasku narzędzi. Spowoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, postrzegane przez system platformy Azure.

   ![Portal Azure — kliknij pozycję Dodaj mój adres IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Przed zapisaniem konfiguracji należy zweryfikować swój adres IP. W niektórych sytuacjach adres IP obserwowany przez witrynę Azure portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może być konieczna zmiana początkowego adresu IP i końcowego adresu IP, aby reguła działała zgodnie z oczekiwaniami.

   Użyj wyszukiwarki lub innego narzędzia online, aby sprawdzić swój własny adres IP. Na przykład wyszukaj "jaki jest mój adres IP".

4. Dodaj dodatkowe zakresy adresów. W regułach zapory dla usługi Azure Database for MySQL można określić jeden adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do jednego adresu IP, wpisz ten sam adres w polach Początkowa własność IP i Koniec ip. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacji dostęp do dowolnej bazy danych na serwerze MySQL, do której mają prawidłowe poświadczenia.

   ![Portal Azure — reguły zapory](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kliknij **przycisk Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończy się pomyślnie.

   ![Portal Azure — kliknij przycisk Zapisz](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure łączenie się z usługą Azure Database dla serwera MySQL, należy włączyć połączenia platformy Azure. Na przykład do obsługi aplikacji usługi Azure Web Apps lub aplikacji, która działa w maszynie Wirtualnej platformy Azure lub połączyć się z bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (sieci wirtualnej) lub grupie zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć tego typu połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie można ustawić opcję **Zezwalaj na dostęp do usług platformy Azure** na **ON** w portalu z **okienka zabezpieczeń Połączenie** i naciśnij **klawisz Zapisz**. Jeśli próba połączenia jest niedozwolona, żądanie nie dociera do bazy danych platformy Azure dla serwera MySQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu witryny Azure portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk **+ Dodaj mój adres IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać dodatkowe adresy IP, wpisz **nazwę reguły**, **START IP**i END **IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne z pól reguły, a następnie zmodyfikuj. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij wielokropek [...], a następnie kliknij przycisk **Usuń**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.


## <a name="next-steps"></a>Następne kroki
     Similarly, you can script to [Create and manage Azure Database for MySQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md).
     Further secure access to your server by [creating and managing Virtual Network service endpoints and rules using the Azure portal](howto-manage-vnet-using-portal.md).
        For help in connecting to an Azure     atabase for MySQL server, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        