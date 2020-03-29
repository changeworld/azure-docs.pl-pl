---
title: Konfigurowanie usługi Personalizacja
description: Konfiguracja usługi obejmuje sposób, w jaki usługa traktuje nagrody, jak często usługa eksploruje, jak często model jest przeszkolony i ile danych jest przechowywanych.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219358"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurowanie pętli uczenia personalizatora

Konfiguracja usługi obejmuje sposób, w jaki usługa traktuje nagrody, jak często usługa eksploruje, jak często model jest przeszkolony i ile danych jest przechowywanych.

Skonfiguruj pętlę uczenia się na stronie **Konfiguracja** w witrynie Azure portal dla tego zasobu Personalizer.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Konfigurowanie nagród dla pętli sprzężenia zwrotnego

Skonfiguruj usługę do korzystania z nagród w pętli edukacyjnej. Zmiany w następujących wartościach spowoduje zresetowanie bieżącego modelu Personalizer i przekwalifikowanie go z ostatnich 2 dni danych.

> [!div class="mx-imgBorder"]
> ![Konfigurowanie wartości nagród dla pętli sprzężenia zwrotnego](media/settings/configure-model-reward-settings.png)

|Wartość|Przeznaczenie|
|--|--|
|Czas oczekiwania na nagrody|Określa czas, w którym Personalizator będzie zbierał wartości nagród dla połączenia rangi, począwszy od momentu wywołania rangi. Ta wartość jest ustawiona przez pytanie: "Jak długo personalizator powinien czekać na nagrody połączeń?" Każda nagroda przynasiąca się po tym oknie zostanie zarejestrowana, ale nie zostanie wykorzystana do nauki.|
|Nagroda domyślna|Jeśli personalizator nie otrzyma żadnego zaproszenia do nagrody w oknie czas oczekiwania na nagrody powiązanym z wezwaniem rangi, Personalizator przypisze nagrodę domyślną. Domyślnie, a w większości scenariuszy nagroda domyślna wynosi zero (0).|
|Agregacja nagród|Jeśli dla tego samego wywołania interfejsu API rangi zostanie odebranych wiele nagród, używana jest ta metoda agregacji: **suma** lub **najwcześniej .** Najwcześniej wybiera najwcześniej otrzymany wynik, a resztę odrzuca. Jest to przydatne, jeśli chcesz wyjątkową nagrodę wśród ewentualnie zduplikowanych połączeń. |

Po zmianie tych wartości należy wybrać opcję **Zapisz**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Skonfiguruj eksplorację, aby umożliwić pętlę uczenia się na dostosowanie

Personalizacja jest w stanie odkryć nowe wzorce i dostosować się do zmian zachowania użytkownika w czasie, eksplorując alternatywy zamiast przy użyciu przewidywania wyszkolonego modelu. Exploration **Exploration** Wartość określa, jaki procent wywołań Ranga są odbierane z eksploracji.

Zmiany tej wartości spowoduje zresetowanie bieżącego modelu Personalizer i przekwalifikowanie go z ostatnich 2 dni danych.

![Wartość eksploracji określa, jaki procent wywołań rangi jest odbierany za pomocą eksploracji](media/settings/configure-exploration-setting.png)

Po zmianie tej wartości należy wybrać opcję **Zapisz**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Konfigurowanie częstotliwości aktualizacji modelu do szkolenia modelu

**Częstotliwość aktualizacji modelu** określa, jak często model jest szkolony.

|Ustawienie częstotliwości|Przeznaczenie|
|--|--|
|1 min|Częstotliwości aktualizacji jednominutowej są przydatne podczas **debugowania** kodu aplikacji przy użyciu personalizatora, wykonywania wersji demonstracyjnych lub interaktywnego testowania aspektów uczenia maszynowego.|
|15 minut|Wysokie częstotliwości aktualizacji modelu są przydatne w sytuacjach, w których chcesz **dokładnie śledzić zmiany** w zachowaniach użytkowników. Przykłady obejmują witryny, które działają na żywo wiadomości, treści wirusowe lub ustalania stawek produktów na żywo. W tych scenariuszach można użyć częstotliwości 15-minutowej. |
|1 godzina|W większości przypadków użycia efektywna jest niższa częstotliwość aktualizacji.|

![Częstotliwość aktualizacji modelu ustawia, jak często nowy model Personalizer jest ponownie przeszkolony.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Po zmianie tej wartości należy wybrać opcję **Zapisz**.

## <a name="data-retention"></a>Przechowywanie danych

**Okres przechowywania danych** określa, ile dni Personalizer przechowuje dzienniki danych. Wcześniejsze dzienniki danych są wymagane do [przeprowadzania ocen offline,](concepts-offline-evaluation.md)które są używane do pomiaru skuteczności personalizatora i optymalizacji zasad uczenia się.

Po zmianie tej wartości należy wybrać opcję **Zapisz**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Ustawienia, które obejmują resetowanie modelu

Następujące akcje obejmują natychmiastowe przekwalifikowanie modelu z ostatnich 2 dni danych.

* Nagroda
* Eksploracja

Aby [wyczyścić](how-to-manage-model.md) wszystkie dane, użyj strony **Model i ustawienia uczenia się **.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zarządzać modelem](how-to-manage-model.md)
