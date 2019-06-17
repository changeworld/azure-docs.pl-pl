---
title: Zmień źródło danych dla HL7 FHIR zasoby — Azure Cosmos DB
description: Dowiedz się, jak skonfigurować powiadomienia o zmianie dla HL7 FHIR opieki zdrowotnej kartoteki przy użyciu usługi Azure Logic Apps, Azure Cosmos DB i Service Bus.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 49ef63969bd603c25d120dc5cb93ed30dda04241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257271"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Powiadamianie pacjentów zmiany opieki zdrowotnej rekordu HL7 FHIR przy użyciu aplikacji logiki i Azure Cosmos DB

Azure MVP Howard Edidin został ostatnio skontaktować się z usługą organizacji opieki zdrowotnej, który chcesz dodać nowe funkcje do ich portal dla pacjentów. One potrzebne do wysyłania powiadomień do pacjentów, ich kondycji rekord został zaktualizowany, gdy są potrzebne pacjentów, aby można było do subskrybowania tych aktualizacji. 

Ten artykuł przeprowadzi Zmień źródło danych powiadomienia rozwiązania utworzone dla organizacji opieki zdrowotnej przy użyciu usługi Azure Cosmos DB, Logic Apps i Service Bus. 

## <a name="project-requirements"></a>Wymagania dotyczące projektu
- Dostawców wysyłanie HL7 Clinical skonsolidowanych architektury dokumentu (witryny CDA C) dokumenty w formacie XML. Dokumenty witryny CDA C obejmują niemal każdego typu clinical dokumentu, w tym clinical dokumenty, takie jak historie rodziny i uodparniania rekordów, jak również jako administracyjne, przepływu pracy i dokumentów finansowych. 
- Dokumenty witryny CDA C są konwertowane na [HL7 FHIR zasobów](https://hl7.org/fhir/2017Jan/resourcelist.html) w formacie JSON.
- Zmodyfikowane dokumenty zasobów FHIR są wysyłane za pośrednictwem poczty e-mail w formacie JSON.

## <a name="solution-workflow"></a>Przepływ pracy w rozwiązaniu 

Na wysokim poziomie projektu wymagane następujące kroki przepływu pracy: 
1. Konwertowanie dokumentów witryny CDA C do FHIR zasobów.
2. Należy wykonać sondowanie zmodyfikowane zasoby FHIR wyzwalacz cykliczny. 
2. Wywołaj niestandardową aplikację FhirNotificationApi połączyć się z usługi Azure Cosmos DB i wykonywania zapytań o nowych lub zmodyfikowanych dokumentów.
3. Zapisz odpowiedź do kolejki usługi Service Bus.
4. Sondowania dla nowych komunikatów w kolejce usługi Service Bus.
5. Wysyłanie powiadomień e-mail dla pacjentów.

## <a name="solution-architecture"></a>Architektura rozwiązania
To rozwiązanie wymaga trzech aplikacji logiki, aby spełnić powyższych wymagań i Ukończ przepływ pracy w rozwiązaniu. Trzy aplikacje logiki są:
1. **Aplikacja HL7 FHIR mapowania**: Odbiera dokumentu HL7 C-Życiorysu, przekształca je do zasobu FHIR, a następnie zapisuje je do usługi Azure Cosmos DB.
2. **Aplikacja EHR**: Repozytorium usługi Azure Cosmos DB FHIR zapytania, a następnie zapisuje odpowiedź do kolejki usługi Service Bus. Ta aplikacja logiki korzysta [aplikacji interfejsu API](#api-app) do pobrania nowych i zmienionych dokumentów.
3. **Proces powiadamiania aplikacji**: Wysyła wiadomość e-mail z powiadomieniem z dokumentami zasobów FHIR w treści.

![Trzy aplikacje logiki wykorzystanych w tym rozwiązaniu opieki zdrowotnej HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Usługi platformy Azure używane w ramach rozwiązania

#### <a name="azure-cosmos-db-sql-api"></a>Interfejs API SQL usługi Azure Cosmos DB
Usługa Azure Cosmos DB jest repozytorium dla zasobów FHIR, jak pokazano na poniższej ilustracji.

![Konto usługi Azure Cosmos DB używane w tym samouczku opieki zdrowotnej HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logic Apps obsługuje procesu przepływu pracy. Poniższe zrzuty ekranu Pokaż aplikacje Logic Apps, utworzone dla tego rozwiązania. 


1. **Aplikacja HL7 FHIR mapowania**: Otrzymuj dokumentu HL7 C-Życiorysu i przekształcić je do zasobu FHIR przy użyciu pakietu integracyjnego dla przedsiębiorstw dla usługi Logic Apps. Pakiet integracyjny dla przedsiębiorstw obsługuje mapowanie z Życiorysu C do FHIR zasobów.

    ![Aplikacja logiki używaną do odbierania HL7 FHIR rekordów opieki zdrowotnej](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplikacja EHR**: Zapytanie usługi Azure Cosmos DB FHIR repozytorium, a następnie zapisz odpowiedź do kolejki usługi Service Bus. Kod aplikacji GetNewOrModifiedFHIRDocuments znajduje się poniżej.

    ![Aplikacja logiki, używany do wykonywania zapytań w usłudze Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Proces powiadamiania aplikacji**: Wyślij wiadomość e-mail z powiadomieniem z dokumentami zasobów FHIR w treści.

    ![Aplikację logiki, która wysyła pacjentów wiadomość e-mail z zasobami HL7 FHIR w treści](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Na poniższej ilustracji przedstawiono pacjentów kolejki. Wartości właściwości Tag jest używany dla tematu wiadomości e-mail.

![Kolejki magistrali usług, które są używane w tym samouczku HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplikacja interfejsu API
Aplikację interfejsu API łączy się z usługi Azure Cosmos DB i zapytania dotyczące nowych lub zmodyfikowanych dokumentów FHIR według typów zasobów. Ta aplikacja ma jeden kontroler **FhirNotificationApi** za pomocą jednej operacji **GetNewOrModifiedFhirDocuments**, zobacz [źródła dla aplikacji interfejsu API](#api-app-source).

Używamy [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) klasy z interfejsem API usługi Azure Cosmos DB SQL platformy .NET. Aby uzyskać więcej informacji, zobacz [artykułu kanału informacyjnego zmian](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operacja GetNewOrModifiedFhirDocuments

**dane wejściowe**
- DatabaseId
- CollectionId
- Nazwa typu zasobu usługi HL7 FHIR
- Boolean: Zacznij od początku
- Int: Liczba zwróconych dokumentów

**Dane wyjściowe**
- Powodzenie: Kod stanu: 200, odpowiedzi: Lista dokumentów (tablicę JSON)
- Błąd: Kod stanu: 404-odpowiedzi: "Znaleziono żadnych dokumentów"*Nazwa zasobu "* typu zasobu"

<a id="api-app-source"></a>

**Źródło dla aplikacji interfejsu API**

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
                "Query for new or modified FHIR Documents By Resource Type " +
                "from Last Run Date or Beginning of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modified Documents found")]
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

Poniższa ilustracja pokazuje, jak struktury swagger został użyty do testowania [FhirNotificationApi](#api-app-source).

![Plik struktury Swagger, używane do testowania aplikacji interfejsu API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Pulpit nawigacyjny witryny Azure portal

Na poniższej ilustracji przedstawiono wszystkich usług platformy Azure dla tego rozwiązania w witrynie Azure portal.

![Witryny Azure portal pokazujący wszystkich usług używanych w tym samouczku HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Podsumowanie

- Wiesz, że usługi Azure Cosmos DB zapewnia natywną obsługę powiadomień o nowych lub zmodyfikowanych dokumentów i jak łatwo jest użycie. 
- Dzięki wykorzystaniu Logic Apps, możesz utworzyć przepływy pracy bez pisania żadnego kodu.
- Obsługa dystrybucji dokumentów HL7 FHIR przy użyciu kolejek usługi Azure Service Bus.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi Azure Cosmos DB, zobacz [strony głównej usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Aby uzyskać więcej informacji o usłudze Logic Apps, zobacz [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


