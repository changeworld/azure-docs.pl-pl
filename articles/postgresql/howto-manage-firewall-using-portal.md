---
title: Tworzenie i zarządzanie nimi reguły zapory w usłudze Azure Database for PostgreSQL
description: Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu witryny Azure portal
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 7211b7f5db14f4bad114556e5c080cf5d535b00b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545488"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu witryny Azure portal
Reguły zapory na poziomie serwera umożliwiają administratorom dostęp do usługi Azure Database dla serwera PostgreSQL z określonego adresu IP lub zakres adresów IP. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- Serwer [Tworzenie serwera usługi Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal
1. Na stronie serwer postgresql — w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Zabezpieczenia połączeń dla usługi Azure Database for PostgreSQL.

  ![Portal Azure — zabezpieczenia połączeń kliknij](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij przycisk **Dodaj mój adres IP** na pasku narzędzi. Powoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP Twojego komputera wyobrażenia systemu platformy Azure.

  ![Witryna Azure portal — kliknij przycisk Dodaj mój adres IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Przed zapisaniem konfiguracji, należy zweryfikować swój adres IP. W niektórych sytuacjach adres IP zaobserwowane przez witryny Azure portal różni się od adres IP używany podczas uzyskiwania dostępu do Internetu i serwery usługi Azure. W związku z tym należy zmienić początkowy adres IP i końcowy adres IP funkcji reguły, zgodnie z oczekiwaniami.
Użyj aparatu wyszukiwania lub innego narzędzia do trybu online, aby sprawdzić adres IP. Na przykład wyszukiwanie "co to jest mój adres IP".

  ![Co to jest mój adres IP wyszukiwanie Bing](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Dodaj zakresy adresów dodatkowe. W regułach zapory dla usługi Azure Database for PostgreSQL można określić pojedynczy adres IP lub zakresu adresów. Jeśli chcesz ograniczyć regułę do w pojedynczy adres IP, wpisz ten sam adres w polu, początkowy adres IP i końcowy adres IP. Otwarcie zapory pozwala administratorom, użytkowników i aplikacji, aby zalogować się do każdej bazy danych na serwer PostgreSQL, do którego mają prawidłowe poświadczenia.

  ![Witryna Azure portal — reguły zapory ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kliknij przycisk **Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Czekaj na potwierdzenie, czy aktualizacja reguły zapory zakończyło się pomyślnie.

  ![Witryna Azure portal — kliknij przycisk Zapisz](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure, nawiązać połączenia z usługi Azure Database for postgresql w warstwie serwera, należy włączyć połączenia platformy Azure. Na przykład, aby hostować aplikację usługi Azure Web Apps lub aplikację, która działa na Maszynie wirtualnej platformy Azure lub do nawiązywania połączenia bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcję **ON** w portalu pochodzące ze **zabezpieczenia połączeń** okienka, a następnie wybierz pozycję **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera usługi Azure Database for postgresql w warstwie serwera.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu witryny Azure Portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk Tak, aby + **Dodaj mój adres IP**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać kolejne adresy IP, wpisz nazwę reguły, początkowy adres IP i końcowy adres IP. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule i wprowadź zmiany. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **Usuń** można usunąć reguły. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Kolejne kroki
- Podobnie, można utworzyć skrypty do [tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).
- Aby uzyskać pomoc podczas nawiązywania połączenia z usługi Azure Database for postgresql w warstwie serwera, zobacz [biblioteki połączeń dla usługi Azure Database for postgresql w warstwie](concepts-connection-libraries.md).
