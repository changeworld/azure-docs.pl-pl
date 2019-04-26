---
title: Łącznik serwera Proxy aplikacji usługi Azure AD instalację | Dokumentacja firmy Microsoft
description: Opisano, jak przeprowadzić instalację nienadzorowaną z platformy Azure łącznika serwera Proxy aplikacji usługi AD, aby zapewnić bezpieczny dostęp zdalny do aplikacji w środowisku lokalnym.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb193119186c2cf9e758f8c74f99f18c5fb389b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292660"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Utwórz skrypt instalacji nienadzorowanej dla łącznika serwera Proxy aplikacji usługi Azure AD

W tym temacie pomaga utworzyć skrypt programu Windows PowerShell, który umożliwia instalację nienadzorowaną i rejestracji dla łącznika serwera Proxy aplikacji usługi Azure AD.

Ta możliwość jest przydatna, gdy chcesz:

* Łącznik można zainstalować na serwerach Windows nie mają interfejsu użytkownika, włączone lub nie można uzyskać dostępu przy użyciu pulpitu zdalnego.
* Zainstaluj i zarejestruj jednocześnie wiele łączników.
* Integracja łącznika instalacji i rejestracji w ramach innej procedury.
* Utwórz obraz standardowy serwer, który zawiera łącznik usługi bits, ale nie jest zarejestrowany.

Aby uzyskać [łącznik serwera Proxy aplikacji](application-proxy-connectors.md) do pracy, musi być zarejestrowana w katalogu usługi Azure AD przy użyciu aplikacji administratora i hasła. Zazwyczaj te informacje są wprowadzane podczas instalacji łącznika w podręcznym oknie dialogowym, ale można użyć programu PowerShell można zautomatyzować ten proces, zamiast tego.

Istnieją dwa kroki dla instalacji nienadzorowanej. Najpierw należy zainstalować łącznik. Po drugie zarejestrowanie łącznika usługi Azure AD. 

## <a name="install-the-connector"></a>Zainstaluj łącznik
Aby zainstalować łącznik bez rejestrowania jej, wykonaj następujące kroki:

1. Otwórz wiersz polecenia.
2. Uruchom następujące polecenie, w którym / q oznacza instalacji w trybie cichym. Instalację cichą nie monit o zaakceptowanie umowy licencyjnej użytkownika końcowego.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Rejestrowanie łącznika z usługą Azure AD
Istnieją dwie metody, których można użyć do zarejestrowania łącznika:

* Rejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
* Rejestrowanie łącznika przy użyciu tokenu, który został utworzony w trybie offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Rejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
1. Utwórz obiekt poświadczeń PowerShell Windows `$cred` zawiera administracyjnej nazwy użytkownika i hasła dla katalogu. Uruchom następujące polecenie, zastępując *\<username\>* i  *\<hasło\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Przejdź do **łącznik serwera Proxy aplikacji usługi AAD w C:\Program Files\Microsoft** i uruchom następujący skrypt, używając `$cred` obiektu, który został utworzony:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Rejestrowanie łącznika przy użyciu tokenu, który został utworzony w trybie offline
1. Utwórz token w trybie offline przy użyciu klasy AuthenticationContext przy użyciu wartości w tym fragmencie kodu lub poniższe polecenia cmdlet programu PowerShell:

    **Przy użyciu języka C#:**

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

    **Przy użyciu programu PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Po utworzeniu tokenu, można utworzyć obiektu SecureString przy użyciu tokenu:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Uruchom następujące polecenie programu Windows PowerShell, zastępując \<dzierżawy GUID\> za pomocą Identyfikatora katalogu:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Kolejne kroki 
* [Publikowanie aplikacji przy użyciu własnej nazwy domeny](application-proxy-configure-custom-domain.md)
* [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
* [Rozwiązywanie problemów z serwerem Proxy aplikacji](application-proxy-troubleshoot.md)


