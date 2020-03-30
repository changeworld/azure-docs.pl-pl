---
title: Tworzenie za pomocą interfejsów API w wersji 3
titleSuffix: Azure Media Services
description: Dowiedz się więcej o regułach, które mają zastosowanie do jednostek i interfejsów API podczas tworzenia programu Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472088"
---
# <a name="develop-with-media-services-v3-apis"></a>Tworzenie za pomocą interfejsów API usługi Media Services w wersji 3

Jako deweloper możesz użyć [interfejsu API REST](https://docs.microsoft.com/rest/api/media/) usługi Media Services lub bibliotek klienta, które umożliwiają interakcję z interfejsem API REST w celu łatwego tworzenia, zarządzania i obsługi niestandardowych przepływów pracy multimediów. Interfejs API [usługi Media Services w wersji 3](https://aka.ms/ams-v3-rest-sdk) jest oparty na specyfikacji OpenAPI (wcześniej znanej jako Swagger).

W tym artykule omówiono reguły, które mają zastosowanie do jednostek i interfejsów API podczas tworzenia z usługą Media Services w wersji 3.

## <a name="accessing-the-azure-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services

Aby uzyskać autoryzację dostępu do zasobów usługi Media Services i interfejsu API usługi Media Services, należy najpierw zostać uwierzytelniony. Usługa Media Services obsługuje uwierzytelnianie [oparte na usłudze Azure Active Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Dwie typowe opcje uwierzytelniania to:
 
* **Uwierzytelnianie jednostkowe usługi:** służy do uwierzytelniania usługi (na przykład: aplikacje sieci web, aplikacje funkcji, aplikacje logiki, interfejs API i mikrousług). Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które uruchamiają usługi demona, usługi warstwy środkowej lub zaplanowane zadania. Na przykład dla aplikacji sieci web zawsze powinna istnieć warstwa środkowa, która łączy się z usługą Media Services z jednostką usługi.
* **Uwierzytelnianie użytkownika:** służy do uwierzytelniania osoby korzystającej z aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja powinna najpierw monitować użytkownika o poświadczenia użytkownika. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo.

Interfejs API usługi Media Services wymaga, aby użytkownik lub aplikacja dokonująca żądań interfejsu API REST miała dostęp do zasobu konta usługi Media Services i używała roli **współautor lub** **właściciel.** Dostęp do interfejsu API można uzyskać za pomocą roli **Czytelnika,** ale dostępne będą tylko operacje **Pobierz** lub **Lista.**Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach dla kont usługi Media Services](rbac-overview.md).

Zamiast tworzyć jednostkę usługi, należy rozważyć użycie tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać dostęp do interfejsu API usługi Media Services za pośrednictwem usługi Azure Resource Manager. Aby dowiedzieć się więcej o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="azure-ad-service-principal"></a>Podmiot zabezpieczeń usługi Azure AD

Jeśli tworzysz jednostkę aplikacji i usługi Azure AD, aplikacja musi znajdować się we własnej dzierżawie. Po utworzeniu aplikacji przyznaj aplikacji **współautora** lub **właściciela** dostępu do konta usługi Media Services.

Jeśli nie masz pewności, czy masz uprawnienia do tworzenia aplikacji usługi Azure AD, zobacz [Wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na poniższej ilustracji liczby reprezentują przepływ żądań w porządku chronologicznym:

![Uwierzytelnianie aplikacji warstwy środkowej za pomocą usługi AAD z internetowego interfejsu API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikacja warstwy środkowej żąda tokenu dostępu usługi Azure AD, który ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.
   * Identyfikator URI zasobu usługi Media Services.
   * Identyfikator URI zasobu dla usługi REST Media Services.
   * Wartości aplikacji usługi Azure AD: identyfikator klienta i klucz tajny klienta.

   Aby uzyskać wszystkie potrzebne wartości, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure](access-api-cli-how-to.md).

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API rest multimediów azure za pomocą tokenu usługi Azure AD.
5. Warstwa środkowa pobiera dane z usługi Media Services.

### <a name="samples"></a>Samples

Zobacz następujące przykłady, które pokazują, jak połączyć się z jednostką usługi Azure AD:

* [Połącz się z REST](media-rest-apis-with-postman.md)  
* [Nawiązywanie połączeń przy użyciu języka Java](configure-connect-java-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy .NET](configure-connect-dotnet-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy Node.js](configure-connect-nodejs-howto.md)
* [Nawiązywanie połączeń przy użyciu języka Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID).

Nazwy zasobów usługi Media Services nie mogą zawierać: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", '+', pojedynczy znak cudzysłowu lub znaki kontrolne. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków.

Aby uzyskać więcej informacji na temat nazewnictwa usługi Azure Resource Manager, zobacz [Wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nazwy plików/obiektów blob w zasobie

Nazwy plików/obiektów blob w zasobie muszą być zgodne zarówno [z wymaganiami dotyczącymi nazwy obiektu blob,](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) jak i [wymaganiami dotyczącymi nazw ntfs](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Powodem tych wymagań jest pliki można uzyskać skopiowane z magazynu obiektów blob do lokalnego dysku NTFS do przetwarzania.

## <a name="long-running-operations"></a>Długotrwałe operacje

Operacje oznaczone `x-ms-long-running-operation` w [plikach swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) usługi Azure Media Services są długotrwałe operacje. 

Aby uzyskać szczegółowe informacje na temat śledzenia asynchronicznych operacji platformy Azure, zobacz [Operacje Asynowen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)

Usługi Media Services mają następujące długotrwałe operacje:

* [Tworzenie wydarzeń na żywo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Aktualizowanie wydarzeń na żywo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Usuń zdarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Rozpocznij wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Zatrzymaj LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Użyj `removeOutputsOnStop` parametru, aby usunąć wszystkie skojarzone dane wyjściowe na żywo podczas zatrzymywania zdarzenia.  
* [Resetowanie liveevent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Tworzenie usługi LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Usuń liveoutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Tworzenie punktu przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Aktualizacja punktu przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Usuń punkt przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Uruchom punkt streamingu](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Zatrzymaj punkt koniecu przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Skaluj punkt wytłazania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Po pomyślnym przesłaniu długiej operacji otrzymasz "202 zaakceptowane" i musisz sondować do zakończenia operacji przy użyciu zwróconego identyfikatora operacji.

W [artykule asynchroniczne operacje platformy Azure śledzenia](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) szczegółowo wyjaśniono, jak śledzić stan asynchronicznych operacji platformy Azure za pomocą wartości zwróconych w odpowiedzi.

Tylko jedna długotrwała operacja jest obsługiwana dla danego zdarzenia na żywo lub dowolnego z jego skojarzonych wyjść na żywo. Po uruchomieniu długotrwała operacja musi zostać ukończona przed rozpoczęciem kolejnej długotrwałej operacji na tym samym liveevent lub wszelkich skojarzonych wyjściach na żywo. W przypadku zdarzeń na żywo z wieloma wyjściami na żywo należy poczekać na zakończenie długotrwałej operacji na jednym wyjściu na żywo przed wyzwoleniem długotrwałej operacji na innym wyjściu na żywo. 

## <a name="sdks"></a>Zestawy SDK

> [!NOTE]
> Zestawy SDK usługi Azure Media Services w wersji 3 nie są gwarantowane jako bezpieczne dla wątków. Podczas tworzenia aplikacji wielowątkowej, należy dodać własną logikę synchronizacji wątków, aby chronić klienta lub użyć nowego obiektu AzureMediaServicesClient na wątek. Należy również uważać na problemy wielowątkowe wprowadzone przez opcjonalne obiekty dostarczone przez kod do klienta (jak httpClient wystąpienie w .NET).

|SDK|Tematy pomocy|
|---|---|
|[Zestaw SDK platformy .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Dokumentacja platformy .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Dokumentacja języka Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Dokumentacja języka Python](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Dokumentacja środowiska Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Zestaw SDK dla języka Go](https://aka.ms/ams-v3-go-sdk) |[Dokumentacja języka Go](https://aka.ms/ams-v3-go-ref)|
|[Zestaw SDK dla języka Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Zobacz też

- [Zestaw SDK EventGrid .NET zawierający zdarzenia usługi Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definicje zdarzeń usługi Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zapoznaj się opencode@microsoft.com z [często zadawanymi pytaniami dotyczącymi kodeksu postępowania](https://opensource.microsoft.com/codeofconduct/faq/) lub kontaktem z innymi pytaniami lub komentarzami.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrowanie, zamawianie, stronicowanie encji usługi Media Services

Zobacz [Filtrowanie, zamawianie, stronicowanie jednostek usługi Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="see-also"></a>Zobacz też

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Następne kroki

* [Łączenie się z usługą Media Services za pomocą języka Java](configure-connect-java-howto.md)
* [Łączenie się z usługą Media Services za pomocą platformy .NET](configure-connect-dotnet-howto.md)
* [Łączenie się z usługą Media Services za pomocą pliku Node.js](configure-connect-nodejs-howto.md)
* [Łączenie się z usługą Media Services za pomocą języka Python](configure-connect-python-howto.md)
