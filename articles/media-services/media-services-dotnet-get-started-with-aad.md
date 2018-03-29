---
title: Uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z platformą .NET | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób uzyskać dostępu do interfejsu API usługi Azure Media Services (AMS) z platformą .NET za pomocą uwierzytelniania usługi Azure Active Directory (Azure AD).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: 8817bb0da526769470e96b63b9c80620309757bf
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Azure Media Services API z platformą .NET dostęp za pomocą uwierzytelniania usługi Azure AD

Począwszy od windowsazure.mediaservices 4.0.0.4 Azure Media Services obsługuje uwierzytelnianie oparte na usłudze Azure Active Directory (Azure AD). W tym temacie przedstawiono sposób użycia uwierzytelniania usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure za pomocą programu Microsoft .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [utworzyć konto usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
- R [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pakietu.
- Znajomość temat [podczas uzyskiwania dostępu do usługi Azure Media usług interfejsu API za pomocą omówienie uwierzytelniania usługi AAD](media-services-use-aad-auth-to-access-ams-api.md). 

Podczas korzystania z uwierzytelniania usługi Azure AD z usługi Azure Media Services, można uwierzytelniać się w jednym z dwóch sposobów:

- **Uwierzytelnianie użytkownika** uwierzytelnia osoby, która korzysta z aplikacji do interakcji z zasobami usługi Azure Media Services. Interaktywna aplikacja powinna pierwszy monit o podanie poświadczeń. Przykładem jest aplikacja konsoli zarządzania używanym przez autoryzowanych użytkowników do monitorowania kodowania zadań lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie głównej usługi** uwierzytelnia usługi. Aplikacje, które zazwyczaj używają tej metody uwierzytelniania są aplikacji uruchamianych demon usługi, usługi warstwy środkowej lub zaplanowanych zadań, takich jak aplikacje sieci web funkcji aplikacji, aplikacji logiki, interfejsów API albo mikrousług.

>[!IMPORTANT]
>Usługa Azure Media obecnie obsługuje model uwierzytelniania w usłudze kontroli dostępu platformy Azure. Jednak autoryzacji kontroli dostępu ma być zastąpiona na 22 czerwca 2018. Zaleca się, jak najszybciej migracji z modelem uwierzytelniania usługi Azure Active Directory.

## <a name="get-an-azure-ad-access-token"></a>Uzyskaj token dostępu usługi Azure AD

Aby połączyć się Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD, aplikacji klienckiej musi żądania tokenu dostępu usługi Azure AD. Korzystając z klienta Media Services na platformie .NET SDK, wiele informacji o sposobie pozyskania token dostępu usługi Azure AD są ujęte i uproszczonego dla Ciebie w [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) i [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) klasy. 

Na przykład, nie trzeba podać urząd usługi Azure AD, identyfikator URI zasobu usługi Media Services lub native szczegóły aplikacji usługi Azure AD. Są to dobrze znane wartości, które zostały już skonfigurowane przez klasę dostawcy tokenu dostępu usługi Azure AD. 

Jeśli nie używasz zestawu .NET SDK usługi Azure Media Service, firma Microsoft zaleca użycie [Azure AD Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md). Aby uzyskać wartości dla parametrów, które są potrzebne do korzystania z usługi Azure AD Authentication Library, zobacz [dostęp do ustawień uwierzytelniania usługi Azure AD za pomocą portalu Azure](media-services-portal-get-started-with-aad.md).

Istnieje również możliwość zastąpienia domyślną implementację elementu **AzureAdTokenProvider** z własną implementację.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalowanie i konfigurowanie zestawu .NET SDK usługi Azure Media Services

>[!NOTE] 
>Aby używać uwierzytelniania usługi Azure AD z SDK .NET usługi Media Services, musisz mieć najnowszej [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pakietu. Ponadto Dodaj odwołanie do **Microsoft.IdentityModel.Clients.ActiveDirectory** zestawu. Jeśli używasz istniejącej aplikacji obejmują **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** zestawu. 

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio.
2. Użyj [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) pakietu NuGet, aby zainstalować **zestawu .NET SDK usługi Azure Media Services**. 

    Aby dodać odwołań za pomocą NuGet, wykonaj następujące czynności: w **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **pakiety zarządzania pakietami NuGet**. Następnie wyszukaj **windowsazure.mediaservices** i wybierz **zainstalować**.
    
    — lub —

    Uruchom następujące polecenie **Konsola Menedżera pakietów** w programie Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Dodaj **przy użyciu** do kodu źródłowego.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Uwierzytelnianie użytkownika

Aby połączyć z interfejsu API usługi multimediów Azure przy użyciu opcji uwierzytelniania użytkownika, aplikacja kliencka musi żądania tokenu usługi Azure AD przy użyciu następujących parametrów:  

- Punkt końcowy dzierżawy usługi Azure AD. Informacje o dzierżawy można pobrać z portalu Azure. Umieść kursor nad zalogowanego użytkownika w prawym górnym rogu.
- Identyfikator URI zasobu usługi Media Services.
- Identyfikator klienta aplikacji usługi Media Services (macierzysty). 
- Identyfikator URI przekierowania aplikacji usługi Media Services (macierzysty). 

Wartości tych parametrów można znaleźć w **AzureEnvironments.AzureCloudEnvironment**. **AzureEnvironments.AzureCloudEnvironment** stała jest pomocnika w zestawu .NET SDK, aby uzyskać prawo środowiska ustawienia zmiennej publicznej centrum danych Azure. 

Zawiera ustawienia środowiska wstępnie zdefiniowane do uzyskiwania dostępu do usługi Media Services w publicznych wyłącznie w centrach danych. W przypadku regionów chmury suwerenne lub dla instytucji rządowych, można użyć **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvrionment**, lub **AzureGermanCloudEnvironment** odpowiednio.

Poniższy przykład kodu tworzy token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Aby rozpocząć Programowanie w odniesieniu do usługi Media Services, musisz utworzyć **CloudMediaContext** wystąpienia, który reprezentuje kontekstu serwera. **CloudMediaContext** zawiera odwołania do kolekcji ważne, łącznie z zadań, zasobów, plików, zasady dostępu i lokalizatorów. 

Należy również przekazać **identyfikator URI dla usługi REST nośnika** do **CloudMediaContext** konstruktora. Aby uzyskać identyfikator URI zasobu dla usługi REST Media Services, zaloguj się do portalu Azure wybierz konto usługi Azure Media Services, wybierz **dostępu do interfejsu API**, a następnie wybierz **nawiązywanie połączenia z usługi Azure Media Services przy użyciu uwierzytelniania użytkownika**. 

Poniższy przykład kodu tworzy **CloudMediaContext** wystąpienie:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Poniższy przykład przedstawia sposób tworzenia tokenu usługi Azure AD i kontekstu:

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
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
>Jeśli otrzymasz wyjątek mówi "serwer zdalny zwrócił błąd: (401) nieautoryzowane" zobacz [kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) części podczas uzyskiwania dostępu do Azure Media Services API, omówienie uwierzytelniania usługi Azure AD.

## <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania głównej usługi
    
Aby połączyć się z interfejsu API Azure Media Services z opcją główna usługi aplikacji warstwy środkowej (sieć web API lub aplikacji sieci web) musi żądania tokenu usługi Azure AD z następującymi parametrami:  

- Punkt końcowy dzierżawy usługi Azure AD. Informacje o dzierżawy można pobrać z portalu Azure. Umieść kursor nad zalogowanego użytkownika w prawym górnym rogu.
- Identyfikator URI zasobu usługi Media Services.
- Wartości aplikacji w usłudze Azure AD: **identyfikator klienta** i **klucz tajny klienta**.

Wartości **identyfikator klienta** i **klucz tajny klienta** parametrów można znaleźć w portalu Azure. Aby uzyskać więcej informacji, zobacz [wprowadzenie do korzystania z uwierzytelniania usługi Azure AD przy użyciu portalu Azure](media-services-portal-get-started-with-aad.md).

Poniższy przykład kodu tworzy token za pomocą **AzureAdTokenCredentials** konstruktora przyjmującego **AzureAdClientSymmetricKey** jako parametr: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Można również określić **AzureAdTokenCredentials** konstruktora przyjmującego **AzureAdClientCertificate** jako parametr. 

Aby uzyskać instrukcje dotyczące sposobu tworzenia i konfigurowania certyfikatu w formularzu, które mogą być używane przez usługę Azure AD, zobacz [uwierzytelniania w usłudze Azure AD w aplikacjach demon z certyfikatami - ręcznego konfigurowania](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Aby rozpocząć Programowanie w odniesieniu do usługi Media Services, musisz utworzyć **CloudMediaContext** wystąpienia, który reprezentuje kontekstu serwera. Należy również przekazać **identyfikator URI dla usługi REST nośnika** do **CloudMediaContext** konstruktora. Możesz uzyskać **identyfikator URI dla usługi REST nośnika** wartość z portalu Azure.

Poniższy przykład kodu tworzy **CloudMediaContext** wystąpienie:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Poniższy przykład przedstawia sposób tworzenia tokenu usługi Azure AD i kontekstu:

    namespace AADAuthSample
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

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z [przekazywanie plików do konta](media-services-dotnet-upload-files.md).
