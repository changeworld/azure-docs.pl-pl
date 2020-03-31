---
title: Zarządzanie grupami inteligentnymi
description: Zarządzanie grupami inteligentnymi utworzonymi za pomocą wystąpień alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: d7077e51282864f1208080838a1bb94ddd773b7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667469"
---
# <a name="manage-smart-groups"></a>Zarządzanie grupami inteligentnymi

[Grupy inteligentne](https://aka.ms/smart-groups) używają algorytmów uczenia maszynowego do grupowanie alertów na podstawie współupadłości lub podobieństwa, dzięki czemu użytkownik może teraz zarządzać grupami inteligentnymi zamiast zarządzać każdym alertem indywidualnie. W tym artykule opisano sposób uzyskiwania dostępu do grup inteligentnych w usłudze Azure Monitor i korzystania z nich.

1. Aby wyświetlić grupy inteligentne utworzone dla wystąpień alertów, możesz

     1. Kliknij **grupy inteligentne** na stronie **Podsumowanie alertów**    
    ![Monitorowanie](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Kliknij alerty grup inteligentnych na stronie Wszystkie alerty   
     ![Monitorowanie](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Spowoduje to przejście do widoku listy dla wszystkich grup inteligentnych utworzonych za pomocą wystąpień alertów. Zamiast przesiewanie przez wiele alertów, można teraz radzić sobie z grup inteligentnych zamiast.   
![Monitorowanie](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Kliknięcie dowolnej grupy inteligentnej powoduje otwarcie strony szczegółów, na której można zobaczyć przyczynę grupowania wraz z alertami członków. Ta agregacja umożliwia radzenie sobie z pojedynczą grupą inteligentną, zamiast przesiewać wiele alertów.   
![Monitorowanie](./media/alerts-managing-smart-groups/sg-details.jpg)
