---
title: Zarządzanie poświadczeniami w usłudze Azure Content Moderator — Content Moderator
titlesuffix: Azure Cognitive Services
description: Zarządzanie poświadczeniami pakietu Content Moderator, które będą musieli używać interfejsów API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f3138a9d0fe201a9486c34c08b8cdb3cca47e1d7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207551"
---
# <a name="manage-content-moderator-service-credentials"></a>Zarządzanie poświadczeniami usługi Content Moderator

Poświadczenia usługi Content Moderator są tworzone w następujących lokalizacjach:

- [Witryna Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Narzędzie do przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/)

W tym artykule opisano, gdzie można je znaleźć oraz jak powiązane są ze sobą.

## <a name="the-azure-portal"></a>Witryna Azure Portal

Na pulpicie nawigacyjnym portalu Azure wybierz konto usługi Content Moderator. W obszarze **zarządzania zasobami**, wybierz opcję **klucze**. Aby skopiować klucz, wybierz ikonę z prawej strony klucza.

![Content Moderator klucze w witrynie Azure portal](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Konto platformy Azure za pomocą narzędzia do przeglądu i przejrzyj interfejsu API
Klucz usługi platformy Azure za pomocą interfejsy API przeglądu, skopiuj identyfikator zasobu na **właściwości** ekranu na następującym zrzucie ekranu, a następnie wprowadź go na ekranie poświadczenia narzędzie do przeglądu w **identyfikatory zasobów na liście dozwolonych** pola, jak pokazano w następującym **identyfikator zasobu** sekcji. 

> [!NOTE]
> Region subskrypcji pakietu Content Moderator powinna odpowiadać region przeglądu zespołu rozpoznaje Twojego zespołu i dostęp do danych zespołu. Na przykład w obrazach na tej stronie **zachodnie stany USA** region **(4)** zawiera subskrypcji Content Moderator platformy Azure i Twojego zespołu.
>
> Po zastąpieniu dwóch miejscach w narzędzie do przeglądu klucz i identyfikator zasobu z subskrypcji platformy Azure Twojej **wersji próbnej Ocp-Apim-Subscription-Key** wyświetlane przy użyciu poświadczeń ekranu nie jest już używany, ale jest zawsze dostępna.
> Wersja próbna klucz ogranicza maksymalny 5000 transakcji miesięcznie, według 1 żądania na sekundę (RPS).

![Identyfikator zasobu usługi Content Moderator w witrynie Azure portal](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Konto platformy Azure za pomocą przepływów pracy w narzędzie do przeglądu

Aby użyć klucza usługi platformy Azure dla przepływów pracy dostępne w ramach pakietu Content Moderator, wprowadź go w **Ocp-Apim-Subscription-Key** pole **ustawienia przepływu pracy** jak pokazano w następującym  **Przepływy pracy** sekcji. Trafienia **'+'** można zapisać swój identyfikator zasobu.

![Content Moderator poświadczenia przepływu pracy w narzędzie do przeglądu](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>Narzędzie do przeglądu

Przegląd narzędzi pulpitu nawigacyjnego, na **ustawienia** zaznacz **poświadczenia**.

![Content Moderator poświadczenia w narzędzie do przeglądu](images/credentials-trial-resource-workflow.PNG)

Następujące części są rozpatrywane wcześniejszej ilustracji, używając bardziej szczegółowo:

### <a name="api"></a>Interfejs API

Pierwsze listy części Twojej **przejrzeć punkt końcowy interfejsu API**, **Identyfikatora zespołu**i **Ocp-Apim-Subscription-Key (klucz wersji próbnej usługi Content Moderator)** wygenerowane jako część Twojego zespołu Tworzenie. Służą one do wywołania wszystkich Content Moderator interfejsów API, tym interfejs API przeglądu.

Należy również zauważyć, że identyfikator region dla punktu końcowego interfejsu API. Na przykład **westus** to region, w "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Klucz zawartości Moderator w narzędzie do przeglądu](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>Identyfikator zasobu

Ten zestaw pól są omówione w poprzedniej sekcji [konta platformy Azure za pomocą narzędzia do przeglądu i interfejsu API](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api). To pole jest zwykle puste, chyba że dodasz do tego pola Identyfikator zasobu usługi Azure zgodnie z opisem w poprzedniej sekcji.

### <a name="workflows"></a>Przepływy

Ten zestaw pól są omówione w poprzedniej sekcji [konta platformy Azure za pomocą przepływów pracy za pomocą narzędzia przeglądu](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). Domyślnie narzędzie do przeglądu używa jej wygenerowany automatycznie klucz wersji próbnej do uruchamiania przepływów pracy, i to, co wyświetla rozpoczynać się. Zezwalaj na dwa pola, przy użyciu list termin i obrazu w operacjach tekst na ekranie i ocenić obraz odpowiednio.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak przy użyciu poświadczeń usługi Content Moderator w swojej [przepływy pracy](workflows.md).
