---
title: Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami | Microsoft Docs
description: Opis sposobu użycia interfejsu API raportowania usługi Azure AD przy użyciu poświadczeń certyfikatu w celu pobrania danych z katalogów bez interwencji użytkownika.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 5ceb3d1ef99189e5b0cfcc48b38906ed28c07730
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588961"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Jeśli chcesz uzyskać dostęp do interfejsu API raportowania usługi Azure AD bez interwencji użytkownika, możesz skonfigurować dostęp z użyciem certyfikatów

W tym artykule:

- Opisano kroki wymagane do uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD przy użyciu certyfikatów.
- Przyjęto założenie, że spełniono [wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania usługi Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 


Aby uzyskiwać dostęp do interfejsu API raportowania przy użyciu certyfikatów, konieczne jest:

1. Zainstalowanie wstępnie wymaganych składników
2. Skonfigurowanie certyfikatu w aplikacji 
3. Udzielenie uprawnień
4. Pobranie tokenu dostępu




Aby uzyskać informacje o kodzie źródłowym, zobacz [Leverage Report API Module](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils) (Wykorzystanie modułu interfejsu API raportowania). 

## <a name="install-prerequisites"></a>Instalacja wymagań wstępnych

Konieczne jest zainstalowanie programu Azure AD PowerShell w wersji 2 i modułu AzureADUtils.

1. Pobierz i zainstaluj program Azure AD Powershell V2, postępując zgodnie z instrukcjami w temacie [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Pobierz moduł Azure AD Utils z lokalizacji [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Ten moduł zapewnia kilka poleceń cmdlet narzędzi, w tym:
    - Najnowszą wersję bibliotek ADAL korzystających z narzędzia Nuget
    - Tokeny dostępu użytkownika, kluczy aplikacji i certyfikatów korzystających z bibliotek ADAL
    - Stronicowane wyniki obsługi interfejsu API programu Graph

**Aby zainstalować moduł Azure AD Utils:**

1. Utwórz katalog w celu zapisania modułu narzędzi (na przykład c:\azureAD) i pobierz moduł z serwisu GitHub.
2. Otwórz sesję programu PowerShell i przejdź do utworzonego właśnie katalogu. 
3. Zaimportuj moduł i zainstaluj go w ścieżce modułu programu PowerShell za pomocą polecenia cmdlet Install-AzureADUtilsModule. 

Sesja powinna wyglądać podobnie do tego ekranu:

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Skonfigurowanie certyfikatu w aplikacji

**Aby skonfigurować certyfikat w aplikacji:**

1. [Pobierz identyfikator obiektu](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) aplikacji z witryny Azure Portal. 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Otwórz sesję programu PowerShell i nawiąż połączenie z usługą Azure AD za pomocą polecenia cmdlet Connect-AzureAD.

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Użyj polecenia cmdlet New-AzureADApplicationCertificateCredential z modułu AzureADUtils, aby dodać do niego poświadczenie certyfikatu. 

>[!Note]
>Należy podać zarejestrowany wcześniej identyfikator obiektu aplikacji, a także obiekt certyfikatu (służy do tego polecenie Cert: drive).
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Aby uzyskać token dostępu, użyj polecenia cmdlet **Get-AzureADGraphAPIAccessTokenFromCert** z modułu AzureADUtils. 

>[!NOTE]
>Należy użyć identyfikatora aplikacji zamiast identyfikatora obiektu użytego w ostatniej sekcji.
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Wykorzystanie tokenu dostępu do wywołania interfejsu API programu Graph

Teraz można utworzyć skrypt. Poniżej zamieszczono przykład korzystający z polecenia **Invoke-AzureADGraphAPIQuery** z modułu AzureADUtils. To polecenie cmdlet obsługuje wyniki wielostronicowe, a następnie wysyła te wyniki do potoku programu PowerShell. 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

Teraz można przystąpić do eksportowania wyników do formatu CSV i zapisu w systemie SIEM. Skrypt można również opakować w zaplanowane zadanie, aby okresowo uzyskiwać dane usługi Azure AD od dzierżawcy bez konieczności przechowywania kluczy aplikacji w kodzie źródłowym. 

## <a name="next-steps"></a>Następne kroki

- [Pierwsze wrażenie dotyczące interfejsów API raportowania](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Tworzenie własnego rozwiązania](active-directory-reporting-api-getting-started-azure-portal.md#customize)




