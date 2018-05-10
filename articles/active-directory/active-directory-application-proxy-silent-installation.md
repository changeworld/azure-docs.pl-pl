---
title: Instalację łącznika serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Uwzględniono również sposób przeprowadzenia instalacji nienadzorowanej programu Azure łącznika serwera Proxy aplikacji usługi AD, aby zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 33719ea36af3b3b554fc1d32878d7d72e11b7886
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Utwórz skrypt instalacji nienadzorowanej dla łącznika serwera Proxy aplikacji usługi Azure AD

Ten temat pomaga utworzyć skrypt programu Windows PowerShell, który umożliwia instalacji nienadzorowanej i rejestracji dla Twojego łącznika serwera Proxy aplikacji usługi Azure AD.

Ta funkcja jest przydatna, gdy chcesz:

* Zainstaluj łącznik na serwerach systemu Windows, które nie mają włączone interfejsu użytkownika, lub nie masz dostępu przy użyciu pulpitu zdalnego.
* Zainstaluj i zarejestruj wiele łączników na raz.
* Integracja instalacja łącznika i rejestracji w ramach innej procedury.
* Utworzyć obraz serwera standardowe zawierającą bitów łącznika, ale nie jest zarejestrowany.

Aby uzyskać [łącznika serwera Proxy aplikacji](application-proxy-understand-connectors.md) do pracy, musi być zarejestrowany w usłudze Azure AD w katalogu za pomocą administratora globalnego i hasło. Zwykle te informacje są wprowadzane podczas instalacji łącznika w oknie dialogowym wyskakujących, ale można zautomatyzować ten proces, zamiast tego środowiska PowerShell.

Istnieją dwa kroki dla instalacji nienadzorowanej. Najpierw należy zainstalować łącznik. Po drugie zarejestrowanie łącznika z usługą Azure AD. 

## <a name="install-the-connector"></a>Instalowanie łącznika
Aby zainstalować łącznik bez zarejestrowania go, wykonaj następujące kroki:

1. Otwórz wiersz polecenia.
2. Uruchom następujące polecenie, w którym/q oznacza instalację cichą. Instalację cichą nie Monituj należy zaakceptować umowę licencyjną użytkownika oprogramowania.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Zarejestrowanie łącznika z usługą Azure AD
Istnieją dwie metody, który umożliwia zarejestrowanie łącznika:

* Zarejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
* Zarejestrowanie łącznika za pomocą tokenu utworzony w trybie offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Zarejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
1. Utwórz obiekt poświadczeń programu Windows PowerShell `$cred` zawierający administracyjne nazwy użytkownika i hasła dla katalogu. Uruchom następujące polecenie, zastępując *\<username\>* i  *\<hasło\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Przejdź do **łącznika serwera Proxy aplikacji C:\Program Files\Microsoft AAD** i uruchomić następujący skrypt przy użyciu `$cred` obiekt, który został utworzony:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Zarejestrowanie łącznika za pomocą tokenu utworzony w trybie offline
1. Utwórz token w trybie offline za pomocą klasy kontekst AuthenticationContext przy użyciu wartości w poniższym przykładzie:

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. Po utworzeniu token, Utwórz SecureString, przy użyciu tokenu:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Uruchom następujące polecenie programu Windows PowerShell, zastępując \<identyfikator GUID dzierżawy\> za pomocą Identyfikatora katalogu:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Kolejne kroki 
* [Publikowanie aplikacji przy użyciu własnej nazwy domeny](active-directory-application-proxy-custom-domains.md)
* [Włączanie logowania jednokrotnego](active-directory-application-proxy-sso-using-kcd.md)
* [Rozwiązywanie problemów, które masz problem z serwerem Proxy aplikacji](active-directory-application-proxy-troubleshoot.md)


