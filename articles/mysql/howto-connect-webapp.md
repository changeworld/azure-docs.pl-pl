---
title: Istniejąca usługa aplikacji Azure połączyć się z bazą danych Azure dla programu MySQL
description: Instrukcje dotyczące sposobu prawidłowo istniejącej usługi aplikacji Azure połączyć się z bazą danych Azure dla programu MySQL
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d8b130876e5fa0f2b2322dff82013a409ff7d30e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Nawiązać MySQL serwera bazy danych Azure istniejącej usługi aplikacji Azure
W tym temacie wyjaśniono, jak połączyć istniejącej usługi aplikacji Azure do bazy danych Azure, serwer MySQL.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się do witryny [Azure Portal](https://portal.azure.com). Utwórz bazę danych Azure dla serwera MySQL. Aby uzyskać więcej informacji, zapoznaj się [tworzenie bazy danych platformy Azure dla serwera MySQL z portalu](quickstart-create-mysql-server-database-using-azure-portal.md) lub [tworzenie bazy danych platformy Azure dla serwera MySQL przy użyciu interfejsu wiersza polecenia](quickstart-create-mysql-server-database-using-azure-cli.md).

Obecnie istnieją dwa rozwiązania umożliwiające dostęp do bazy danych Azure z usługi Azure App Service dla programu MySQL. Oba rozwiązania wymagają konfigurowania reguł zapory na poziomie serwera.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>1 — rozwiązanie Tworzenie reguły zapory zezwalająca na wszystkie adresy IP
Bazy danych platformy Azure dla programu MySQL zapewnia zabezpieczenia dostępu przy użyciu zapory, aby chronić dane. Podczas nawiązywania połączenia z usługi aplikacji Azure Azure bazy danych MySQL serwera, należy pamiętać, że wychodzących adresów IP usługi aplikacji są dynamiczne charakter. 

Aby zapewnić dostępność usługi Azure App Service, firma Microsoft zaleca używanie tego rozwiązania zezwalająca na wszystkie adresy IP.

> [!NOTE]
> Firma Microsoft pracuje dłuższy czas, aby uniknąć, dzięki czemu wszystkie adresy IP dla usług Azure nawiązać połączenia z bazą danych Azure dla programu MySQL.

1. W bloku serwera MySQL, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć blok połączenia zabezpieczeń bazy danych Azure dla programu MySQL.

   ![Portal Azure — kliknij przycisk Zabezpieczenia połączeń](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Wprowadź **Nazwa reguły**, **START IP**, i **KOŃCOWEMU adresowi IP**, a następnie kliknij przycisk **zapisać**.
   - Nazwa reguły: Zezwalaj-All-adresów IP
   - Uruchom IP: 0.0.0.0
   - Zakończenie IP: 255.255.255.255

   ![Portal Azure — Dodaj wszystkie adresy IP](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>2 — rozwiązanie utworzyć regułę zapory, aby jawnie zezwolić na wychodzący adresów IP
Musisz jawnie dodać wszystkie wychodzące adresy IP z usługi aplikacji platformy Azure.

1. W bloku aplikacji usługi właściwości wyświetlania Twojego **WYCHODZĄCY adres IP**.

   ![Portal Azure — widok wychodzących adresów IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. W bloku zabezpieczeń połączenia MySQL Dodaj wychodzących adresów IP pojedynczo.

   ![Portal Azure — Dodaj jawne adresów IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Pamiętaj, aby **zapisać** reguł zapory.

Chociaż usługa aplikacji Azure próbuje zachować adresy IP stałej wraz z upływem czasu, istnieją przypadki, w którym może zmienić adresów IP. Na przykład, taka sytuacja może wystąpić podczas odtwarzania aplikacji lub operacji skalowania lub dodania nowych komputerów w danych Azure regionalnych centrach w celu zwiększenia pojemności. Podczas zmiany adresów IP, aplikacja może przestój w przypadku, gdy nie można już połączyć z serwerem MySQL. W przypadku wybrania jednej z opisanych rozwiązań warto pamiętać o tego.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL
Azure bazy danych dla programu MySQL jest domyślnie włączony protokół SSL. Jeśli aplikacja nie używa protokołu SSL do połączenia z bazą danych, należy wyłączyć protokół SSL na serwerze programu MySQL. Aby uzyskać więcej informacji na temat konfigurowania protokołu SSL, zobacz [przy użyciu protokołu SSL z bazą danych Azure dla programu MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących parametrów połączenia, zapoznaj się [parametry połączenia](howto-connection-string.md).
