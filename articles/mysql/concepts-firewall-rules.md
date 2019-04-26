---
title: Azure Database dla reguły zapory serwera MySQL
description: W tym artykule opisano reguły zapory dla usługi Azure Database for MySQL server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a7016b8ca43abee9c3f346c6dec55a101ce4020a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60528319"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database dla reguły zapory serwera MySQL
Zapora uniemożliwia wszelki dostęp do serwera bazy danych, do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do serwera, na podstawie źródłowego adresu IP każdego żądania.

Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Można utworzyć reguły zapory na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całej bazy danych Azure dla serwera MySQL, oznacza to, że wszystkie bazy danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować przy użyciu witryny Azure portal lub poleceń interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musi być właścicielem bądź współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Wszystkie dostępu do bazy danych z usługi Azure Database dla serwera MySQL jest domyślnie blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Reguły zapory, aby określić, które IP zakresów adresów z Internetu, aby zezwolić na użycie. Dostęp do witryny portalu Azure, sama nie ma wpływu reguły zapory.

Próby nawiązania połączenia z Internetem a platformą Azure muszą najpierw przejść przez zaporę zanim dotrą do bazy danych Azure database for MySQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych w usłudze Azure Database dla serwera MySQL.

Jeśli adres IP żądania do jednego z zakresów określonych w regułach zapory na poziomie serwera, połączenie zostanie ustanowione.

Jeśli adres IP żądania znajduje się poza zakresów określonych w dowolnej reguły zapory na poziomie serwera lub na poziomie bazy danych, żądanie połączenia kończy się niepowodzeniem.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure, nawiązać połączenia z usługi Azure Database for MySQL server, należy włączyć połączenia platformy Azure. Na przykład, aby hostować aplikację usługi Azure Web Apps lub aplikację, która działa na Maszynie wirtualnej platformy Azure lub do nawiązywania połączenia bramy zarządzania danymi usługi Azure Data Factory. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcję **ON** w portalu pochodzące ze **zabezpieczenia połączeń** okienka, a następnie wybierz pozycję **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera serwera Azure Database for MySQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie Zezwalaj na dostęp do usług platformy Azure w portalu](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz witryny Azure portal reguły zapory można zarządzać programowo przy użyciu wiersza polecenia platformy Azure. Zobacz też [tworzenie i zarządzanie nimi — Azure Database dla MySQL reguł zapory przy użyciu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych
Podczas dostępu do bazy danych Microsoft Azure dla usługi serwera MySQL nie zachowywać się zgodnie z oczekiwaniami, należy rozważyć następujące kwestie:

* **Zmiany do listy dozwolonych nie zostały uwzględnione jeszcze:** Może to być jak pięciu minut opóźnienia, zanim zmiany do usługi Azure Database for MySQL serwera konfiguracji zapory zaczęły obowiązywać.

* **Nazwa logowania nie ma autoryzacji lub użyto nieprawidłowego hasła:** Jeśli logowanie nie ma uprawnień w usłudze Azure Database dla serwera MySQL lub użyte hasło jest nieprawidłowe, odmowa połączenia z serwerem Azure Database for MySQL. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń.

* **Dynamiczny adres IP:** Jeśli masz połączenie internetowe za pomocą dynamicznego adresowania IP i problemy z przejściem przez zaporę, możesz wypróbować jedną z następujących rozwiązań:

* Poproś usługodawcy internetowego (ISP) dla zakresu adresów IP, które są przypisane do komputerów klienckich uzyskujących dostęp do usługi Azure Database for MySQL server, a następnie dodać zakres adresów IP jako reguły zapory.

* Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie i zarządzanie nimi — Azure Database dla MySQL reguł zapory przy użyciu witryny Azure portal](./howto-manage-firewall-using-portal.md)
[tworzenie i zarządzanie nimi — Azure Database dla MySQL reguł zapory przy użyciu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)
