---
title: Bazy danych platformy Azure dla reguł zapory serwera MySQL
description: Opisuje reguły zapory bazy danych Azure, aby serwer MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bd075a98e75c6fcb609103eefe260f4d7fc216b1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264657"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Bazy danych platformy Azure dla reguł zapory serwera MySQL
Zapory uniemożliwić dostęp do serwera bazy danych do chwili określenia komputery, które ma uprawnienia. Zapora udziela dostępu do serwera, na podstawie źródłowego adresu IP dla każdego żądania.

Aby skonfigurować zaporę, tworzyć reguły zapory, określających zakresów adresów IP akceptowane. Można tworzyć reguły zapory na poziomie serwera.

**Reguły zapory:** reguły te umożliwiają klientom dostęp do całej bazy danych Azure, aby serwer MySQL, oznacza to, że wszystkie bazy danych w tym samym serwerze logicznym. Reguły zapory poziomu serwera można skonfigurować przy użyciu portalu Azure lub poleceń z wiersza polecenia platformy Azure. Aby utworzyć reguły zapory poziomu serwera, musi być właściciela subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Wszelki dostęp do bazy danych Azure bazy danych MySQL serwera jest domyślnie blokowane przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory poziomu serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory do określenia IP zakresów adresów z Internetu, aby umożliwić. Dostęp do witryny portalu Azure, sama nie ma wpływu na reguły zapory.

Próby nawiązania połączenia z Internetem i Azure musi najpierw przejść przez zaporę przed dotarciem Azure bazy danych dla bazy danych MySQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera dotyczą wszystkich baz danych w bazie danych Azure dla serwera MySQL.

Jeśli adres IP żądania znajduje się w jednym z zakresów określonych w regułach zapory na poziomie serwera, zostanie ustanowione połączenie.

Jeśli adres IP żądania jest spoza zakresu określonego dowolną regułę zapory poziomu serwera lub na poziomie bazy danych, żądanie połączenia zakończy się niepowodzeniem.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom połączenia z bazą danych Azure dla serwera MySQL na platformie Azure, można włączyć połączenia platformy Azure. Na przykład do obsługi aplikacji Azure aplikacje sieci Web lub aplikacji, która działa w maszynie Wirtualnej platformy Azure lub nawiązywania połączenia z bramą zarządzania danymi fabryki danych Azure. Zasoby nie muszą znajdować się w tej samej sieci wirtualnej (VNet) lub grupy zasobów dla reguły zapory, aby włączyć te połączenia. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod, aby włączyć te typy połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie, można ustawić **zezwolić na dostęp do usług platformy Azure** opcji w celu **ON** w portalu z **zabezpieczenia połączeń** okienko i naciśnij klawisz **zapisać**. Jeśli próba połączenia nie jest dozwolone, żądanie nie osiąga Azure bazy danych dla serwera MySQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Skonfiguruj Zezwalaj na dostęp do usług platformy Azure w portalu](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz portalu Azure reguł zapory można zarządzać programowo przy użyciu wiersza polecenia platformy Azure. Zobacz też [tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych
Podczas dostępu do bazy danych Microsoft Azure dla usługi serwera MySQL działają zgodnie z oczekiwaniami, należy rozważyć następujące kwestie:

* **Zmiany na liście dozwolonych nie zostały uwzględnione jeszcze:** może być jak opóźnienie 5 minutową zmienia się z bazą danych Azure konfiguracji zapory serwera MySQL zaczęły obowiązywać.

* **Nazwa logowania nie jest autoryzowany lub niepoprawne hasło było używane:** nazwy logowania nie ma uprawnień w bazie danych Azure MySQL serwera lub hasło jest niepoprawne, odmowa połączenia z bazą danych Azure dla serwera MySQL. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń.

* **Dynamicznego adresu IP:** Jeśli masz połączenie internetowe z dynamicznych adresów IP i problemy z uzyskiwania przez zaporę, można wypróbować jedno z następujących rozwiązań:

* Zakres adresów IP, które są przypisane do komputerów klienckich łączących się z bazą danych Azure dla serwera MySQL poproś o usługodawcy internetowego (ISP), a następnie Dodaj zakres adresów IP reguły zapory.

* Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu portalu Azure](./howto-manage-firewall-using-portal.md)
[tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)
