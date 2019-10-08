---
title: Badanie alertów za pomocą usługi Azure wskaźnikowego | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zbadać alerty za pomocą usługi Azure wskaźnikowej.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6d61b94a50716966ac0b3c75551ddd9d8e291965
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024460"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Samouczek: wykrywanie zagrożeń wbudowanych


> [!IMPORTANT]
> Wbudowane wykrywanie zagrożeń jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md)@no__t 1To na platformie Azure — chcesz otrzymywać powiadomienia o wystąpieniu podejrzanych sytuacji. Aby można było to zrobić, wskaźnik na platformie Azure udostępnia gotowy do użycia wbudowane szablony. Te szablony zostały zaprojektowane przez zespół ekspertów ds. zabezpieczeń i analityków firmy Microsoft na podstawie znanych zagrożeń, typowych wektorów ataków oraz podejrzanych łańcuchów eskalacji działań. Po włączeniu tych szablonów zostaną automatycznie wyszukane wszystkie działania, które wyglądają podejrzanie w danym środowisku. Wiele szablonów można dostosować w taki sposób, aby wyszukiwać lub odfiltrować działania, zgodnie z potrzebami. Alerty wygenerowane przez te szablony spowodują utworzenie zdarzeń, które można przypisać i zbadać w danym środowisku.

Ten samouczek ułatwia wykrywanie zagrożeń przy użyciu platformy Azure — wskaźnik:

> [!div class="checklist"]
> * Korzystanie z wbudowanych wykryć
> * Automatyzowanie odpowiedzi na zagrożenia

## <a name="about-out-of-the-box-detections"></a>Informacje o wykryciuch wbudowanych

Aby wyświetlić wszystkie gotowe do użycia wykrycia, przejdź do pozycji **Analiza** , a następnie pozycję **Szablony reguł**. Ta karta zawiera wszystkie wbudowane reguły kontrolki Azure — wskaźnik.

   ![Używanie wbudowanych wykryć do znajdowania zagrożeń przy użyciu platformy Azure — wskaźnik](media/tutorial-detect-built-in/view-oob-detections.png)

Dostępne są następujące typy szablonów:

- **Zabezpieczenia firmy Microsoft** — szablony zabezpieczeń firmy Microsoft automatycznie tworzą zdarzenia wskaźnikowe platformy Azure na podstawie alertów wygenerowanych w innych rozwiązaniach zabezpieczeń firmy Microsoft w czasie rzeczywistym. Możesz użyć reguł zabezpieczeń firmy Microsoft jako szablonu, aby utworzyć nowe reguły z podobną logiką. Aby uzyskać więcej informacji na temat reguł zabezpieczeń, zobacz [Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft](create-incidents-from-alerts.md).
- Oparta na **fuzji** technologia syntezy, zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure wskaźnikowo używa skalowalnych algorytmów uczenia maszynowego, które mogą skorelować wiele alertów o niskiej wierności i zdarzeń w wielu produktach w celu zapewnienia wysokiej wierności i podejmowania akcji zdarzenia. Funkcja Fusion jest domyślnie włączona. Ponieważ logika jest ukryta, nie można jej użyć jako szablonu do utworzenia więcej niż jednej reguły.
- **Analiza zachowań uczenia maszynowego** — te szablony są oparte na własnościowe algorytmy uczenia maszynowego firmy Microsoft, dlatego nie można zobaczyć wewnętrznej logiki działania i uruchomienia. Ponieważ logika jest ukryta, nie można jej użyć jako szablonu do utworzenia więcej niż jednej reguły.
-   **Zaplanowane** — zaplanowane kwerendy są planowane przez ekspertów zabezpieczeń firmy Microsoft. Można wyświetlić logikę zapytań i wprowadzić w niej zmiany. Możesz użyć reguł zaplanowanych jako szablonu, aby utworzyć nowe reguły z podobną logiką.

## <a name="use-out-of-the-box-detections"></a>Korzystanie z wbudowanych wykryć

1. Aby użyć wbudowanego szablonu, kliknij pozycję **Utwórz regułę** , aby utworzyć nową aktywną regułę opartą na tym szablonie. Każdy wpis ma listę wymaganych źródeł danych, które są automatycznie sprawdzane, co może spowodować wyłączenie **reguły** .
  
   ![Używanie wbudowanych wykryć do znajdowania zagrożeń przy użyciu platformy Azure — wskaźnik](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Spowoduje to otwarcie Kreatora tworzenia reguły na podstawie wybranego szablonu. Wszystkie szczegóły są wypełniane, a dla **reguł zaplanowanych** lub **reguł zabezpieczeń firmy Microsoft**można dostosować logikę w celu lepszego dostosowania do organizacji lub utworzyć dodatkowe reguły na podstawie wbudowanego szablonu. Po wykonaniu kroków opisanych w Kreatorze tworzenia reguł i zakończeniu tworzenia reguły na podstawie szablonu Nowa reguła zostanie wyświetlona na karcie **aktywne reguły** .

Aby uzyskać więcej informacji na temat pól w kreatorze, zobacz [Samouczek: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure. 

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [Skonfiguruj automatyczne reagowanie na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).

