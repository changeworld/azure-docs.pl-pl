---
title: Korzystanie z usługi Azure Active Directory — usługa Azure Database for PostgreSQL — single server
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory (AAD) do uwierzytelniania za pomocą usługi Azure Database for PostgreSQL — single server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804711"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Uwierzytelnianie za pomocą usługi PostgreSQL za pomocą usługi Azure Active Directory

W tym artykule omówisz kroki konfigurowania dostępu usługi Azure Active Directory za pomocą usługi Azure Database for PostgreSQL oraz sposobu łączenia się przy użyciu tokenu usługi Azure AD.

> [!IMPORTANT]
> Uwierzytelnianie usługi Azure AD dla usługi Azure Database for PostgreSQL jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Ustawianie użytkownika administratora usługi Azure AD

Tylko użytkownicy administratora usługi Azure AD mogą tworzyć/włączać użytkowników do uwierzytelniania opartego na usłudze Azure AD. Firma Microsoft zaleca, aby nie używać administratora usługi Azure AD dla regularnych operacji bazy danych, ponieważ ma podwyższone uprawnienia użytkownika (np. CREATEDB).

Aby ustawić administratora usługi Azure AD (można użyć użytkownika lub grupy), wykonaj następujące kroki

1. W witrynie Azure portal wybierz wystąpienie usługi Azure Database dla postgreSQL, które chcesz włączyć dla usługi Azure AD.
2. W obszarze Ustawienia wybierz pozycję Administrator usługi Active Directory:

![ustawianie administratora reklam platformy Azure][2]

3. Wybierz prawidłowego użytkownika usługi Azure AD w dzierżawie klienta, aby być administratorem usługi Azure AD.

> [!IMPORTANT]
> Podczas ustawiania administratora, nowy użytkownik jest dodawany do usługi Azure Database dla serwera PostgreSQL z pełnymi uprawnieniami administratora. Użytkownik administratora usługi Azure AD w bazie danych platformy `azure_ad_admin`Azure dla postgreSQL będzie miał rolę .

Tylko jeden administrator usługi Azure AD można utworzyć na serwer postgresql i wybór innego zastąpi istniejącego administratora usługi Azure AD skonfigurowany dla serwera. Można określić grupę usługi Azure AD zamiast pojedynczego użytkownika, aby mieć wielu administratorów. Należy pamiętać, że następnie zalogujesz się przy tej nazwie do celów administracyjnych.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Łączenie się z usługą Azure Database dla postgreSQL przy użyciu usługi Azure AD

Poniższy diagram wysokiego poziomu podsumowuje przepływ pracy przy użyciu uwierzytelniania usługi Azure AD z usługą Azure Database for PostgreSQL:

![przepływ uwierzytelniania][1]

Zaprojektowaliśmy integrację usługi Azure AD do pracy ze typowymi narzędziami PostgreSQL, takimi jak psql, które nie są obsługują usługi Azure AD i obsługują tylko określanie nazwy użytkownika i hasła podczas łączenia się z postgreSQL. Przekazujemy token usługi Azure AD jako hasło, jak pokazano na powyższym rysunku.

Obecnie przetestowaliśmy następujących klientów:

- psql commandline (wykorzystać zmienną PGPASSWORD przekazać token, patrz poniżej)
- Usługa Azure Data Studio (przy użyciu rozszerzenia PostgreSQL)
- Inni klienci z siedzibą w libpq (np. wspólne struktury aplikacji i ORM)

> [!NOTE]
> Należy pamiętać, że przy użyciu tokenu usługi Azure AD z pgAdmin nie jest obecnie obsługiwany, ponieważ ma zakodowane ograniczenie 256 znaków dla haseł (który przekracza token).

Są to kroki, które użytkownik/aplikacja będzie musiał zrobić uwierzytelnić za pomocą usługi Azure AD opisane poniżej:

### <a name="step-1-authenticate-with-azure-ad"></a>Krok 1: Uwierzytelnij się za pomocą usługi Azure AD

Upewnij się, że masz [zainstalowany plik CLI platformy Azure](/cli/azure/install-azure-cli).

Wywoływanie narzędzia interfejsu wiersza polecenia platformy Azure w celu uwierzytelniania za pomocą usługi Azure AD. Wymaga podania identyfikatora użytkownika usługi Azure AD i hasła.

```azurecli-interactive
az login
```

To polecenie uruchomi okno przeglądarki na stronie uwierzytelniania usługi Azure AD.

> [!NOTE]
> Aby wykonać te kroki, można również użyć usługi Azure Cloud Shell.
> Należy pamiętać, że podczas pobierania tokenu dostępu usługi Azure AD w `az login` usłudze Azure Cloud Shell należy jawnie wywołać i zalogować się ponownie (w osobnym oknie z kodem). Po tym zaloguje się polecenie będzie działać zgodnie z `get-access-token` oczekiwaniami.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2: Pobieranie tokenu dostępu usługi Azure AD

Wywołaj narzędzie interfejsu wiersza polecenia platformy Azure, aby uzyskać token dostępu dla uwierzytelnionego użytkownika usługi Azure AD z kroku 1 do dostępu do bazy danych platformy Azure dla postgreSQL.

Przykład (dla chmury publicznej):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Powyższa wartość zasobu musi być określona dokładnie tak, jak pokazano. W przypadku innych chmur wartość zasobu można sprawdzić za pomocą:

```azurecli-interactive
az cloud show
```

W przypadku interfejsu wiersza polecenia platformy Azure w wersji 2.0.71 i nowszej można określić polecenie w następującej, wygodniejszej wersji dla wszystkich chmur:

```azurecli-interactive
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

Token jest ciągiem Base 64, który koduje wszystkie informacje o uwierzytelnionym użytkowniku i który jest przeznaczony dla usługi Azure Database for PostgreSQL.

> [!NOTE]
> Ważność tokenu dostępu wynosi od 5 minut do 60 minut. Zalecamy uzyskanie tokenu dostępu tuż przed zainicjowaniem logowania do usługi Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Krok 3: Użyj tokenu jako hasła do logowania się za pomocą PostgreSQL

Podczas łączenia należy użyć tokenu dostępu jako hasło użytkownika PostgreSQL.

Podczas korzystania `psql` z klienta wiersza polecenia token dostępu `PGPASSWORD` musi być przekazywany przez zmienną `psql` środowiskową, ponieważ token dostępu przekracza długość hasła, które można zaakceptować bezpośrednio:

Przykład systemu Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Przykład systemu Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Teraz możesz zainicjować połączenie z usługą Azure Database dla PostgreSQL, tak jak zwykle:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Teraz uwierzytelniony na serwerze PostgreSQL przy użyciu uwierzytelniania usługi Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Tworzenie użytkowników usługi Azure AD w usłudze Azure Database dla postgreSQL

Aby dodać użytkownika usługi Azure AD do bazy danych usługi Azure Database for PostgreSQL, wykonaj następujące kroki po nawiązaniu połączenia (zobacz sekcję nowszą dotyczącą sposobu nawiązania połączenia):

1. Najpierw upewnij się, `<user>@yourtenant.onmicrosoft.com` że użytkownik usługi Azure AD jest prawidłowym użytkownikiem w dzierżawie usługi Azure AD.
2. Zaloguj się do usługi Azure Database dla wystąpienia PostgreSQL jako użytkownik administratora usługi Azure AD.
3. Tworzenie `<user>@yourtenant.onmicrosoft.com` roli w usłudze Azure Database dla postgreSQL.
4. Zamieć `<user>@yourtenant.onmicrosoft.com` członka roli azure_ad_user. To może być podane tylko użytkownikom usługi Azure AD.

**Przykład:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Uwierzytelnianie użytkownika za pośrednictwem usługi Azure AD nie daje użytkownikowi żadnych uprawnień dostępu do obiektów w bazie danych azure bazy danych dla postgreSql bazy danych. Należy ręcznie udzielić użytkownikowi wymaganych uprawnień.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Tworzenie grup usługi Azure AD w usłudze Azure Database dla postgreSQL

Aby włączyć grupę usługi Azure AD w celu uzyskania dostępu do bazy danych, użyj tego samego mechanizmu, co dla użytkowników, ale zamiast tego określ nazwę grupy:

**Przykład:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Podczas logowania członkowie grupy będą używać tokenów dostępu osobistego, ale podpisują nazwę grupy określoną jako nazwa użytkownika.

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

Uwierzytelnianie usługi Azure AD w usłudze Azure Database for PostgreSQL zapewnia, że użytkownik istnieje na serwerze PostgreSQL i sprawdza ważność tokenu, sprawdzając poprawność zawartości tokenu. Wykonywane są następujące kroki sprawdzania poprawności tokenu:

- Token jest podpisany przez usługę Azure AD i nie został zmodyfikowany
- Token został wystawiony przez usługę Azure AD dla dzierżawy skojarzonej z serwerem
- Token nie wygasł
- Token jest przeznaczony dla zasobu usługi Azure Database dla postgreSQL (a nie innego zasobu platformy Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrowanie istniejących użytkowników postgresql do uwierzytelniania opartego na usłudze Azure AD

Można włączyć uwierzytelnianie usługi Azure AD dla istniejących użytkowników. Istnieją dwa przypadki do rozważenia:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Przypadek 1: Nazwa użytkownika PostgreSQL jest zgodna z główną nazwą użytkownika usługi Azure AD

W mało prawdopodobnym przypadku, że istniejący użytkownicy są już `azure_ad_user` zgodne z nazwami użytkowników usługi Azure AD, można udzielić im roli, aby włączyć je do uwierzytelniania usługi Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Będą teraz mogli logować się przy użyciu poświadczeń usługi Azure AD zamiast używać wcześniej skonfigurowanego hasła użytkownika PostgreSQL.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Przypadek 2: Nazwa użytkownika PostgreSQL różni się od nazwy głównej użytkownika usługi Azure AD

Jeśli użytkownik PostgreSQL nie istnieje w usłudze Azure AD lub ma inną nazwę użytkownika, można użyć grup usługi Azure AD do uwierzytelniania jako ten użytkownik PostgreSQL. Można migrować istniejącą usługę Azure Database dla użytkowników PostgreSQL do usługi Azure AD, tworząc grupę usługi Azure AD o nazwie odpowiadającej użytkownikowi PostgreSQL, a następnie przyznając rolę azure_ad_user do istniejącego użytkownika PostgreSQL:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Zakłada się, że utworzono grupę "DBReadUser" w usłudze Azure AD. Użytkownicy należący do tej grupy będą teraz mogli zalogować się do bazy danych jako ten użytkownik.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj ogólne pojęcia [uwierzytelniania usługi Azure Active Directory za pomocą usługi Azure Database for PostgreSQL — single server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
