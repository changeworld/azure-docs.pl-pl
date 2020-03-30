---
title: Reguły zapory — usługa Azure Database for MySQL
description: Dowiedz się więcej o używaniu reguł zapory w celu umożliwienia połączeń z usługą Azure Database dla serwera MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: a82d2317314c79a82fe80c5a25afc950fb728815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155200"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Reguły zapory serwera usługi Azure Database for MySQL
Zapory uniemożliwiają dostęp do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zapora udziela dostępu do serwera na podstawie źródłowego adresu IP każdego żądania.

Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całej usługi Azure Database dla serwera MySQL, czyli wszystkich baz danych w ramach tego samego serwera logicznego. Reguły zapory na poziomie serwera można skonfigurować przy użyciu poleceń witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Dostęp do bazy danych usługi Azure Database dla serwera MySQL jest domyślnie blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, który adres IP ma na to pozwalać. Na dostęp do samej witryny sieci Web witryny azure portal nie mają wpływu reguły zapory.

Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim będą mogły dotrzeć do bazy danych usługi Azure Database dla mysql, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na serwerze Usługi Azure Database for MySQL.

Jeśli adres IP żądania mieści się w jednym z zakresów określonych w regułach zapory na poziomie serwera, połączenie zostanie udzielone.

Jeśli adres IP żądania znajduje się poza zakresami określonymi w dowolnej z reguł zapory na poziomie bazy danych lub serwera, żądanie połączenia nie powiedzie się.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Zaleca się znalezienie wychodzącego adresu IP dowolnej aplikacji lub usługi i jawne zezwolenie na dostęp do tych indywidualnych adresów IP lub zakresów. Na przykład można znaleźć wychodzący adres IP usługi Azure App Service lub użyć publicznego adresu IP powiązanego z maszyną wirtualną lub innym zasobem (zobacz poniżej, aby uzyskać informacje na temat łączenia się z prywatnym adresem IP maszyny wirtualnej za pomocą punktów końcowych usługi). 

Jeśli stały wychodzący adres IP nie jest dostępny dla usługi platformy Azure, można rozważyć włączenie połączeń ze wszystkich adresów IP centrum danych platformy Azure. To ustawienie można włączyć w witrynie Azure portal, ustawiając opcję **Zezwalaj na dostęp do usług platformy Azure** na **Wł.** z **okienka zabezpieczeń Połączenie** i naciskając przycisk **Zapisz**. W usłudze Azure CLI ustawienie reguły zapory z adresem początkowym i końcowym równym 0.0.0.0 robi równoważne. Jeśli próba połączenia jest niedozwolona, żądanie nie dociera do bazy danych platformy Azure dla serwera MySQL.

> [!IMPORTANT]
> Opcja **Zezwalaj na dostęp do usług platformy Azure** konfiguruje zaporę, aby zezwalać na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie zezwalaj na dostęp do usług platformy Azure w portalu](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Łączenie z sieci wirtualnej
Aby bezpiecznie połączyć się z usługą Azure Database dla serwera MySQL z sieci wirtualnej, należy rozważyć użycie [punktów końcowych usługi sieci wirtualnej.](./concepts-data-access-and-security-vnet.md) 

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz witryny Azure portal reguły zapory mogą być zarządzane programowo przy użyciu interfejsu wiersza polecenia platformy Azure. Zobacz też [Tworzenie reguł zapory usługi Azure Database dla usługi MySQL i zarządzanie nią przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Rozwiązywanie problemów z zaporą
Należy wziąć pod uwagę następujące kwestie, gdy dostęp do usługi serwera Microsoft Azure Database for MySQL nie działa zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie weszły jeszcze w życie:** Może być aż pięć minut opóźnienia dla zmian w usłudze Azure Database for MySQL Server konfiguracji zapory, aby wejść w życie.

* **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:** Jeśli login nie ma uprawnień do usługi Azure Database dla serwera MySQL lub hasło używane jest niepoprawne, połączenie z usługą Azure Database dla serwera MySQL zostanie odrzucone. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń.

* **Dynamiczny adres IP:** Jeśli masz połączenie internetowe z dynamicznym adresatem IP i masz problemy z przedostaniem się przez zaporę, możesz wypróbować jedno z następujących rozwiązań:

   * Zapytaj usługodawcę internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do usługi Azure Database dla serwera MySQL, a następnie dodaj zakres adresów IP jako regułę zapory.

   * Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

* **Adres IP serwera wydaje się być publiczny:** Połączenia z usługą Azure Database dla serwera MySQL są kierowane za pośrednictwem publicznie dostępnej bramy platformy Azure. Rzeczywisty adres IP serwera jest jednak chroniony przez zaporę. Aby uzyskać więcej informacji, odwiedź [artykuł o architekturze łączności](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory usługi Azure Database dla mysql i zarządzanie nimi przy użyciu witryny Azure portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory usługi Azure Database dla mysql i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)
- [Punkty końcowe usługi sieci wirtualnej w bazie danych platformy Azure dla mysql](./concepts-data-access-and-security-vnet.md)
