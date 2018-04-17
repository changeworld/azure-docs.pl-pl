---
title: Alias systemu DNS dla usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Aplikacje mogą łączyć się aliasu dla nazwy serwera bazy danych SQL Azure. W tym samym czasie można zmienić bazy danych SQL alias wskazuje w dowolnym momencie, aby ułatwić testowanie i tak dalej.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: DNS alias
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;ayolubek
ms.author: dmalik
ms.openlocfilehash: 1793f44026db55d17fabde5fb92bc46795109418
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias systemu DNS dla bazy danych SQL Azure

Baza danych SQL Azure zawiera serwer systemu nazw domen (DNS, Domain Name System). Zaakceptuj środowiska PowerShell i interfejsów API REST [wywołania do tworzenia i zarządzania nimi aliasów DNS](#anchor-powershell-code-62x) dla nazwy serwera bazy danych SQL.

A *DNS alias* można użyć zamiast nazwy serwera bazy danych SQL Azure. Programy klienckie można użyć tego aliasu w ich parametry połączenia. DNS alias zapewnia warstwę tłumaczenia, którego można przekierowywać programy klienta na różnych serwerach. Ta warstwa zaoszczędzi trudności z konieczności Znajdowanie i Edycja wszystkich klientów i ich parametry połączenia.

Najczęstsze zastosowania dla aliasu DNS obejmują następujących przypadkach:

- Utwórz łatwe do zapamiętania nazwę serwera SQL Azure.
- Podczas tworzenia początkowej aliasu mogą odwoływać się do badania serwera bazy danych SQL. Gdy aplikacja przechodzi na żywo, można zmodyfikować alias do odwoływania się do serwera produkcyjnego. Przejście z testów w środowisku produkcyjnym nie wymaga żadnych modyfikacji konfiguracji wielu klientów, którzy nawiązują połączenia z serwerem bazy danych.
- Załóżmy, że bazy danych tylko do aplikacji są przenoszone do innego serwera bazy danych SQL. W tym miejscu można zmodyfikować alias bez konieczności modyfikowania konfiguracji kilku klientów.

#### <a name="domain-name-system-dns-of-the-internet"></a>System nazw domen (DNS) przez Internet

Internet korzysta z serwera DNS. DNS tłumaczy przyjazne nazwy na nazwę serwera bazy danych SQL Azure.

## <a name="scenarios-with-one-dns-alias"></a>Scenariusze z jednym alias DNS

Załóżmy, że chcesz przełączyć systemu na nowy serwer bazy danych SQL Azure. W przeszłości potrzebne do odnaleźć i zaktualizować parametry połączenia, co w programie każdego klienta. Ale teraz, jeśli parametry połączenia używane DNS alias, właściwość alias należy zaktualizować.

Funkcja alias DNS bazy danych SQL Azure może pomóc w następujących scenariuszach:

#### <a name="test-to-production"></a>Testowanie w środowisku produkcyjnym

Po rozpoczęciu tworzenia programów klienckich, niech Użyj aliasu DNS w ich parametry połączenia. Właściwości punktu alias dokonać wersji testowej serwera bazy danych SQL Azure.

Później nowy system systemowi na żywo w środowisku produkcyjnym, można zaktualizować właściwości aliasu, aby wskazywały serwer bazy danych SQL produkcji. Niezbędne jest Brak zmian do programów klienckich.

#### <a name="cross-region-support"></a>Obsługa między regionu

Odzyskiwanie po awarii może być przesunięcia serwer bazy danych SQL w innym regionie geograficznym. System nie został używa aliasu DNS, należy odnaleźć i zaktualizować parametry połączenia dla wszystkich klientów można uniknąć. Zamiast tego należy zaktualizować alias do odwoływania się do nowego serwera bazy danych SQL, który teraz udostępnia bazę danych.




## <a name="properties-of-a-dns-alias"></a>Właściwości DNS alias

Następujące właściwości dotyczą każdego alias systemu DNS dla serwera bazy danych SQL:

- *Unikatowa nazwa:* nazwy aliasu tworzenia jest unikatowa na wszystkich serwerach usługi Azure SQL Database, podobnie jak serwera są nazwy.

- *Wymagany jest serwer:* A DNS alias nie można utworzyć odwołuje się do dokładnie jeden serwer, a serwer musi już istnieć. Zaktualizowano alias zawsze musi odwoływać się dokładnie jeden serwer.
    - Po upuszczeniu serwera bazy danych SQL systemu Azure również porzuca wszystkie aliasy systemu DNS, które odwołują się do serwera.

- *Nie jest powiązany z dowolnym regionie:* aliasów DNS nie są powiązane z obszarem. Wszystkie aliasy systemu DNS może zostać zaktualizowana do odwoływania się do serwera bazy danych SQL Azure, która znajduje się w dowolnym regionie geograficznym.
    - Jednak podczas aktualizowania alias do odwoływania się do innego serwera, oba serwery muszą istnieć w tej samej Azure *subskrypcji*.

- *Uprawnienia:* do zarządzania DNS alias, użytkownik musi mieć *współautora serwera* uprawnienia lub nowszej. Aby uzyskać więcej informacji, zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure](../role-based-access-control/overview.md).





## <a name="manage-your-dns-aliases"></a>Zarządzanie aliasy DNS

Polecenia cmdlet zarówno interfejsów API REST, jak i programu PowerShell są dostępne w celu umożliwienia można programowo zarządzać aliasy DNS.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>Interfejsów API REST zarządzania aliasy DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

Dokumentację dla interfejsów API REST jest dostępny niemal w następującej lokalizacji w sieci web:
- [Baza danych Azure SQL interfejsu API REST](https://docs.microsoft.com/rest/api/sql/)

Ponadto interfejsów API REST można wyświetlić w witrynie GitHub pod:
- [Serwer bazy danych SQL Azure, DNS alias interfejsów API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell do zarządzania aliasy DNS

Polecenia cmdlet programu PowerShell są dostępne, które wywołują interfejsów API REST.

Przykładowy kod, poleceń cmdlet programu PowerShell jest używane do zarządzania aliasów DNS jest udokumentowany na:
- [Środowiska PowerShell dla aliasu DNS do bazy danych Azure SQL](dns-alias-powershell.md)


Polecenia cmdlet używane w przykładzie kodu są następujące:
- [Nowy AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): tworzy nowy alias DNS w systemie usługi baza danych SQL Azure. Alias odwołuje się do serwera bazy danych SQL Azure 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): lista wszystkich aliasów DNS, które są przypisane do bazy danych SQL server 1 i Get.
- [Zestaw AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Modyfikuje nazwę serwera skonfigurowanego do aliasu dotyczą z serwera 1 serwer bazy danych SQL. 2.
- [Usuń AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Usuń DNS alias z serwera bazy danych SQL 2, przy użyciu nazwy aliasu.


Powyższe polecenia cmdlet zostały dodane do **AzureRM.Sql** modułu, począwszy od wersji modułu 5.1.1.




## <a name="limitations-during-preview"></a>Ograniczenia wersji zapoznawczej

Obecnie usługa DNS alias ma następujące ograniczenia:

- *Opóźnienie maksymalnie 2 minuty:* trwa maksymalnie 2 minuty, aż alias DNS należy zaktualizować lub usunąć.
    - Niezależnie od wszelkich krótkie opóźnienie alias natychmiastowe zatrzymanie przywołuje połączeń klientów z starszego serwera.

- *Wyszukiwania DNS:* obecnie tylko autorytatywne sposób, aby sprawdzić, jakie serwera DNS podany alias odwołuje się do wykonując [wyszukiwania DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[Inspekcja tabeli nie jest obsługiwana](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* nie można użyć aliasu DNS na serwerze bazy danych SQL Azure, która ma *inspekcji tabeli* włączona w bazie danych.
    - Inspekcja tabeli jest przestarzały.
    - Zalecamy przeniesienie [Inspekcja obiektów Blob](sql-database-auditing.md).




## <a name="related-resources"></a>Powiązane zasoby

- [Omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md), włączając odzyskiwania po awarii.



## <a name="next-steps"></a>Kolejne kroki

- [Środowiska PowerShell dla aliasu DNS do bazy danych Azure SQL](dns-alias-powershell.md)

