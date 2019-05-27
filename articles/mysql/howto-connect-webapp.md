---
title: Łączenie z istniejącej usługi Azure App Service do usługi Azure Database for MySQL
description: Instrukcje dotyczące nawiązywania prawidłowo istniejących w usłudze Azure App Service do usługi Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/21/2019
ms.openlocfilehash: 3fbffc805afb540499e38f1c0853260968228b22
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002005"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Łączenie istniejących w usłudze Azure App Service do usługi Azure Database dla serwera MySQL
W tym temacie wyjaśniono, jak połączyć istniejące w usłudze Azure App Service do usługi Azure Database for MySQL server.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenie usługi Azure Database dla serwera MySQL. Aby uzyskać szczegółowe informacje, zapoznaj się [sposób tworzenia usługi Azure Database dla serwera MySQL z witryny Portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [sposób tworzenia usługi Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia](quickstart-create-mysql-server-database-using-azure-cli.md).

Obecnie istnieją dwa rozwiązania umożliwiające dostęp z usługi Azure App Service do usługi Azure Database for MySQL. Oba rozwiązania wymagają konfigurowania reguł zapory na poziomie serwera.

## <a name="solution-1---allow-azure-services"></a>Rozwiązanie 1 — Zezwalaj usługom platformy Azure
Usługa Azure Database for MySQL zapewnia zabezpieczenia dostępu do używania innej zapory, aby chronić dane. Podczas nawiązywania połączenia z usługi Azure App Service — Azure Database dla serwera MySQL, należy pamiętać o tym, że adresy IP ruchu wychodzącego usług App Service ma dynamiczny charakter. Wybranie opcji "Zezwalaj na dostęp do usług platformy Azure" umożliwi app service w celu łączenia się z serwerem MySQL.

1. W bloku serwera MySQL, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć blok zabezpieczenia połączeń dla usługi Azure Database for MySQL.

   ![Portal Azure — zabezpieczenia połączeń kliknij](./media/howto-connect-webapp/1-connection-security.png)

2. Wybierz **ON** w **zezwolić na dostęp do usług platformy Azure**, następnie **Zapisz**.
   ![Portal Azure — dostęp do Zezwalaj na platformie Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Rozwiązanie 2 — Tworzenie reguły zapory, aby jawnie zezwolić na adresy IP ruchu wychodzącego
Można jawnie dodać wszystkie wychodzące adresy IP usługi Azure App Service.

1. W bloku właściwości usługi aplikacji, wyświetlić swoje **WYCHODZĄCY adres IP**.

   ![Witryna Azure portal — widok wychodzące adresy IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. W bloku zabezpieczeń połączenia z serwerem MySQL dodać wychodzące adresy IP jedno po drugim.

   ![Witryna Azure portal — Dodawanie jawnych adresów IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Pamiętaj, aby **Zapisz** reguły zapory.

Chociaż usługa Azure App service próbuje zachować adresy IP stałych wraz z upływem czasu, istnieją przypadki, w których adresy IP mogą ulec zmianie. Na przykład, to może wystąpić, gdy występuje odtwarzanie aplikacji lub operację skalowania lub kiedy nowe komputery są dodawane w danych platformy Azure regionalnych centrów w celu zwiększenia pojemności. W przypadku zmiany adresów IP aplikacji mogą wystąpić przestój w przypadku, gdy nie można już połączyć z serwerem MySQL. Wybierając jedną z poprzedniego rozwiązania, należy pamiętać o tym zagadnieniem.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL
Usługa Azure Database for MySQL jest domyślnie włączony protokół SSL. Jeśli aplikacja nie używa protokołu SSL do łączenia z bazą danych, musisz wyłączyć protokół SSL na serwerze MySQL. Aby uzyskać więcej informacji na temat konfigurowania protokołu SSL, zobacz [przy użyciu protokołu SSL z usługą Azure Database for MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat parametrów połączenia dotyczą [parametry połączenia](howto-connection-string.md).
