---
title: Opracowywanie zawartości przy użyciu interfejsów API w wersji 3 — Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono reguły mające zastosowanie do interfejsów API oraz jednostek, podczas tworzenia za pomocą usługi Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4c5b30ab075bbca22b6a58ccf65e55d332820937
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406543"
---
# <a name="developing-with-media-services-v3-apis"></a>Tworzenie aplikacji za pomocą usługi Media Services v3 interfejsów API

W tym artykule omówiono reguły mające zastosowanie do interfejsów API oraz jednostek, podczas tworzenia za pomocą usługi Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services

Autoryzowanie dostępu do zasobów usługi Media Services i interfejsu API usług Media Services, użytkownik musi najpierw zostać uwierzytelnione. Usługa Media Services obsługuje [usługi Azure Active Directory (Azure AD) — na podstawie](../../active-directory/fundamentals/active-directory-whatis.md) uwierzytelniania. Są dwa typowe opcje uwierzytelniania:
 
* **Uwierzytelnianie jednostki usługi** — używany do uwierzytelniania usługi (na przykład: aplikacji, aplikacje funkcji, logic apps, API i mikrousług w sieci web). Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które działają usługi demona, usługi warstwy środkowej lub zaplanowanych zadań. Na przykład dla sieci Web aplikacji należy zawsze warstwie pośredniej, który nawiązuje połączenie z usługi Media Services za pomocą jednostki usługi.
* **Uwierzytelnianie użytkownika** — używany do uwierzytelniania osoby, która jest za pomocą aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja najpierw powinien zostać wyświetlony monit użytkownika o poświadczenia użytkownika. Przykładem jest aplikacja do konsoli zarządzania używany przez użytkowników autoryzowanych do monitorowania zadań kodowania lub transmisja strumieniowa na żywo.

Interfejsu API usług Media Services wymaga, czy użytkownik lub aplikacja, dzięki czemu interfejs API REST żądań mają dostęp do zasobów konta usługi Media Services i użyj **Współautor** lub **właściciela** roli. Interfejs API jest możliwy za pomocą **czytnika** roli, ale tylko **uzyskać** lub **listy**   operacje będą dostępne. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach do konta usługi Media Services](rbac-overview.md).

Zamiast tworzenia nazwy głównej usługi, należy wziąć pod uwagę przy użyciu zarządzanych tożsamości dla zasobów platformy Azure na dostęp do interfejsu API usług Media Services za pomocą usługi Azure Resource Manager. Aby dowiedzieć się więcej na temat zarządzanych tożsamości dla zasobów platformy Azure, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Nazwa główna usługi Azure AD 

Jeśli tworzysz aplikację usługi Azure AD i usługi jednostki, aplikacja musi znajdować się w jego własnej dzierżawy. Po utworzeniu aplikacji, nadaj aplikacji **Współautor** lub **właściciela** roli dostęp do konta usługi Media Services. 

Jeśli nie masz pewności, czy masz uprawnienia do tworzenia aplikacji usługi Azure AD, zobacz [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na poniższej ilustracji liczby reprezentują przepływ żądań w kolejności chronologicznej:

![Aplikacje warstwy środkowej](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikacja warstwy środkowej żąda tokenu dostępu usługi Azure AD, która ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.
   * Identyfikator URI zasobu usługi Media Services.
   * Identyfikator URI dla usługi Media Services REST zasobu.
   * Wartości aplikacji w usłudze Azure AD: identyfikator klienta oraz klucz tajny klienta.
   
   Aby uzyskać wszystkie potrzebne wartości, zobacz [dostępu Azure interfejsu API Media Services przy użyciu wiersza polecenia platformy Azure](access-api-cli-how-to.md)

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API REST usługi Azure Media z tokenem usługi Azure AD.
5. Warstwa środkowa otrzymuje dane z usługi Media Services.

### <a name="samples"></a>Przykłady

Zobacz poniższe przykłady pokazujące, jak połączyć się z nazwy głównej usługi Azure AD:

* [Łączenie z użyciem usług REST](media-rest-apis-with-postman.md)  
* [Nawiązywanie połączeń przy użyciu języka Java](configure-connect-java-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy .NET](configure-connect-dotnet-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy Node.js](configure-connect-nodejs-howto.md)
* [Nawiązywanie połączeń przy użyciu języka Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID). 

Nazwy zasobów usługi Media Services nie mogą zawierać znaków „<”, „>”, „%”, „&”, „:”, „&#92;”, „?”, „/”, „*”, „+”, „.”, pojedynczych cudzysłowów ani żadnych znaków sterujących. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków. 

Aby uzyskać więcej informacji na temat nazewnictwa w usłudze Azure Resource Manager zobacz: [Wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [Konwencje nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Długotrwałych operacji

Operacje oznaczone `x-ms-long-running-operation` w usłudze Azure Media Services [swagger pliki](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) długie długotrwałych operacji. 

Aby uzyskać szczegółowe informacje na temat śledzenie operacji asynchronicznych na platformie Azure, zobacz [operacje asynchroniczne](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Usługa Media Services obsługuje następujące operacje długotrwałych:

* Utwórz element LiveEvent
* Element LiveEvent aktualizacji
* Usuń element LiveEvent
* Element LiveEvent Start
* Zatrzymaj element LiveEvent
* Resetuj element LiveEvent
* Utwórz LiveOutput
* Usuń LiveOutput
* Utwórz StreamingEndpoint
* Aktualizuj StreamingEndpoint
* Usuń StreamingEndpoint
* Rozpocznij StreamingEndpoint
* Zatrzymaj StreamingEndpoint
* StreamingEndpoint skalowania

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Stronicowanie filtrowania, sortowania, jednostek usługi Media Services

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Azure Media Services](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

[Zacznij programować przy użyciu interfejsu API w wersji 3 usługa Media Services przy użyciu narzędzi/zestawów SDK](developers-guide.md)
