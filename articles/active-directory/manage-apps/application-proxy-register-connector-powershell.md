---
title: Łącznik serwera proxy aplikacji usługi Azure AD w instalacji dyskretnej | Dokumenty firmy Microsoft
description: Obejmuje sposób wykonywania instalacji nienadzorowanych usługi Azure AD Application Proxy Connector w celu zapewnienia bezpiecznego dostępu zdalnego do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b43d2de0a366d7e69a025b2e4e2998dccda2038e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756215"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Tworzenie skryptu instalacji nienadzorowanego dla łącznika serwera proxy aplikacji usługi Azure AD

W tym temacie pomaga utworzyć skrypt programu Windows PowerShell, który umożliwia instalację i rejestrację nienadzorowane dla łącznika serwera proxy aplikacji usługi Azure AD.

Ta funkcja jest przydatna, gdy chcesz:

* Zainstaluj łącznik na serwerach systemu Windows, na których nie ma włączonego interfejsu użytkownika lub do którego nie można uzyskać dostępu za pomocą pulpitu zdalnego.
* Zainstaluj i zarejestruj wiele złączy jednocześnie.
* Zintegruj instalację i rejestrację łącznika w ramach innej procedury.
* Utwórz standardowy obraz serwera, który zawiera bity łącznika, ale nie jest zarejestrowany.

Aby [łącznik serwera proxy aplikacji](application-proxy-connectors.md) działał, musi być zarejestrowany w katalogu usługi Azure AD przy użyciu administratora aplikacji i hasła. Zwykle te informacje są wprowadzane podczas instalacji łącznika w wyskakującym oknie dialogowym, ale zamiast tego można zautomatyzować ten proces za pomocą programu PowerShell.

Istnieją dwa kroki dla instalacji nienadzorowane. Najpierw zainstaluj złącze. Po drugie zarejestruj łącznik za pomocą usługi Azure AD. 

## <a name="install-the-connector"></a>Zainstaluj złącze
Aby zainstalować łącznik bez rejestrowania, należy wykonać następujące czynności:

1. Otwórz wiersz polecenia.
2. Uruchom następujące polecenie, w którym /q oznacza cichą instalację. Cicha instalacja nie powoduje monitu o zaakceptowanie umowy licencyjnej użytkownika końcowego.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Rejestrowanie łącznika w usłudze Azure AD
Istnieją dwie metody, których można użyć do zarejestrowania łącznika:

* Rejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
* Rejestrowanie łącznika przy użyciu tokenu utworzonego w trybie offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Rejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
1. Utwórz obiekt `$cred` Poświadczenia programu Windows PowerShell zawierający administracyjną nazwę użytkownika i hasło do katalogu. Uruchom następujące polecenie, * \<\> * zastępując nazwę użytkownika i * \<hasło:\>*
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Przejdź do **folderu C:\Program Files\Microsoft AAD App** `$cred` Proxy Connector i uruchom następujący skrypt przy użyciu utworzonego obiektu:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Rejestrowanie łącznika przy użyciu tokenu utworzonego w trybie offline
1. Utwórz token w trybie offline przy użyciu klasy AuthenticationContext przy użyciu wartości we wpisie kodu lub poleceń cmdlet programu PowerShell poniżej:

    **Korzystanie z języka C#:**

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

    **Korzystanie z programu PowerShell:**

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

2. Gdy masz token, utwórz SecureString przy użyciu tokenu:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Uruchom następujące polecenie programu Windows \<PowerShell, zastępując identyfikator GUID\> dzierżawy identyfikatorem katalogu:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Następne kroki 
* [Publikowanie aplikacji przy użyciu własnej nazwy domeny](application-proxy-configure-custom-domain.md)
* [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
* [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)


