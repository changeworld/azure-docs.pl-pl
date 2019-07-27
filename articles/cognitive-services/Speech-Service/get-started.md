---
title: Wypróbuj bezpłatnie usługę mowy
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do usługi mowy jest proste i niedrogie. 30-dniowej bezpłatnej wersji próbnej umożliwia odnajdywanie usługi wykonaj co zdecydować, czy jest on odpowiedni dla twojej aplikacji potrzebom.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a8797afdfacfcfc75445c7f35083aeb9bb847ac1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562932"
---
# <a name="try-speech-services-for-free"></a>Wypróbuj bezpłatnie usługi mowy

Wprowadzenie do usług mowy jest proste i niedrogie. 30-dniowej bezpłatnej wersji próbnej umożliwia odnajdywanie usługi wykonaj co zdecydować, czy jest on odpowiedni dla twojej aplikacji potrzebom.

Jeśli potrzebujesz więcej czasu, zarejestruj się, aby skorzystać z konta usługi Microsoft Azure — jest to opłata za usługę $200 w wysokości.

Na koniec usługi mowy oferują bezpłatną, niską warstwę, która jest odpowiednia do tworzenia aplikacji. Możesz zachować tej bezpłatnej subskrypcji, nawet po wygaśnięciu środków usługi.

## <a name="free-trial"></a>Bezpłatna wersja próbna

30-dniowej bezpłatnej wersji próbnej umożliwia dostęp do ze standardowej warstwy cenowej przez ograniczony czas.

Aby zarejestrować się w 30-dniowej bezpłatnej wersji próbnej:

1. Przejdź do [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Wybierz **interfejsy API rozpoznawania mowy** kartę.

   ![Karta usługi mowy](media/index/try-speech-api-free-trial1.png)

1. W obszarze **usługi mowy**wybierz przycisk **Pobierz klucz interfejsu API** .

   ![Klucz interfejsu API](media/index/try-speech-api-free-trial2.png)

1. Zgody na warunki, a następnie wybierz ustawienia regionalne, z menu rozwijanego.

   ![Wyraź zgodę na warunki](media/index/try-speech-api-free-trial3.png)

1. Zaloguj się przy użyciu swojego konta Microsoft, Facebook, LinkedIn i GitHub.

    Możesz zasubskrybować bezpłatnego konta Microsoft na [portalu konta usługi Microsoft](https://account.microsoft.com/account). Aby rozpocząć, kliknij pozycję **Zaloguj się przy użyciu Microsoft** , a następnie po wyświetleniu monitu, aby zalogować się, kliknij **utwórz je.** Wykonaj kroki, aby utworzyć i zweryfikować nowego konta Microsoft.

Po zalogowaniu się do usług Cognitive Services spróbuj zaczyna się Twoja bezpłatna wersja próbna. Wyświetlane strony sieci Web Wyświetla listę wszystkich usług Azure Cognitive Services do których użytkownik ma obecnie subskrypcji wersji próbnej. Dwa klucze subskrypcji są wyświetlane obok **usługi Speech Services**. Żadnego z nich można używać w aplikacjach.

> [!NOTE]
> Wszystkie subskrypcje bezpłatnej wersji próbnej znajdują się w regionie zachodnie stany USA. Po wprowadzeniu żądań, należy użyć `westus` punktu końcowego.

## <a name="new-azure-account"></a>Nowe konto platformy Azure

Nowe konta usługi Azure otrzymywać 200 USD środków usługa, która jest dostępna przez maksymalnie 30 dni. Możesz użyć tego kredytu, aby dowiedzieć się więcej o usługach mowy lub rozpocząć tworzenie aplikacji.

Aby utworzyć nowe konto platformy Azure, przejdź do [strona tworzenia konta usługi Azure](https://azure.microsoft.com/free/ai/), kliknij przycisk **Rozpocznij za darmo,** i Utwórz nowe konto platformy Azure przy użyciu konta Microsoft.

Możesz zasubskrybować bezpłatnego konta Microsoft na [portalu konta usługi Microsoft](https://account.microsoft.com/account). Aby rozpocząć, kliknij pozycję **Zaloguj się przy użyciu Microsoft** , a następnie po wyświetleniu monitu, aby zalogować się, kliknij **utwórz je.** Wykonaj kroki, aby utworzyć i zweryfikować nowego konta Microsoft.

Po utworzeniu konta platformy Azure postępuj zgodnie z instrukcjami w następnej sekcji, aby rozpocząć subskrypcję usługi Speech Services.

## <a name="create-a-speech-resource-in-azure"></a>Utwórz zasób mowy na platformie Azure

Aby dodać zasób usługi Speech Services (warstwa Bezpłatna lub płatna) do konta platformy Azure:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta Microsoft.

1. Wybierz **Utwórz zasób** w lewym górnym rogu portalu.

    ![Tworzenie zasobu](media/index/try-speech-api-create-speech1.png)

1. W **New** okna, wyszukaj **mowy**.

1. W wynikach wyszukiwania wybierz **mowy**.

    ![Wybierz opcję rozpoznawania mowy](media/index/try-speech-api-create-speech2.png)

1. W obszarze **mowy**, wybierz opcję **Utwórz** przycisku.

    ![Kliknij przycisk Utwórz](media/index/try-speech-api-create-speech3.png)

1. W obszarze **Utwórz**, wpisz:

   * Nazwa dla nowego zasobu. Nazwa służy do rozróżnienia wielu subskrypcji tej samej usługi.
   * Wybierz subskrypcję platformy Azure, nowy zasób z którym skojarzony jest ustalenie, jak są naliczane opłaty.
   * Wybierz [region](regions.md) , w którym będzie używany zasób.
   * Wybierz opcję bezpłatnej i płatnej warstwy cenowej. Kliknij przycisk **Wyświetl pełne szczegóły ceny** pełne informacje dotyczące cen i użycie przydziały dla każdej warstwy.
   * Utwórz nową grupę zasobów dla tej subskrypcji mowy lub Przypisz subskrypcję przechodząc do istniejącej grupy zasobów. Pomoc, której możesz przechowywać różne subskrypcje platformy Azure uporządkowane grupy zasobów.
   * Wygodny dostęp do Twojej subskrypcji w przyszłości, wybierz **Przypnij do pulpitu nawigacyjnego** pole wyboru.
   * Wybierz **utworzyć.**

     ![Kliknij przycisk Utwórz](media/index/try-speech-api-create-speech4.png)

     Może potrwać kilka minut, aby utworzyć i wdrożyć nowy zasób mowy. Wybierz **Szybki Start** Aby wyświetlić informacje dotyczące nowego zasobu.

     ![Panel szybkiego startu](media/index/try-speech-api-create-speech5.png)

1. W obszarze **Szybki Start**, kliknij przycisk **klucze** łącze w kroku 1, aby wyświetlić klucze subskrypcji. Każda subskrypcja ma dwa klucze; Możesz użyć dowolnego klucza w aplikacji. Wybierz przycisk obok każdego klucza, aby skopiować go do Schowka wklejania w kodzie.

> [!NOTE]
> Można utworzyć nieograniczoną liczbę subskrypcji do warstwy standardowa w jednym lub wielu regionach. Jednak można utworzyć tylko jedną subskrypcję z warstwy bezpłatna. Wdrożenia modelu w ramach warstwy bezpłatna, które pozostają nieużywane przez 7 dni będzie można ponowić automatycznie.

## <a name="switch-to-a-new-subscription"></a>Przełącz się do nowej subskrypcji

Aby przełączyć się z jednej subskrypcji, na przykład kiedy Twoja bezpłatna wersja próbna wygaśnie lub gdy publikujesz aplikację, należy zastąpić klucz regionu i subskrypcji w kodzie za pomocą klucza regionu i subskrypcji nowego zasobu platformy Azure.

> [!NOTE]
> Bezpłatna wersja próbna klucze są tworzone w regionie zachodnie stany USA (`westus`) regionie. Z subskrypcji utworzonej za pomocą pulpitu nawigacyjnego platformy Azure może być w niektórych innym regionie, wybranie opcji Tak.

* Jeśli aplikacja używa [zestaw SDK rozpoznawania mowy](speech-sdk.md), takich jak Podaj kod regionu `westus`, podczas tworzenia konfiguracji mowy.
* Jeśli aplikacja używa jednego z [interfejsów API REST](rest-apis.md)usługi mowy, region jest częścią identyfikatora URI punktu końcowego, który jest używany podczas tworzenia żądań.

Kluczy utworzonych dla regionu są prawidłowe tylko w tym regionie. Podjęto próbę użycia je z innymi regionami będą powodować błędy uwierzytelniania.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj jedną z naszych 10-minutowe Przewodniki Szybki Start, lub Sprawdź nasze przykłady zestawu SDK:

> [!div class="nextstepaction"]
> [Szybki start: Rozpoznawanie mowy w C# ](quickstart-csharp-dotnet-windows.md) 
>  [przykładach zestawu Speech SDK](speech-sdk.md#get-the-samples)
