---
title: Łączenie z serwerami usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie i pobieranie danych z serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9a8863189ee9cb63d86b157c0bbebb6fd16116b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027987"
---
# <a name="connecting-to-servers"></a>Łączenie z serwerami

W tym artykule opisano, w połączeniu z serwerem przy użyciu modelowania danych i zarządzania aplikacji, takich jak SQL Server Management Studio (SSMS) lub SQL Server Data Tools (SSDT). Lub przy użyciu klienta raportowania aplikacji, takich jak program Microsoft Excel, Power BI Desktop lub niestandardowych aplikacji. Połączenia z usługami Azure Analysis Services używają protokołu HTTPS.

## <a name="client-libraries"></a>Biblioteki klienta

[Pobierz najnowsze biblioteki klienta](analysis-services-data-providers.md)

Wszystkie połączenia z serwerem, niezależnie od typu, wymagają zaktualizowanego AMO, ADOMD.NET i OLEDB biblioteki klienckie do interfejsu z serwerem usług Analysis Services i połączyć się z. Dla programu SSMS, narzędzi SSDT, program Excel 2016 lub nowszy i usługi Power BI najnowsze biblioteki klienta są zainstalowano lub zaktualizowano z wersjami miesięcznych. Jednak w niektórych przypadkach jest to możliwe, aplikacja może nie mieć najnowsze informacje. Na przykład podczas aktualizacji zasad opóźnienia lub aktualizacji usługi Office 365 są w kanale opóźnieniem.

## <a name="server-name"></a>Nazwa serwera

Podczas tworzenia serwera usług Analysis Services na platformie Azure, należy określić unikatową nazwę i region, w którym ma zostać utworzony serwer. Podczas określania nazwy serwera w przypadku połączenia, to schemat nazewnictwa serwera:

```
<protocol>://<region>/<servername>
```
 Gdy protokół jest ciągiem **asazure**, region jest identyfikator Uri, do której został utworzony serwer (na przykład westus.asazure.windows.net) i servername jest nazwą serwera unikatowy w obrębie regionu.

### <a name="get-the-server-name"></a>Pobierz nazwę serwera

W **witryny Azure portal** > serwer > **Przegląd** > **nazwy serwera**, skopiuj nazwę całego serwera. Jeśli innym użytkownikom w organizacji nawiązują połączenie z tym serwerem zbyt, możesz udostępnić tę nazwę serwera z nimi. W przypadku podawania nazwy serwera, należy użyć całej ścieżki.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokół dla regionu wschodnie stany USA 2 jest **aspaaseastus2**.

## <a name="connection-string"></a>Parametry połączenia

Podczas nawiązywania połączenia przy użyciu tabelarycznego modelu obiektu usług Azure Analysis Services, należy użyć następujących formatów ciąg połączenia:

###### <a name="integrated-azure-active-directory-authentication"></a>Zintegrowane uwierzytelnianie usługi Azure Active Directory

Uwierzytelnianie zintegrowane przejmuje pamięci podręcznej poświadczeń usługi Azure Active Directory, jeśli jest dostępny. Jeśli nie, wyświetlane jest okno logowania do platformy Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Uwierzytelnianie usługi Active Directory systemu Azure przy użyciu nazwy użytkownika i hasła

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows authentication (zabezpieczeń zintegrowanych)

Użyj konta Windows uruchamiania bieżącego procesu.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Nawiązywanie połączenia przy użyciu pliku odc

Ze starszymi wersjami programu Excel użytkownicy mogą łączyć się z serwerem usług Azure Analysis Services przy użyciu pliku połączenia danych pakietu Office (odc). Aby dowiedzieć się więcej, zobacz [Tworzenie pliku połączenia danych pakietu Office (odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Kolejne kroki

[Łączenie z programem Excel](analysis-services-connect-excel.md)    
[Łączenie z usługą Power BI](analysis-services-connect-pbi.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

