---
title: Reguły zapory — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak używać reguł zapory do łączenia się z usługą Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157274"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Reguły zapory w usłudze Azure Database dla postgreSQL — pojedynczy serwer
Zapora serwera Usługi Azure Database for PostgreSQL uniemożliwia dostęp do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zapora udziela dostępu do serwera na podstawie źródłowego adresu IP każdego żądania.
Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całej usługi Azure Database for PostgreSQL Server, czyli wszystkich baz danych w ramach tego samego serwera logicznego. Reguły zapory na poziomie serwera można skonfigurować przy użyciu witryny Azure portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Cały dostęp do bazy danych usługi Azure Database dla serwera PostgreSQL jest domyślnie blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, który adres IP ma na to pozwalać. Na dostęp do samej witryny sieci Web witryny azure portal nie mają wpływu reguły zapory.
Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim będą mogły dotrzeć do bazy danych PostgreSQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na tym samym serwerze usługi Azure Database for PostgreSQL. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, ustanawiane jest połączenie.
Jeśli adres IP żądania nie mieści się w zakresach określonych w żadnej z reguł zapory na poziomie serwera, żądanie połączenia kończy się niepowodzeniem.
Na przykład jeśli aplikacja łączy się ze sterownikiem JDBC dla PostgreSQL, może wystąpić ten błąd próbuje połączyć się, gdy zapora blokuje połączenie.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Zaleca się znalezienie wychodzącego adresu IP dowolnej aplikacji lub usługi i jawne zezwolenie na dostęp do tych indywidualnych adresów IP lub zakresów. Na przykład można znaleźć wychodzący adres IP usługi Azure App Service lub użyć publicznego adresu IP powiązanego z maszyną wirtualną lub innym zasobem (zobacz poniżej, aby uzyskać informacje na temat łączenia się z prywatnym adresem IP maszyny wirtualnej za pomocą punktów końcowych usługi). 

Jeśli stały wychodzący adres IP nie jest dostępny dla usługi platformy Azure, można rozważyć włączenie połączeń ze wszystkich adresów IP centrum danych platformy Azure. To ustawienie można włączyć w witrynie Azure portal, ustawiając opcję **Zezwalaj na dostęp do usług platformy Azure** na **Wł.** z **okienka zabezpieczeń Połączenie** i naciskając przycisk **Zapisz**. W usłudze Azure CLI ustawienie reguły zapory z adresem początkowym i końcowym równym 0.0.0.0 robi równoważne. Jeśli próba połączenia jest niedozwolona, żądanie nie dociera do usługi Azure Database dla serwera PostgreSQL.

> [!IMPORTANT]
> Opcja **Zezwalaj na dostęp do usług platformy Azure** konfiguruje zaporę, aby zezwalać na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie zezwalaj na dostęp do usług platformy Azure w portalu](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Łączenie z sieci wirtualnej
Aby bezpiecznie połączyć się z serwerem usługi Azure Database for PostgreSQL z sieci wirtualnej, należy rozważyć użycie [punktów końcowych usługi sieci wirtualnej.](./concepts-data-access-and-security-vnet.md) 

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz witryny Azure portal reguły zapory można zarządzać programowo przy użyciu interfejsu wiersza polecenia platformy Azure.
Zobacz też [Tworzenie reguł zapory usługi Azure Database dla postgreSQL i zarządzanie nią przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Rozwiązywanie problemów z zaporą
Należy wziąć pod uwagę następujące punkty, gdy dostęp do usługi Microsoft Azure Database for PostgreSQL Server nie działa zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie weszły jeszcze w życie:** Może być aż pięć minut opóźnienia dla zmian w usłudze Azure Database dla konfiguracji zapory serwera PostgreSQL, aby wejść w życie.

* **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:** Jeśli login nie ma uprawnień do usługi Azure Database dla serwera PostgreSQL lub hasło używane jest niepoprawne, połączenie z usługą Azure Database for PostgreSQL serwera jest odrzucane. Utworzenie ustawienia zapory umożliwia tylko klientom możliwość nawiązania próby nawiązania połączenia z serwerem; każdy klient musi nadal podać niezbędne poświadczenia zabezpieczeń.

   Na przykład przy użyciu klienta JDBC może pojawić się następujący błąd.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: uwierzytelnianie hasłem nie powiodło się dla użytkownika "yourusername"

* **Dynamiczny adres IP:** jeśli używane jest połączenie internetowe za pomocą dynamicznego adresowania IP i występują problemy z przejściem przez zaporę, można wypróbować jedno z poniższych rozwiązań:

   * Zapytaj usługodawcę internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do usługi Azure Database dla serwera PostgreSQL, a następnie dodaj zakres adresów IP jako regułę zapory.

   * Pobierz statyczne adresy IP zamiast dla komputerów klienckich, a następnie dodaj statyczny adres IP jako regułę zapory.

* **Adres IP serwera wydaje się być publiczny:** Połączenia z usługą Azure Database for PostgreSQL są kierowane za pośrednictwem publicznie dostępnej bramy platformy Azure. Rzeczywisty adres IP serwera jest jednak chroniony przez zaporę. Aby uzyskać więcej informacji, odwiedź [artykuł o architekturze łączności](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z artykułami dotyczącymi tworzenia reguł zapory na poziomie serwera i bazy danych, zobacz:
* [Tworzenie reguł zapory usługi Azure Database dla postgreSQL i zarządzanie nimi przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory usługi Azure Database dla postgreSQL i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md)
- [Punkty końcowe usługi sieci wirtualnej w bazie danych platformy Azure dla postgreSQL](./concepts-data-access-and-security-vnet.md)
