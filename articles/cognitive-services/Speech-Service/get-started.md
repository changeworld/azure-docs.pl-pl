---
title: Wypróbuj bezpłatnie usługę rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do usługi mowy jest proste i niedrogie. Dostępne są bezpłatne opłaty za korzystanie z dwóch opcji, dzięki czemu możesz odkryć, co usługa może zrobić, i zdecydować, czy jest to odpowiednie dla Twoich potrzeb.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 30bdbf9fa0ea346892622c3e7f24f9f31652a650
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280478"
---
# <a name="try-the-speech-service-for-free"></a>Wypróbuj bezpłatnie usługę rozpoznawania mowy

Korzystanie z usługi Speech jest proste i niedrogie. Dostępne są bezpłatne opłaty za korzystanie z dwóch opcji, dzięki czemu można odkryć, co może zrobić usługa, i zdecydować, czy jest to odpowiednie dla Twoich potrzeb:

- Uzyskaj bezpłatną wersję próbną bez podawania żadnych informacji o kartach kredytowych (musisz mieć istniejące konto platformy Azure)
- Utwórz nowe konto platformy Azure bez opłat za okres próbny (wymagane są informacje o karcie kredytowej)

W tym artykule opisano, jak wybrać jedną z tych opcji, które najlepiej odpowiadają potrzebom.

> [!NOTE]
> Usługa mowy ma dwie warstwy usług: bezpłatna i subskrypcja, które mają różne ograniczenia i korzyści. Po zarejestrowaniu się w celu uzyskania bezpłatnego konta platformy Azure otrzymasz od $200 w wysokości, które można zastosować do płatnej subskrypcji usługi mowy, która jest ważna przez maksymalnie 30 dni.
>
> W przypadku korzystania z bezpłatnej warstwy usługi mowy o niskiej pojemności można zachować tę bezpłatną subskrypcję nawet po wygaśnięciu bezpłatnej wersji próbnej lub opłaty za usługę.
>
> Aby uzyskać więcej informacji, zobacz [Cognitive Services ceny — usługi mowy](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="try-the-speech-service-without-credit-card-info"></a>Wypróbuj usługę mowy bez informacji o karcie kredytowej

Chociaż zalecamy podjęcie próby skorzystania z usługi mowy przy użyciu instrukcji w następnej sekcji, możesz preferować instrukcje tej sekcji w przypadku zastosowania następujących warunków:

- Masz już aktywne konto platformy Azure.
- Chcesz oszacować usługę mowy bez tworzenia nowego konta platformy Azure.
- Nie jest wymagana karta kredytowa ani nie zapisano danych po okresie próbnym.

> [!NOTE]
> Okres próbny rozpocznie się natychmiast po zakończeniu następujących kroków.

1. Przejdź do pozycji [Wypróbuj usługę poznawczej](https://azure.microsoft.com/try/cognitive-services/).
1. Wybierz **interfejsy API rozpoznawania mowy** kartę.
1. Wybierz pozycję **Pobierz klucz interfejsu API**.

Zostaną wyświetlone opcje rozliczeń. Wybierz opcję bezpłatna, a następnie przeczytaj i zatwierdź umowę użytkownika. Zostaną wyświetlone klucze, których można użyć w celu wypróbowania usługi mowy przez ograniczony czas.

## <a name="try-the-speech-service-using-a-new-azure-account"></a>Wypróbuj usługę mowy przy użyciu nowego konta platformy Azure

Aby zarejestrować się w celu uzyskania nowego konta platformy Azure, musisz mieć konto Microsoft. Jeśli nie masz konto Microsoft, możesz skorzystać z jednej bezpłatnej rejestracji w [portalu konto Microsoft](https://account.microsoft.com/account). Wybierz pozycję **Zaloguj się przy użyciu konta Microsoft** a następnie, gdy zostanie wyświetlony monit o zalogowanie się, wybierz pozycję **Utwórz konto Microsoft**. Wykonaj kroki, aby utworzyć i zweryfikować nowego konta Microsoft.

Gdy masz konto Microsoft, przejdź do [strony tworzenia konta platformy Azure](https://azure.microsoft.com/free/ai/), wybierz pozycję **Rozpocznij bezpłatnie**i Utwórz nowe konto platformy Azure przy użyciu konto Microsoft.

### <a name="create-a-speech-resource-in-azure"></a>Utwórz zasób mowy na platformie Azure

> [!NOTE]
> Można utworzyć nieograniczoną liczbę subskrypcji do warstwy standardowa w jednym lub wielu regionach. Jednak można utworzyć tylko jedną subskrypcję z warstwy bezpłatna. Wdrożenie modelu w warstwie Bezpłatna, które pozostanie nieużywane przez 7 dni, zostanie zlikwidowane automatycznie.

Aby dodać zasób usługi mowy (w warstwie bezpłatna lub płatna) do konta platformy Azure:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta Microsoft.

1. Wybierz **Utwórz zasób** w lewym górnym rogu portalu. Jeśli nie widzisz opcji **Utwórz zasób**, zawsze możesz ją znaleźć, wybierając menu zwinięte w lewym górnym rogu:

   ![zwinięty przycisk nawigacji](media/index/collapsed-nav.png)

1. W **nowym** oknie wpisz "mowę" w polu wyszukiwania, a następnie naciśnij klawisz ENTER.

1. W wynikach wyszukiwania wybierz **mowy**.

   ![Wyniki wyszukiwania mowy](media/index/speech-search.png)

1. Wybierz pozycję **Utwórz**, a następnie:

   - Nadaj unikatowej nazwie nowemu zasobowi. Nazwa służy do rozróżnienia wielu subskrypcji tej samej usługi.
   - Wybierz subskrypcję platformy Azure, nowy zasób z którym skojarzony jest ustalenie, jak są naliczane opłaty.
   - Wybierz [region](regions.md) , w którym będzie używany zasób.
   - Wybierz opcję bezpłatna (F0) lub płatna (S0). Aby uzyskać pełne informacje na temat cen i przydziałów użycia dla każdej warstwy, wybierz pozycję **Wyświetl pełne szczegóły cennika**.
   - Utwórz nową grupę zasobów dla tej subskrypcji mowy lub Przypisz subskrypcję przechodząc do istniejącej grupy zasobów. Pomoc, której możesz przechowywać różne subskrypcje platformy Azure uporządkowane grupy zasobów.
   - Wybierz **tworzenie**. Spowoduje to przejście do omówienia wdrażania i wyświetlenie komunikatów o postępie wdrażania.

Wdrożenie nowego zasobu mowy trwa kilka minut. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu** , a następnie w okienku nawigacji po lewej stronie wybierz pozycję **klucze** , aby wyświetlić klucze subskrypcji usługi mowy. Każda subskrypcja ma dwa klucze; Możesz użyć dowolnego klucza w aplikacji. Aby szybko skopiować/wkleić klucz do edytora kodu lub innej lokalizacji, wybierz przycisk Kopiuj obok każdego klucza, Przełącz system Windows, aby wkleić zawartość schowka do żądanej lokalizacji.

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi poznawczej. Nie udostępniaj kluczy. Przechowuj je bezpiecznie — na przykład przy użyciu Azure Key Vault. Zalecamy także regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest wymagany do wywołania interfejsu API. Po ponownym wygenerowaniu pierwszego klucza można użyć drugiego klawisza w celu uzyskania ciągłego dostępu do usługi.

## <a name="switch-to-a-new-subscription"></a>Przełącz się do nowej subskrypcji

Aby przełączyć się z jednej subskrypcji, na przykład kiedy Twoja bezpłatna wersja próbna wygaśnie lub gdy publikujesz aplikację, należy zastąpić klucz regionu i subskrypcji w kodzie za pomocą klucza regionu i subskrypcji nowego zasobu platformy Azure.

## <a name="about-regions"></a>Regiony — informacje

- Jeśli aplikacja używa [zestaw SDK rozpoznawania mowy](speech-sdk.md), takich jak Podaj kod regionu `westus`, podczas tworzenia konfiguracji mowy.
- Jeśli aplikacja używa jednej z usług mowy [interfejsów API REST](rest-apis.md), region jest częścią punktu końcowego identyfikatora URI, można użyć podczas wysyłania żądań.
- Kluczy utworzonych dla regionu są prawidłowe tylko w tym regionie. Podjęto próbę użycia je z innymi regionami będą powodować błędy uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

Wykonaj jedną z naszych 10-minutowe Przewodniki Szybki Start, lub Sprawdź nasze przykłady zestawu SDK:

> [!div class="nextstepaction"]
> [Szybki Start: Rozpoznawanie mowy w języku C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [przykładowych zestawach SDK rozpoznawania mowy](speech-sdk.md#get-the-samples)
