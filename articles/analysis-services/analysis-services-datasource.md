---
title: Źródła danych obsługiwane w Azure Analysis Services | Microsoft Docs
description: Opisuje źródła danych obsługiwane przez modele danych w Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 81fc73ffd61a49eae1c4f107733b6f9f53efbb4f
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993390"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Źródła danych obsługiwane w Azure Analysis Services

Źródła danych i łączniki wyświetlane w Kreatorze pobieranie danych lub importowanie w programie Visual Studio są wyświetlane dla obu Azure Analysis Services i SQL Server Analysis Services. Nie wszystkie wyświetlane źródła danych i łączniki są jednak obsługiwane w Azure Analysis Services. Typy źródeł danych, z którymi można nawiązać połączenie, są zależne od wielu czynników, takich jak poziom zgodności modelu, dostępne łączniki danych, typ uwierzytelniania, dostawcy i obsługa lokalnej bramy danych. 

## <a name="azure-data-sources"></a>Źródła danych na platformie Azure

|Źródło danych  |W pamięci  |Tryb DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Tak      |    Tak      |
|Azure SQL Data Warehouse     |   Tak      |   Tak       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Tak       |    Nie      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Tak       |    Nie      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Tak        |  Nie        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Tak       |    Nie      |
|Usługa Azure HDInsight w systemie plików HDFS<sup>[1](#tab1400a)</sup>     |     Tak     |   Nie       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Tak       |   Nie       |
||||

tylko <a name="tab1400a">1</a> -tabelaryczny model 1400 i wyższe.   
Obsługiwane jest <a name="azsqlmanaged">2</a> Azure SQL Database wystąpienia zarządzanego. Ponieważ wystąpienie zarządzane działa w sieci wirtualnej platformy Azure z prywatnym adresem IP, w tym wystąpieniu musi być włączony publiczny punkt końcowy. Jeśli ta funkcja nie jest włączona, wymagana jest lokalna Brama danych.    
<a name="databricks">3</a> — Azure Databricks przy użyciu łącznika Spark nie jest obecnie obsługiwany.   
<a name="gen2">4</a> — ADLS Gen2 nie jest obecnie obsługiwane.


**Dostawcy**   
Modele w pamięci i DirectQuery łączące się ze źródłami danych platformy Azure używają .NET Framework Dostawca danych do SQL Server.

## <a name="other-data-sources"></a>Inne źródła danych

Nawiązywanie połączenia z lokalnymi źródłami danych z programu i platformy Azure jako serwera wymaga bramy lokalnej. W przypadku korzystania z bramy wymagane są 64-bitowe dostawcy.

### <a name="in-memory-and-directquery"></a>W pamięci i DirectQuery

|Źródło danych | Dostawca w pamięci | Dostawca zapytania bezpośredniego |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11,0, dostawca OLE DB firmy Microsoft dla SQL Server .NET Framework Dostawca danych dla SQL Server | .NET Framework Dostawca danych SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11,0, dostawca OLE DB firmy Microsoft dla SQL Server .NET Framework Dostawca danych dla SQL Server | .NET Framework Dostawca danych SQL Server |
| Oracle | OLE DB Provider for Oracle, Oracle Dostawca danych dla platformy .NET |Oracle Dostawca danych dla platformy .NET |
| Teradata |Dostawca OLE DB dla programu Teradata, Dostawca danych programu Teradata dla platformy .NET |Dostawca danych programu Teradata dla platformy .NET |
| | | |

### <a name="in-memory-only"></a>Tylko w pamięci

|Źródło danych  |  
|---------|
|Baza danych programu Access     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|System platformy analizy     |  
|Plik CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Skoroszyt programu Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Folder<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (beta) |
|Dokument JSON<sup>[1](#tab1400b)</sup>     |  
|Wiersze z pliku binarnego<sup>[1](#tab1400b)</sup>     | 
|Baza danych MySQL     | 
|Źródło danych OData<sup>[1](#tab1400b)</sup>     |  
|Zapytanie ODBC     | 
|OLE DB     |   
|Baza danych PostgreSQL<sup>[1](#tab1400b)</sup>    | 
|Obiekty usług Salesforce<sup>[1](#tab1400b)</sup> |  
|Raporty usług Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|Lista programu SharePoint<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Baza danych programu Sybase     |  
|Plik TXT  |
|Tabela XML<sup>[1](#tab1400b)</sup>    |  
||
 
tylko <a name="tab1400b">1</a> -tabelaryczny model 1400 i wyższe.   
<a name="filesSP">2</a> — pliki w lokalnym programie SharePoint nie są obsługiwane.

## <a name="specifying-a-different-provider"></a>Określanie innego dostawcy

Modele danych w Azure Analysis Services mogą wymagać różnych dostawców danych podczas łączenia się z pewnymi źródłami danych. W niektórych przypadkach modele tabelaryczne łączące się ze źródłami danych przy użyciu natywnych dostawców, takich jak SQL Server Native Client (SQLNCLI11), mogą zwrócić błąd. W przypadku korzystania z natywnych dostawców innych niż SQLOLEDB może zostać wyświetlony komunikat o błędzie: **Dostawca "sqlncli 11.1" nie jest zarejestrowany**. Lub, jeśli masz model zapytania bezpośredniego łączący się z lokalnymi źródłami danych i używasz dostawców natywnych, może pojawić się komunikat o błędzie: **Wystąpił błąd podczas tworzenia zestawu wierszy OLE DB. Nieprawidłowa składnia w sąsiedztwie "** limit".

W przypadku migrowania lokalnego modelu SQL Server Analysis Services tabelarycznego do Azure Analysis Services, może być konieczne zmianę dostawcy.

**Aby określić dostawcę**

1. W SSDT >**źródła danych** >  **Eksploratora modeli tabelarycznych**kliknij prawym przyciskiem myszy połączenie ze źródłem danych, a następnie kliknij polecenie **Edytuj źródło danych**.
2. W obszarze **Edytuj połączenie**kliknij pozycję **Zaawansowane** , aby otworzyć okno właściwości zaawansowana.
3. W obszarze **Ustaw zaawansowane właściwości** > **dostawców**wybierz odpowiedniego dostawcę.

## <a name="impersonation"></a>Personifikacja
W niektórych przypadkach może być konieczne określenie innego konta personifikacji. Konto personifikacji można określić w programie Visual Studio (SSDT) lub w programie SSMS.

Dla lokalnych źródeł danych:

* W przypadku korzystania z uwierzytelniania SQL, personifikacja powinna być kontem usługi.
* Jeśli używasz uwierzytelniania systemu Windows, ustaw użytkownika/hasło systemu Windows. W przypadku SQL Server Uwierzytelnianie systemu Windows z określonym kontem personifikacji jest obsługiwane tylko dla modeli danych w pamięci.

W przypadku źródeł danych w chmurze:

* W przypadku korzystania z uwierzytelniania SQL, personifikacja powinna być kontem usługi.

## <a name="oauth-credentials"></a>Poświadczenia uwierzytelniania OAuth

Dla modeli tabelarycznych na poziomie zgodności 1400 i wyższym Azure SQL Database, Azure SQL Data Warehouse, Dynamics 365 i lista programu SharePoint obsługują poświadczenia uwierzytelniania OAuth. Azure Analysis Services zarządza odświeżanie tokenów dla źródeł danych OAuth w celu uniknięcia przekroczeń limitu czasu dla długotrwałych operacji odświeżania. Aby wygenerować prawidłowe tokeny, Ustaw poświadczenia za pomocą programu SSMS.

## <a name="next-steps"></a>Następne kroki
[Brama lokalna](analysis-services-gateway.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

