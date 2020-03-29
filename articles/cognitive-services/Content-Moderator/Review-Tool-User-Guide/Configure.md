---
title: Konfigurowanie ustawień narzędzia recenzja — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Narzędzie Przeglądanie służy do konfigurowania lub pobierania zespołu, tagów, łączników, przepływów pracy i poświadczeń dla moderatora zawartości.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220128"
---
# <a name="configure-the-review-tool"></a>Konfigurowanie narzędzia do przeglądu

[Narzędzie Recenzja](https://contentmoderator.cognitive.microsoft.com) ma kilka ważnych funkcji, do których można uzyskać dostęp za pomocą menu **Ustawienia** na pulpicie nawigacyjnym.

![Menu ustawień moderatora zawartości Przejrzyj też](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Zarządzanie zespołami i podzespołami

Karta **Zespół** umożliwia zarządzanie zespołem i&mdash;grupami podrzędnymi użytkowników, którzy mogą otrzymywać powiadomienia o rozpoczęciu niektórych [opinii o człowieku.](../review-api.md#reviews) Możesz mieć tylko jeden zespół (który tworzysz po zarejestrowaniu się za pomocą narzędzia Recenzja), ale możesz utworzyć wiele podzespołów. Administrator zespołu może zapraszać członków, ustawiać ich uprawnienia i przypisywać ich do różnych podzespołów.

![Przeglądanie ustawień zespołu narzędzi](images/settings-2-team.png)

Podzespołami są przydatne do tworzenia zespołów eskalacji lub zespołów poświęconych przeglądaniu określonych kategorii zawartości. Na przykład możesz wysłać treści dla dorosłych do oddzielnego zespołu w celu dalszego sprawdzenia.

W tej sekcji wyjaśniono, jak tworzyć podzespoły i szybko przypisywać recenzje na bieżąco. Można jednak użyć [przepływów pracy](workflows.md) do przypisywania recenzji na podstawie określonych kryteriów.

### <a name="create-a-subteam"></a>Tworzenie podzespołów

Przejdź do sekcji **Podzespołowie** i kliknij pozycję **Dodaj podzespoł .** Wprowadź nazwę podzespołów w oknie dialogowym i kliknij przycisk **Zapisz**.

![Nazwa podzespołania](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Zapraszanie kolegów z drużyny

Nie można przypisać kogoś do podzespoły, jeśli nie są one jeszcze członkiem domyślnego zespołu, więc należy najpierw dodać recenzentów do domyślnego zespołu. Kliknij **pozycję Zaproś** na karcie **Zespół.**

![Zapraszanie użytkowników](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Przypisywanie członków drużyny do podzespołów

Kliknij przycisk **Dodaj członka,** aby przypisać członków z domyślnego zespołu do jednego lub większej liczby podzespołów. Do podzespołów można dodawać tylko istniejących użytkowników. Aby dodać nowych użytkowników, którzy nie znajdują się w narzędziu do recenzji, zaproś ich za pomocą przycisku "Zaproś" na stronie Ustawienia zespołu.

![Przypisywanie członków podzespołów](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Przypisywanie recenzji do podzespołów

Po utworzeniu podzespołów i przypisanych członków można rozpocząć przypisywanie [recenzji](../review-api.md#reviews) zawartości do tych podzespołów. Odbywa się to na karcie **Recenzja** witryny.
Aby przypisać zawartość do podzespołu, kliknij wielokropek w prawym górnym rogu, wybierz pozycję **Przenieś do**, a następnie wybierz podzespołę.

![Przypisywanie recenzji obrazu do podzespołów](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Przełączanie między podzespołami

Jeśli jesteś członkiem więcej niż jednej podzespoły, możesz przełączać się między tymi podzespołami, aby zmienić, które recenzje zawartości są wyświetlane dla Ciebie. Na karcie **Recenzja** wybierz menu rozwijane z etykietą **Domyślna** i wybierz pozycję **Wybierz podzespoł**. Możesz przeglądać recenzje zawartości dla różnych podzespołów, ale tylko te, których jesteś członkiem.

![Przełączanie między podzespołami](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tagi

Karta **Znaczniki** umożliwia definiowanie niestandardowych tagów moderowania oprócz&mdash;dwóch domyślnych tagów moderowania**isadult** (**a**) i **isracy** (**r**). Podczas tworzenia tagu niestandardowego staje się on dostępny w recenzjach obok tagów domyślnych. Możesz zmienić tagi, które są wyświetlane w recenzjach, przełączając ich ustawienia widoczności.

![Widok znaczników, w tym pola wyboru "Jest widoczny"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Tworzenie tagów niestandardowych

Aby utworzyć nowy znacznik, należy wprowadzić krótki kod, nazwę i opis w odpowiednich polach.

- **Krótki kod:** Wprowadź dwuliterowy kod tagu. Przykład: **cb**
- **Nazwa**: Wprowadź krótką i opisową nazwę znacznika w małych literach bez spacji. Przykład: **nękanie**.
- **Opis**: (opcjonalnie) Wprowadź opis rodzaju zawartości, na którą kieruje tag. Przykład: **Obrazy lub przypadki cybernękania**.

Kliknij **przycisk Dodaj,** aby dodać znacznik, a następnie kliknij przycisk **Zapisz** po zakończeniu tworzenia znaczników.

![Narzędzie przeglądu tworzenie nowego tagu](images/settings-3-tags.png)

### <a name="delete-tags"></a>Usuwanie znaczników

Znaczniki niestandardowe można usunąć, wybierając ikonę kosza obok ich wpisów na liście Znaczniki, ale nie można usunąć tagów domyślnych.

## <a name="connectors"></a>Łączniki

Karta **Łączniki** umożliwia zarządzanie łącznikami, które są wtyczkami specyficznymi dla usługi, które mogą przetwarzać zawartość na różne sposoby w ramach [przepływów pracy](../review-api.md#workflows)zawartości .

Domyślnym łącznikiem podczas tworzenia przepływu pracy jest łącznik Moderator zawartości, który może oznaczać zawartość jako **dorosłą** lub **rasistońską**, znaleźć wulgaryzmy itd. Można jednak użyć innych łączników wymienionych w tym miejscu, o ile masz poświadczenia dla ich odpowiednich usług (aby na przykład użyć łącznika Face, na przykład, trzeba będzie uzyskać klucz subskrypcji [face).](https://docs.microsoft.com/azure/cognitive-services/face/overview)

[Narzędzie Recenzja](./human-in-the-loop.md) zawiera następujące łączniki:

- Rozpoznawanie emocji
- Rozpoznawanie twarzy
- Usługa w chmurze PhotoDNA
- Analiza tekstu

### <a name="add-a-connector"></a>Dodawanie łącznika

Aby dodać łącznik (i udostępnić go do użycia w [przepływach pracy](../review-api.md#workflows)zawartości), wybierz odpowiedni przycisk **Połącz.** W następnym oknie dialogowym wprowadź klucz subskrypcji dla tej usługi. Po zakończeniu nowy łącznik powinien pojawić się w górnej części strony.

![Ustawienia łączników moderatora zawartości](images/settings-4-connectors.png)

## <a name="workflows"></a>Przepływy

Karta **Przepływy pracy** umożliwia zarządzanie [przepływami pracy](../review-api.md#workflows). Przepływy pracy są filtrami opartymi na chmurze dla zawartości i współpracują z łącznikami, aby sortować zawartość na różne sposoby i podejmować odpowiednie działania. W tym miejscu można definiować, edytować i testować przepływy pracy. Zobacz [Definiowanie i używanie przepływów pracy,](Workflows.md) aby uzyskać wskazówki dotyczące tego, jak to zrobić.

![Ustawienia przepływu pracy moderatora zawartości](images/settings-5-workflows.png)

## <a name="credentials"></a>Poświadczenia

Karta **Poświadczenia** zapewnia szybki dostęp do klucza subskrypcji moderatora zawartości, który będzie potrzebny do uzyskania dostępu do dowolnej usługi moderowania z połączenia REST lub sdk klienta.

![Poświadczenia moderatora zawartości](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Używanie poświadczeń zewnętrznych dla przepływów pracy

[Narzędzie Recenzja](https://contentmoderator.cognitive.microsoft.com) generuje bezpłatny klucz próbny dla usług Azure Content Moderator podczas rejestracji, ale można również skonfigurować go do używania istniejącego klucza z konta platformy Azure. Jest to zalecane w przypadku scenariuszy na dużą skalę, ponieważ bezpłatne klucze próbne mają ścisłe limity użycia[(Ceny i limity).](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Jeśli utworzono [zasób moderatora zawartości](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) na platformie Azure, przejdź do niego w witrynie Azure portal i wybierz blok **Klucze.** Skopiuj jeden z kluczy.

![Klucze moderatora zawartości w witrynie Azure portal](images/credentials-azure-portal-keys.PNG)

Na karcie **Poświadczenia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com)przejdź do okienka Ustawienia **przepływu pracy,** wybierz pozycję **Edytuj**i wklej klucz do pola Klucz **subskrypcji Ocp-Apim-Subscription-Key.** Teraz przepływy pracy, które wywołują moderowanie interfejsów API użyje poświadczeń platformy Azure.

> [!NOTE]
> Pozostałe dwa pola w okienku **Ustawienia przepływu pracy** są przeznaczone dla list terminów niestandardowych i obrazów. Zapoznaj się z [niestandardowymi terminami](../try-terms-list-api.md) lub [niestandardowymi obrazami,](../try-image-list-api.md) aby dowiedzieć się o nich.

### <a name="use-your-azure-account-with-the-review-apis"></a>Korzystanie z konta platformy Azure z interfejsami API recenzji

Aby użyć klucza platformy Azure z interfejsami API przeglądu, należy pobrać identyfikator zasobu. Przejdź do zasobu moderatora zawartości w witrynie Azure portal i wybierz przy oknie bloku **Właściwości.** Skopiuj wartość Identyfikator zasobu i wklej ją do pola **Identyfikatory zasobów umieszczonych na białej liście** na karcie **Poświadczenia** narzędzia Recenzja.

![Identyfikator zasobu moderatora zawartości w witrynie Azure portal](images/credentials-azure-portal-resourceid.PNG)

Jeśli klucz subskrypcji został wprowadzony w obu miejscach, klucz wersji próbnej dostarczany z kontem narzędzia recenzja nie będzie używany, ale pozostanie dostępny.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [narzędziem Recenzja Szybki start,](../quick-start.md) aby rozpocząć korzystanie z narzędzia Recenzja w scenariuszach moderowania zawartości.