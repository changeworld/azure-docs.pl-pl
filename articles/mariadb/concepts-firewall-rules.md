---
title: Reguły zapory serwera Azure Database for MariaDB
description: Dowiedz się więcej na temat używania reguł zapory do włączania połączeń z serwerem Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 89c4bce33b80e988a9da363a89854e921bee30b0
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973657"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Reguły zapory serwera Azure Database for MariaDB
Zapory uniemożliwiają dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora przyznaje dostęp do serwera na podstawie źródłowego adresu IP każdego żądania.

Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy akceptowalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całego serwera Azure Database for MariaDB, czyli wszystkich baz danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować za pomocą poleceń Azure Portal lub interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Dostęp wszystkich baz danych do serwera Azure Database for MariaDB jest blokowany domyślnie przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, które zakresy adresów IP z Internetu mają być dozwolone. Reguły zapory nie wpływają na dostęp do samej witryny sieci Web Azure Portal.

Próby połączenia z Internetu i platformy Azure muszą być przekazywane przez zaporę przed uzyskaniem dostępu do bazy danych Azure Database for MariaDB, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na serwerze Azure Database for MariaDB.

Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, nawiązanie połączenia zostanie nadane.

Jeśli adres IP żądania wykracza poza zakresy określone w regułach zapory na poziomie bazy danych lub na poziomie serwera, żądanie połączenia kończy się niepowodzeniem.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure Łączenie się z serwerem Azure Database for MariaDB, należy włączyć połączenia platformy Azure. Na przykład w celu hostowania aplikacji Web Apps platformy Azure lub aplikacji działającej na maszynie wirtualnej platformy Azure lub w celu nawiązania połączenia z Azure Data Factory bramy zarządzania danymi. Zasoby nie muszą znajdować się w tej samej Virtual Network (VNet) lub grupie zasobów dla reguły zapory w celu włączenia tych połączeń. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod włączania tych typów połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie możesz ustawić opcję Zezwalaj na **dostęp do usług platformy Azure** **w portalu** w okienku **zabezpieczenia połączenia** i kliknąć przycisk **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera Azure Database for MariaDB.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie zezwalania na dostęp do usług platformy Azure w portalu](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz Azure Portal reguły zapory można zarządzać programowo przy użyciu interfejsu wiersza polecenia platformy Azure. 

<!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-firewall-issues"></a>Rozwiązywanie problemów z zaporą
Należy wziąć pod uwagę następujące kwestie, gdy dostęp do usługi Microsoft Azure Database for MariaDB Server nie zachowuje się zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Aby zmiany w konfiguracji zapory serwera Azure Database for MariaDB zaczęły obowiązywać, może wystąpić nawet pięć minut.

* **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:** Jeśli logowanie nie ma uprawnień na serwerze Azure Database for MariaDB lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure Database for MariaDB. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń.

* **Dynamiczny adres IP:** Jeśli masz połączenie internetowe z dynamicznym adresem IP i masz problemy z uzyskaniem przez zaporę, możesz wypróbować jedno z następujących rozwiązań:

   * Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure Database for MariaDB, a następnie Dodaj zakres adresów IP jako regułę zapory.

   * Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

* **Adres IP serwera wydaje się być publiczny:** Połączenia z serwerem Azure Database for MariaDB są kierowane za pomocą publicznie dostępnej bramy platformy Azure. Rzeczywisty adres IP serwera jest jednak chroniony przez zaporę. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym architektury łączności](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Następne kroki
- [Tworzenie reguł zapory Azure Database for MariaDB i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->
