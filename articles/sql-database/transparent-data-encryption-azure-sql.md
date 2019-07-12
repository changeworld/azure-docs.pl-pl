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
ms.date: 04/19/2019
ms.openlocfilehash: dfc48fec948ab0cf3d16a49bb60eb3c274f2864e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723275"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Przezroczyste szyfrowanie danych dla bazy danych SQL i magazynu danych

Przezroczyste szyfrowanie danych (TDE) ułatwia ochronę usługi Azure SQL Database, wystąpienia zarządzanego Azure SQL i Azure Data Warehouse przed złośliwymi działaniami w trybie offline szyfrowanie danych magazynowanych. Wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji. Domyślnie funkcja TDE jest włączona dla wszystkich nowo wdrożonym baz danych Azure SQL. Funkcja TDE nie może być używane do szyfrowania logicznej **wzorca** bazy danych w bazie danych SQL.  **Wzorca** baza danych zawiera obiekty, które są wymagane do wykonywania operacji TDE w bazach danych użytkownika.

Funkcja TDE musi zostać włączona ręcznie dla wystąpienia zarządzanego Azure SQL, starszych baz danych Azure SQL Database lub Azure SQL Data Warehouse.  

Przezroczyste szyfrowanie danych szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. Ten klucz szyfrowania bazy danych jest chroniona przez funkcję technologii transparent data encryption ochrony. Funkcja ochrony jest albo zarządzanego przez usługę certyfikatu (zarządzane przez usługę technologii transparent data encryption) lub klucza asymetrycznego, przechowywane w usłudze Azure Key Vault (Bring Your Own Key). Funkcja ochrony szyfrowania danych jest ustawiony na poziomie serwera dla usługi Azure SQL Database i Data Warehouse i poziomu wystąpienia dla wystąpienia zarządzanego Azure SQL. Termin *serwera* odnosi się zarówno do serwera i wystąpienia, w tym dokumencie, o ile nie wskazano inaczej.

Podczas uruchamiania bazy danych klucz szyfrowania szyfrowanej bazy danych jest odszyfrowywany i następnie używany do odszyfrowywania i ponownie szyfrować pliki bazy danych w procesie aparatu bazy danych programu SQL Server. Przezroczyste szyfrowanie danych wykonuje się w czasie rzeczywistym operacji We/Wy szyfrowanie i odszyfrowywanie danych na poziomie strony. Każda strona jest odszyfrowywany podczas odczytu do pamięci i następnie szyfrowane przed zapisaniem na dysku. Aby uzyskać ogólny opis technologii transparent data encryption, zobacz [technologii Transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server uruchomiony na maszynie wirtualnej platformy Azure, również można użyć klucza asymetrycznego z usługi Key Vault. Kroki konfiguracji różnią się od przy użyciu klucza asymetrycznego, w bazie danych SQL i wystąpienie zarządzane usługi SQL. Aby uzyskać więcej informacji, zobacz [Rozszerzalne zarządzanie kluczami za pomocą usługi Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Zarządzane przez usługę przezroczyste szyfrowanie danych

Na platformie Azure domyślne ustawienie dla technologii transparent data encryption jest, że klucz szyfrowania bazy danych jest chroniony za pomocą wbudowanego serwera certyfikatu. Certyfikat wbudowanego serwera jest unikatowy dla każdego serwera, a algorytm szyfrowania używany jest AES 256. Jeśli baza danych jest w relacji replikacji geograficznej, zarówno podstawowej i pomocniczej geograficznej bazy danych są chronione przez klucz serwera nadrzędnego podstawowej bazy danych. Jeśli połączenie dwóch baz danych na tym samym serwerze, również udostępnić ten sam certyfikat wbudowanych.  Microsoft automatycznie przełącza te certyfikaty niezgodne z zasadami bezpieczeństwa wewnętrznego i klucz główny jest chroniony przez Microsoft wewnętrznego magazynu wpisów tajnych.  Klientom sprawdzenie zgodności bazy danych SQL z zasadami zabezpieczeń wewnętrznych w niezależnie od innych firm raportów dostępnych w [Microsoft Trust Center](https://servicetrust.microsoft.com/).

Firma Microsoft również bezproblemowo przenosi i zarządza kluczami, zgodnie z potrzebami dla replikacji geograficznej i przywraca.

> [!IMPORTANT]
> Wszystkie nowo utworzone bazy danych SQL są domyślnie szyfrowane za pomocą zarządzane przez usługę przezroczyste szyfrowanie danych. Domyślnie nie są szyfrowane baz danych w wystąpieniu zarządzanym usługi SQL Azure, utworzonych przed maj 2017 istniejących baz danych SQL i SQL bazy danych utworzone przez Przywracanie, replikację geograficzną i kopii bazy danych.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Zarządzane przez klienta niewidoczne szyfrowanie danych — własnego klucza

[Funkcja TDE za pomocą kluczy zarządzanych przez klienta w usłudze Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) umożliwia szyfrowania klucza szyfrowania bazy danych (DEK) przy użyciu zarządzanych przez klienta klucza asymetrycznego wywoływana funkcja ochrony TDE.  Jest to również powszechnie określane jako Bring Your Own Key (BYOK), obsługa funkcji Transparent Data Encryption. W tym scenariuszu BYOK ochrony TDE jest przechowywany w należącej do klienta i zarządzane [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), platformy Azure opartej na chmurze zewnętrznego kluczy systemu zarządzania. Może być ochrony TDE [generowanych przez usługi key vault lub przenoszone do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) na lokalnego modułu HSM z urządzenia z systemem. Funkcja TDE klucza szyfrowania danych, który jest przechowywany na stronę rozruchu bazy danych, zostaje zaszyfrowany i odszyfrowany przez funkcji ochrony TDE, która jest przechowywana w usłudze Azure Key Vault i nigdy nie opuszcza magazynu kluczy.  Baza danych SQL musi mieć przyznane uprawnienia do należącej do klienta magazynu kluczy do odszyfrowywania i szyfrowania klucza szyfrowania danych. Jeśli uprawnienia serwera logicznego SQL do magazynu kluczy zostaną odwołane, bazy danych staną się niedostępne, a wszystkie dane są szyfrowane. Usługi Azure SQL Database funkcji ochrony TDE jest ustawiona na poziomie serwera logicznego SQL i jest dziedziczona przez wszystkie bazy danych skojarzonych z tym serwerem. Dla [wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) (funkcja BYOK w wersji zapoznawczej), funkcji ochrony TDE jest ustawiona na poziomie wystąpienia i jest dziedziczona przez wszystkie *zaszyfrowanych* baz danych w tym wystąpieniu. Termin *serwera* odnosi się zarówno do serwera i wystąpienia, w tym dokumencie, o ile nie wskazano inaczej.

Za pomocą funkcji TDE dzięki integracji usługi Azure Key Vault użytkownicy można kontrolować zadania zarządzania kluczami, w tym wymiany kluczy, uprawnień usługi key vault, kopie zapasowe kluczy i Włącz inspekcję/raportowanie na wszystkie funkcje ochrony TDE, za pomocą funkcji usługi Azure Key Vault. Usługa Key Vault umożliwia centralne zarządzanie kluczami, wykorzystuje ściśle monitorowanych sprzętowych modułach zabezpieczeń (HSM) i umożliwia rozdzielenie obowiązków między danych i zarządzanie kluczami w celu zapewnienia zgodności z zasadami zabezpieczeń.
Aby dowiedzieć się więcej na temat technologii transparent data encryption dzięki integracji usługi Azure Key Vault (Obsługa Bring Your Own Key) dla usługi Azure SQL Database, wystąpienia zarządzanego usługi SQL (funkcja BYOK w wersji zapoznawczej) i Data Warehouse, zobacz [przezroczyste szyfrowanie danych za pomocą klucza usługi Azure Integracja magazynu](transparent-data-encryption-byok-azure-sql.md).

Aby rozpocząć korzystanie z technologii transparent data encryption dzięki integracji usługi Azure Key Vault (Obsługa Bring Your Own Key), zobacz Przewodnik z instrukcjami dotyczącymi [Włączanie technologii transparent data encryption przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Przenoszenie bazy danych chronione przez szyfrowanie danych

Nie ma potrzeby odszyfrowania bazy danych dla operacji na platformie Azure. Ustawienia szyfrowania danych w źródłowej bazy danych lub podstawowej bazie danych przezroczyste są dziedziczone w elemencie docelowym. Operacje, które są uwzględniane obejmują:

- Przywracanie geograficzne
- Samoobsługowe Przywracanie do punktu w czasie
- Przywracanie usuniętej bazy danych
- Aktywna replikacja geograficzna
- Tworzenie kopii bazy danych
- Przywracanie pliku kopii zapasowej do wystąpienia zarządzanego Azure SQL

> [!IMPORTANT]
> Ręczne COPY-ONLY kopii zapasowej bazy danych zaszyfrowanych przez zarządzane przez usługę TDE nie jest dozwolona w wystąpienia zarządzanego SQL Azure, ponieważ certyfikat używany do szyfrowania jest niedostępny. Funkcja punktu w czasie przywracania umożliwia przeniesienie tego typu bazy danych do innego wystąpienia zarządzanego.

Podczas eksportowania bazy danych chronione przez szyfrowanie danych wyeksportowaną zawartość bazy danych nie jest zaszyfrowany. Ten wyeksportowany zawartość jest przechowywana w niezaszyfrowanego plików BACPAC. Pamiętaj odpowiednio chronić pliki BACPAC i włączanie technologii transparent data encryption, po zakończeniu importowania Nowa baza danych.

Na przykład plik BACPAC jest wyeksportowany z wystąpienia programu SQL Server w środowisku lokalnym, zaimportowana zawartość nowej bazy danych nie są automatycznie szyfrowane. Podobnie pliku BACPAC są eksportowane do wystąpienia programu SQL Server w środowisku lokalnym, Nowa baza danych także nie są automatycznie szyfrowane.

Jedynym wyjątkiem jest podczas eksportowania do i z bazy danych SQL. Przezroczyste szyfrowanie danych jest włączone w nowej bazy danych, ale sam plik BACPAC nie są szyfrowane.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Zarządzanie przezroczyste szyfrowanie danych w witrynie Azure portal

Aby skonfigurować przezroczyste szyfrowanie danych za pośrednictwem witryny Azure portal, musi być podłączony jako Azure właścicielem, współautorem lub Menedżera zabezpieczeń programu SQL.

Przezroczyste szyfrowanie danych włączać i wyłączać Włącz poziomu bazy danych. Aby włączyć przezroczyste szyfrowanie danych w bazie danych, przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się przy użyciu konta administratora platformy Azure lub współautora. Znajdź ustawienia szyfrowania danych w bazie danych użytkownika. Domyślnie używany jest zarządzane przez usługę przezroczyste szyfrowanie danych. Certyfikat szyfrowania danych jest generowany automatycznie dla serwera, który zawiera bazę danych. Dla wystąpienia zarządzanego Azure SQL umożliwia włączanie technologii transparent data encryption włączać i wyłączać w bazie danych języka T-SQL.

![Zarządzane przez usługę przezroczyste szyfrowanie danych](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Klucz główny szyfrowania danych, nazywane również przezroczyste szyfrowanie moduł ochrony danych, należy ustawić na poziomie serwera. Aby korzystać z technologii transparent data encryption z obsługą własnego klucza i chronić baz danych za pomocą klucza z magazynu kluczy, należy otworzyć ustawienia szyfrowania danych na Twoim serwerze.

![Przezroczyste szyfrowanie danych z obsługą własnego klucza](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Zarządzanie przezroczyste szyfrowanie danych za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą programu PowerShell, musi być podłączony jako Azure właścicielem, współautorem lub Menedżera zabezpieczeń programu SQL.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Polecenia cmdlet dla usługi Azure SQL Database i Data Warehouse

Użyj następujących poleceń cmdlet dla usługi Azure SQL Database i Data Warehouse:

| Polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Pobiera stan szyfrowania danych dla bazy danych |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Sprawdza, czy postęp szyfrowania bazy danych |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Dodaje klucz usługi Key Vault do wystąpienia programu SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Pobiera klucze usługi Key Vault dla serwera usługi Azure SQL Database  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ustawia funkcja ochrony szyfrowania danych dla wystąpienia programu SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Pobiera funkcja ochrony szyfrowania danych |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Usuwa klucz usługi Key Vault z wystąpienia programu SQL Server |
|  | |

> [!IMPORTANT]
> Dla wystąpienia zarządzanego Azure SQL, użyj języka T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) polecenie, aby włączyć technologii transparent data encryption włączać i wyłączać na poziomie bazy danych i sprawdź [przykładowy skrypt programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) do zarządzania danymi przezroczyste szyfrowanie na poziomie wystąpienia.

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Zarządzanie przezroczyste szyfrowanie danych za pomocą języka Transact-SQL

Połączenia z bazą danych przy użyciu nazwy logowania, który jest administratorem lub członkiem **dbmanager** roli w bazie danych master.

| Polecenie | Opis |
| --- | --- |
| [Instrukcja ALTER DATABASE (baza danych SQL platformy Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | Ustaw/wyłączenie szyfrowania szyfruje i odszyfrowuje bazy danych |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Zwraca informacje dotyczące stanu szyfrowania bazy danych i jego skojarzonej bazy danych klucze szyfrowania |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Zwraca informacje dotyczące stanu szyfrowania poszczególnych danych magazynu węzła i jego skojarzonej bazy danych klucze szyfrowania |
|  | |

Funkcja ochrony szyfrowania danych nie można przełączyć do klucza z usługi Key Vault przy użyciu języka Transact-SQL. Użyj programu PowerShell lub witryny Azure portal.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Zarządzanie przezroczyste szyfrowanie danych za pomocą interfejsu API REST

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą interfejsu API REST, musi być podłączony jako Azure właścicielem, współautorem lub Menedżera zabezpieczeń programu SQL.
Użyj następującego zestawu poleceń dla usługi Azure SQL Database i Data Warehouse:

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

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólny opis technologii transparent data encryption, zobacz [technologii Transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Aby dowiedzieć się więcej na temat technologii transparent data encryption z obsługą Bring Your Own Key dla usługi Azure SQL Database, wystąpienia zarządzanego Azure SQL i magazynu danych, zobacz [technologii Transparent data encryption z obsługą Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Aby rozpocząć korzystanie z technologii transparent data encryption z obsługą własnego klucza, zobacz Przewodnik z instrukcjami dotyczącymi [Włączanie technologii transparent data encryption przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Aby uzyskać więcej informacji na temat usługi Key Vault, zobacz [stronę z dokumentacją dotyczącą usługi Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
