---
title: 'Wyszukiwanie niestandardowe Bing: Zdefiniuj niestandardowe automatycznego sugerowania sugestie | Dokumentacja firmy Microsoft'
description: Opisuje sposób konfigurowania niestandardowych automatycznego sugerowania z niestandardowych sugestie
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347849"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurowanie środowiska Autosugerowanie niestandardowych
Jeśli masz subskrypcję wyszukiwanie niestandardowe na odpowiednim poziomie (zobacz [cennik stron](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), można dostosować sugestie dotyczące wyszukiwania w działania niestandardowego. Niestandardowe Autosugerowanie zwraca listę sugerowane zapytań opartych na ciąg zapytania częściowy, który zawiera użytkownika. Z automatycznego sugerowania niestandardowych można zapewnić odpowiednie środowiska wyszukiwania sugestie wyszukiwania niestandardowego. Możesz określić, czy zwracać tylko niestandardowych sugestie lub również zawierają sugestie Bing. Jeśli dołączysz sugestie Bing niestandardowych sugestie występować przed sugestie Bing. Sugestie Bing są ograniczone do kontekstu wystąpienia wyszukiwanie niestandardowe.

## <a name="configure-custom-autosuggest"></a>Skonfiguruj niestandardowe automatycznego sugerowania
Użyj poniższych instrukcji, aby skonfigurować niestandardowe automatycznego sugerowania dla swojego wystąpienia wyszukiwanie niestandardowe.

1.  Zaloguj się do [wyszukiwania niestandardowego](https://customsearch.ai).
2.  Kliknij przycisk wystąpienie wyszukiwanie niestandardowe. Aby utworzyć wystąpienie, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).
3.  Kliknij przycisk **Autosugerowanie** kartę.

## <a name="enable-bing-suggestions"></a>Włącz sugestie Bing
Aby włączyć sugestie Bing, Przełącz **sugestie automatyczne Bing** suwak do pozycji na. Suwak staje się niebieski.

## <a name="add-suggestions"></a>Dodaj sugestie
Aby dodać sugestię, wprowadzić go w polu tekstowym. Naciśnij klawisz enter lub kliknij przycisk **+** ikony. Sugestie niestandardowy można w dowolnym języku i pojawi się przed sugestie Bing.

## <a name="upload-suggestions"></a>Przekaż sugestie
Możesz przekazać listy sugestii z pliku. Umieść każdy uwag w osobnym wierszu. Kliknij ikonę przekazywania i wybierz plik.

## <a name="remove-suggestions"></a>Usuń sugestie
Aby usunąć sugestię, kliknij ikonę Usuń obok uwag, które chcesz usunąć.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Może potrwać do 24 godzin automatycznego sugerowania niestandardowe zmiany konfiguracji zostały wprowadzone.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz sugestie dotyczące niestandardowych](./get-custom-suggestions.md)
- [Wyszukiwanie wystąpienia niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie i używanie niestandardowej hostowanej interfejsu użytkownika](./hosted-ui.md)