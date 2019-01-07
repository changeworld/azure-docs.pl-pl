---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728565"
---
## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL

Baza danych Azure SQL istnieje w ramach zdefiniowanego zestawu [zasobów obliczeniowych i magazynowych](../articles/sql-database/sql-database-service-tiers-dtu.md). Baza danych działa w ramach [grupy zasobów platformy Azure](../articles/azure-resource-manager/resource-group-overview.md) oraz [serwera logicznego bazy danych Azure SQL Database](../articles/sql-database/sql-database-features.md).

Wykonaj poniższe czynności, aby utworzyć pustą bazę danych SQL.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycje **Bazy danych** > **SQL Database**.

   ![tworzenie pustej bazy danych](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. W okienku **SQL Database** wpisz lub wybierz następujące wartości:

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa bazy danych** | *yourDatabase* | Prawidłowe nazwy baz danych opisano w artykule [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). |
   | **Subskrypcja** | *yourSubscription*  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | *yourResourceGroup* | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Wybierz źródło** | Pusta baza danych | Określa, że ma zostać utworzona pusta baza danych. |

   ![tworzenie bazy danych](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Wybierz pozycję **Serwer**, aby skonfigurować serwer dla nowej bazy danych. Następnie wpisz lub wybierz następujące wartości:

      | Ustawienie       | Sugerowana wartość | Opis |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nazwa serwera** | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
      | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa | Prawidłowe nazwy identyfikatorów logowania opisano w artykule [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych).|
      | **Hasło** | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej 8 znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
      | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz [Regiony systemu Azure](https://azure.microsoft.com/regions/). |

      Wybierz pozycję **Wybierz**.

      ![tworzenie serwera bazy danych](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Wybierz pozycję **Warstwa cenowa**, aby określić warstwę usługi, liczbę jednostek DTU i ilość miejsca do magazynowania. Przejrzyj opcje jednostek DTU i miejsca do magazynowania dostępne dla poszczególnych warstw usługi.

      Po wybraniu warstwy serwera, liczby jednostek DTU i ilości miejsca do magazynowania wybierz przycisk **Zastosuj**.

   1. Wprowadź **sortowanie** dla pustej bazy danych (na potrzeby tego samouczka użyj wartości domyślnej). Aby uzyskać więcej informacji na temat sortowań, zobacz [Collations](/sql/t-sql/statements/collations) (Sortowania).

1. Teraz, po uzupełnieniu formularza usługi **SQL Database**, wybierz przycisk **Utwórz**, aby utworzyć bazę danych. Wykonanie tego kroku może potrwać maksymalnie półtorej minuty.

1. Na pasku narzędzi kliknij pozycję **Powiadomienia**, aby monitorować proces wdrażania.

     ![powiadomienie](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Tworzenie reguły zapory

Usługa SQL Database tworzy zaporę na poziomie serwera, aby uniemożliwić zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze. Wykonaj następujące kroki, aby utworzyć [regułę zapory usługi SQL Database na poziomie serwera](../articles/sql-database/sql-database-firewall-configure.md) dla Twojego adresu IP klienta. Ten proces włącza zewnętrzną łączność przez zaporę usługi SQL Database tylko dla Twojego adresu IP.

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez firmową zaporę. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że administrator otworzy port 1433.

1. Po ukończeniu wdrażania wybierz pozycję **Bazy danych SQL** w menu po lewej stronie i wybierz bazę danych *yourDatabase* na stronie **Bazy danych SQL**. Dla bazy danych zostanie otwarta strona **Przegląd** zawierająca w pełni kwalifikowaną nazwę serwera (na przykład *yourserver.database.windows.net*) i opcje dalszej konfiguracji.

1. Skopiuj w pełni kwalifikowaną nazwę serwera do użycia w celu nawiązania połączenia z serwerem i jego bazami danych w późniejszych krokach.

   ![nazwa serwera](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Wybierz pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera SQL Database.

   ![reguła zapory serwera](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

   1. Wybierz pozycję **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera otwierająca port 1433 na serwerze logicznym.

   1. Wybierz przycisk **OK**, a następnie zamknij stronę **Ustawienia zapory**.

Teraz z Twojego adresu IP można połączyć się przez zaporę z serwerem usługi SQL Database i jego bazami danych przy użyciu programu SQL Server Management Studio lub innego wybranego narzędzia. Używaj wcześniej utworzonego konta administratora serwera.

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Wybierz przycisk **WYŁ.** na tej stronie, aby wyłączyć tę opcję dla wszystkich usług platformy Azure.
