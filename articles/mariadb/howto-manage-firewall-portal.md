---
title: Tworzenie i zarządzanie nimi MariaDB reguły zapory w usłudze Azure Database dla serwera MariaDB
description: Tworzenie i zarządzanie nimi — Azure Database dla MariaDB reguł zapory przy użyciu witryny Azure portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: e9ab243692f5a4a1ec7de25774f5bad867698fc3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787082"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi — Azure Database dla MariaDB reguł zapory przy użyciu witryny Azure portal
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database dla serwera MariaDB z określonego adresu IP lub zakres adresów IP.

Wirtualne reguł sieci wirtualnej można również bezpieczny dostęp do serwera. Dowiedz się więcej o [creating and managing Virtual Network i punktów końcowych i reguł za pomocą witryny Azure portal usługi](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

1. Na stronie serwera MariaDB, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć stronę Zabezpieczenia połączeń dla usługi Azure Database dla serwera MariaDB.

   ![Portal Azure — zabezpieczenia połączeń kliknij](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Kliknij przycisk **Dodaj mój adres IP** na pasku narzędzi. Powoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP Twojego komputera wyobrażenia systemu platformy Azure.

   ![Witryna Azure portal — kliknij przycisk Dodaj mój adres IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Przed zapisaniem konfiguracji, należy zweryfikować swój adres IP. W niektórych sytuacjach adres IP zaobserwowane przez witryny Azure portal różni się od adres IP używany podczas uzyskiwania dostępu do Internetu i serwery usługi Azure. W związku z tym należy zmienić początkowy adres IP i końcowy adres IP funkcji reguły, zgodnie z oczekiwaniami.

   Użyj aparatu wyszukiwania lub innego narzędzia do trybu online, aby sprawdzić adres IP. Na przykład wyszukiwanie "co to jest mój adres IP".

4. Dodaj zakresy adresów dodatkowe. W regułach zapory dla usługi Azure Database dla serwera MariaDB można określić pojedynczy adres IP lub zakresu adresów. Jeśli chcesz ograniczyć regułę do w pojedynczy adres IP, wpisz ten sam adres w polu początkowy adres IP i końcowy adres IP. Otwarcie zapory pozwala administratorom, użytkowników i aplikacji dostępu do dowolnej bazy danych na serwerze MariaDB, do którego mają prawidłowe poświadczenia.

   ![Witryna Azure portal — reguły zapory](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Kliknij przycisk **Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Czekaj na potwierdzenie, czy powiodła się aktualizacja reguły zapory.

   ![Witryna Azure portal — kliknij przycisk Zapisz](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure, nawiązać połączenia z usługi Azure Database dla serwera MariaDB, należy włączyć połączenia platformy Azure. Na przykład, aby hostować aplikację usługi Azure Web Apps lub aplikację, która działa na Maszynie wirtualnej platformy Azure lub do nawiązywania połączenia bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcję **ON** w portalu pochodzące ze **zabezpieczenia połączeń** okienku i kliknij przycisk **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera usługi Azure Database dla serwera MariaDB.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Zarządzanie istniejące reguły zapory w witrynie Azure portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij **+ Dodaj mój adres IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać dodatkowych adresów IP, wpisz w **Nazwa reguły**, **POCZĄTKOWY adres IP**, i **końcowy adres IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule, a następnie zmodyfikuj. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **Usuń**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Kolejne kroki
 - Podobnie, można utworzyć skrypty do [tworzenie i zarządzanie nimi — Azure Database dla MariaDB reguł zapory przy użyciu wiersza polecenia platformy Azure](howto-manage-firewall-cli.md).
 - Dodatkowo zabezpieczyć dostęp do serwera przez [creating and managing Virtual Network i punktów końcowych i reguł za pomocą witryny Azure portal usługi](howto-manage-vnet-portal.md).