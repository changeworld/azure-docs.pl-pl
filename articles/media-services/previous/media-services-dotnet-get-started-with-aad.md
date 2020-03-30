---
title: Użyj uwierzytelniania usługi Azure AD, aby uzyskać dostęp do interfejsu API usługi Azure Media Services za pomocą platformy .NET | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać uwierzytelniania usługi Azure Active Directory (Azure AD) do uzyskiwania dostępu do interfejsu API usługi Azure Media Services (AMS) za pomocą platformy .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b53fca292630ef988ee1357ea50adc4d7b7e9be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162883"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Użyj uwierzytelniania usługi Azure AD, aby uzyskać dostęp do interfejsu API usługi Azure Media Services za pomocą platformy .NET

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Począwszy od windowsazure.mediaservices 4.0.0.4, usługa Azure Media Services obsługuje uwierzytelnianie oparte na usłudze Azure Active Directory (Azure AD). W tym temacie pokazano, jak używać uwierzytelniania usługi Azure AD do uzyskiwania dostępu do interfejsu API usługi Azure Media Services za pomocą platformy Microsoft .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure Portal](media-services-portal-create-account.md).
- Najnowszy pakiet [NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices)
- Znajomość tematu [Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services z omówieniem uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Korzystając z uwierzytelniania usługi Azure AD za pomocą usługi Azure Media Services, można uwierzytelnić się na jeden z dwóch sposobów:

- **Uwierzytelnianie użytkownika** uwierzytelnia osobę, która używa aplikacji do interakcji z zasobami usługi Azure Media Services. Interaktywna aplikacja powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania, która jest używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 
- **Uwierzytelnianie jednostkowe usługi** uwierzytelnia usługę. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które uruchamiają usługi demona, usługi warstwy środkowej lub zaplanowane zadania, takie jak aplikacje sieci web, aplikacje funkcji, aplikacje logiki, interfejsy API lub mikrousług.

>[!IMPORTANT]
>Usługa Azure Media Service obsługuje obecnie model uwierzytelniania usługi Azure Access Control Service. Jednak autoryzacja kontroli dostępu zostanie przestarzała 22 czerwca 2018 r. Zaleca się jak najszybsze migrację do modelu uwierzytelniania usługi Azure Active Directory.

## <a name="get-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD

Aby połączyć się z interfejsem API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD, aplikacja kliencka musi zażądać tokenu dostępu usługi Azure AD. Korzystając z SDK klienta usługi Media Services .NET wiele szczegółów dotyczących uzyskiwania tokenu dostępu usługi Azure AD jest zawijanych i uproszczonych w klasach [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) i [AzureAdTokenCredentials.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) 

Na przykład nie trzeba podawać urząd usługi Azure AD, URI zasobów usługi Media Services lub natywnych szczegółów aplikacji usługi Azure AD. Są to dobrze znane wartości, które są już skonfigurowane przez klasę dostawcy tokenu dostępu usługi Azure AD. 

Jeśli nie używasz narzędzia Azure Media Service .NET SDK, zaleca się użycie [biblioteki uwierzytelniania usługi Azure AD.](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) Aby uzyskać wartości parametrów, których potrzebujesz z biblioteką uwierzytelniania usługi Azure AD, zobacz Korzystanie z [witryny Azure Portal w celu uzyskania dostępu do ustawień uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md).

Istnieje również możliwość zastąpienia domyślnej implementacji **AzureAdTokenProvider** z własnej implementacji.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalowanie i konfigurowanie pakietu Azure Media Services .NET SDK

>[!NOTE] 
>Aby używać uwierzytelniania usługi Azure AD przy użyciu zestawu SDK usługi Media Services .NET, musisz mieć najnowszy pakiet [NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices) Ponadto należy dodać odwołanie do zestawu **Microsoft.IdentityModel.Clients.ActiveDirectory.** Jeśli używasz istniejącej aplikacji, dołącz zestaw **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll.** 

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio.
2. Użyj pakietu [Windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet, aby zainstalować pakiet **Azure Media Services .NET SDK**. 

    Aby dodać odwołania przy użyciu nuget, należy wykonać następujące kroki: w **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Następnie wyszukaj **pozycję windowsazure.mediaservices** i wybierz pozycję **Zainstaluj**.
    
    — lub —

    Uruchom następujące polecenie w **konsoli Menedżera pakietów** w programie Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Dodaj **za pomocą** do kodu źródłowego.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Korzystanie z uwierzytelniania użytkowników

Aby połączyć się z interfejsem API usługi Azure Media Service za pomocą opcji uwierzytelniania użytkownika, aplikacja kliencka musi zażądać tokenu usługi Azure AD przy użyciu następujących parametrów:  

- Punkt końcowy dzierżawy usługi Azure AD. Informacje o dzierżawie można pobrać z witryny Azure portal. Umieść wskaźnik myszy na zalogowanym użytkowniku w prawym górnym rogu.
- Identyfikator URI zasobu usługi Media Services.
- Identyfikator klienta aplikacji usługi Media Services (natywnej). 
- Przekierowanie identyfikatora URI aplikacji usługi Media Services (natywnej). 

Wartości tych parametrów można znaleźć w **witrynie AzureEnvironments.AzureCloudEnvironment**. **AzureEnvironments.AzureCloudEnvironment** stała jest pomocnikiem w .NET SDK, aby uzyskać odpowiednie ustawienia zmiennych środowiskowych dla publicznego centrum danych platformy Azure. 

Zawiera wstępnie zdefiniowane ustawienia środowiska dostępu do usługi Media Services tylko w publicznych centrach danych. W przypadku suwerennych lub rządowych regionów chmury można użyć **azurechinacloudenvironment**, **AzureUsGovernmentEnvironment**lub **AzureGermanCloudEnvironment** odpowiednio.

Poniższy przykład kodu tworzy token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Aby rozpocząć programowanie dla usługi Media Services, należy utworzyć **cloudmediacontext wystąpienie,** które reprezentuje kontekst serwera. **CloudMediaContext** zawiera odwołania do ważnych kolekcji, w tym zadań, zasobów, plików, zasad dostępu i lokalizatorów. 

Należy również przekazać **identyfikator URI zasobu dla media REST Services** do konstruktora **CloudMediaContext.** Aby uzyskać identyfikator URI zasobu dla usług Media REST Services, zaloguj się do witryny Azure Portal, wybierz konto usługi Azure Media Services, wybierz pozycję Dostęp do **interfejsu API,** a następnie wybierz pozycję **Połącz z usługą Azure Media Services z uwierzytelnianiem użytkowników.** 

Poniższy przykład kodu tworzy **CloudMediaContext wystąpienie:**

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

W poniższym przykładzie pokazano, jak utworzyć token usługi Azure AD i kontekst:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Jeśli pojawi się wyjątek z napisem "Serwer zdalny zwrócił błąd: (401) Nieautoryzowane", zobacz sekcję [Kontroli dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) w interfejsie API usługi Azure Media Services z omówieniem uwierzytelniania usługi Azure AD.

## <a name="use-service-principal-authentication"></a>Korzystanie z uwierzytelniania głównego usługi
    
Aby połączyć się z interfejsem API usługi Azure Media Services za pomocą opcji głównej usługi, aplikacja warstwy środkowej (internetowy interfejs API lub aplikacja sieci web) musi zażądać tokenu usługi Azure AD z następującymi parametrami:  

- Punkt końcowy dzierżawy usługi Azure AD. Informacje o dzierżawie można pobrać z witryny Azure portal. Umieść wskaźnik myszy na zalogowanym użytkowniku w prawym górnym rogu.
- Identyfikator URI zasobu usługi Media Services.
- Wartości aplikacji usługi Azure AD: **identyfikator klienta** i klucz **tajny klienta**.

Wartości dla **identyfikatora klienta** i tajne parametry **klienta** można znaleźć w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do uwierzytelniania usługi Azure AD przy użyciu witryny Azure portal](media-services-portal-get-started-with-aad.md).

Poniższy przykład kodu tworzy token przy użyciu konstruktora **AzureAdTokenCredentials,** który przyjmuje **AzureAdClientSymmetricKey** jako parametr: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Można również określić **Konstruktor AzureAdTokenCredentials,** który przyjmuje **AzureAdClientCertificate** jako parametr. 

Aby uzyskać instrukcje dotyczące tworzenia i konfigurowania certyfikatu w formularzu, który może być używany przez usługę Azure AD, zobacz [Uwierzytelnianie do usługi Azure AD w aplikacjach demonów z certyfikatami — ręczne kroki konfiguracji](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Aby rozpocząć programowanie dla usługi Media Services, należy utworzyć **cloudmediacontext wystąpienie,** które reprezentuje kontekst serwera. Należy również przekazać **identyfikator URI zasobu dla media REST Services** do konstruktora **CloudMediaContext.** Identyfikator **URI zasobu dla** usługi Media REST Services można uzyskać również z witryny Azure portal.

Poniższy przykład kodu tworzy **CloudMediaContext wystąpienie:**

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
W poniższym przykładzie pokazano, jak utworzyć token usługi Azure AD i kontekst:

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Następne kroki

Zacznij od [przesyłania plików na swoje konto](media-services-dotnet-upload-files.md).
