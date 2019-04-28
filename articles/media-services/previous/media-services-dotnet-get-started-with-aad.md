---
title: Użyj usługi Azure AD uwierzytelniania dostępu do interfejsu API usługi multimediów Azure przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak używać uwierzytelniania usługi Azure Active Directory (Azure AD) dostęp do interfejsu API usługi Azure Media Services (AMS), przy użyciu platformy .NET.
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
ms.openlocfilehash: 15b986d4e7567be48c582e4a39b727ab110de2be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230951"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Użyj uwierzytelniania usługi Azure AD dostęp do interfejsu API usługi multimediów Azure przy użyciu platformy .NET

Począwszy od windowsazure.mediaservices 4.0.0.4 usługi Azure Media Services obsługuje uwierzytelnianie oparte na usłudze Azure Active Directory (Azure AD). W tym temacie dowiesz się, jak używać usługi Azure AD uwierzytelniania dostępu do interfejsu API usługi multimediów Azure przy użyciu platformy Microsoft .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure portal](media-services-portal-create-account.md).
- Najnowsze [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pakietu.
- Znajomość temat [uzyskiwania dostępu do usługi Azure Media Services interfejsu API za pomocą omówienie uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Podczas korzystania z uwierzytelniania usługi Azure AD za pomocą usługi Azure Media Services, można uwierzytelniać się w jeden z dwóch sposobów:

- **Uwierzytelnianie użytkownika** uwierzytelnia osobę, która jest za pomocą aplikacji do interakcji z zasobami usługi Azure Media Services. Interaktywna aplikacja najpierw powinien zostać wyświetlony monit o podanie poświadczeń użytkownika. Przykładem jest aplikacja do konsoli zarządzania, która jest używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie jednostki usługi** uwierzytelnia usługi. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które działają usługi demona, usługi warstwy środkowej lub zaplanowanego zadania, takie jak aplikacje sieci web, aplikacji funkcji, logic apps, interfejsów API lub mikrousług.

>[!IMPORTANT]
>Usługa Azure Media obecnie obsługuje model uwierzytelniania usługi Azure Access Control Service. Jednak kontroli dostępu autoryzacji zamierza wycofane 22 czerwca 2018 r. Zaleca się, jak najszybciej Migruj do modelu uwierzytelniania usługi Azure Active Directory.

## <a name="get-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD

Aby połączyć się z interfejsem API usług Media platformy Azure przy użyciu uwierzytelniania usługi Azure AD, aplikacja kliencka musi żądać token dostępu usługi Azure AD. Gdy używasz zestawu SDK klienta platformy .NET usług Media Services, wiele informacji o tym, jak można uzyskać tokenu dostępu usługi Azure AD są opakowane i uproszczony dla Ciebie w [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) i [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)klasy. 

Na przykład, nie trzeba podać urząd usługi Azure AD, identyfikator URI zasobu usługi Media Services lub natywnych szczegóły aplikacji usługi Azure AD. Są dobrze znane wartości, które zostały już skonfigurowane, klasa dostawcy tokenu dostępu usługi Azure AD. 

Jeśli nie używasz zestawu SDK .NET usługi Azure Media, firma Microsoft zaleca użycie [Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md). Aby uzyskać wartości dla parametrów, które należy użyć za pomocą biblioteki uwierzytelniania usługi Azure AD, zobacz [użyj witryny Azure portal, aby uzyskać dostęp ustawienia uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md).

Istnieje również możliwość zastąpienia domyślnej implementacji **AzureAdTokenProvider** z Twojej własnej implementacji.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalowanie i konfigurowanie usługi Azure Media Services .NET SDK

>[!NOTE] 
>Aby użyć uwierzytelniania usługi Azure AD przy użyciu zestawu SDK .NET usługi Media Services, musisz mieć najnowsze [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pakietu. Ponadto Dodaj odwołanie do **Microsoft.IdentityModel.Clients.ActiveDirectory** zestawu. Jeśli używasz istniejącej aplikacji obejmują **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** zestawu. 

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio.
2. Użyj [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) pakiet NuGet w celu zainstalowania **usługi Azure Media Services .NET SDK**. 

    Aby dodać odwołania za pomocą pakietu NuGet, wykonaj następujące czynności: w **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **Zarządzaj pakietami NuGet**. Następnie wyszukaj **windowsazure.mediaservices** i wybierz **zainstalować**.
    
    — lub —

    Uruchom następujące polecenie **Konsola Menedżera pakietów** w programie Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Dodaj **przy użyciu** do kodu źródłowego.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Uwierzytelnianie użytkownika

Aby połączyć interfejs API usługi multimediów Azure przy użyciu opcji Uwierzytelnianie użytkownika, aplikacja kliencka musi żądać tokenu usługi Azure AD przy użyciu następujących parametrów:  

- Punkt końcowy dzierżawy usługi Azure AD. W witrynie Azure portal można pobrać informacji o dzierżawie. Umieść kursor nad zalogowanego użytkownika w prawym górnym rogu.
- Identyfikator URI zasobu usługi Media Services.
- Identyfikator klienta aplikacji Media Services (macierzysty). 
- Identyfikator URI przekierowania aplikacji Media Services (macierzysty). 

Wartości tych parametrów można znaleźć w **AzureEnvironments.AzureCloudEnvironment**. **AzureEnvironments.AzureCloudEnvironment** stała jest pomocnika w zestawie SDK platformy .NET, aby uzyskać ustawienia zmiennej publicznej centrum danych platformy Azure w odpowiednim środowisku. 

Zawiera on ustawienia środowiska wstępnie zdefiniowane do uzyskiwania dostępu do usługi Media Services w publicznej wyłącznie w centrach danych. W przypadku regionów chmury suwerennych lub dla instytucji rządowych, można użyć **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment**, lub **AzureGermanCloudEnvironment** odpowiednio.

Poniższy przykład kodu tworzy token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Aby rozpocząć Programowanie w odniesieniu do usługi Media Services, musisz utworzyć **CloudMediaContext** wystąpienia, która reprezentuje kontekstu serwera. **CloudMediaContext** zawiera odwołania do kolekcji ważne, łącznie z zadań, zasobów, pliki, zasady dostępu i lokalizatorów. 

Należy również przekazać **identyfikator URI usługi Media REST Services zasobu** do **CloudMediaContext** konstruktora. Aby uzyskać identyfikator URI zasobu dla usługi Media Services REST, zaloguj się do witryny Azure portal wybierz swoje konto usługi Azure Media Services, wybierz **dostęp do interfejsu API**, a następnie wybierz pozycję **nawiązywanie połączenia z usługi Azure Media Services przy użyciu uwierzytelniania użytkownika**. 

Poniższy przykład kodu tworzy **CloudMediaContext** wystąpienie:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Poniższy przykład pokazuje, jak utworzyć tokenu usługi Azure AD i kontekstu:

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
>Jeśli pojawi się wyjątek, który jest wyświetlany komunikat "serwer zdalny zwrócił błąd: (401) nieautoryzowane,"zobacz [kontroli dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) części uzyskiwania dostępu do nośnika usług interfejsu API usługi Azure, omówienie uwierzytelniania usługi Azure AD.

## <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania jednostki usługi
    
Aby nawiązać połączenie z interfejsem API usługi Azure Media Services z opcją nazwy głównej usługi aplikacji warstwy środkowej musi (sieć web API lub aplikacji sieci web) żądania tokenu usługi Azure AD z następującymi parametrami:  

- Punkt końcowy dzierżawy usługi Azure AD. W witrynie Azure portal można pobrać informacji o dzierżawie. Umieść kursor nad zalogowanego użytkownika w prawym górnym rogu.
- Identyfikator URI zasobu usługi Media Services.
- Wartości aplikacji w usłudze Azure AD: **identyfikator klienta** i **klucz tajny klienta**.

Wartości **identyfikator klienta** i **klucz tajny klienta** parametrów można znaleźć w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [rozpoczęcie korzystania z uwierzytelniania usługi Azure AD przy użyciu witryny Azure portal](media-services-portal-get-started-with-aad.md).

Poniższy przykład kodu tworzy token za pomocą **AzureAdTokenCredentials** konstruktora przyjmującego **AzureAdClientSymmetricKey** jako parametr: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Można również określić **AzureAdTokenCredentials** konstruktora przyjmującego **AzureAdClientCertificate** jako parametr. 

Aby uzyskać instrukcje dotyczące sposobu tworzenia i konfigurowania certyfikatu w formularzu, który może służyć przez usługę Azure AD, zobacz [uwierzytelniania w usłudze Azure AD w narzędziu aplikacje demona z certyfikatami — kroki ręcznej konfiguracji](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Aby rozpocząć Programowanie w odniesieniu do usługi Media Services, musisz utworzyć **CloudMediaContext** wystąpienia, która reprezentuje kontekstu serwera. Należy również przekazać **identyfikator URI usługi Media REST Services zasobu** do **CloudMediaContext** konstruktora. Możesz uzyskać **identyfikator URI usługi Media REST Services zasobu** wartość w witrynie Azure portal.

Poniższy przykład kodu tworzy **CloudMediaContext** wystąpienie:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Poniższy przykład pokazuje, jak utworzyć tokenu usługi Azure AD i kontekstu:

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

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą [przekazywania plików na koncie](media-services-dotnet-upload-files.md).
