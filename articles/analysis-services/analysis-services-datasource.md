---
title: Źródła danych obsługiwane w usłudze Azure Analysis Services | Dokumentacja firmy Microsoft
description: Zawiera opis źródła danych obsługiwane w przypadku modeli danych z usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8a98f2ed2ce55f74ea3967dbb23a762fce566a2f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595635"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Źródła danych obsługiwane w usłudze Azure Analysis Services

Źródła danych i łączniki, Pobierz dane lub Kreatora importu programu Visual Studio są wyświetlane dla usług Azure Analysis Services i SQL Server Analysis Services. Jednak nie wszystkie źródła danych i łączniki, wyświetlane są obsługiwane w usług Azure Analysis Services. Typy źródeł danych, w którym można się połączyć są zależne od wielu czynników, takich jak model, poziom zgodności, łączników dostępnych danych, typ uwierzytelniania, dostawców i obsługę bramy danych lokalnych. 

## <a name="azure-data-sources"></a>Źródła danych Azure

|Źródło danych  |W pamięci  |Tryb DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Yes      |    Yes      |
|Azure SQL Data Warehouse     |   Yes      |   Yes       |
|Azure Blob Storage*     |   Yes       |    Nie      |
|Azure Table Storage*    |   Yes       |    Nie      |
|Azure Cosmos DB*     |  Yes        |  Nie        |
|Azure Data Lake Store*     |   Yes       |    Nie      |
|Azure HDInsight HDFS*     |     Yes     |   Nie       |
|Azure HDInsight Spark *     |   Yes       |   Nie       |
||||

\* Tylko modele w 1400 tabelarycznych.

**Dostawcy**   
W pamięci i modelach zapytania bezpośredniego połączenia ze źródłami danych Azure należy użyć dostawcy danych .NET Framework dla programu SQL Server.

## <a name="on-premises-data-sources"></a>Lokalne źródła danych

Łączenie z lokalnych źródeł danych z, a serwer Azure AS wymagają bramy lokalnego. Podczas korzystania z bramy, 64-bitowego dostawcy są wymagane.

### <a name="in-memory-and-directquery"></a>W pamięci i DirectQuery

|Źródło danych | Dostawca w pamięci | Dostawca zapytania bezpośredniego |
|  --- | --- | --- |
| Oprogramowanie SQL Server |SQL Server Native Client 11.0, Dostawca Microsoft OLE DB dla programu SQL Server, .NET Framework Data Provider for SQL Server | .NET framework Data Provider for SQL Server |
| Magazyn danych serwera SQL Server |SQL Server Native Client 11.0, Dostawca Microsoft OLE DB dla programu SQL Server, .NET Framework Data Provider for SQL Server | .NET framework Data Provider for SQL Server |
| Oracle |Dostawca Microsoft OLE DB dla Oracle, dostawca danych programu Oracle dla platformy .NET |Dostawca danych programu Oracle dla platformy .NET | |
| Teradata |Dostawca OLE DB dla programu Teradata, dostawca danych programu Teradata dla platformy .NET |Dostawca danych programu Teradata dla platformy .NET | |
| | | |

### <a name="in-memory-only"></a>W pamięci tylko

|Źródło danych  |  
|---------|---------|
|Dostępu do bazy danych     |  
|Active Directory *     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM*     |  
|Skoroszyt programu Excel     |  
|Exchange*     |  
|Folder *     | 
|Dokument JSON *     |  
|Wiersze z danych binarnych *     | 
|Baza danych MySQL     | 
|Źródła danych OData *     |  
|Zapytanie ODBC     | 
|OLE DB     |   
|Baza danych Postgre SQL *    | 
|SAP HANA *    |  
|SAP Business Warehouse *    |  
|SharePoint*     |   
|Baza danych programu Sybase     |  
|Tabela XML *    |  
|||
 
\* Tylko modele w 1400 tabelarycznych.

## <a name="specifying-a-different-provider"></a>Określenie innego dostawcy

Modeli danych z usług Azure Analysis Services może wymagać różnych dostawców podczas nawiązywania połączenia z niektórych źródeł danych. W niektórych przypadkach modeli tabelarycznych łączenie ze źródłami danych przy użyciu macierzystych dostawców, takich jak SQL Server Native Client (SQLNCLI11) może zostać zwrócony błąd. Jeśli przy użyciu macierzystych dostawców innych niż SQLOLEDB, może zostać wyświetlony komunikat o błędzie: **dostawcy "SQLNCLI11.1" nie jest zarejestrowana**. Lub, jeśli masz model zapytania bezpośredniego połączenia ze źródłami danych lokalnie i korzystać z natywnych dostawców, może zostać wyświetlony komunikat o błędzie: **wystąpił błąd podczas tworzenia zestawu wierszy OLE DB. Błędna składnia w pobliżu "Granica"**.

Podczas migrowania lokalnymi modelu tabelarycznego usług SQL Server Analysis Services do usług Azure Analysis Services, może być konieczna zmiana dostawcy.

**Aby określić dostawcę**

1. Programu SSDT > **Eksplorator modelu tabelarycznego** > **źródeł danych**, kliknij prawym przyciskiem myszy połączenie ze źródłem danych, a następnie kliknij przycisk **Edytuj źródło danych**.
2. W **Edytuj połączenie**, kliknij przycisk **zaawansowane** aby otworzyć okno właściwości wyprzedzeniem.
3. W **ustawić właściwości zaawansowane** > **dostawców**, następnie wybierz odpowiedniego dostawcę.

## <a name="impersonation"></a>Personifikacja
W niektórych przypadkach może być konieczne określenie konta różnych personifikacji. W programie Visual Studio (SSDT) lub SSMS można określić konta personifikacji.

Dla lokalnych źródeł danych:

* Jeśli uwierzytelnianie SQL personifikacji powinna być konta usługi.
* Jeśli przy użyciu uwierzytelniania systemu Windows, należy ustawić hasło użytkownika systemu Windows. Dla programu SQL Server uwierzytelnianie systemu Windows przy użyciu konta personifikacji określonych jest obsługiwana tylko w przypadku modeli danych w pamięci.

Dla źródeł danych w chmurze:

* Jeśli uwierzytelnianie SQL personifikacji powinna być konta usługi.

## <a name="next-steps"></a>Kolejne kroki
[Brama lokalna](analysis-services-gateway.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

