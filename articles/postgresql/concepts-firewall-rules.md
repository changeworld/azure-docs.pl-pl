---
title: Reguły zapory w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano reguły zapory dla usługi Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 40a675fbefe9743f5de1f9766cf33ae7dba9e5a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073585"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Reguły zapory w usłudze Azure Database for PostgreSQL — pojedynczy serwer
Usługa Azure Database for postgresql w warstwie zapory serwera uniemożliwia dostęp do serwera bazy danych, do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do serwera, na podstawie źródłowego adresu IP każdego żądania.
Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Można utworzyć reguły zapory na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całej bazy danych platformy Azure przez serwer PostgreSQL, oznacza to, że wszystkie bazy danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować za pomocą witryny Azure portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musi być właścicielem bądź współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Wszystkie dostępu do bazy danych z usługi Azure Database dla serwera PostgreSQL jest zablokowany przez zaporę, domyślnie. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Reguły zapory, aby określić, które IP zakresów adresów z Internetu, aby zezwolić na użycie. Dostęp do witryny portalu Azure, sama nie ma wpływu reguły zapory.
Próby nawiązania połączenia z Internetem a platformą Azure muszą najpierw przejść przez zaporę zanim dotrą do bazy danych postgresql w warstwie, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na tej samej usługi Azure Database for postgresql w warstwie serwera. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, ustanawiane jest połączenie.
Jeśli adres IP żądania nie jest w zakresach określonych w dowolnej reguły zapory na poziomie serwera, żądanie połączenia kończy się niepowodzeniem.
Na przykład jeśli aplikacja łączy się przy użyciu sterownika JDBC dla PostgreSQL, możesz napotkać ten błąd podczas próby Połącz, gdy Zapora blokuje połączenie.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Błąd krytyczny: nie pg\_hba.conf wpis dla hosta: "123.45.67.890" użytkownika "adminuser", baza danych "postgresql", protokołu SSL

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure, nawiązać połączenia z usługi Azure Database for postgresql w warstwie serwera, należy włączyć połączenia platformy Azure. Na przykład, aby hostować aplikację usługi Azure Web Apps lub aplikację, która działa na Maszynie wirtualnej platformy Azure lub do nawiązywania połączenia bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcję **ON** w portalu pochodzące ze **zabezpieczenia połączeń** okienka, a następnie wybierz pozycję **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera usługi Azure Database for postgresql w warstwie serwera.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie Zezwalaj na dostęp do usług platformy Azure w portalu](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz witryny Azure portal reguły zapory można zarządzać programowo przy użyciu wiersza polecenia platformy Azure.
Zobacz też [tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Rozwiązywanie problemów z zapory serwera bazy danych
Gdy dostęp do bazy danych Microsoft Azure usługi serwera PostgreSQL nie zachowywać się zgodnie z oczekiwaniami, należy wziąć pod uwagę następujące kwestie:

* **Zmiany do listy dozwolonych nie zostały uwzględnione jeszcze:** Może to być jak pięciu minut opóźnienia, zanim zmiany do usługi Azure Database dla konfiguracji zapory serwera PostgreSQL zaczęły obowiązywać.

* **Nazwa logowania nie ma autoryzacji lub użyto nieprawidłowego hasła:** Jeśli logowanie nie ma uprawnień w usłudze Azure Database dla serwera PostgreSQL lub użyte hasło jest nieprawidłowe, odmowa połączenia z usługi Azure Database for postgresql w warstwie serwera. Tylko utworzenie ustawień zapory zapewnia klientom możliwość próby nawiązywania połączenia z serwerem; Każdy klient nadal musi podać niezbędne poświadczenia zabezpieczeń.

Na przykład za pomocą klienta JDBC, może się pojawić następujący błąd.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Błąd krytyczny: uwierzytelnianie przy użyciu hasła użytkownika nie powiodło się "nazwa_użytkownika"

* **Dynamiczny adres IP:** Jeśli masz połączenie internetowe za pomocą dynamicznego adresowania IP i problemy z przejściem przez zaporę, można wypróbować jedno z następujących rozwiązań:

* Poproś usługodawcy internetowego (ISP) dla zakresu adresów IP, które są przypisane do komputerów klienckich uzyskujących dostęp do usługi Azure Database dla serwera PostgreSQL, a następnie dodać zakres adresów IP jako reguły zapory.

* Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodać statyczny adres IP jako reguły zapory.

## <a name="next-steps"></a>Kolejne kroki
Artykuły na temat tworzenia reguł zapory na poziomie serwera i na poziomie bazy danych zobacz:
* [Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md)
* [Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md)
