---
title: Samouczek pobieranie danych przy użyciu usługi Azure API raportowania usługi AD z certyfikatami | Dokumentacja firmy Microsoft
description: W tym samouczku opisano sposób użycia usługi Azure AD interfejsu API raportowania przy użyciu poświadczeń certyfikatu w celu pobrania danych z katalogów bez interwencji użytkownika.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e006111cce7f53ff87f1c6d60b2a5147da02e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60284933"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Samouczek: Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](concept-reporting-api.md) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. Jeśli chcesz uzyskać dostęp usługi Azure AD Reporting API bez interwencji użytkownika, należy skonfigurować dostęp do używania certyfikatów.

W tym samouczku dowiesz się, jak dostęp do interfejsu API programu Graph MS dla raportowania za pomocą certyfikatu testu. Nie zaleca się korzystania z certyfikatów testów w środowisku produkcyjnym. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Aby uzyskać dostęp do danych logowania, upewnij się, że masz już dzierżawę usługi Azure Active Directory z licencją premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach. 

2. Tworzenie lub przełącz się do konta użytkownika w **administratora globalnego**, **administrator zabezpieczeń**, **Czytelnik zabezpieczeń** lub **czytnika raportu** Rola dla dzierżawy. 

3. Wykonaj [wymagania wstępne dotyczące dostępu do usługi Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

4. Pobierz i zainstaluj [usługi Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Zainstaluj [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ten moduł zapewnia kilka poleceń cmdlet narzędzi, w tym:
    - Biblioteki ADAL, służące do uwierzytelniania
    - Tokeny dostępu użytkownika, kluczy aplikacji i certyfikatów korzystających z bibliotek ADAL
    - Stronicowane wyniki obsługi interfejsu API programu Graph

6. Jeśli po raz pierwszy przy użyciu modułu uruchamiania **MSCloudIdUtilsModule instalacji**, w przeciwnym razie importować go za pomocą **Import-Module** polecenia programu Powershell. Sesja powinna wyglądać podobnie do tego ekranu: ![Program Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Użyj **New-SelfSignedCertificate** polecenia cmdlet programu Powershell do utworzenia certyfikatu testowego.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Użyj **eksportu certyfikatu** polecenia cmdlet, aby wyeksportować je do pliku certyfikatu.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

1. Przejdź do [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Azure Active Directory**, a następnie wybierz **rejestracje aplikacji** i wybierz aplikację z listy. 

2. Wybierz **ustawienia** > **klucze** i wybierz **Przekaż klucz publiczny**.

3. Wybierz plik certyfikatu z poprzedniego kroku, a następnie wybierz **Zapisz**. 

4. Zanotuj identyfikator aplikacji i odcisk palca certyfikatu, które właśnie zostało zarejestrowane z aplikacją. Aby znaleźć odcisku palca, ze strony aplikacji w portalu, przejdź do **ustawienia** i kliknij przycisk **klucze**. Odcisk palca będzie wymieniony w obszarze **kluczy publicznych** listy.

5. Otwórz manifest aplikacji w edytorze manifestu w tekście i Zastąp *keyCredentials* właściwości z informacjami certyfikatu przy użyciu następującego schematu. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Umożliwia zapisanie manifestu. 
  
7. Teraz możesz uzyskać dostęp do tokenu dla interfejsu API programu Graph MS przy użyciu tego certyfikatu. Użyj **Get MSCloudIdMSGraphAccessTokenFromCert** polecenia cmdlet z modułu MSCloudIdUtils PowerShell, przekazując Identyfikatora aplikacji i odcisk palca uzyskanego w poprzednim kroku. 

   ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Korzystanie z tokenu dostępu za pomocą skryptu programu Powershell do wykonywania zapytań interfejsu API programu Graph. Użyj **Invoke MSCloudIdMSGraphQuery** polecenia cmdlet z MSCloudIDUtils wyliczyć plik logowań do i directoryAudits punkt końcowy. To polecenie cmdlet obsługuje wyniki wielostronicowe, a następnie wysyła te wyniki do potoku programu PowerShell.

9. Wyślij zapytanie do endpoint directoryAudits, aby pobrać dzienniki inspekcji. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Wyślij zapytanie do endpoint plik logowań do można pobrać dzienników logowania.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Teraz możesz wyeksportować te dane do woluminu CSV i zapisywanie w systemie SIEM. Skrypt można również opakować w zaplanowane zadanie, aby okresowo uzyskiwać dane usługi Azure AD od dzierżawcy bez konieczności przechowywania kluczy aplikacji w kodzie źródłowym. 

## <a name="next-steps"></a>Kolejne kroki

* [Pierwsze wrażenie dotyczące interfejsów API raportowania](concept-reporting-api.md)
* [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Raport aktywności logowania, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
