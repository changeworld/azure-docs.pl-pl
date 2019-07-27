---
title: Czym jest usługa Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi Content Moderator do śledzenia, flagowania, oceniania i filtrowania nieodpowiednich treści w zawartości wygenerowanej przez użytkownika.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: a78a92a33075a97ddadb2e1fe677b7ded541d12c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565582"
---
# <a name="what-is-azure-content-moderator"></a>Czym jest usługa Azure Content Moderator?

Interfejs API usługi Azure Content Moderator to usługa poznawcza, która sprawdza tekst, obrazy i zawartość wideo pod kątem treści potencjalnie obraźliwych, ryzykownych lub w jakikolwiek inny sposób niepożądanych. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników. Zobacz sekcję dotyczącą [moderowania interfejsów API](#moderation-apis) , aby dowiedzieć się więcej na temat tego, co wskazuje różne flagi zawartości.

## <a name="where-it-is-used"></a>Gdzie jest używany

Poniżej przedstawiono kilka scenariuszy, w których deweloper lub zespół ds. oprogramowania będą używać pakietu Content Moderator:

- Portale online, które moderowają katalogi produktów i inną zawartość wygenerowaną przez użytkownika.
- Firmy zajmujące się giermi, które moderowają wygenerowane przez użytkownika artefakty gier i pokoje rozmowy.
- Platformy do obsługi wiadomości społecznościowych, które mają umiarkowane obrazy, tekst i filmy wideo dodane przez ich użytkowników.
- Firmy zajmujące się multimediami przedsiębiorstwa, które implementują scentralizowane moderowanie dla ich zawartości.
- K-12 dostawcy rozwiązań edukacyjnych, który umożliwia filtrowanie zawartości nieodpowiedniej dla uczniów i wykładowców.

> [!NOTE]
> Nie można użyć Content Moderator do wykrywania niedozwolonych podrzędnych obrazów wykorzystujących luki w zabezpieczeniach. Jednak kwalifikacje organizacji mogą korzystać z usługi w chmurze [PhotoDNA](https://www.microsoft.com/photodna "firmy Microsoft PhotoDNA") dla tego typu zawartości.

## <a name="what-it-includes"></a>Co zawiera

Usługa Content Moderator składa się z kilku interfejsów API usług internetowych dostępnych za pośrednictwem wywołań REST i zestawu .NET SDK. Obejmuje również narzędzie przeglądu przez ludzi, dzięki któremu recenzenci mogą pomóc usłudze i ulepszyć lub dostosować jej funkcję moderowania.

## <a name="moderation-apis"></a>Interfejsy API moderowania

Usługa Content Moderator obejmuje interfejsy API moderowania, które sprawdzają zawartość materiałów, które są potencjalnie nieodpowiednie lub niepożądane.

![Diagram blokowy dla interfejsów API moderowania Content Moderator](images/content-moderator-mod-api.png)

W poniższej tabeli opisano różne typy interfejsów API moderowania.

| Grupa interfejsów API | Opis |
| ------ | ----------- |
|[**Moderowanie tekstu**](text-moderation-api.md)| Skanuje tekst w poszukiwaniu obraźliwej zawartości, niepopłciowo jawnej lub sugerującej zawartość, wulgarność i dane osobowe.|
|[**Niestandardowe listy terminów**](try-terms-list-api.md)| Skanuje tekst względem niestandardowych list terminów, poza terminami wbudowanymi. Użyj list niestandardowych, aby blokować zawartość lub zezwalać na nią zgodnie z własnymi zasadami dotyczącymi zawartości.|  
|[**Moderowanie obrazów**](image-moderation-api.md)| Skanuje obrazy pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywa tekst w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywa twarze.|
|[**Niestandardowe listy obrazów**](try-image-list-api.md)| Skanuje obrazy względem niestandardowej listy obrazów. Użyj niestandardowych list obrazów, aby filtrować wystąpienia często powtarzającej się zawartości, której nie chcesz klasyfikować ponownie.|
|[**Moderowanie filmów wideo**](video-moderation-api.md)| Skanuje filmy wideo pod kątem potencjalnej zawartości dla dorosłych lub zawartości erotycznej i zwraca znaczniki czasu dla wypowiadanej zawartości.|

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu pozwalają zintegrować potok moderowania z recenzentami ludzkich. Za pomocą [zadań](review-api.md#jobs), [przeglądów](review-api.md#reviews)i operacji [przepływu pracy](review-api.md#workflows) można tworzyć i automatyzować przepływy pracy w pętli, korzystając z [Narzędzia do przeglądu](#the-review-tool) (poniżej).

> [!NOTE]
> Interfejs API przepływu pracy nie jest jeszcze dostępny w zestawie SDK platformy .NET, ale może być używany z punktem końcowym REST.

![Diagram blokowy dla interfejsów API przeglądu Content Moderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Narzędzie do przeglądu

Usługa Content Moderator obejmuje również [Narzędzie do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md)w sieci Web, które hostuje przeglądy zawartości dla moderatorów ludzkich do przetworzenia. Dane wejściowe wprowadzane przez ludzi nie uczą usługi, ale połączona praca usługi i zespołów ds. przeglądu przez ludzi pozwala deweloperom uzyskać równowagę między wydajnością i dokładnością. Narzędzie Recenzja zapewnia również przyjazne dla użytkownika fronton dla różnych zasobów Content Moderator.

![Strona główna narzędzia do przeglądu przez ludzi usługi Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Content Moderator powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z usługi Content Moderator, postępując zgodnie z instrukcjami w przewodniku [Wypróbowywanie usługi Content Moderator w Internecie](quick-start.md).