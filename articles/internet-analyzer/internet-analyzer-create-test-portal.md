---
title: Tworzenie testu analizatora internetowego przy użyciu portalu | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak utworzyć pierwszy test analizatora internetowego.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501771"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Tworzenie testu analizatora internetowego przy użyciu portalu (wersja zapoznawcza)

Istnieją dwa sposoby tworzenia zasobu analizatora internetowego przy użyciu witryny Azure portal lub przy użyciu [interfejsu wiersza polecenia.](internet-analyzer-cli.md) Ta sekcja ułatwia tworzenie nowego zasobu analizatora internetu platformy Azure przy użyciu naszego środowiska portalu.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Publiczna wersja zapoznawcza jest dostępna do użycia globalnie; jednak przechowywanie danych jest ograniczona do *us West 2* podczas podglądu.

## <a name="basics"></a>Podstawy

1. Uzyskaj dostęp w wersji zapoznawczej analizatora internetowego, [Azure Internet Analyzer FAQ](internet-analyzer-faq.md)wykonując instrukcje **dotyczące uczestniczenia w wersji zapoznawczej?**
2. Ze strony głównej w [witrynie Azure portal](https://preview.portal.azure.com)kliknij pozycję **+ Utwórz zasób**. Analizator internetowy jest obecnie dostępny tylko w wersji zapoznawczej witryny Azure portal.
3. Na stronie **Nowy** wyszukaj hasło "Analizator internetowy" w polu *Wyszukaj w portalu Marketplace.*
4. Kliknij **pozycję Analizator internetowy (wersja zapoznawcza)**. Upewnij się, że wydawcą jest *firma Microsoft,* a kategorią jest *Sieć*.
5. Na stronie **Analizator internetowy (wersja zapoznawcza)** kliknij pozycję **Utwórz,** aby otworzyć stronę **Utwórz analizatora internetowego.**
6. Określ następujące ustawienia konfiguracji zasobu analizatora internetowego:

    * **Subskrypcja:** Subskrypcja platformy Azure do obsługi nowego zasobu analizatora internetowego. ***Użyj tego samego identyfikatora subskrypcji, który jest używany do żądania dostępu w wersji zapoznawczej.***
    * **Grupa zasobów:** Grupa zasobów platformy Azure, w których zostanie utworzony nowy zasób analizatora internetowego. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Nazwa:** Nazwa nowego profilu zasobów analizatora internetowego.
    * **Region:** Region publiczny platformy Azure, w których zostanie utworzony zasób. Podczas podglądu dostępny jest tylko *us West 2.*

7. Po zakończeniu określania ustawień profilu kliknij przycisk **Przejrzyj + utwórz**.

## <a name="configuration"></a>Konfigurowanie

Wykonanie podstawowych kroków jest warunkiem wstępnym skonfigurowania testu i osadzania klienta JavaScript. Po utworzeniu profilu przejdź do **ustawienia > konfiguracji,** aby skonfigurować pierwszy test.

1. Nadaj testowi nazwę w polu **Nazwa testu.**
2. Dodaj opis testu w polu **Opis.**
3. Kliknij **pozycję Konfiguruj punkt końcowy** — karta pojawi się od prawej strony. Wybierz typ punktu końcowego, który chcesz skonfigurować — pojedynczy region platformy Azure, wiele regionów platformy Azure lub niestandardowy punkt końcowy.

    >
    ***Wstępnie skonfigurowane punkty końcowe: pojedyncze i wiele kombinacji regionów platformy Azure***
    * Wybierz region lub zestaw regionów ze [wstępnie skonfigurowanej listy punktów końcowych platformy Azure](internet-analyzer-faq.md).
    * Następnie wybierz typ aplikacji lub architektury dostarczania zawartości, którą chcesz ocenić.
        * Region pojedynczej platformy Azure: przyspieszenie witryny[(drzwiami frontowymi platformy Azure),](https://azure.microsoft.com/services/frontdoor/)buforowanie zawartości statycznej[(usługa Azure CDN dla firmy Microsoft)](https://azure.microsoft.com/services/cdn/)lub brak.
        * Wiele regionów platformy Azure: przyspieszenie witryny[(drzwiami frontowymi platformy Azure),](https://azure.microsoft.com/services/frontdoor/)sterowanie DNS[(Usługa Azure Traffic Manager)](https://azure.microsoft.com/services/traffic-manager/)  

    ***Niestandardowe punkty końcowe***
    * Postępuj zgodnie z instrukcjami na stronie [Tworzenie niestandardowego punktu końcowego.](internet-analyzer-custom-endpoint.md)
    * Wklej lokalizację adresu URL HTTPS obrazu jedno pikselowego w portalu.
    >

4. Kliknij **przycisk Dodaj,** aby dodać punkt końcowy do testu.
5. Powtórz kroki 1-4, aby skonfigurować drugi punkt końcowy. Punkt końcowy B jest zawsze mierzona względem punktu końcowego A — podczas konfigurowania punktów końcowych należy wziąć pod uwagę, który punkt końcowy powinien być kontrolą testu.
6. Kliknij przycisk **Zapisz,** aby zapisać test. Po zapisaniu testu nie można już edytować punktów końcowych określonego testu.
7. Wybierz testy, które chcesz uruchomić, i kliknij przycisk **Rozpocznij test**. Spowoduje to zmianę ***stanu*** testu (testów) na ***Uruchomione***. Testy można rozpocząć w dowolnym momencie, ale klient JavaScript musi być osadzony, aby test mógł rozpocząć zbieranie pomiarów.
8. Dodaj więcej testów w dowolnym momencie. Należy zauważyć, że unikatowy klient JavaScript nie zostanie wygenerowany, dopóki nie zostanie utworzony jeden test.

## <a name="embed-client"></a>Osadź klienta

Aby rozpocząć dowolny test, klient JavaScript musi być osadzony w aplikacji sieci Web. Po skonfigurowaniu co najmniej jednego testu kliknij przycisk **Przejrzyj + utwórz**, przejdź do **ustawienia > konfiguracja**i skopiuj klienta JavaScript. Szczegółowe instrukcje można znaleźć na stronie [Osażony klient analizatora internetowego.](internet-analyzer-embed-client.md)  

## <a name="next-steps"></a>Następne kroki

* Przeczytaj często zadawane [pytania dotyczące analizatora internetowego](internet-analyzer-faq.md)
* Dowiedz się więcej o osadzaniu [klienta analizatora internetowego](internet-analyzer-embed-client.md) i tworzeniu [niestandardowego punktu końcowego](internet-analyzer-custom-endpoint.md).
