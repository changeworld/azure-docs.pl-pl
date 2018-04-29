---
title: Dostęp do interfejsów API usługi Azure Mobile Engagement Service przy użyciu zestawu .NET SDK
description: Opisuje sposób umożliwia dostęp do interfejsów API usługi Azure Mobile Engagement usługi Mobile Engagement .NET SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4a303db638b8ea304db5cba4075b0b102eedd0f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Dostęp do interfejsów API usługi Azure Mobile Engagement Service przy użyciu zestawu .NET SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

Usługa Azure Mobile Engagement udostępnia zestaw interfejsów API do zarządzania urządzeniami, kampanie Zasięgowe lub wypchnąć itp. Wchodzić w interakcje z poniższych interfejsów API, firma Microsoft udostępnia również możesz [plik struktury Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) której można z narzędzia do generowania zestawów SDK preferowany język. Firma Microsoft zaleca używanie [AutoRest](https://github.com/Azure/AutoRest) narzędzie do generowania zestawu SDK z naszych plik struktury Swagger.

> [!NOTE]
> Usługa Azure Mobile Engagement zostanie wycofana w marcu 2018 r. i jest obecnie dostępna wyłącznie dla dotychczasowych klientów. Aby uzyskać więcej informacji, zobacz [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

Zestaw .NET SDK został utworzony w podobny sposób, co pozwala na interakcję z poniższych interfejsów API przy użyciu otoki C# i nie masz negocjacji token uwierzytelniania i Odśwież samodzielnie.  

W tym przykładzie przechodzi przez zestaw kroków, które należy wykonać, aby użyć zestawu .NET SDK:

1. Przede wszystkim należy skonfigurować uwierzytelnianie dla interfejsów API przy użyciu usługi Azure Active Directory zgodnie z opisem [tutaj](mobile-engagement-api-authentication.md#authentication). Po wykonaniu tych kroków, powinien mieć prawidłową **SubscriptionId**, **TenantId**, **ApplicationId** i **klucz tajny**. 
2. Aby zademonstrować pracy przy użyciu zestawu .NET SDK ze scenariuszem tworzenie kampanii anonsu użyjemy prostej aplikacji konsoli systemu Windows. Sposób otwarcia programu Visual Studio i utworzyć **aplikacji konsoli**.   
3. Następnie należy pobrać zestaw SDK platformy .NET, który jest dostępny jako **Biblioteka zarządzania Microsoft Azure Engagement** w galerii Nuget [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Instalowania Nuget w programie Visual Studio, należy się upewnić, że masz wyboru oznaczone **Uwzględnij wersję wstępną** opcja podczas wyszukiwania dla pakietu:
   
    ![][1]
4. W `Program.cs` plików, dodawanie następujących przestrzeni nazw:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Następnie należy zdefiniować następujące ograniczenia, które będą używane do uwierzytelniania i interakcji z Mobile Engagement App, w którym tworzysz kampanii anonsu:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Zdefiniuj `EngagementManagementClient` zmiennej, który zostanie wykorzystany do wywołania metody zestaw SDK usługi Mobile Engagement:
   
        static EngagementManagementClient engagementClient; 
7. Dodaj następujący kod do Twojej `Main` metody:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Zdefiniuj następującą metodę, która zajmuje się inicjowanie `EngagementManagementClient` najpierw uwierzytelniania i kojarzenie sam z Mobile Engagement App, w którym zamierzasz utworzyć kampanię anonsu:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Należy pamiętać, że należy użyć **nazwa zasobów aplikacji** zgodnie z definicją w portalu zarządzania platformy Azure dla parametru AppName. 
   > 
   > 
9. Ponadto zdefiniować metody CreateCampaign, który zajmie się przy użyciu wcześniej zainicjowana EngagementClient utworzyć prostą **w każdej chwili** & **tylko powiadomienia** kampanii z tytułu i komunikat: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Uruchom aplikację konsoli i będzie widoczny jest następujący komunikat o pomyślnym utworzeniu kampanii:
    
    **Identyfikator kampanii "159" został pomyślnie utworzony i jest w stanie "Projekt"**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
