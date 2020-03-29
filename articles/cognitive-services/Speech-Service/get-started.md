---
title: Wypróbuj bezpłatnie usługę rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do usługi mowy jest łatwe i niedrogie. Dostępne są dwie opcje dostępne bezpłatnie, dzięki czemu można dowiedzieć się, co usługa może zrobić i zdecydować, czy jest odpowiedni dla Twoich potrzeb.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219666"
---
# <a name="try-the-speech-service-for-free"></a>Wypróbuj bezpłatnie usługę rozpoznawania mowy

W tym artykule wybierz opcję, aby łatwo przetestować usługę mowy bezpłatnie, dzięki czemu można dowiedzieć się, co usługa może zrobić i zdecydować, czy jest to odpowiednie dla Twoich potrzeb. Wybierz jedną z dwóch następujących opcji w zależności od sytuacji i przypadku użycia:

- [Opcja 1:](#no-card)Natychmiast uzyskaj **bezpłatne klucze interfejsu** API próbnego bez podawania żadnych informacji o karcie kredytowej (musisz mieć istniejące konto platformy Azure). **Bezpłatna wersja próbna** trwa 30 dni, a dane są usuwane na końcu. Ta opcja jest najlepsza do szybkiego eksperymentowania z usługą.
- [Opcja 2:](#new-resource)Utwórz nowy zasób mowy na platformie Azure, bezpłatnie korzystając z **bezpłatnej subskrypcji** (wymagane informacje o karcie kredytowej). **Bezpłatna subskrypcja** ma głównie bardziej rygorystyczne limity stawek niż płatna subskrypcja. Ta opcja jest najlepsza, jeśli chcesz przetestować usługę, ale również planujesz uaktualnienie do płatnej subskrypcji w przyszłości i nie chcesz utracić danych.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Wypróbuj usługę Mowy bez informacji o karcie kredytowej

Wykonaj następujące kroki, aby aktywować 30-dniową bezpłatną wersję próbną i uzyskać klucze interfejsu API. Okres próbny rozpoczyna się natychmiast po wykonaniu następujących kroków.

1. Przejdź do [try cognitive service](https://azure.microsoft.com/try/cognitive-services/).
1. Wybierz kartę **Interfejsy API mowy.**
1. Wybierz **pozycję Pobierz klucz interfejsu API**.

Otrzymasz opcje rozliczeń. Wybierz opcję bezpłatną, a następnie przeczytaj i zatwierdź umowę z użytkownikiem. Przez 30 dni otrzymasz klucze, za pomocą których możesz wypróbować bezpłatną usługę mowy.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Wypróbuj usługę Mowy, tworząc zasób platformy Azure

W poniższych krokach potrzebne jest zarówno konto Microsoft, jak i konto platformy Azure. Jeśli nie masz konta Microsoft, możesz zarejestrować się w jednym bezpłatnie w [portalu konta Microsoft](https://account.microsoft.com/account). Wybierz pozycję **Zaloguj się za pomocą firmy Microsoft,** a następnie, gdy zostaniesz poproszony o zalogowanie się, wybierz pozycję **Utwórz konto Microsoft**. Wykonaj kroki, aby utworzyć i zweryfikować nowe konto Microsoft.

Po utworzeniu konta Microsoft przejdź do [strony rejestracji platformy Azure](https://azure.microsoft.com/free/ai/), wybierz pozycję **Rozpocznij bezpłatnie**i utwórz nowe konto platformy Azure przy użyciu konta Microsoft.

> [!NOTE]
> Usługa Mowy ma dwie warstwy usług: bezpłatna i subskrypcja, które mają różne ograniczenia i korzyści. Po zarejestrowaniu się na bezpłatne konto platformy Azure pochodzi z $200 w kredytach na usługi, które można zastosować do płatnej subskrypcji usługi mowy, ważne przez maksymalnie 30 dni.
>
> Jeśli korzystasz z bezpłatnej, niskonakładowej warstwy usługi mowy, możesz zachować tę bezpłatną subskrypcję nawet po wygaśnięciu bezpłatnego okresu próbnego lub kredytu na usługę.
>
> Aby uzyskać więcej informacji, zobacz [Ceny usług Cognitive Services — usługa mowy](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Tworzenie zasobu

Aby dodać zasób usługi mowy (warstwy bezpłatnej lub płatnej) do konta platformy Azure:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta Microsoft.

1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu portalu. Jeśli nie widzisz opcji **Utwórz zasób,** zawsze możesz go znaleźć, wybierając zwinięte menu w lewym górnym rogu:

   ![zwinięty przycisk nawigacyjny](media/index/collapsed-nav.png)

1. W oknie **Nowy** wpisz "mowa" w polu wyszukiwania i naciśnij klawisz ENTER.

1. W wynikach wyszukiwania wybierz pozycję **Mowa**.

   ![wyniki wyszukiwania mowy](media/index/speech-search.png)

1. Wybierz **pozycję Utwórz**, a następnie:

   - Nadaj unikatową nazwę nowego zasobu. Nazwa pomaga odróżnić wiele subskrypcji powiązanych z tą samą usługą.
   - Wybierz subskrypcję platformy Azure, z którą jest skojarzony nowy zasób, aby określić sposób naliczania opłat.
   - Wybierz [region,](regions.md) w którym będzie używany zasób.
   - Wybierz bezpłatną warstwę cenową (F0) lub płatną (S0). Aby uzyskać pełne informacje na temat cen i przydziałów użycia dla każdej warstwy, wybierz **pozycję Wyświetl pełne szczegóły cen**.
   - Utwórz nową grupę zasobów dla tej subskrypcji mowy lub przypisz subskrypcję do istniejącej grupy zasobów. Grupy zasobów ułatwiają organizowanie różnych subskrypcji platformy Azure.
   - Wybierz **pozycję Utwórz**. Spowoduje to przejście do przeglądu wdrażania i wyświetli komunikaty postępu wdrażania.

> [!NOTE]
> Można utworzyć nieograniczoną liczbę subskrypcji warstwy standardowej w jednym lub wielu regionach. Można jednak utworzyć tylko jedną subskrypcję warstwy bezpłatnej. Wdrożenia modelu w warstwie bezpłatnej, które pozostają nieużywane przez 7 dni, zostaną automatycznie wycofane.

Wdrożenie nowego zasobu mowy zajmuje kilka chwil. Po zakończeniu wdrażania wybierz **pozycję Przejdź do zasobu,** a w lewym okienku nawigacji wybierz pozycję **Klawisze,** aby wyświetlić klucze subskrypcji usługi mowy. Każda subskrypcja ma dwa klucze; można użyć jednego z kluczy w aplikacji. Aby szybko skopiować/wkleić klucz do edytora kodu lub innej lokalizacji, wybierz przycisk kopiowania obok każdego klawisza, przełącz okna, aby wkleić zawartość schowka w żądaną lokalizację.

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi Cognitive Service. Nie udostępniaj kluczy. Przechowuj je bezpiecznie — na przykład przy użyciu usługi Azure Key Vault. Zalecamy również regularne regenerowanie tych kluczy. Tylko jeden klucz jest niezbędny do wywołania interfejsu API. Podczas ponownego generowania pierwszego klucza, można użyć drugiego klucza do dalszego dostępu do usługi.

## <a name="switch-to-a-new-subscription"></a>Przełączanie do nowej subskrypcji

Aby przełączyć się z jednej subskrypcji do innej, na przykład po wygaśnięciu bezpłatnej wersji próbnej lub podczas publikowania aplikacji, zastąp region i klucz subskrypcji w kodzie regionem i kluczem subskrypcji nowego zasobu platformy Azure.

## <a name="about-regions"></a>Informacje o regionach

- Jeśli aplikacja używa [SDK mowy](speech-sdk.md), należy podać kod `westus`regionu, takich jak , podczas tworzenia konfiguracji mowy.
- Jeśli aplikacja używa jednego z interfejsów [API REST](rest-apis.md)usługi mowy, region jest częścią identyfikatora URI punktu końcowego używanego podczas składania żądań.
- Klucze utworzone dla regionu są prawidłowe tylko w tym regionie. Próba użycia ich z innymi regionami spowoduje błędy uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

Ukończ jeden z naszych 10-minutowych przewodników Szybki start lub sprawdź nasze próbki SDK:

> [!div class="nextstepaction"]
> [Szybki start: rozpoznawanie mowy w](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [przykładach SDK mowy](speech-sdk.md#get-the-samples) języka C#
