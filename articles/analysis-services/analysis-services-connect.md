---
title: Łączenie się z usługami Azure Analysis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie i Pobierz dane z serwerem usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8493ff1e2b9be48fc8eec1e727684fe09f6af72c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connect-to-an-azure-analysis-services-server"></a>Połącz się z serwerem usług Azure Analysis Services

W tym artykule opisano łączenia się z serwerem za pomocą modelowania danych i zarządzania aplikacji, takich jak SQL Server Management Studio (SSMS) lub SQL Server Data Tools (SSDT). Lub z klientem raportowanie aplikacji, takich jak program Microsoft Excel, Power BI Desktop lub niestandardowych aplikacji. Połączenia do usług Azure Analysis Services używają protokołu HTTPS.

## <a name="client-libraries"></a>Biblioteki klienckie
[Pobierz najnowsze biblioteki klienta](analysis-services-data-providers.md)

Wszystkie połączenia z serwerem, niezależnie od tego typu, wymagają zaktualizowanej biblioteki AMO, ADOMD.NET i OLEDB klienta do nawiązania połączenia i łączyć się z serwerem usług Analysis Services. Dla narzędzia SSMS, narzędzi SSDT Excel 2016 i usługi Power BI najnowsze biblioteki klienta są zainstalowane lub zaktualizowane wersje miesięcznych. Jednak w niektórych przypadkach jest aplikacji nie może mieć r. Na przykład gdy opóźnienie zasady aktualizacji lub aktualizacji usługi Office 365 są w kanale opóźnieniem.

## <a name="server-name"></a>Nazwa serwera

Podczas tworzenia serwera usług Analysis Services na platformie Azure, należy określić unikatową nazwę i regionu, w którym można utworzyć serwera. Podczas określania nazwy serwera na połączenie, jest schemat nazewnictwa serwera:

```
<protocol>://<region>/<servername>
```
 Gdy protokół jest ciągiem **asazure**, region jest identyfikatorem Uri, gdy serwer został utworzony (na przykład westus.asazure.windows.net) i servername jest nazwą serwera unikatowy w obrębie regionu.

### <a name="get-the-server-name"></a>Pobierz nazwę serwera
W **portalu Azure** > serwera > **omówienie** > **nazwy serwera**, skopiuj nazwę całego serwera. Jeśli innym użytkownikom w organizacji są zbyt połączenie do tego serwera, można udostępniać tę nazwę serwera z nimi. Podczas określania nazwy serwera, należy użyć pełną ścieżkę.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Parametry połączenia

Podczas nawiązywania połączenia przy użyciu modelu tabelarycznego obiektu usług Azure Analysis Services, użyj ciągu połączenia w następujących formatach:

###### <a name="integrated-azure-active-directory-authentication"></a>Zintegrowane uwierzytelnianie usługi Azure Active Directory
Uwierzytelnianie zintegrowane przejmuje pamięci podręcznej poświadczeń usługi Azure Active Directory, jeśli jest dostępna. Jeśli nie jest wyświetlane okno logowania do platformy Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure uwierzytelniania usługi Active Directory za pomocą nazwy użytkownika i hasła

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Uwierzytelnianie systemu Windows (zintegrowane zabezpieczenia)
Użyj konta systemu Windows uruchomiona bieżącego procesu.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```



## <a name="connect-using-an-odc-file"></a>Połącz, używając pliku odc
W starszych wersjach programu Excel użytkownicy mogą łączyć się z serwerem usług Azure Analysis Services przy użyciu pliku połączenia danych pakietu Office (odc). Aby dowiedzieć się więcej, zobacz [Tworzenie pliku połączenia danych pakietu Office (odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Kolejne kroki
[Połącz przy użyciu programu Excel](analysis-services-connect-excel.md)    
[Uzyskuj dostęp do usługi Power BI](analysis-services-connect-pbi.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

