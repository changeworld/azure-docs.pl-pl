---
title: Wypróbuj bezpłatnie usługę mowy
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do usługi mowy jest proste i niedrogie. 30-dniowa bezpłatna wersja próbna umożliwia wykrycie działania usługi i określenie, czy jest ona odpowiednia dla potrzeb aplikacji.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 46ada91060aa095b7c041ff75abb6256f3d05853
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464698"
---
# <a name="try-speech-services-for-free"></a>Wypróbuj bezpłatnie usługi mowy

Wprowadzenie do usług mowy jest proste i niedrogie. 30-dniowa bezpłatna wersja próbna umożliwia wykrycie działania usługi i określenie, czy jest ona odpowiednia dla potrzeb aplikacji.

Jeśli potrzebujesz więcej czasu, zarejestruj się, aby skorzystać z konta usługi Microsoft Azure — jest to opłata za usługę $200 w wysokości.

Na koniec usługi mowy oferują bezpłatną, niską warstwę, która jest odpowiednia do tworzenia aplikacji. Tę bezpłatną subskrypcję można zachować nawet po wygaśnięciu środków na korzystanie z usługi.

## <a name="free-trial"></a>Bezpłatna wersja próbna

30-dniowa bezpłatna wersja próbna zapewnia dostęp do warstwy cenowej standardowa przez ograniczony czas.

Aby zarejestrować się w ramach 30-dniowej bezpłatnej wersji próbnej:

1. Przejdź do pozycji [wypróbuj Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Wybierz kartę **interfejsy API rozpoznawania mowy** .

   ![Speech API — karta usługi rozpoznawania mowy](media/index/cognitive-services-speech-api-tab.png)

1. W obszarze **usługi mowy**wybierz pozycję **Pobierz klucz interfejsu API**.

   ![Speech API — Pobierz klucz interfejsu API](media/index/speech-api-get-api-key.png)

1. Zaakceptuj warunki i wybierz ustawienia regionalne z menu rozwijanego.

   ![Speech API — wyrażanie zgody na warunki](media/index/speech-api-agree-to-terms.png)

1. Zaloguj się przy użyciu konta Microsoft, Facebook, LinkedIn lub GitHub.

    Możesz zarejestrować się w celu uzyskania bezpłatnej konto Microsoft w [portalu konto Microsoft](https://account.microsoft.com/account). Aby rozpocząć, wybierz pozycję **Zaloguj się przy użyciu konta Microsoft** a następnie, gdy zostanie wyświetlony monit o zalogowanie się, wybierz pozycję **Utwórz.** Postępuj zgodnie z instrukcjami, aby utworzyć i zweryfikować nowe konto Microsoft.

Po zalogowaniu się w celu wypróbowania Cognitive Services możesz rozpocząć korzystanie z bezpłatnej wersji próbnej. Wyświetlana strona sieci Web zawiera listę wszystkich usług Cognitive Services platformy Azure, dla których obecnie są dostępne subskrypcje wersji próbnej. Dwa klucze subskrypcji są wyświetlane obok **usługi Speech Services**. Możesz użyć dowolnego klucza w aplikacjach.

> [!NOTE]
> Wszystkie bezpłatne subskrypcje wersji próbnej znajdują się w regionie zachodnie stany USA. Podczas wprowadzania żądań upewnij się, że używasz punktu końcowego `westus`.

## <a name="new-azure-account"></a>Nowe konto platformy Azure

Nowe konta platformy Azure otrzymują środki na korzystanie z usługi $200 dostępnej przez maksymalnie 30 dni. Możesz użyć tego kredytu, aby dowiedzieć się więcej o usługach mowy lub rozpocząć tworzenie aplikacji.

Aby zarejestrować się w celu utworzenia nowego konta platformy Azure, przejdź na [stronę rejestracji w usłudze Azure](https://azure.microsoft.com/free/ai/), wybierz pozycję **Rozpocznij bezpłatnie** i Utwórz nowe konto platformy Azure przy użyciu konto Microsoft.

Możesz zarejestrować się w celu uzyskania bezpłatnej konto Microsoft w [portalu konto Microsoft](https://account.microsoft.com/account). Aby rozpocząć, wybierz pozycję **Zaloguj się przy użyciu konta Microsoft** a następnie, gdy zostanie wyświetlony monit o zalogowanie się, wybierz pozycję **Utwórz.** Postępuj zgodnie z instrukcjami, aby utworzyć i zweryfikować nowe konto Microsoft.

Po utworzeniu konta platformy Azure postępuj zgodnie z instrukcjami w następnej sekcji, aby rozpocząć subskrypcję usługi Speech Services.

## <a name="create-a-speech-resource-in-azure"></a>Tworzenie zasobu mowy na platformie Azure

Aby dodać zasób usługi Speech Services (warstwa Bezpłatna lub płatna) do konta platformy Azure:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konto Microsoft.

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

    ![Speech API — Tworzenie zasobu](media/index/speech-api-create-resource.png)

1. W **nowym** oknie Wyszukaj tekst **Speech**.

1. W wynikach wyszukiwania wybierz pozycję **Speech**.

    ![Speech API — wybierz mowę](media/index/speech-api-select-speech.png)

1. W obszarze **mowa**wybierz przycisk **Utwórz** .

    ![Speech API — przycisk Utwórz](media/index/speech-api-create-button.png)

1. W obszarze **Utwórz**wpisz:

   * Nazwa nowego zasobu. Nazwa pomaga rozróżnić wiele subskrypcji do tej samej usługi.
   * Wybierz subskrypcję platformy Azure, z którą jest skojarzony nowy zasób, aby określić, w jaki sposób opłaty są naliczane.
   * Wybierz [region](regions.md) , w którym będzie używany zasób.
   * Wybierz bezpłatną lub płatną warstwę cenową. Wybierz pozycję **Wyświetl pełne szczegóły cennika** , aby uzyskać pełne informacje na temat cen i przydziałów użycia dla każdej warstwy.
   * Utwórz nową grupę zasobów dla tej subskrypcji mowy lub Przypisz subskrypcję do istniejącej grupy zasobów. Grupy zasobów ułatwiają zachowanie różnych subskrypcji platformy Azure.
   * Aby wygodnie uzyskać dostęp do subskrypcji w przyszłości, zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** .
   * Wybierz pozycję **Utwórz.**

     ![Speech API — wybierz pozycję Utwórz](media/index/speech-api-select-create.png)

     Tworzenie i wdrażanie nowego zasobu mowy trwa chwilę. Wybierz pozycję **Szybki Start** , aby wyświetlić informacje o nowym zasobie.

     ![Speech API — wdrażanie zasobu](media/index/speech-api-deploy-resource.png)

1. W obszarze **Szybki Start**wybierz link **klucze** w obszarze Krok 1, aby wyświetlić klucze subskrypcji. Każda subskrypcja ma dwa klucze: Możesz użyć dowolnego klawisza w aplikacji. Wybierz przycisk obok każdego klucza, aby skopiować go do Schowka w celu wklejenia do kodu.

> [!NOTE]
> W jednym lub wielu regionach można utworzyć nieograniczoną liczbę subskrypcji w warstwie Standardowa. Można jednak utworzyć tylko jedną subskrypcję warstwy Bezpłatna. Wdrożenie modelu w warstwie Bezpłatna, które pozostanie nieużywane przez 7 dni, zostanie decomissioned automatycznie.

## <a name="switch-to-a-new-subscription"></a>Przełącz do nowej subskrypcji

Aby przełączyć się z jednej subskrypcji na drugą, na przykład jeśli bezpłatna wersja próbna wygaśnie lub opublikowanie aplikacji zastąpi klucz regionu i subskrypcji w kodzie przy użyciu regionu i klucza subskrypcji nowego zasobu platformy Azure.

> [!NOTE]
> Klucze bezpłatnej wersji próbnej są tworzone w regionie zachodnie stany USA (`westus`). Subskrypcja utworzona za pośrednictwem pulpitu nawigacyjnego platformy Azure może znajdować się w innym regionie, jeśli wybierzesz tę opcję.

* Jeśli aplikacja korzysta z [zestawu Speech SDK](speech-sdk.md), podczas tworzenia konfiguracji mowy należy podać kod regionu, taki jak `westus`.
* Jeśli aplikacja używa jednego z [interfejsów API REST](rest-apis.md)usługi mowy, region jest częścią identyfikatora URI punktu końcowego, który jest używany podczas tworzenia żądań.

Klucze utworzone dla regionu są prawidłowe tylko w tym regionie. Próba użycia ich z innymi regionami spowoduje błędy uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

Wykonaj jedną z naszych 10-minutowych przewodników szybki start lub zapoznaj się z naszymi przykładami zestawu SDK:

> [!div class="nextstepaction"]
> [Szybki Start: Rozpoznawanie mowy C# w](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [przykłady zestawu Speech SDK](speech-sdk.md#get-the-samples)
