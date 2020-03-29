---
title: Reguły zapory — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: W tym artykule opisano reguły zapory dla usługi Azure Database for PostgreSQL — Hiperskala (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975571"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Reguły zapory w usłudze Azure Database for PostgreSQL — hiperskala (Citus)
Zapora serwera Usługi Azure Database for PostgreSQL uniemożliwia dostęp do węzła koordynatora hiperskali (Citus), dopóki nie określisz, które komputery mają uprawnienia. Zapora udziela dostępu do serwera na podstawie źródłowego adresu IP każdego żądania.
Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do węzła koordynatora hiperskali (Citus), czyli wszystkich baz danych w obrębie tego samego serwera logicznego. Reguły zapory na poziomie serwera można skonfigurować przy użyciu witryny Azure portal. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Cały dostęp do bazy danych do węzła koordynatora jest domyślnie blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, który adres IP ma na to pozwalać. Na dostęp do samej witryny sieci Web witryny azure portal nie mają wpływu reguły zapory.
Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim będą mogły dotrzeć do bazy danych PostgreSQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Łączenie się z Internetu i platformy Azure

Zapora grupy serwerów o zmiennym skali (Citus) określa, kto może łączyć się z węzłem koordynatora grupy. Zapora określa dostęp, korzystając z konfigurowalnej listy reguł. Każda reguła jest adresem IP lub zakresem adresów, które są dozwolone w.

Gdy zapora blokuje połączenia, może powodować błędy aplikacji. Na przykład użycie sterownika JDBC PostgreSQL powoduje wyświetlenie następującego błędu:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

Zobacz [Tworzenie reguł zapory i zarządzanie nimi,](howto-hyperscale-manage-firewall-using-portal.md) aby dowiedzieć się, jak zdefiniowano reguły.

## <a name="troubleshooting-the-database-server-firewall"></a>Rozwiązywanie problemów z zaporą serwera bazy danych
Gdy dostęp do usługi Microsoft Azure Database for PostgreSQL — Hyperscale (Citus) nie działa zgodnie z oczekiwaniami, należy wziąć pod uwagę następujące kwestie:

* **Zmiany na liście dozwolonych nie weszły jeszcze w życie:** Może wystąpić nawet pięciominutowe opóźnienie, aby zmiany w konfiguracji zapory w skali hiperskali (Citus) zostały zastosowane.

* **Użytkownik nie jest autoryzowany lub użyto nieprawidłowego hasła:** Jeśli użytkownik nie ma uprawnień na serwerze lub używane hasło jest niepoprawne, połączenie z serwerem zostanie odrzucone. Utworzenie ustawienia zapory umożliwia tylko klientom możliwość nawiązania próby nawiązania połączenia z serwerem; każdy klient musi nadal podać niezbędne poświadczenia zabezpieczeń.

Na przykład przy użyciu klienta JDBC może pojawić się następujący błąd.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: uwierzytelnianie hasłem nie powiodło się dla użytkownika "yourusername"

* **Dynamiczny adres IP:** jeśli używane jest połączenie internetowe za pomocą dynamicznego adresowania IP i występują problemy z przejściem przez zaporę, można wypróbować jedno z poniższych rozwiązań:

* Zapytaj usługodawcę internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do węzła koordynatora hiperskali (Citus), a następnie dodaj zakres adresów IP jako regułę zapory.

* Pobierz statyczne adresy IP zamiast dla komputerów klienckich, a następnie dodaj statyczny adres IP jako regułę zapory.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z artykułami dotyczącymi tworzenia reguł zapory na poziomie serwera i bazy danych, zobacz:
* [Tworzenie reguł zapory usługi Azure Database dla postgreSQL i zarządzanie nimi przy użyciu witryny Azure portal](howto-hyperscale-manage-firewall-using-portal.md)
