---
title: Poświadczenia na platformie Azure zawartości moderatora | Dokumentacja firmy Microsoft
description: Zarządzanie zawartością moderatora poświadczenia do użycia z interfejsami API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346964"
---
# <a name="manage-credentials"></a>Zarządzanie poświadczeniami

Poświadczenia moderatora zawartości są tworzone w następujących lokalizacjach:

- [Witryna Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Narzędzie przeglądu moderatora zawartości](http://contentmoderator.cognitive.microsoft.com/)

W tym artykule opisano, gdzie można je znaleźć i jak są one powiązane ze sobą.

## <a name="the-azure-portal"></a>Portalu Azure

Na pulpicie nawigacyjnym portalu Azure wybierz konto moderatora zawartości. W obszarze **zarządzanie zasobami**, wybierz pozycję **klucze**. Aby skopiować klucz, wybierz ikonę z prawej strony klucza.

![Zawartości moderatora klucze w portalu Azure](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Jak używać konta platformy Azure za pomocą narzędzia przeglądu
Do korzystania z usługi Azure klucza z przeglądem interfejsów API, należy skopiować identyfikator zasobu wymienione na **właściwości** ekranu na poniższym zrzucie ekranu, a następnie wprowadź go na ekranie poświadczenia narzędzie przeglądu **identyfikatorów zasobów białej** pola, jak pokazano w następującym **identyfikator zasobu** sekcji. 

Do korzystania z usługi Azure klucza dla przepływów pracy dostępne w ramach moderatora zawartości, wprowadź go w **Ocp-Apim-subskrypcji — klucz** w **ustawienia przepływu pracy** sekcji, jak pokazano w następującym  **Przepływy pracy** sekcji.

> [!NOTE]
> Po zastąpieniu dwóch miejscach w narzędziu przeglądu klucz i identyfikator zasobu z subskrypcją platformy Azure z **wersji próbnej Ocp-Apim-subskrypcji — klucz** wyświetlane przy użyciu poświadczeń ekranu nie jest już używany, ale jest zawsze dostępna.
> Wersja próbna klucza ogranicza maksymalną transakcji 5000 miesięcznie na żądanie 1 na sekundę (RPS).

![Identyfikator zasobu moderatora zawartości w portalu Azure](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>Narzędzie przeglądu

Na przeglądu narzędzia pulpitu nawigacyjnego, w **ustawienia** wybierz opcję **poświadczenia**.

![Zawartości poświadczenia moderatora w narzędziu do przeglądu](images/credentials-trial-resource-workflow.PNG)

Poniższa sekcja sprawdza poprzedniego obrazu bardziej szczegółowo:


### <a name="api"></a>Interfejs API

Pierwsze listy części Twojego **Przejrzyj punkt końcowy interfejsu API**, **zespołu identyfikator**i **Ocp-Apim-subskrypcji-kluczem (moderatora zawartości wersji próbnej)** wygenerowane jako część zespołu przeglądu Tworzenie. Służą one do wywoływania wszystkich zawartości moderatora interfejsów API, łącznie z interfejsu API przeglądu.

Należy również zauważyć identyfikatora regionu dla punktu końcowego interfejsu API. Na przykład **westus** to region w "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Klucz zawartości moderatora w narzędziu do przeglądu](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>Identyfikator zasobu

Drugi uruchamia części się jako pusty z nie identyfikator zasobu. **Aby użyć klucza subskrypcji platformy Azure przy użyciu interfejsu API przeglądu, przejść do ekranu identyfikator zasobu, jak pokazano wcześniej i skopiuj ją do pola pokazano**. Trafienia **"+"** Aby zapisać swój identyfikator zasobu.

> [!NOTE]
> Regionie Twojej subskrypcji zawartości moderatora powinna być zgodna region zespołu przeglądu rozpoznaje zespołu i uzyskać dostęp do danych zespołu. Na przykład w przypadku obrazów na tej stronie **zachodnie stany USA** region **(4)** zawiera subskrypcji Azure moderatora zawartości i zespołu przeglądu.

![Identyfikator zasobu moderatora zawartości w narzędziu do przeglądu](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Przepływy

Trzecia część zawiera informacje używane do uruchamiania przepływów pracy. Po uruchomieniu przedstawiający domyślnie automatycznie wygenerowany klucz wersji próbnej. 

**Zaktualizuj kluczem Azure podczas uzyskiwanie subskrypcji platformy Azure**. Dwa pola Zezwalaj przy użyciu list termin i obrazu w operacjach tekstowych ekranu i ocenić obrazów odpowiednio.

![Zawartości moderatora poświadczenia przepływu pracy w narzędziu do przeglądu](images/credentials-workflow.PNG)


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak używać poświadczeń moderatora zawartości w Twojej [przepływy pracy](workflows.md).
