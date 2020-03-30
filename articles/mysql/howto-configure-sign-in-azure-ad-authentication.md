---
title: Korzystanie z usługi Azure Active Directory — usługa Azure Database dla mysql
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory (Azure AD) do uwierzytelniania za pomocą usługi Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299009"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory za pomocą usługi MySQL

W tym artykule omówisz kroki konfigurowania dostępu usługi Azure Active Directory za pomocą usługi Azure Database for MySQL i jak połączyć się przy użyciu tokenu usługi Azure AD.

> [!IMPORTANT]
> Uwierzytelnianie usługi Azure AD dla usługi Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Ustawianie użytkownika administratora usługi Azure AD

Tylko użytkownik administratora usługi Azure AD może tworzyć/włączać użytkowników do uwierzytelniania opartego na usłudze Azure AD. Aby utworzyć i użytkownik usługi Azure AD Admin, wykonaj następujące kroki

1. W witrynie Azure portal wybierz wystąpienie usługi Azure Database dla mysql, które chcesz włączyć dla usługi Azure AD.
2. W obszarze Ustawienia wybierz pozycję Administrator usługi Active Directory:

![ustawianie administratora reklam platformy Azure][2]

3. Wybierz prawidłowego użytkownika usługi Azure AD w dzierżawie klienta, aby być administratorem usługi Azure AD.

> [!IMPORTANT]
> Podczas ustawiania administratora, nowy użytkownik jest dodawany do usługi Azure Database dla serwera MySQL z pełnymi uprawnieniami administratora.

Tylko jeden administrator usługi Azure AD można utworzyć na serwerze MySQL i wybór innego zastąpi istniejącego administratora usługi Azure AD skonfigurowany dla serwera.

W przyszłej wersji będziemy obsługiwać określanie grupy usługi Azure AD zamiast pojedynczego użytkownika, aby mieć wielu administratorów, jednak obecnie nie jest to jeszcze obsługiwane.

Po skonfigurowaniu administratora możesz teraz zalogować się:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Łączenie się z usługą Azure Database dla mysql przy użyciu usługi Azure AD

Poniższy diagram wysokiego poziomu podsumowuje przepływ pracy przy użyciu uwierzytelniania usługi Azure AD z usługą Azure Database for MySQL:

![przepływ uwierzytelniania][1]

Zaprojektowaliśmy integrację usługi Azure AD do pracy ze typowymi narzędziami MySQL, takimi jak mysql CLI, które nie są obsługują usługę Azure AD i obsługują tylko określanie nazwy użytkownika i hasła podczas łączenia się z MySQL. Przekazujemy token usługi Azure AD jako hasło, jak pokazano na powyższym rysunku.

Obecnie przetestowaliśmy następujących klientów:

- MySQLWorkbench 
- Mysql CLI

Przetestowaliśmy również większość typowych sterowników aplikacji, możesz zobaczyć szczegóły na końcu tej strony.

Są to kroki, które użytkownik/aplikacja będzie musiał zrobić uwierzytelnić za pomocą usługi Azure AD opisane poniżej:

### <a name="step-1-authenticate-with-azure-ad"></a>Krok 1: Uwierzytelnij się za pomocą usługi Azure AD

Upewnij się, że masz [zainstalowany plik CLI platformy Azure](/cli/azure/install-azure-cli).

Wywoływanie narzędzia interfejsu wiersza polecenia platformy Azure w celu uwierzytelniania za pomocą usługi Azure AD. Wymaga podania identyfikatora użytkownika usługi Azure AD i hasła.

```
az login
```

To polecenie uruchomi okno przeglądarki na stronie uwierzytelniania usługi Azure AD.

> [!NOTE]
> Aby wykonać te kroki, można również użyć usługi Azure Cloud Shell.
> Należy pamiętać, że podczas pobierania tokenu dostępu usługi Azure AD w `az login` usłudze Azure Cloud Shell należy jawnie wywołać i zalogować się ponownie (w osobnym oknie z kodem). Po tym zaloguje się polecenie będzie działać zgodnie z `get-access-token` oczekiwaniami.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2: Pobieranie tokenu dostępu usługi Azure AD

Wywołaj narzędzie interfejsu wiersza polecenia platformy Azure, aby uzyskać token dostępu dla uwierzytelnionego użytkownika usługi Azure AD z kroku 1 do dostępu do bazy danych platformy Azure dla mysql.

Przykład (dla chmury publicznej):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Powyższa wartość zasobu musi być określona dokładnie tak, jak pokazano. W przypadku innych chmur wartość zasobu można sprawdzić za pomocą:

```shell
az cloud show
```

W przypadku interfejsu wiersza polecenia platformy Azure w wersji 2.0.71 i nowszej można określić polecenie w następującej, wygodniejszej wersji dla wszystkich chmur:

```shell
az account get-access-token --resource-type oss-rdbms
```

Po pomyślnym uwierzytelnieniu usługa Azure AD zwróci token dostępu:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Token jest ciągiem Base 64, który koduje wszystkie informacje o uwierzytelnionym użytkowniku i który jest przeznaczony dla usługi Azure Database for MySQL.

> [!NOTE]
> Ważność tokenu dostępu wynosi od 5 minut do 60 minut. Zalecamy uzyskanie tokenu dostępu tuż przed zainicjowaniem logowania do usługi Azure Database dla MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Krok 3: Użyj tokenu jako hasła do logowania się za pomocą MySQL

Podczas łączenia należy użyć tokenu dostępu jako hasła użytkownika MySQL. Podczas korzystania z klientów GUI, takich jak MySQLWorkbench, można użyć powyższej metody, aby pobrać token. 

Podczas korzystania z interfejsu wiersza polecenia można użyć tej krótkiej ręki do połączenia: 

**Przykład (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Zwróć uwagę na ustawienie "enable-cleartext-plugin" — musisz użyć podobnej konfiguracji z innymi klientami, aby upewnić się, że token zostanie wysłany do serwera bez mieszania.

Teraz uwierzytelniony na serwerze MySQL przy użyciu uwierzytelniania usługi Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Tworzenie użytkowników usługi Azure AD w bazie danych platformy Azure dla mysql

Aby dodać użytkownika usługi Azure AD do bazy danych usługi Azure dla mysql, wykonaj następujące kroki po nawiązaniu połączenia (zobacz sekcję nowszą dotyczącą sposobu nawiązania połączenia):

1. Najpierw upewnij się, `<user>@yourtenant.onmicrosoft.com` że użytkownik usługi Azure AD jest prawidłowym użytkownikiem w dzierżawie usługi Azure AD.
2. Zaloguj się do usługi Azure Database dla wystąpienia MySQL jako użytkownik administratora usługi Azure AD.
3. Tworzenie `<user>@yourtenant.onmicrosoft.com` użytkownika w usłudze Azure Database dla mysql.

**Przykład:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

W przypadku nazw użytkowników przekraczających 32 znaki zaleca się używanie aliasu, który ma być używany podczas łączenia: 

Przykład:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Uwierzytelnianie użytkownika za pośrednictwem usługi Azure AD nie daje użytkownikowi żadnych uprawnień dostępu do obiektów w bazie danych usługi Azure Database dla mysql bazy danych. Należy ręcznie udzielić użytkownikowi wymaganych uprawnień.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Tworzenie grup usługi Azure AD w bazie danych platformy Azure dla mysql

Aby włączyć grupę usługi Azure AD w celu uzyskania dostępu do bazy danych, użyj tego samego mechanizmu, co dla użytkowników, ale zamiast tego określ nazwę grupy:

**Przykład:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Podczas logowania członkowie grupy będą używać tokenów dostępu osobistego, ale podpisują nazwę grupy określoną jako nazwa użytkownika.

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

Uwierzytelnianie usługi Azure AD w usłudze Azure Database for MySQL zapewnia, że użytkownik istnieje na serwerze MySQL i sprawdza ważność tokenu, sprawdzając poprawność zawartości tokenu. Wykonywane są następujące kroki sprawdzania poprawności tokenu:

-   Token jest podpisany przez usługę Azure AD i nie został zmodyfikowany
-   Token został wystawiony przez usługę Azure AD dla dzierżawy skojarzonej z serwerem
-   Token nie wygasł
-   Token jest przeznaczony dla usługi Azure Database dla zasobu MySQL (a nie innego zasobu platformy Azure)

## <a name="compatibility-with-application-drivers"></a>Zgodność ze sterownikami aplikacji

Większość sterowników są obsługiwane, jednak upewnij się, że używasz ustawień do wysyłania hasła w postaci zwykłego tekstu, więc token zostanie wysłany bez modyfikacji.

* C/C++
  * libmysqlclient: Obsługiwane
  * mysql-connector-c++: Obsługiwane
* Java
  * Złącze/J (mysql-connector-java): Obsługiwane, `useSSL` musi korzystać z ustawienia
* Python
  * Łącznik/Python: Obsługiwane
* Ruby
  * mysql2: Obsługiwane
* .NET
  * mysql-connector-net: Obsługiwane, trzeba dodać plugin do mysql_clear_password
  * mysql-net/MySqlConnector: Obsługiwane
* Node.js
  * mysqljs: Nie obsługiwane (nie wysyła tokenu w cleartext bez patcha)
  * node-mysql2: Obsługiwane
* Perl
  * DBD::mysql: Obsługiwane
  * Sieć::MySQL: Nie jest obsługiwana
* Przejdź
  * go-sql-driver: Obsługiwane, `?tls=true&allowCleartextPasswords=true` dodawanie do ciągu połączenia

## <a name="next-steps"></a>Następne kroki

* Przejrzyj ogólne pojęcia [uwierzytelniania usługi Azure Active Directory za pomocą usługi Azure Database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
