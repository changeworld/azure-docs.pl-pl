---
title: Biblioteki klienckie wymaganych do łączenia się z usługami Azure Analysis Services | Dokumentacja firmy Microsoft
description: W tym artykule opisano bibliotek klienta wymagane dla aplikacji klienckich i narzędzia do łączenia z usług Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bfd488828d9c96461cb371b35bb0a0cb24fff373
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891943"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Biblioteki klienckie do łączenia się z usług Azure Analysis Services

Biblioteki klienckie są niezbędne dla aplikacji klienckich i narzędzia do łączenia się z serwerami usług Analysis Services. Aplikacje klienckie firmy Microsoft, takich jak Power BI Desktop, Excel, SQL Server Management Studio (SSMS), a SQL Server Data Tools (SSDT) zainstaluj wszystkie trzy biblioteki klienta i aktualizować je wraz z aplikacji regularne aktualizacje. W niektórych przypadkach może być konieczne zainstalowanie nowszej wersji bibliotek klienckich. Niestandardowych aplikacji klienckich również wymagają, że są zainstalowane biblioteki klienckie.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Pobierz najnowsze biblioteki klienta (Instalator Windows)  

|Do pobrania  |Wersja produktu  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.11.19    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.11.19      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.15.0.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.15.0.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO i ADOMD (pakietów NuGet)

Biblioteki klienckie usług Analysis Services Management Objects (AMO) i ADOMD są dostępne jako możliwe do zainstalowania pakietów z [NuGet.org](https://www.nuget.org/). Zalecane jest, że migracja do odwołań NuGet zamiast przy użyciu Instalatora Windows. 

|Pakiet  | Wersja produktu  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.15.0.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.15.0.0      |

Zestawy pakietów NuGet AssemblyVersion wykonaj semantycznego wersji: MAJOR.MINOR.PATCH. Odwołania do NuGet obciążenia oczekiwanej wersji, nawet jeśli dostępny jest inna wersja w globalnej pamięci podręcznej zestawów (wynikające z instalacji MSI). POPRAWKI są zwiększane dla każdej wersji. Wersje AMO i ADOMD są utrzymywane w synchronizacji.

## <a name="understanding-client-libraries"></a>Omówienie biblioteki klienckie

Korzystanie z trzy biblioteki klienta, znany także jako dostawcy danych usług Analysis Services. Program ADOMD.NET i usługi Analysis Services Management Objects (AMO) są biblioteki klienckie typu zarządzanego. Dostawca Analysis Services OLE DB (MSOLAP DLL) to biblioteka klienta natywnego. Zazwyczaj wszystkie trzy są instalowane w tym samym czasie. **Usługa Azure Analysis Services wymaga najnowszej wersji wszystkich trzech bibliotek**. 

Aplikacje klienckie firmy Microsoft, takich jak program Excel i Power BI Desktop Zainstaluj wszystkie trzy biblioteki klienta i aktualizować je, gdy są dostępne nowe wersje. W zależności od wersji lub częstotliwości aktualizacji niektóre biblioteki klienta mogą nie być w najnowszej wersji wymaganej przez usługi Azure Analysis Services. To samo dotyczy aplikacji niestandardowych lub innych interfejsów, takich jak AsCmd, TOM czy ADOMD.NET. Te aplikacje wymagają ręcznie lub programowo instalacji bibliotek. Biblioteki klienckie, aby przeprowadzić ręczną instalację znajdują się w SQL Server feature Pack jako dystrybucyjny pakietów. Jednak tych bibliotek klienckich są powiązane z wersji programu SQL Server i nie może być r.  

Biblioteki klienckie dla połączeń klienckich różnią się od dostawców danych wymagane do połączenia z serwerem usług Azure Analysis Services ze źródłem danych. Aby dowiedzieć się więcej na temat połączenia źródła danych, zobacz [połączenia źródła danych](analysis-services-datasource.md).

## <a name="client-library-types"></a>Klient biblioteki typów

### <a name="analysis-services-ole-db-provider-msolap"></a>Dostawca usług Analysis Services OLE DB (MSOLAP) 

 Analiza usługi OLE DB Provider (MSOLAP) jest biblioteka klienta natywnego dla połączenia z bazą danych usług Analysis Services. Jest pośrednio używany zarówno przez program ADOMD.NET i AMO, delegowanie żądań połączenia do dostawcy danych. Dostawca OLE DB można również wywołać bezpośrednio w kodzie aplikacji.  
  
 Dostawca Analysis Services OLE DB jest instalowana automatycznie przez większość narzędzi i aplikacji klienckich, które umożliwiają dostęp do bazy danych usług Analysis Services. Musi być zainstalowany na komputerach, które umożliwiają dostęp do danych usług Analysis Services.  
  
 Dostawcy OLE DB często są określone w parametrach połączenia. Parametry połączenia usługi Analysis Services używa różnych nomenklatury do odwoływania się do dostawcy OLE DB: MSOLAP. \<wersji > .dll.

### <a name="amo"></a>AMO  

 AMO jest zarządzana biblioteka kliencka służy do administrowania serwerem i definicję danych. Zostanie on zainstalowany i używana przez narzędzia i aplikacje klienckie. Na przykład SQL Server Management Studio (SSMS) używa AMO nawiązywania połączenia z usługami Analysis Services. Połączenie przy użyciu AMO jest zazwyczaj minimalny, składający się z `"data source=\<servername>"`. Po nawiązaniu połączenia możesz za pomocą interfejsu API z kolekcji baz danych i obiektów głównych. Program SSMS i SSDT Użyj AMO, aby połączyć się z wystąpieniem usług Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 Program ADOMD.NET jest biblioteka klienta zarządzanych danych używane do wykonywania zapytań z danymi usług Analysis Services. Zostanie on zainstalowany i używana przez narzędzia i aplikacje klienckie. 
  
 Podczas nawiązywania połączenia z bazą danych, właściwości parametrów połączenia dla wszystkich trzech bibliotek są podobne. Parametry połączenia, prawie każdy ADOMD.NET są definiowane za pomocą [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) działa również w przypadku AMO i Analysis Services OLE DB dostawcy (MSOLAP). Aby dowiedzieć się więcej, zobacz [właściwości parametrów połączenia &#40;usług Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Jak określić wersji biblioteki klienta   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Przejdź do pozycji `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` (Plik > Nowy > Inny). Jeśli masz więcej niż jednego folderu, wybierz wyższy numer.
  
2.  Kliknij prawym przyciskiem myszy **msolap.dll** > **właściwości** > **szczegóły**. Jeśli nazwa pliku jest msolap140.dll, jest starsza niż najnowszej wersji i powinny zostać uaktualnione.
    
    ![Szczegóły biblioteki klienta](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Przejdź do pozycji `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` (Plik > Nowy > Inny). Jeśli masz więcej niż jednego folderu, wybierz wyższy numer.
2. Kliknij prawym przyciskiem myszy **Microsoft.AnalysisServices** > **właściwości** > **szczegóły**.  

### <a name="adomd"></a>ADOMD

1. Przejdź do pozycji `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` (Plik > Nowy > Inny). Jeśli masz więcej niż jednego folderu, wybierz wyższy numer.
2. Kliknij prawym przyciskiem myszy **Microsoft.AnalysisServices.AdomdClient** > **właściwości** > **szczegóły**.  


## <a name="next-steps"></a>Kolejne kroki
[Łączenie z programem Excel](analysis-services-connect-excel.md)    
[Łączenie z usługą Power BI](analysis-services-connect-pbi.md)
