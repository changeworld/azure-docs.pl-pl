---
title: Konfiguruj ustawienia narzędzia przeglądu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Narzędzie do przeglądu umożliwiają konfigurowanie lub pobrać zespołu, tagi, łączniki, przepływy pracy i poświadczeń dla pakietu Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285555"
---
# <a name="configure-the-review-tool"></a>Konfigurowanie narzędzia do przeglądu

[Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) ma kilka ważnych funkcji, które są dostępne za pośrednictwem **ustawienia** menu na pulpicie nawigacyjnym.

![Zbyt Content Moderator Przegląd menu Ustawienia](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Zarządzanie zespół i zespoły podrzędne

**Zespołu** karta pozwala zarządzać Twojego zespołu i zespoły podrzędne&mdash;grup użytkowników, którzy mogą być powiadamiany, gdy niektóre [ludziom dokonywanie](../review-api.md#reviews) są uruchamiane. Może mieć tylko jeden zespół, (które są tworzone po zarejestrowaniu się przy użyciu narzędzia do przeglądu), ale można utworzyć wiele zespoły podrzędne. Administrator zespołu może zapraszać członków, ustawić dla nich uprawnienia i przypisać je do różnych zespoły podrzędne.

![Przejrzyj ustawienia zespołu narzędzia](images/settings-2-team.png)

Zespoły podrzędne są przydatne do tworzenia eskalacji zespoły lub zespoły przeznaczone do przeglądania zawartości określonych kategorii. Na przykład można wysłać do zawartości dla dorosłych do oddzielny zespół do dalszego przeglądu.

W tej sekcji opisano sposób tworzenia zespoły podrzędne i szybko przypisać recenzje na bieżąco. Można jednak użyć [przepływy pracy](workflows.md) można przypisać recenzji, w oparciu o określone kryteria.

### <a name="create-a-subteam"></a>Utwórz ustalenie

Przejdź do **zespoły podrzędne** sekcji, a następnie kliknij przycisk **Dodaj ustalenie**. Wprowadź nazwę ustalenie, w oknie dialogowym, a następnie kliknij przycisk **Zapisz**.

![Nazwa ustalenie](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Zapraszanie współpracowników

Nie można przypisać ktoś ustalenie, jeśli nie są już członkami zespołu domyślnego, więc trzeba najpierw Dodaj recenzentów do zespołu domyślnego. Kliknij przycisk **zaprosić** na **zespołu** kartę.

![Zaproś użytkowników](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Przypisz współpracowników, aby subteam

Kliknij przycisk **Dodawanie elementu członkowskiego** przycisk, aby przypisać członków zespołu domyślnego do co najmniej jeden zespoły podrzędne. Na ustalenie można dodawać tylko istniejących użytkowników. Dodawanie nowych użytkowników, którzy nie znajdują się w narzędzie do przeglądu, Zaproś ich przy użyciu przycisku "Zaproś" na stronie ustawienia zespołu.

![Przypisz elementy członkowskie ustalenie](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Przypisz przeglądów zespoły podrzędne

Po utworzeniu Twoje zespoły podrzędne i przypisać członków, możesz rozpocząć przypisywanie zawartości [przeglądy](../review-api.md#reviews) do tych zespoły podrzędne. Można to zrobić w **przeglądu** kartę.
Aby przypisać zawartość do ustalenie, kliknij wielokropek w prawym górnym rogu wybierz **przenieść**i wybierz ustalenie.

![Przypisz Przegląd obrazu, aby subteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Przełączanie między zespoły podrzędne

Jeśli jesteś członkiem więcej niż jeden ustalenie, można przełączać się między tymi zespoły podrzędne do zmiany, które przeglądy zawartości są wyświetlane dla Ciebie. W **przeglądu** , a następnie wybierz z menu rozwijanego etykietą **domyślne** i wybierz **wybierz ustalenie**. Można wyświetlić zawartości przeglądy różne zespoły podrzędne, ale tylko te, które usługi są członkiem.

![Przełączanie między zespoły podrzędne](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tagi

**Tagi** kartę umożliwia definiowanie tagów niestandardowych Moderowanie oprócz tagów Moderowanie dwóch domyślnych&mdash;**isadult** (**a**) i **isracy**  (**r**). Możesz utworzyć niestandardowy tag, staje się dostępna w przeglądach obok znaczniki domyślne. Można zmienić, jakie znaczniki mają wyświetlane w recenzji, przełączając ich ustawień widoczności.

![Wyświetlanie tagów, łącznie z pola wyboru "Jest widoczny"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Tworzenie niestandardowych znaczników

Aby utworzyć nowy tag, wprowadź krótki kod, nazwę i opis w odpowiednich polach.

- **Kod krótki**: Wprowadź kod dwuliterowych tag. Przykład: **cb**
- **Nazwa**: Wprowadź nazwę tagu krótki i opisowy małymi literami, bez spacji. Przykład: **isbullying**.
- **Opis**: wprowadź (opcjonalnie) opis typu zawartości, elementy docelowe tagu. Przykład: **Sceny lub wystąpień bullying cybernetycznymi**.

Kliknij przycisk **Dodaj** dodać tag, a następnie kliknij przycisk **Zapisz** po zakończeniu tworzenia tagów.

![Narzędzie do przeglądu utworzyć okno dialogowe Nowy tag](images/settings-3-tags.png)

### <a name="delete-tags"></a>Usuwanie tagów

Niestandardowe tagi można usuwać, wybierając ikonę Kosza obok ich wpisów na liście tagi, ale nie można usunąć znaczniki domyślne.

## <a name="connectors"></a>Łączniki

**Łączników** kartę umożliwia zarządzanie łączników, które są specyficzne dla usługi wtyczek, która może przetwarzać zawartości na różne sposoby, jako część zawartości [przepływy pracy](../review-api.md#workflows).

Łącznik domyślne podczas tworzenia przepływu pracy jest łącznik Content Moderator, który można oznaczyć zawartość jako **treści dla dorosłych** lub **erotycznej**, Znajdź wulgaryzmów i tak dalej. Jednak można użyć innych łączników, wymienione w tym miejscu, tak długo, jak użytkownik mający poświadczenia dla ich odpowiednich usług (do korzystania z łącznika interfejsu API rozpoznawania twarzy, na przykład, konieczne będzie uzyskanie [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview) klucz subskrypcji).

[Narzędzie do przeglądu](./human-in-the-loop.md) obejmuje następujące łączniki:

- Interfejs API rozpoznawania emocji
- Interfejs API rozpoznawania twarzy
- Usługa w chmurze PhotoDNA
- Interfejs API analizy tekstu

### <a name="add-a-connector"></a>Dodaj łącznik

Aby dodać łącznik (i udostępnić go do użytku w zawartości [przepływów pracy](../review-api.md#workflows)), wybierz odpowiedni **Connect** przycisku. W następnym oknie dialogowym Wprowadź klucz subskrypcji dla tej usługi. Gdy wszystko będzie gotowe, nowy łącznik powinien pojawić się w górnej części strony.

![Moderator łączników ustawienia zawartości](images/settings-4-connectors.png)

## <a name="workflows"></a>Przepływy

**Przepływy pracy** kartę umożliwia zarządzanie swoje [przepływy pracy](../review-api.md#workflows). Przepływy pracy są oparte na chmurze filtrów zawartości i pracują z łączników, aby posortować zawartość na różne sposoby i podjąć odpowiednie działania. W tym miejscu można zdefiniować, edytować i testowania swoich przepływów pracy. Zobacz [definiowanie i używanie przepływów pracy](Workflows.md) wskazówki dotyczące sposobu wykonania tego zadania.

![Content Moderator ustawienia przepływu pracy](images/settings-5-workflows.png)

## <a name="credentials"></a>Poświadczenia

**Poświadczenia** kartę zapewnia szybki dostęp do klucz subskrypcji pakietu Content Moderator, który jest potrzebny dostęp do dowolnej usługi Moderowanie z wywołania REST lub zestawu SDK klienta.

![Poświadczenia usługi Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Użyj poświadczeń zewnętrznych dla przepływów pracy

[Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) generuje klucz bezpłatnej wersji próbnej dla usługi Azure Content Moderator, gdy zarejestrujesz się, ale można również skonfigurować go do używania istniejącego klucza z kontem platformy Azure. Jest to zalecane w przypadku scenariuszy na dużą skalę, zgodnie z bezpłatnej wersji próbnej klucze limitami ograniczeniami ([ceny i limity](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Jeśli utworzono [zasobów pakietu Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) na platformie Azure, przejdź do niego w witrynie Azure portal i wybierz pozycję **klucze** bloku. Skopiuj jeden z kluczy.

![Content Moderator klucze w witrynie Azure portal](images/credentials-azure-portal-keys.PNG)

W [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com)firmy **poświadczenia** karty, wróć do **ustawienia przepływu pracy** okienku wybierz **Edytuj**i Wklej swój klucz do **Ocp-Apim-Subscription-Key** pola. Teraz przepływy pracy, które wywołują interfejsy API moderowania użyje poświadczeń do platformy Azure.

> [!NOTE]
> Inne dwa pola w **ustawienia przepływu pracy** okienko związanych z list niestandardowych termin i obrazów. Zobacz [warunki niestandardowe](../try-terms-list-api.md) lub [niestandardowych obrazów](../try-image-list-api.md) przewodników, aby dowiedzieć się więcej na temat tych.

### <a name="use-your-azure-account-with-the-review-apis"></a>Twoje konto platformy Azure za pomocą interfejsy API przeglądu

Za pomocą klucza usługi Azure przeglądu interfejsów API, musisz pobrać identyfikatora zasobu Przejdź do zasobu usługi Content Moderator w witrynie Azure portal i wybierz pozycję **właściwości** bloku. Skopiuj wartość Identyfikatora zasobu i wklej go do **identyfikatory zasobów na liście dozwolonych** pole narzędzie do przeglądu **poświadczenia** kartę.

![Identyfikator zasobu usługi Content Moderator w witrynie Azure portal](images/credentials-azure-portal-resourceid.PNG)

Klucz subskrypcji zostały wprowadzone w obu miejscach, wersji próbnej klucz, który jest dostarczany z Twoim kontem narzędzie do przeglądu, nie będą używane, ale nadal będą dostępne.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [szybkiego startu narzędzia przeglądu](../quick-start.md) aby rozpocząć korzystanie z narzędzia do przeglądu w scenariuszach moderowanie zawartości.