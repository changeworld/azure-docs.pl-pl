---
title: Zarządzanie ustawieniami modelu i uczenia się - Personalizer
description: Model i ustawienia uczenia się nauczane maszynowo można wyeksportować w celu tworzenia kopii zapasowej we własnym systemie kontroli źródła.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624298"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Jak zarządzać ustawieniami modelu i uczenia się

Model i ustawienia uczenia się nauczane maszynowo można wyeksportować w celu tworzenia kopii zapasowej we własnym systemie kontroli źródła.

## <a name="export-the-personalizer-model"></a>Eksportowanie modelu Personalizer

W sekcji Zarządzanie zasobami dla **ustawień modelu i uczenia się**przejrzyj tworzenie modelu i datę ostatniej aktualizacji i wyeksportuj bieżący model. Za pomocą witryny Azure portal lub interfejsów API personalizatora można wyeksportować plik modelu do celów archiwalnych.

![Eksportowanie bieżącego modelu Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Wyczyść dane dla pętli uczenia się

1. W witrynie Azure portal dla zasobu Personalizer na stronie **Ustawienia modelu i uczenia się** wybierz pozycję **Wyczyść dane**.
1. Aby wyczyścić wszystkie dane i zresetować pętlę uczenia do stanu pierwotnego, zaznacz wszystkie 3 pola wyboru.

    ![W witrynie Azure portal wyczyść dane z zasobu Personalizer.](./media/settings/clear-data-from-personalizer-resource.png)

    |Wartość|Przeznaczenie|
    |--|--|
    |Zarejestrowane dane personalizacji i nagrody.|Te dane rejestrowania są używane w ocenach offline. Wyczyść dane, jeśli resetujesz zasób.|
    |Zresetuj model personalizatora.|Ten model zmienia się przy każdym przekwalifikowaniu. Ta częstotliwość szkolenia jest określona w **częstotliwości modelu przekazywania** na stronie **Konfiguracja.** |
    |Ustaw domyślną zasadę uczenia się.|Jeśli zasady uczenia się zostały zmienione w ramach oceny w trybie offline, spowoduje to zresetowanie do pierwotnej zasady uczenia się.|

1. Wybierz **wyczyść wybrane dane,** aby rozpocząć proces rozliczania. Stan jest zgłaszany w powiadomieniach platformy Azure w prawym górnym rogu nawigacji.

## <a name="import-a-new-learning-policy"></a>Importowanie nowych zasad uczenia się

Ustawienia [zasad uczenia się](concept-active-learning.md#understand-learning-policy-settings) określają _hiperparametry_ szkolenia modelu. Wykonaj [ocenę w trybie offline,](how-to-offline-evaluation.md) aby znaleźć nową zasadę uczenia się.

1. Otwórz [portal Azure](https://portal.azure.com)i wybierz zasób Personalizer.
1. Wybierz **pozycję Ustawienia modelu i uczenia się** w sekcji Zarządzanie **zasobami.**
1. W przypadku **ustawień uczenia importowania** wybierz plik utworzony w formacie JSON określonym powyżej, a następnie wybierz przycisk **Przekaż.**

    Poczekaj na powiadomienie, że zasady uczenia się zostały pomyślnie przekazane.

## <a name="export-a-learning-policy"></a>Eksportowanie zasad uczenia się

1. Otwórz [portal Azure](https://portal.azure.com)i wybierz zasób Personalizer.
1. Wybierz **pozycję Ustawienia modelu i uczenia się** w sekcji Zarządzanie **zasobami.**
1. W przypadku **ustawień uczenia importu** wybierz przycisk **Eksportuj ustawienia nauki.** Spowoduje to `json` zapisanie pliku na komputerze lokalnym.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zarządzać zasadami uczenia się](how-to-manage-model.md)
