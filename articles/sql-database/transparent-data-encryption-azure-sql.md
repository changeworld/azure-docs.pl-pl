---
title: Przezroczyste szyfrowanie danych dla usługi Azure SQL Database i Data Warehouse | Dokumentacja firmy Microsoft
description: Przegląd przezroczyste szyfrowanie danych dla bazy danych SQL Database i Data Warehouse. Dokument obejmuje jego zalety i opcje konfiguracji, która zawiera zarządzane przez usługę przezroczyste szyfrowanie danych i własnego klucza.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 0d5b7e484024294eb5c95b632dbef85c377b717e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469031"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Przezroczyste szyfrowanie danych dla bazy danych SQL i magazynu danych

Przezroczyste szyfrowanie danych (TDE) ułatwia ochronę przed złośliwymi działaniami usługi Azure SQL Database i Azure Data Warehouse. Wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji. Domyślnie funkcja TDE jest włączona dla wszystkich nowo wdrożonym baz danych Azure SQL. Funkcja TDE nie może być używane do szyfrowania logicznej **wzorca** bazy danych w bazie danych SQL.  **Wzorca** baza danych zawiera obiekty, które są wymagane do wykonywania operacji TDE w bazach danych użytkownika.

Funkcja TDE będzie musiał zostać włączona ręcznie dla starszych baz danych lub usługi Azure SQL Data Warehouse.  

Przezroczyste szyfrowanie danych szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. Ten klucz szyfrowania bazy danych jest chroniona przez funkcję technologii transparent data encryption ochrony. Funkcja ochrony jest albo zarządzanego przez usługę certyfikatu (zarządzane przez usługę technologii transparent data encryption) lub klucza asymetrycznego, przechowywane w usłudze Azure Key Vault (Bring Your Own Key). Funkcja ochrony szyfrowania danych jest ustawiony na poziomie serwera.

Podczas uruchamiania bazy danych klucz szyfrowania szyfrowanej bazy danych jest odszyfrowywany i następnie używany do odszyfrowywania i ponownie szyfrować pliki bazy danych w procesie aparatu bazy danych programu SQL Server. Przezroczyste szyfrowanie danych wykonuje się w czasie rzeczywistym operacji We/Wy szyfrowanie i odszyfrowywanie danych na poziomie strony. Każda strona jest odszyfrowywany podczas odczytu do pamięci i następnie szyfrowane przed zapisaniem na dysku. Aby uzyskać ogólny opis technologii transparent data encryption, zobacz [technologii Transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server uruchomiony na maszynie wirtualnej platformy Azure, również można użyć klucza asymetrycznego z usługi Key Vault. Czynności konfiguracyjne są inne niż w bazie danych SQL przy użyciu klucza asymetrycznego. Aby uzyskać więcej informacji, zobacz [Rozszerzalne zarządzanie kluczami za pomocą usługi Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Zarządzane przez usługę przezroczyste szyfrowanie danych

Na platformie Azure domyślne ustawienie dla technologii transparent data encryption jest, że klucz szyfrowania bazy danych jest chroniony za pomocą wbudowanego serwera certyfikatu. Certyfikat wbudowanego serwera jest unikatowy dla każdego serwera. Jeśli baza danych jest w relacji replikacji geograficznej, zarówno podstawowej i pomocniczej geograficznej bazy danych są chronione przez klucz serwera nadrzędnego podstawowej bazy danych. Jeśli dwie bazy danych są podłączone do tego samego serwera, współużytkują one ten sam certyfikat wbudowanych. Microsoft automatycznie przełącza tych certyfikatów, co 90 dni.

Firma Microsoft również bezproblemowo przenosi i zarządza kluczami, zgodnie z potrzebami dla replikacji geograficznej i przywraca.

> [!IMPORTANT]
> Wszystkie nowo utworzone bazy danych SQL są domyślnie szyfrowane za pomocą zarządzane przez usługę przezroczyste szyfrowanie danych. Domyślnie nie są szyfrowane istniejących baz danych maja 2017 r. i bazy danych utworzone przez Przywracanie, replikację geograficzną i kopii bazy danych.

## <a name="bring-your-own-key"></a>Użyj własnego klucza

Dzięki obsłudze własnego klucza, może potrwać kontrolę nad kluczami szyfrowania danych i określania, kto może uzyskiwać do nich dostęp i kiedy. Key Vault, która jest system Azure opartych na chmurze zewnętrzne zarządzanie kluczami, to pierwsza usługa zarządzania kluczami tej technologii transparent data encryption została zintegrowana z obsługą własnego klucza. Z obsługą własnego klucza klucz szyfrowania bazy danych jest chroniony przez klucz asymetryczny przechowywanych w usłudze Key Vault. Klucz asymetryczny nigdy nie opuszcza usługi Key Vault. Po serwer ma uprawnienia do magazynu kluczy, serwer wysyła żądania operacja klucza podstawowego do niego za pośrednictwem usługi Key Vault. Ustaw klucz asymetryczny na poziomie serwera i wszystkich bazach danych na tym serwerze dziedziczą go.

Dzięki obsłudze Bring Your Own Key teraz można kontrolować zadania zarządzania kluczami, takie jak uprawnienia usługi key vault i wymiany kluczy. Możesz również usunąć klucze i Włącz inspekcję/raporty dotyczące wszystkich kluczy szyfrowania. Usługa Key Vault umożliwia centralne zarządzanie kluczami i używa ściśle monitorowanych sprzętowych modułach zabezpieczeń. Usługa Key Vault promuje separacji zarządzania kluczami i dane, aby zapewnić zgodność z przepisami. Aby dowiedzieć się więcej o usłudze Key Vault, zobacz [stronę z dokumentacją dotyczącą usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Aby dowiedzieć się więcej na temat technologii transparent data encryption z obsługą własnego klucza dla bazy danych SQL Database i Data Warehouse, zobacz [technologii Transparent data encryption z obsługą Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).

Aby rozpocząć korzystanie z technologii transparent data encryption z obsługą własnego klucza, zobacz Przewodnik z instrukcjami dotyczącymi [Włączanie technologii transparent data encryption przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Przenoszenie bazy danych chronione przez szyfrowanie danych

Nie ma potrzeby odszyfrowania bazy danych dla operacji na platformie Azure. Ustawienia szyfrowania danych w źródłowej bazy danych lub podstawowej bazie danych przezroczyste są dziedziczone w elemencie docelowym. Operacje, które są uwzględniane obejmują:

- Przywracanie geograficzne
- Samoobsługowe Przywracanie do punktu w czasie
- Przywracanie usuniętej bazy danych
- Aktywna replikacja geograficzna
- Tworzenie kopii bazy danych

Podczas eksportowania bazy danych chronione przez szyfrowanie danych wyeksportowaną zawartość bazy danych nie jest zaszyfrowany. Ten wyeksportowany zawartość jest przechowywana w niezaszyfrowanego plików BACPAC. Pamiętaj odpowiednio chronić pliki BACPAC i włączanie technologii transparent data encryption, po zakończeniu importowania Nowa baza danych.

Na przykład plik BACPAC jest wyeksportowany z wystąpienia programu SQL Server w środowisku lokalnym, zaimportowana zawartość nowej bazy danych nie są automatycznie szyfrowane. Podobnie pliku BACPAC są eksportowane do wystąpienia programu SQL Server w środowisku lokalnym, Nowa baza danych także nie są automatycznie szyfrowane.

Jedynym wyjątkiem jest podczas eksportowania do i z bazy danych SQL. Przezroczyste szyfrowanie danych jest włączone w nowej bazy danych, ale sam plik BACPAC nie są szyfrowane.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Zarządzanie przezroczyste szyfrowanie danych w witrynie Azure portal

Aby skonfigurować przezroczyste szyfrowanie danych za pośrednictwem witryny Azure portal, musi być podłączony jako Azure właścicielem, współautorem lub Menedżera zabezpieczeń programu SQL.

Przezroczyste szyfrowanie danych jest ustawiony na poziomie bazy danych. Aby włączyć przezroczyste szyfrowanie danych w bazie danych, przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się przy użyciu konta administratora platformy Azure lub współautora. Znajdź ustawienia szyfrowania danych w bazie danych użytkownika. Domyślnie używany jest zarządzane przez usługę przezroczyste szyfrowanie danych. Certyfikat szyfrowania danych jest generowany automatycznie dla serwera, który zawiera bazę danych.

![Zarządzane przez usługę przezroczyste szyfrowanie danych](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Klucz główny szyfrowania danych, nazywane również przezroczyste szyfrowanie moduł ochrony danych, należy ustawić na poziomie serwera. Aby korzystać z technologii transparent data encryption z obsługą własnego klucza i chronić baz danych za pomocą klucza z magazynu kluczy, zobacz ustawienia szyfrowania danych na Twoim serwerze.

![Przezroczyste szyfrowanie danych z obsługą własnego klucza](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Zarządzanie przezroczyste szyfrowanie danych za pomocą programu PowerShell

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą programu PowerShell, musi być podłączony jako Azure właścicielem, współautorem lub Menedżera zabezpieczeń programu SQL.

| Polecenie cmdlet | Opis |
| --- | --- |
| [Zestaw AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Pobiera stan szyfrowania danych dla bazy danych |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Sprawdza, czy postęp szyfrowania bazy danych |
| [Dodaj AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Dodaje klucz usługi Key Vault do wystąpienia programu SQL Server |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Pobiera klucze usługi Key Vault dla wystąpienia programu SQL server  |
| [Polecenie set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Ustawia funkcja ochrony szyfrowania danych dla wystąpienia programu SQL Server |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Pobiera funkcja ochrony szyfrowania danych |
| [Usuń AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Usuwa klucz usługi Key Vault z wystąpienia programu SQL Server |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Zarządzanie przezroczyste szyfrowanie danych za pomocą języka Transact-SQL

Połączenia z bazą danych przy użyciu nazwy logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master.

| Polecenie | Opis |
| --- | --- |
| [Instrukcja ALTER DATABASE (baza danych SQL platformy Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | Ustaw/wyłączenie szyfrowania szyfruje i odszyfrowuje bazy danych |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Zwraca informacje dotyczące stanu szyfrowania bazy danych i jego skojarzonej bazy danych klucze szyfrowania |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Zwraca informacje dotyczące stanu szyfrowania poszczególnych danych magazynu węzła i jego skojarzonej bazy danych klucze szyfrowania |
|  | |

Funkcja ochrony szyfrowania danych nie można przełączyć do klucza z usługi Key Vault przy użyciu języka Transact-SQL. Użyj programu PowerShell lub witryny Azure portal.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Zarządzanie przezroczyste szyfrowanie danych za pomocą interfejsu API REST

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą interfejsu API REST, musi być podłączony jako Azure właścicielem, współautorem lub Menedżera zabezpieczeń programu SQL.

| Polecenie | Opis |
| --- | --- |
|[Utwórz lub zaktualizuj serwer](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Dodaje tożsamości usługi Azure Active Directory do wystąpienia programu SQL Server (używane do udzielania dostępu do usługi Key Vault)|
|[Utwórz lub zaktualizuj klucz serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Dodaje klucz usługi Key Vault do wystąpienia programu SQL Server|
|[Usuń klucz serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Usuwa klucz usługi Key Vault z wystąpienia programu SQL Server|
|[Pobierz klucze serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Pobiera określony klucz usługi Key Vault z wystąpienia programu SQL Server|
|[Lista kluczy serwera przez serwer](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Pobiera klucze usługi Key Vault dla wystąpienia programu SQL Server |
|[Utwórz lub zaktualizuj funkcja ochrony szyfrowania](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Ustawia funkcja ochrony szyfrowania danych dla wystąpienia programu SQL Server|
|[Pobierz funkcja ochrony szyfrowania](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Pobiera funkcja ochrony szyfrowania danych dla wystąpienia programu SQL Server|
|[Funkcje ochrony kluczy szyfrowania w liście przez serwer](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Pobiera funkcje technologii transparent data encryption ochrony dla wystąpienia programu SQL Server |
|[Utwórz lub zaktualizuj Transparent Data Encryption konfiguracji](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
|[Pobieranie Transparent Data Encryption konfiguracji](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Pobiera konfigurację szyfrowania danych dla bazy danych|
|[Listy Transparent Data Encryption konfiguracji wyników](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Pobiera wynik szyfrowania bazy danych|

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólny opis technologii transparent data encryption, zobacz [technologii Transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Aby dowiedzieć się więcej na temat technologii transparent data encryption z obsługą własnego klucza dla bazy danych SQL Database i Data Warehouse, zobacz [technologii Transparent data encryption z obsługą Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Aby rozpocząć korzystanie z technologii transparent data encryption z obsługą własnego klucza, zobacz Przewodnik z instrukcjami dotyczącymi [Włączanie technologii transparent data encryption przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Aby uzyskać więcej informacji na temat usługi Key Vault, zobacz [stronę z dokumentacją dotyczącą usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
