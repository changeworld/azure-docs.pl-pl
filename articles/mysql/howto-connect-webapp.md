---
title: Łączenie się z usługą Azure App Service — usługa Azure Database for MySQL
description: Instrukcje dotyczące prawidłowego łączenia istniejącej usługi Azure App Service z usługą Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062464"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Łączenie istniejącej usługi Azure App Service z usługą Azure Database dla serwera MySQL
W tym temacie wyjaśniono, jak połączyć istniejącą usługę Azure App Service z usługą Azure Database dla serwera MySQL.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się do [Portalu Azure](https://portal.azure.com). Utwórz usługę Azure Database dla serwera MySQL. Aby uzyskać szczegółowe informacje, zobacz [Jak utworzyć usługę Azure Database dla serwera MySQL z portalu](quickstart-create-mysql-server-database-using-azure-portal.md) lub Jak utworzyć usługę Azure Database dla serwera [MySQL przy użyciu interfejsu WIERSZA POLECENIA.](quickstart-create-mysql-server-database-using-azure-cli.md)

Obecnie istnieją dwa rozwiązania umożliwiające dostęp z usługi Azure App Service do bazy danych platformy Azure dla MySQL. Oba rozwiązania obejmują konfigurowanie reguł zapory na poziomie serwera.

## <a name="solution-1---allow-azure-services"></a>Rozwiązanie 1 — zezwalaj na usługi platformy Azure
Usługa Azure Database for MySQL zapewnia zabezpieczenia dostępu przy użyciu zapory w celu ochrony danych. Podczas łączenia się z usługi Azure App Service do usługi Azure Database dla serwera MySQL, należy pamiętać, że wychodzące adresy IP usługi App Service mają charakter dynamiczny. Wybranie opcji "Zezwalaj na dostęp do usług platformy Azure" umożliwi usłudze aplikacji łączenie się z serwerem MySQL.

1. W bloku serwera MySQL w obszarze Nagłówek Ustawienia kliknij pozycję **Zabezpieczenia połączenia,** aby otworzyć blok Zabezpieczeń połączeń dla usługi Azure Database for MySQL.

   ![Portal Azure — kliknij pozycję Zabezpieczenia połączeń](./media/howto-connect-webapp/1-connection-security.png)

2. Wybierz **wł. w** **obszarze Zezwalaj na dostęp do usług platformy Azure**, a następnie **zapisz**.
   ![Witryna azure portal — zezwalaj na dostęp do platformy Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Rozwiązanie 2 — tworzenie reguły zapory w celu jawnego zezwalania na wychodzące serwery IP
Możesz jawnie dodać wszystkie wychodzące usługi IP usługi Azure App Service.

1. W bloku Właściwości usługi aplikacji wyświetl **wychodzący adres IP**.

   ![Witryna Azure portal — wyświetlanie wychodzących obiektów IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. W bloku zabezpieczeń Połączenie MySQL dodaj wychodzące ustawienia IP jeden po drugim.

   ![Witryna Azure portal — dodawanie jawnych usług IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Pamiętaj, **aby zapisać** reguły zapory.

Chociaż usługa aplikacji Azure próbuje zachować stałe adresy IP w czasie, istnieją przypadki, w których adresy IP mogą ulec zmianie. Na przykład może to nastąpić, gdy aplikacja odtwarza lub występuje operacja skalowania lub gdy nowe komputery są dodawane w regionalnych centrach danych platformy Azure w celu zwiększenia pojemności. Po zmianie adresów IP aplikacja może wystąpić przestój w przypadku, gdy nie może już połączyć się z serwerem MySQL. Należy pamiętać o tym, wybierając jedno z powyższych rozwiązań.

## <a name="ssl-configuration"></a>Konfiguracja SSL
Usługa Azure Database for MySQL ma domyślnie włączony protokół SSL. Jeśli aplikacja nie używa SSL do łączenia się z bazą danych, należy wyłączyć ssl na serwerze MySQL. Aby uzyskać szczegółowe informacje na temat konfigurowania ssl, zobacz [Korzystanie z SSL z usługą Azure Database for MySQL](howto-configure-ssl.md).

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

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ciągów połączeń, zobacz [Parametry połączenia](howto-connection-string.md).
