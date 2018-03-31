---
title: Azure Mobile Engagement — integracja wewnętrznej bazy danych
description: Łączenie usługi Azure Mobile Engagement z zapleczem programu SharePoint, aby utworzyć kampanie z programu SharePoint
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cf530ca6dce32050487f367aa80b10bf7f20b7e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement — Integracja z interfejsem API
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

W systemie marketing automatyczne tworzenie i aktywowanie kampanii marketingowych również automatycznie są wykonywane. W tym celu — usługi Azure Mobile Engagement umożliwia tworzenie takich automatycznych kampanii marketingowych, jak również za pomocą interfejsów API. 

Zwykle klientów umożliwia utworzenie anonsów/sond itp jako część ich kampanii marketingowych interfejs frontonu usługi Mobile Engagement. Jednak ponieważ dojrzałe zaczynają kampanii marketingowych, istnieje potrzeba wykorzystanie danych zablokowane w systemach wewnętrznej bazy danych (na przykład systemu CRM lub system CMS, takich jak SharePoint), aby w pełni zautomatyzowanego procesu mogą być tworzone, co powoduje kampanii w dynamicznie na podstawie danych przesyłane w od systemów zaplecza usługi Mobile Engagement. 

![][5]

W tym samouczku przechodzi przez takiej sytuacji, gdy użytkowników biznesowych programu SharePoint wypełnia listę programu SharePoint przy użyciu danych marketingowych i zautomatyzowany proces przejmuje elementów na liście i łączy się z systemu Mobile Engagement przy użyciu dostępnych interfejsach API REST, aby utworzyć kampanię marketingową na podstawie danych programu SharePoint. 

> [!IMPORTANT]
> Ogólnie rzecz biorąc można użyć tego przykładu jako punktu wyjścia do zrozumieć, jak wywołać interfejsu API REST żadnych Mobile Engagement, jak szczegółowe informacje dotyczące wywoływania interfejsów API - parametry uwierzytelniania i przechodzącą dwa kluczowe aspekty. 
> 
> 

## <a name="sharepoint-integration"></a>Integracja z programem SharePoint
1. Oto, jak wygląda przykładową listę programu SharePoint. **Tytuł**, **kategorii**, **NotificationTitle**, **komunikat** i **adres URL** są używane do tworzenia anonsu. Brak kolumnę o nazwie **IsProcessed** używany przez proces automatyzacji próbki w formie program konsoli. Można uruchomić tej konsoli programu jako zadanie WebJob platformy Azure, dzięki czemu można zaplanować lub możesz bezpośrednio użyć przepływu pracy programu SharePoint do programu, tworzenia i aktywowanie powiadomienia po wstawieniu elementu do listy programu SharePoint. W tym przykładzie używamy programu konsoli, który przechodzi przez elementy programu SharePoint listy i utworzyć anons w usłudze Azure Mobile Engagement dla każdej z nich, a następnie oznacza koniec **IsProcessed** flagi spełniony przy tworzeniu anonsu powiodło się.
   
    ![][1]
2. Używamy kodu z próbki *uwierzytelniania zdalnego w programie SharePoint Online przy użyciu Client Object Model* [tutaj](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) do uwierzytelniania za pomocą listy programu SharePoint.
3. Po uwierzytelnieniu pętla za pomocą elementów listy, aby dowiedzieć się, wszystkie nowo utworzone elementy (będą mieć **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integracja usługi Mobile Engagement
1. Po znaleźliśmy elementu, który wymaga przetwarzania — możemy wyodrębnić informacje wymagane do tworzenia anonsu z elementu listy, a wywołania `CreateAzMECampaign` go utworzyć, a następnie `ActivateAzMECampaign` aby aktywować go. Są to zasadniczo wywołania interfejsu API REST wywoływanie z zapleczem usługi Mobile Engagement. 
2. Wymagaj interfejsów API REST Mobile Engagement **nagłówek autoryzacji HTTP schemat uwierzytelniania podstawowego** który składa się z `ApplicationId` i `ApiKey` uzyskane z portalu Azure. Upewnij się, że używasz klucza z **klucze interfejsu api** sekcji i *nie* z **klucze sdk** sekcji. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. Do tworzenia anonsu typu kampanii - zajrzyj do [dokumentacji](https://msdn.microsoft.com/library/azure/mt683750.aspx). Należy się upewnić, że określasz kampanii `kind` jako *anonsu* i [ładunku](https://msdn.microsoft.com/library/azure/mt683751.aspx) i przekazaniem go jako FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. Po utworzeniu, aby utworzyć anons, zobaczysz ekran podobny do następujących w portalu Mobile Engagement (należy pamiętać, że stan = roboczą i Activated = Brak)
   
    ![][3]
5. `CreateAzMECampaign` Tworzy kampanii anons i zwraca jego identyfikator do obiektu wywołującego. `ActivateAzMECampaign` wymaga to identyfikator jako parametru, aby aktywować kampanię. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. Po utworzeniu anons aktywowany, zostanie wyświetlony przypominać następujące w portalu Mobile Engagement:
   
    ![][4]
7. Jak aktywować pobiera kampanii, wszystkie urządzenia, które spełniają kryterium kampanii rozpocznie się wyświetlać powiadomienia. 
8. Będzie także zauważyć, że element listy oznaczonych IsProcessed = false została ustawiona na wartość True po utworzeniu kampanii anonsu.  

W tym przykładzie tworzone kampanii proste anonsu określania najczęściej wymagane właściwości. To ustawienie można dostosować zgodnie z potrzebami, korzystając z informacji w portalu można [tutaj](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



