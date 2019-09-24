---
title: Reguły zapory w Azure Database for PostgreSQL-pojedynczym serwerze
description: W tym artykule opisano reguły zapory dla Azure Database for PostgreSQL-pojedynczego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: a48e9e2583afbde584987e5a1ac61da9734058d1
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200131"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Reguły zapory w Azure Database for PostgreSQL-pojedynczym serwerze
Zapora serwera Azure Database for PostgreSQL uniemożliwia dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora przyznaje dostęp do serwera na podstawie źródłowego adresu IP każdego żądania.
Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całego serwera Azure Database for PostgreSQL, czyli wszystkich baz danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować za pomocą Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Domyślnie dostęp do serwera Azure Database for PostgreSQL jest blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, które zakresy adresów IP z Internetu mają być dozwolone. Reguły zapory nie wpływają na dostęp do samej witryny sieci Web Azure Portal.
Próby połączenia z Internetu i platformy Azure muszą być przekazywane przez zaporę przed uzyskaniem dostępu do bazy danych PostgreSQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na tym samym serwerze Azure Database for PostgreSQL. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, ustanawiane jest połączenie.
Jeśli adres IP żądania nie należy do zakresów określonych w regułach zapory na poziomie serwera, żądanie połączenia kończy się niepowodzeniem.
Jeśli na przykład aplikacja nawiązuje połączenie z sterownikiem JDBC dla PostgreSQL, może wystąpić błąd podczas próby nawiązania połączenia, gdy Zapora blokuje połączenie.
> Java. util. współbieżne. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: Błąd krytyczny:\_brak wpisu HBA. conf dla hosta "123.45.67.890", użytkownika "AdminUser", bazy danych "PostgreSQL", SSL

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure Łączenie się z serwerem Azure Database for PostgreSQL, należy włączyć połączenia platformy Azure. Na przykład w celu hostowania aplikacji Web Apps platformy Azure lub aplikacji działającej na maszynie wirtualnej platformy Azure lub w celu nawiązania połączenia z Azure Data Factory bramy zarządzania danymi. Zasoby nie muszą znajdować się w tej samej Virtual Network (VNet) lub grupie zasobów dla reguły zapory w celu włączenia tych połączeń. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod włączania tych typów połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie możesz ustawić opcję Zezwalaj na **dostęp do usług platformy Azure** w portalu w okienku **zabezpieczenia połączenia** i kliknąć przycisk **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera Azure Database for PostgreSQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie zezwalania na dostęp do usług platformy Azure w portalu](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz Azure Portal reguły zapory można zarządzać programowo przy użyciu interfejsu wiersza polecenia platformy Azure.
Zobacz też [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Rozwiązywanie problemów z zaporą
Należy wziąć pod uwagę następujące kwestie, gdy dostęp do usługi Microsoft Azure Database for PostgreSQL Server nie zadziała zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Może wystąpić do pięciu minut opóźnienia, zanim zmiany konfiguracji zapory serwera usługi Azure Database for PostgreSQL zostaną zastosowane.

* **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:** Jeśli logowanie nie ma uprawnień na serwerze Azure Database for PostgreSQL lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure Database for PostgreSQL. Utworzenie ustawienia zapory zapewnia klientom możliwość próby nawiązania połączenia z serwerem; Każdy klient musi nadal podawać niezbędne poświadczenia zabezpieczeń.

   Na przykład przy użyciu klienta JDBC może pojawić się następujący błąd.
   > Java. util. współbieżne. ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: Błąd krytyczny: uwierzytelnianie hasła użytkownika "yourUserName" nie powiodło się

* **Dynamiczny adres IP:** Jeśli masz połączenie internetowe z dynamicznym adresem IP i masz problemy z uzyskaniem przez zaporę, możesz wypróbować jedno z następujących rozwiązań:

   * Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure Database for PostgreSQL, a następnie Dodaj zakres adresów IP jako regułę zapory.

   * Zamiast tego należy pobrać statyczne adresy IP dla komputerów klienckich, a następnie dodać statyczny adres IP jako regułę zapory.

* **Adres IP serwera wydaje się być publiczny:** Połączenia z serwerem Azure Database for PostgreSQL są kierowane za pomocą publicznie dostępnej bramy platformy Azure. Rzeczywisty adres IP serwera jest jednak chroniony przez zaporę. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym architektury łączności](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z artykułami dotyczącymi tworzenia reguł zapory na poziomie serwera i na poziomie bazy danych, zobacz:
* [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](howto-manage-firewall-using-cli.md)
