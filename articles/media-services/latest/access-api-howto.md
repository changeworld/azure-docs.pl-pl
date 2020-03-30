---
title: Wprowadzenie do uwierzytelniania usługi Azure AD
description: Dowiedz się, jak uzyskać dostęp do uwierzytelniania usługi Azure Active Directory (Azure AD) w celu korzystania z interfejsu API usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478255"
---
# <a name="get-credentials-to-access-media-services-api"></a>Uzyskiwanie poświadczeń w celu uzyskania dostępu do interfejsu API usługi Media Services  

Podczas korzystania z uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API usługi Azure Media Services dostępne są dwie opcje uwierzytelniania:

- **Uwierzytelnianie jednostkowe usługi** (zalecane)

    Uwierzytelnij usługę. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które uruchamiają usługi demona, usługi warstwy środkowej lub zaplanowane zadania: aplikacje sieci web, aplikacje funkcji, aplikacje logiki, interfejsy API lub mikrousługi.
- **Uwierzytelnianie użytkowników**

    Uwierzytelnij osobę, która korzysta z aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 

W tym artykule opisano kroki uzyskiwania poświadczeń dostępu do interfejsu API usługi Media Services. Wybierz jedną z następujących kart.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, zacznij od [bezpłatnej wersji próbnej platformy Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure Portal](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

### <a name="api-access"></a>Dostęp za pomocą interfejsu API 

Strona **dostępu do interfejsu API** umożliwia wybranie metody uwierzytelniania, której chcesz użyć do nawiązania połączenia z interfejsem API. Strona zawiera również wartości potrzebne do połączenia z interfejsem API.

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz swoje konto usługi Media Services.
2. Wybierz sposób łączenia się z interfejsem API usługi Media Services.
3. W obszarze **Połącz z interfejsem API usługi Media Services**wybierz wersję interfejsu API usługi Media Services, z którą chcesz się połączyć (wersja 3 jest najnowszą wersją usługi).

### <a name="service-principal-authentication--recommended"></a>Uwierzytelnianie jednostkowe usługi (zalecane)

Uwierzytelnia usługę przy użyciu aplikacji usługi Azure Active Directory (Azure AD) i klucz tajny. Jest to zalecane dla wszystkich usług warstwy środkowej wywołujących interfejs API usługi Media Services. Przykładami są aplikacje sieci Web, funkcje, aplikacje logiki, interfejsy API i mikrousług. Jest to zalecana metoda uwierzytelniania.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Zarządzanie aplikacją usługi Azure AD i kluczem tajnym

Sekcja **Zarządzaj aplikacją AAD i kluczem tajnym** umożliwia wybranie lub utworzenie nowej aplikacji usługi Azure AD i wygenerowanie klucza tajnego. Ze względów bezpieczeństwa klucz tajny nie może być wyświetlany po zamknięciu bloku. Aplikacja używa identyfikatora aplikacji i klucza tajnego do uwierzytelniania, aby uzyskać prawidłowy token dla usług multimedialnych.

Upewnij się, że masz wystarczające uprawnienia do rejestrowania aplikacji w dzierżawie usługi Azure AD i przypisywania aplikacji do roli w subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Wymagane uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Łączenie się z interfejsem API usługi Media Services

**Interfejs API connect to Media Services** zawiera wartości używane do łączenia aplikacji jednostkowej usługi. Można uzyskać wartości tekstowe lub skopiować bloki JSON lub XML.

### <a name="user-authentication"></a>Uwierzytelnianie użytkowników

Ta opcja może służyć do uwierzytelniania pracownika lub członka usługi Azure Active Directory, który używa aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja powinna najpierw monitować użytkownika o poświadczenia użytkownika. Ta metoda uwierzytelniania powinna być używana tylko dla aplikacji zarządzania.

#### <a name="connect-to-media-services-api"></a>Łączenie się z interfejsem API usługi Media Services

Skopiuj poświadczenia, aby połączyć aplikację użytkownika z sekcji Interfejs API połącz z **usługą Media Services.** Można uzyskać wartości tekstowe lub skopiować bloki JSON lub XML.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Następne kroki

[Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów za pomocą usługi Media Services w wersji 3](stream-files-tutorial-with-api.md).
