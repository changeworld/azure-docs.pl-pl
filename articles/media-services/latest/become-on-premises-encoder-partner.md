---
title: Zostań lokalnym partnerem kodera - Azure Media Services
description: W tym artykule omówiono sposób weryfikacji lokalnych koderów strumieniowych na żywo.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298635"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Jak zweryfikować lokalnie koder transmisji strumieniowej na żywo

Jako lokalny partner kodera usługi Azure Media Services usługa Media Services promuje produkt, polecając koder klientom korporacyjnym. Aby stać się lokalnym partnerem kodera, należy sprawdzić zgodność kodera lokalnego z usługą Media Services. Aby to zrobić, wykonaj następujące weryfikacje.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Weryfikacja zdarzenia na żywo

1. Na koncie usługi Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiony. 
2. Utwórz i rozpocznij **transmisję** wydarzenia na żywo. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder lokalny tak, aby używał adresu URL do wysyłania strumienia na żywo o wielu szybkościach transmisji bitów do usługi Media Services.
4. Pobierz adres URL wersji zapoznawczej i użyj go, aby sprawdzić, czy dane wejściowe z kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobu.**
6. Utwórz **dane wyjściowe na żywo** i użyj utworzonej nazwy zasobu.
7. Utwórz **lokalizator przesyłania strumieniowego** z wbudowanymi typami **zasad przesyłania strumieniowego.**
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego,** aby przywrócić adresy URL do użycia.
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego,** z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo. 
13. Użyj odtwarzacza, takiego jak [Azure Media Player,](https://aka.ms/azuremediaplayer) aby obejrzeć zarchiwizowanego zasobu, aby upewnić się, że odtwarzanie nie ma widocznych usterek na wszystkich poziomach jakości. Możesz też oglądać i sprawdzać poprawność za pomocą adresu URL wersji zapoznawczej podczas sesji na żywo.
14. Zarejestruj identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używaną z kodera na żywo.
15. Zresetuj stan zdarzenia na żywo po utworzeniu każdej próbki.
16. Powtórz kroki od 5 do 15 dla wszystkich konfiguracji obsługiwanych przez koder (z sygnalizacją reklamową, podpisami lub różnymi szybkościami kodowania i bez niej).

## <a name="live-encoding-live-event-verification"></a>Weryfikacja zdarzenia na żywo kodowania na żywo

1. Na koncie usługi Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiony. 
2. Utwórz i rozpocznij wydarzenie na żywo **kodowania na** żywo. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder do wypychania strumienia na żywo o pojedynczej szybkości transmisji bitów do usługi Media Services.
4. Pobierz adres URL wersji zapoznawczej i użyj go, aby sprawdzić, czy dane wejściowe z kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobu.**
6. Utwórz **dane wyjściowe na żywo** i użyj utworzonej nazwy zasobu.
7. Utwórz **lokalizator przesyłania strumieniowego** z wbudowanymi typami **zasad przesyłania strumieniowego.**
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego,** aby przywrócić adresy URL do użycia.
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego,** z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo.
13. Użyj odtwarzacza, takiego jak [Azure Media Player,](https://aka.ms/azuremediaplayer) aby obejrzeć zarchiwizowanego zasobu, aby upewnić się, że odtwarzanie nie ma widocznych usterek dla wszystkich poziomów jakości. Możesz też oglądać i sprawdzać poprawność za pomocą adresu URL wersji zapoznawczej podczas sesji na żywo.
14. Zarejestruj identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używaną z kodera na żywo.
15. Zresetuj stan zdarzenia na żywo po utworzeniu każdej próbki.
16. Powtórz kroki od 5 do 15 dla wszystkich konfiguracji obsługiwanych przez koder (z sygnalizacją reklamową, podpisami lub różnymi szybkościami kodowania i bez niej).

## <a name="longevity-verification"></a>Weryfikacja długowieczności

Wykonaj te same kroki, co w [weryfikacji zdarzenia na żywo przekazywane,](#pass-through-live-event-verification) z wyjątkiem kroku 11. <br/>Zamiast 10 minut, uruchom koder na żywo przez tydzień lub dłużej. Użyj odtwarzacza, takiego jak [Azure Media Player,](https://aka.ms/azuremediaplayer) aby od czasu do czasu oglądać transmisje na żywo (lub zarchiwizowane zasoby), aby upewnić się, że odtwarzanie nie ma widocznych usterek.

## <a name="email-your-recorded-settings"></a>Wyślij wiadomość e-mail z zarejestrowanymi ustawieniami

Na koniec wyślij wiadomość e-mail do nagranych amshelp@microsoft.com ustawień i parametrów archiwum na żywo do usługi Azure Media Services w celu powiadomienia, że wszystkie kontrole samoweryfikacji zostały przekazane. Należy również podać swoje dane kontaktowe w przypadku jakichkolwiek działań następczych. Możesz skontaktować się z zespołem usługi Azure Media Services w przypadku jakichkolwiek pytań dotyczących tego procesu.

## <a name="see-also"></a>Zobacz też

[Przetestowane kodery lokalne](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe na żywo za pomocą usługi Media Services w wersji 3](live-streaming-overview.md)
