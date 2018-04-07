---
title: Zmiana źródła danych dla zasobów HL7 FHIR - DB rozwiązania Cosmos Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować powiadomienia o zmianie dla HL7 FHIR pacjenta rekordów opieki zdrowotnej przy użyciu usługi Azure Logic Apps, bazy danych rozwiązania Cosmos Azure i usługi Service Bus.
keywords: HL7 fhir
services: cosmos-db
author: hedidin
manager: kfile
documentationcenter: ''
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: d9894709cc10c55295e63f64a0db89137bf36826
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Powiadamianie pacjentów HL7 FHIR opieki zdrowotnej rekord zmian za pomocą aplikacji logiki i bazy danych Azure rozwiązania Cosmos

Azure MVP Howard Edidin ostatnio skontaktowała opieki zdrowotnej organizacja, która chce Dodawanie nowych funkcji do ich pacjenta portalu. One potrzebne do wysyłania powiadomień do pacjentów po ich kondycji rekordów została zaktualizowana i zachodzi potrzeba pacjentów, aby można było do subskrybowania tych aktualizacji. 

W tym artykule przedstawiono zmiany źródła danych rozwiązania powiadomień utworzone dla tej organizacji opieki zdrowotnej, przy użyciu bazy danych rozwiązania Cosmos Azure Logic Apps i usługi Service Bus. 

## <a name="project-requirements"></a>Wymagania dotyczące projektu
- Dostawców wysłanie HL7 klinicznych skonsolidowanych architektury dokument (dysk CD C) dokumenty w formacie XML. Dysk CD C dokumenty obejmują o niemal każdego typu klinicznych dokumencie, łącznie z klinicznych dokumentów, takie jak Historia rodziny i rejestruje uodparniania również jako administracyjne, przepływu pracy i dokumentów finansowych. 
- Dysk CD C dokumenty są konwertowane na [zasobów FHIR HL7](http://hl7.org/fhir/2017Jan/resourcelist.html) w formacie JSON.
- Zmodyfikowane dokumenty zasobów FHIR są wysyłane za pośrednictwem poczty e-mail w formacie JSON.

## <a name="solution-workflow"></a>Rozwiązania przepływu pracy 

Na wysokim poziomie projektu wymagane czynności przepływu pracy: 
1. Konwertuj dysk CD C dokumentów na FHIR zasobów.
2. Wykonaj cyklicznego wyzwalacza sondowanie zmodyfikowanych zasobów FHIR. 
2. Wywołanie aplikacji niestandardowej FhirNotificationApi łączyć się z bazy danych Azure rozwiązania Cosmos i zapytanie o nowych lub zmodyfikowanych dokumentów.
3. Zapisz odpowiedzi do magistrali usługi kolejki.
4. Sondowania nowe wiadomości w kolejce usługi Service Bus.
5. Pacjentów wysyłania powiadomień e-mail.

## <a name="solution-architecture"></a>Architektura rozwiązania
To rozwiązanie wymaga trzech powyższych wymagań i ukończenia przepływu pracy rozwiązania do aplikacji logiki. Trzy aplikacje logiki to:
1. **HL7 FHIR mapowania aplikacji**: dokumentu HL7 C-dysk CD, przekształca je do zasobu FHIR, a następnie zapisuje go do bazy danych Azure rozwiązania Cosmos.
2. **Aplikacja EHR**: wysyła zapytanie do repozytorium FHIR DB rozwiązania Cosmos Azure i zapisuje odpowiedź do kolejki usługi Service Bus. Korzysta z tej aplikacji logiki [aplikacji interfejsu API](#api-app) do pobierania nowych i zmienionych dokumentów.
3. **Proces powiadomienie aplikacji**: wysyła wiadomość e-mail z powiadomieniem z dokumentami zasobów FHIR w treści.

![Trzy Logic Apps używane w tym rozwiązaniu opieki zdrowotnej HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Usługi platformy Azure, używane w rozwiązaniu

#### <a name="azure-cosmos-db-sql-api"></a>Interfejsu API Azure rozwiązania Cosmos bazy danych SQL
Azure DB rozwiązania Cosmos jest repozytorium dla zasobów FHIR, jak pokazano na poniższej ilustracji.

![Konto bazy danych Azure rozwiązania Cosmos używane w tym samouczku opieki zdrowotnej HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Aplikacje logiki obsługi procesu przepływu pracy. Poniższe zrzuty ekranu pokazują Logic apps utworzone dla tego rozwiązania. 


1. **HL7 FHIR mapowania aplikacji**: odbierania dokumentu HL7 C-dysk CD i przetransformować je do zasobu FHIR, używając pakiet integracyjny dla przedsiębiorstw dla usługi Logic Apps. Pakiet integracyjny dla przedsiębiorstw obsługuje mapowanie dysk CD C FHIR zasobów.

    ![Aplikację logiki, używany do odbierania HL7 FHIR opieki zdrowotnej rekordów](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplikacja EHR**: zapytanie w repozytorium Azure rozwiązania Cosmos DB FHIR i Zapisz odpowiedzi do kolejki usługi Service Bus. Kod aplikacji GetNewOrModifiedFHIRDocuments znajduje się poniżej.

    ![Aplikację logiki wykorzystywane do badania bazy danych Azure rozwiązania Cosmos](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Proces powiadomienie aplikacji**: Wyślij wiadomość e-mail z powiadomieniem z dokumentami zasobów FHIR w treści.

    ![Aplikację logiki, która wysyła pacjenta wiadomości e-mail z zasobem HL7 FHIR w treści](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Na poniższej ilustracji przedstawiono pacjentów kolejki. Wartość właściwości tagu jest używany dla tematu wiadomości e-mail.

![Kolejki magistrali usług używanych w tym samouczku HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplikacja interfejsu API
Aplikację interfejsu API nawiązanie połączenia bazy danych Azure rozwiązania Cosmos oraz zapytania dotyczące nowych lub zmodyfikowanych dokumentów FHIR według typów zasobów. Ta aplikacja ma jeden kontroler **FhirNotificationApi** z jednej operacji **GetNewOrModifiedFhirDocuments**, zobacz [źródła dla aplikacji interfejsu API](#api-app-source).

Używamy [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) klasy z interfejsu API Azure rozwiązania Cosmos bazy danych SQL platformy .NET. Aby uzyskać więcej informacji, zobacz [zmiany źródła artykułu](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operacja GetNewOrModifiedFhirDocuments

**Dane wejściowe**
- DatabaseId
- CollectionId
- Nazwa typu zasobu FHIR HL7
- Wartość logiczna: Rozpocznij od początku
- Int: Liczba zwracanych dokumentów

**Dane wyjściowe**
- Powodzenie: Kod stanu: odpowiedzi 200,: listy dokumentów (tablica JSON)
- Błędu: Kod stanu: odpowiedzi 404,: "znaleziono żadnych dokumentów"*Nazwa zasobu "* typu zasobu"

<a id="api-app-source"></a>

**Źródła dla aplikacji interfejsu API**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testowanie FhirNotificationApi 

Poniższa ilustracja pokazuje, jak swagger użyto do do testowania [FhirNotificationApi](#api-app-source).

![Plik struktury Swagger wykorzystywane do testowania aplikacji interfejsu API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Pulpitu nawigacyjnego portalu Azure

Na poniższej ilustracji przedstawiono wszystkie usługi platformy Azure dla tego rozwiązania, w portalu Azure.

![Wyświetlanie wszystkich usług, które są używane w tym samouczku HL7 FHIR portalu Azure](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Podsumowanie

- Wiesz już, że bazy danych Azure rozwiązania Cosmos ma obsługuje natywnych powiadomień o nowych lub zmodyfikowane dokumenty i jak łatwo jest użycie. 
- Dzięki wykorzystaniu Logic Apps, mogą tworzyć przepływy pracy bez pisania żadnego kodu.
- Do obsługi dystrybucji HL7 FHIR dokumentów za pomocą kolejek usługi Azure Service Bus.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat bazy danych Azure rozwiązania Cosmos, zobacz [strony głównej bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/). Aby uzyskać więcej informaiton o Logic Apps, zobacz [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


