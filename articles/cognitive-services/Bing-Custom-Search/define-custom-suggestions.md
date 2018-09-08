---
title: 'Wyszukiwanie niestandardowe Bing: Definiowanie niestandardowego automatycznego sugerowania sugestie | Dokumentacja firmy Microsoft'
description: Opisuje sposób konfigurowania niestandardowego automatycznego sugerowania przy użyciu niestandardowych sugestii
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: e7a62a79bdc2e486fb6bfca34eb4addeba2bde0e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158317"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurowanie środowiska funkcję niestandardowego automatycznego sugerowania
Jeśli masz subskrypcję do wyszukiwania niestandardowego na odpowiednim poziomie (zobacz [stronach z cennikami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), można dostosować sugestie dotyczące wyszukiwania w środowisko wyszukiwania niestandardowego. Funkcję niestandardowego automatycznego sugerowania zwraca listę proponowanych zapytań na podstawie ciągu zapytania częściowego, zapewniająca przez użytkownika. Za pomocą niestandardowego automatycznego sugerowania możesz podać odpowiednie środowiska wyszukiwania sugestie wyszukiwania niestandardowego. Należy określić, czy zwracać tylko niestandardowych sugestii, czy też zawierają sugestie Bing. Jeśli dodasz sugestie Bing, niestandardowych sugestii występować przed sugestie Bing. Sugestie Bing są ograniczone do kontekstu wystąpienia wyszukiwania niestandardowego.

## <a name="configure-custom-autosuggest"></a>Konfigurowanie niestandardowego automatycznego sugerowania
Skorzystaj z poniższych instrukcji, aby skonfigurować niestandardowego automatycznego sugerowania wystąpienia wyszukiwania niestandardowego.

1.  Zaloguj się do [wyszukiwania niestandardowego](https://customsearch.ai).
2.  Kliknij wystąpienie wyszukiwania niestandardowego. Aby utworzyć wystąpienie, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).
3.  Kliknij przycisk **automatycznego sugerowania** kartę.

## <a name="enable-bing-suggestions"></a>Włącz sugestie Bing
Aby włączyć sugestie Bing, Przełącz **sugestie automatyczne Bing** suwak w pozycji. Suwak staje się niebieski.

## <a name="add-suggestions"></a>Dodawanie sugestii
Aby dodać sugestię, wprowadź go w polu tekstowym. Naciśnij klawisz enter lub kliknij przycisk **+** ikony. Sugestie niestandardowe mogą znajdować się w dowolnym języku i pojawi się przed sugestie Bing.

## <a name="upload-suggestions"></a>Przekazywanie sugestii
Możesz przekazać listę sugestii pochodzących z pliku. Umieść każdy sugestię w osobnym wierszu. Kliknij ikonę przekazywania i wybierz swój plik.

## <a name="remove-suggestions"></a>Usuń sugestie
Aby usunąć sugestię, kliknij ikonę Usuń obok sugestii, który chcesz usunąć.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Może upłynąć do 24 godzin niestandardowego automatycznego sugerowania zmiany konfiguracji zostały wprowadzone.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz sugestie dotyczące niestandardowych](./get-custom-suggestions.md)
- [Wyszukaj wystąpienia niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie i używanie niestandardowych hostowanej interfejsu użytkownika](./hosted-ui.md)