---
title: Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami | Microsoft Docs
description: Opis sposobu użycia interfejsu API raportowania usługi Azure AD przy użyciu poświadczeń certyfikatu w celu pobrania danych z katalogów bez interwencji użytkownika.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 44ec19721ba59898915f6547231fb586cb44eb30
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389756"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](active-directory-reporting-api-getting-started-azure-portal.md) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. Jeśli chcesz uzyskać dostęp usługi Azure AD Reporting API bez interwencji użytkownika, można skonfigurować dostęp do używania certyfikatów.

Obejmuje to następujące czynności:

1. [Zainstaluj wymagania wstępne](#install-prerequisites)
2. [Zarejestruj certyfikat w swojej aplikacji](#register-the-certificate-in-your-app)
3. [Uzyskiwanie tokenu dostępu do interfejsu API programu Graph MS](#get-an-access-token-for-ms-graph-api)
4. [Zapytanie punkty końcowe interfejsu API programu Graph MS](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Instalacja wymagań wstępnych

1. Najpierw upewnij się, że zostały wykonane [wymagania wstępne dotyczące dostępu do usługi Azure Active Directory reporting API](active-directory-reporting-api-prerequisites-azure-portal.md). 

2. Pobieranie i instalowanie programu Azure AD Powershell V2, postępując zgodnie z instrukcjami w [programu PowerShell usługi Azure Active Directory](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)

3. Zainstaluj MSCloudIDUtils z [galerii PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ten moduł zapewnia kilka poleceń cmdlet narzędzi, w tym:
    - Biblioteki ADAL, służące do uwierzytelniania
    - Tokeny dostępu użytkownika, kluczy aplikacji i certyfikatów korzystających z bibliotek ADAL
    - Stronicowane wyniki obsługi interfejsu API programu Graph

4. Jeśli po raz pierwszy przy użyciu modułu uruchamiania **MSCloudIdUtilsModule instalacji** aby ukończyć instalację, w przeciwnym razie możesz po prostu go zaimportować za pomocą **Import-Module** polecenia programu Powershell.

Sesja powinna wyglądać podobnie do tego ekranu:

  ![Windows Powershell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Zarejestruj certyfikat w swojej aplikacji

1. Po pierwsze przejdź do strony rejestracji aplikacji. Można to zrobić, przechodząc do [witryny Azure portal](https://portal.azure.com), klikając pozycję **usługi Azure Active Directory**, klikając **rejestracje aplikacji** i wybierając aplikację z listy. 

2. Następnie postępuj zgodnie z instrukcjami, aby [rejestrowanie certyfikatu za pomocą usługi Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) dla aplikacji. 

3. Zanotuj identyfikator aplikacji i odcisk palca certyfikatu, które właśnie zostało zarejestrowane z aplikacją. Aby znaleźć odcisku palca, ze strony aplikacji w portalu, przejdź do **ustawienia** i kliknij przycisk **klucze**. Odcisk palca będzie wymieniony w obszarze **kluczy publicznych** listy.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Uzyskiwanie tokenu dostępu do interfejsu API programu Graph MS

Aby uzyskać token dostępu do interfejsu API programu Graph MS, należy użyć **Get MSCloudIdMSGraphAccessTokenFromCert** polecenia cmdlet z modułu programu MSCloudIdUtils PowerShell. 

>[!NOTE]
>Należy użyć Identyfikatora aplikacji (nazywane również ClientID) oraz odcisk palca certyfikatu z kluczem prywatnym zainstalowany w magazynie certyfikatów komputera (CurrentUser lub LocalMachine magazynu certyfikatów).
>

 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Wykorzystanie tokenu dostępu do wywołania interfejsu API programu Graph

Teraz służy tokenu dostępu za pomocą skryptu programu Powershell do wykonywania zapytań interfejsu API programu Graph. Poniżej przedstawiono przykład używa **Invoke MSCloudIdMSGraphQuery** polecenia cmdlet z MSCloudIDUtils wyliczyć plik logowań do lub diectoryAudits punktu końcowego. To polecenie cmdlet obsługuje wyniki wielostronicowe, a następnie wysyła te wyniki do potoku programu PowerShell.

### <a name="query-the-directoryaudits-endpoint"></a>Zapytanie DirectoryAudits punktu końcowego
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Zapytanie plik logowań do punktu końcowego
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-signins.png)

Teraz możesz wyeksportować te dane do woluminu CSV i zapisywanie w systemie SIEM. Skrypt można również opakować w zaplanowane zadanie, aby okresowo uzyskiwać dane usługi Azure AD od dzierżawcy bez konieczności przechowywania kluczy aplikacji w kodzie źródłowym. 


## <a name="next-steps"></a>Kolejne kroki

* [Pierwsze wrażenie dotyczące interfejsów API raportowania](active-directory-reporting-api-getting-started-azure-portal.md)
* [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Raport aktywności logowania, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



