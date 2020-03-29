---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694193"
---
## <a name="lifecycle"></a>Cykl życia

Na karcie **Cykl życia** można określić, kiedy wygaśnie przypisanie użytkownika do pakietu dostępu. Można również określić, czy użytkownicy mogą rozszerzać swoje przypisania.

1. W sekcji **Wygasanie** ustaw **przypisania pakietów programu Access wygaśnie** **na Data,** **Liczba dni**lub **Nigdy.**

    W **polu Data**w dniu wybierz datę wygaśnięcia w przyszłości.

    W przypadku **liczby dni**określ liczbę z 0 do 3660 dni.

    Na podstawie wyboru przypisanie użytkownika do pakietu dostępu wygasa w określonym dniu, określoną liczbę dni po ich zatwierdzeniu lub nigdy.

1. Kliknij **pozycję Pokaż zaawansowane ustawienia wygaśnięcia,** aby wyświetlić dodatkowe ustawienia.

    ![Pakiet dostępu — ustawienia wygaśnięcia cyklu życia](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Aby zezwolić użytkownikowi na rozszerzanie ich przypisań, ustaw **opcję Zezwalaj użytkownikom na rozszerzanie dostępu** do **Tak**.

    Jeśli rozszerzenia są dozwolone w zasadach, użytkownik otrzyma wiadomość e-mail 14 dni, a także 1 dzień przed ich przypisanie pakietu dostępu jest ustawiony na wygaśnięcie monitując ich o przedłużenie przypisania. Jeśli użytkownik przesyła żądanie rozszerzenia dostępu, data rozszerzenia musi być na lub przed wygaśnięciem przydziałów zgodnie z definicją w zasadach, które zostały użyte do przyznania użytkownikowi dostępu do pakietu dostępu. Jeśli na przykład zasada wskazuje, że przydziały mają wygasnąć 30 czerwca, maksymalnym rozszerzeniem, o które użytkownik może zażądać, jest 30 czerwca.

    Jeśli dostęp użytkownika zostanie rozszerzony, nie będzie mógł zażądać pakietu dostępu po określonej dacie rozszerzenia (data ustawiona w strefie czasowej użytkownika, który utworzył zasady).

1. Aby wymagać zatwierdzenia w celu udzielenia przedłużenia, ustaw **opcję Wymagaj zatwierdzenia, aby udzielić rozszerzenia** na **Tak**.

    Zostaną użyte te same ustawienia zatwierdzania, które zostały określone na karcie **Żądania.**

1. Kliknij **przycisk Dalej** lub **Zaktualizuj**.
