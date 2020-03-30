---
title: Co to jest wersja testowa? | Azure Marketplace
description: Wyjaśnienie funkcji Platformy Testowej w Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bdfadf359195464c4024b28c5e597c571305481a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278233"
---
<a name="what-is-test-drive"></a>Co to jest wersja testowa?
===================

Test Drive to świetny sposób, aby zaprezentować swoją ofertę \'potencjalnym\'klientom, dając im możliwość wypróbowania przed zakupem, co skutkuje zwiększoną konwersją i generowaniem wysoko wykwalifikowanych potencjalnych klientów.

Po podaniu informacji kontaktowych klienci mogą uzyskać dostęp do wstępnie utworzonego środowiska z\'jazdy testowej: praktycznej, samodzielnej wersji próbnej kluczowych funkcji produktu i korzyści zademonstrowanych w rzeczywistym scenariuszu implementacji.

Test Drive pozwala ożywić swój produkt i generować wysoko wykwalifikowanych potencjalnych klientów w procesie.

<a name="how-does-a-test-drive-work"></a>Jak działa dysk testowy?
---------------------------

Potencjalny klient odnajduje twoją aplikację w Marketplace, loguje się i zgadza się na warunki użytkowania. W tym momencie klient otrzymuje wstępnie skonfigurowane środowisko, aby spróbować przez określoną liczbę godzin, podczas gdy otrzymasz wysoko wykwalifikowany lead do podjęcia działań następczych.

![Krok pierwszy. Wyświetlana oferta marketplace](./media/what-is-test-drive/step1.png)

![Krok drugi. Ekran logowania do oferty w marketplace](./media/what-is-test-drive/step1andahalf.png)

![Krok trzeci. Ekran umowy wydawcy oferty w marketplace](./media/what-is-test-drive/step2.png)

![Krok czwarty. Ekran konfiguracji dysku testowego](./media/what-is-test-drive/step3.png)

A poniżej znajduje się przykład jak oferta wygląda, gdy potrzebuje czasu na wdrożenie:

![Wdrażana oferta Marketplace](./media/what-is-test-drive/step4.png)

![Ekran gotowości do testowania w portalu Marketplace](./media/what-is-test-drive/step5.png)

![Pełny ekran z dysku testowego w marketplace](./media/what-is-test-drive/step6.png)

Bez względu na to, jak złożona jest aplikacja, dysk testowy microsoft pomaga w ożywiać produkt dla klienta. Obecnie oferujemy trzy różne typy dysków testowych w zależności od typu produktu, scenariusza i rynku, na który się znajdujesz.

- **[Usługa Azure Resource Manager:](./azure-resource-manager-test-drive.md)** Dysk testowy usługi Azure Resource Manager to szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, które składają się na rozwiązanie tworzone przez wydawcę. Produkty, które pasują do tego scenariusza są te, które używają tylko zasobów platformy Azure.
- **[Aplikacja logiki:](./logic-app-test-drive.md)** Logic App Test Drive to szablon wdrożenia, który ma obejmować wszystkie architektury złożonych rozwiązań. Wszystkie aplikacje Dynamics lub produkty niestandardowe powinny używać tego typu dysku testowego.
- **Power BI:** Dysk testowy usługi Power BI to po prostu osadzone łącze do niestandardowego pulpitu nawigacyjnego. Każdy produkt, który chce po prostu zademonstrować interaktywną wizualizację usługi Power BI, powinien używać tego typu dysku testowego.
    Wszystko, co musisz przesłać tutaj, to osadzony adres URL usługi Power BI.

<a name="what-goes-on-in-the-background"></a>Co dzieje się w tle?
-------------------------------

Usługa Test Drive jest stworzona z myślą o ciągłej obsłudze i obsłudze klientów bez konieczności ręcznego wysiłku użytkownika. Twoim zadaniem jako wydawcy jest zarządzanie ustawieniami dysku testowego i konfigurowanie ich w portalu partnerówCloud, a następnie to ustawienie będzie bezpośrednio dostępne dla klientów.

Dzieje się tak, ponieważ po ustawieniu konfiguracji dla dysku testowego każda jazda testowa staje się wystąpieniem zarządzanym, które zostanie wdrożone na żądanie dla klienta, który go zażąda. Po przypisaniu wystąpienia dysku testowego, testowa jest dostępna do użycia przez określony czas, a następnie jest usuwana, aby utworzyć miejsce dla innego klienta.

<a name="next-steps"></a>Następne kroki
----------

Teraz, gdy wiesz, o co chodzi w testowej, przejdź do określonego typu dysku testowego, który chcesz opublikować, aby dowiedzieć się wszystkiego o wymaganych polach.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[Aplikacja logika](./logic-app-test-drive.md)**

Jeśli masz więcej pytań, szukasz porad dotyczących rozwiązywania problemów lub chcesz zwiększyć sukces na dysku testowym, przejdź do [faq, rozwiązywania problemów & najlepszych praktyk.](./marketing-and-best-practices.md)
