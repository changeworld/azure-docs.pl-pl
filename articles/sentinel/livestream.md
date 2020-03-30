---
title: Użyj polowania Livestream w usłudze Azure Sentinel do wykrywania zagrożeń| Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać polowania livestream w usłudze Azure Sentinel do śledzenia danych.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582130"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Używanie polowania na żywo w usłudze Azure Sentinel do wykrywania zagrożeń

> [!IMPORTANT]
> Polowanie na żywo w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej i stopniowo wdrażane dla dzierżawców.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Polowanie livestream do tworzenia interaktywnych sesji, które umożliwiają testowanie nowo utworzonych zapytań w miarę występowania zdarzeń, otrzymywać powiadomienia z sesji po znalezieniu dopasowania i w razie potrzeby rozpocząć badania. Można szybko utworzyć sesję transmisji na żywo przy użyciu dowolnej kwerendy usługi Log Analytics.

- **Testowanie nowo utworzonych zapytań w miarę występowania zdarzeń**
    
    Można testować i dostosowywać zapytania bez żadnych konfliktów do bieżących reguł, które są aktywnie stosowane do zdarzeń. Po potwierdzeniu tych nowych kwerend działa zgodnie z oczekiwaniami, łatwo jest promować je do niestandardowych reguł alertów, wybierając opcję, która podnosi sesję do alertu.

- **Otrzymywali powiadomienia o wystąpieniu zagrożeń**
    
    Można porównać źródła danych o zagrożeniach z zagregowanymi danymi dziennika i otrzymywać powiadomienia o wystąpieniu dopasowania. Źródła danych o zagrożeniach są ciągłymi strumieniami danych związanych z potencjalnymi lub bieżącymi zagrożeniami, więc powiadomienie może wskazywać potencjalne zagrożenie dla twojej organizacji. Utwórz sesję transmisji na żywo zamiast niestandardowej reguły alertu, jeśli chcesz otrzymywać powiadomienia o potencjalnym problemie bez kosztów ogólnych związanych z utrzymaniem niestandardowej reguły alertu.

- **Wszczęcie dochodzeń**
    
    Jeśli istnieje aktywne dochodzenie, które obejmuje zasób, takich jak host lub użytkownik, można wyświetlić określone (lub dowolne) działanie w danych dziennika, jak występuje na tym zasobie. Możesz otrzymywać powiadomienia, gdy wystąpi to działanie.


## <a name="create-a-livestream-session"></a>Tworzenie sesji transmisji na żywo

Można utworzyć sesję strumienia na żywo z istniejącej kwerendy myśliwskiej lub utworzyć sesję od podstaw.

1. W witrynie Azure portal przejdź do pozycji Polowanie na**zarządzanie zagrożeniami** >  **wartownika** > .**Hunting**

2. Aby utworzyć sesję transmisji na żywo z kwerendy myśliwskiej:
    
    1. Na karcie **Kwerendy** znajdź kwerendę polującą do użycia.
    2. Kliknij prawym przyciskiem myszy kwerendę i wybierz polecenie **Dodaj do transmisji na żywo**. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![tworzenie sesji Livestream z kwerendy polowania usługi Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Aby utworzyć sesję transmisji na żywo od podstaw: 
    
    1. Wybierz kartę **Strumień na żywo**
    2. Wybierz **pozycję Przejdź do transmisji na żywo**.
    
4. W okienku **Strumień na żywo:**
    
    - Jeśli rozpoczęto transmisję na żywo z kwerendy, przejrzyj kwerendę i wykonuj zmiany, które chcesz wprowadzić.
    - Jeśli rozpoczęto transmisję na żywo od podstaw, utwórz zapytanie. 

5. Z paska poleceń wybierz **polecenie Odtwórz.**
    
    Pasek stanu pod pasem poleceń wskazuje, czy sesja transmisji na żywo jest uruchomiona, czy wstrzymana. W poniższym przykładzie sesja jest uruchomiona:
    
    > [!div class="mx-imgBorder"]
    > ![tworzenie sesji transmisji na żywo z polowania usługi Azure Sentinel](./media/livestream/livestream-session.png)

6. Wybierz **pozycję Zapisz** z paska poleceń.
    
    Jeśli nie **wybierzesz wstrzymaj,** sesja będzie nadal działać do momentu wylogowywania się z witryny Azure portal.

## <a name="view-your-livestream-sessions"></a>Wyświetlanie sesji transmisji na żywo

1. W witrynie Azure portal przejdź do karty Polowanie**na transmisję na żywo** z**zarządzania zagrożeniami** >  **wartownika.** > **Hunting** > 

2. Wybierz sesję transmisji na żywo, którą chcesz wyświetlić lub edytować. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![tworzenie sesji livestream z kwerendy polowania usługi Azure Sentinel](./media/livestream/livestream-tab.png)
    
    Wybrana sesja transmisji na żywo zostanie otwarta do odtwarzania, wstrzymywania, edytowania itd.

## <a name="receive-notifications-when-new-events-occur"></a>Otrzymywanie powiadomień o wystąpieniu nowych zdarzeń

Ponieważ powiadomienia na żywo dla nowych zdarzeń używają powiadomień portalu Azure, te powiadomienia są wyświetlane przy każdym użyciu witryny Azure portal. Przykład:

![Powiadomienie portalu platformy Azure dla transmisji na żywo](./media/livestream/notification.png)

Wybierz powiadomienie, aby otworzyć okienko **Strumień na żywo.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Podniesienie poziomu sesji transmisji na żywo do alertu

Można podwyższyć poziom sesji transmisji na żywo do nowego **alertu, wybierając pozycję Podnieś, aby alertować** z paska poleceń w odpowiedniej sesji transmisji na żywo:

> [!div class="mx-imgBorder"]
> ![Podniesienie poziomu sesji transmisji na żywo do alertu](./media/livestream/elevate-to-alert.png)

Ta akcja otwiera kreatora tworzenia reguł, który jest wstępnie wypełniony kwerendą skojarzoną z sesją strumienia na żywo.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać polowania na żywo w usłudze Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:

- [Proaktywne polowanie na zagrożenia](hunting.md)
- [Uruchamianie automatycznych kampanii łowieckich za pomocą notesów](notebooks.md)
