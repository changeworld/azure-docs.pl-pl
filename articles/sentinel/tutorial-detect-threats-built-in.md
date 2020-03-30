---
title: Badanie alertów za pomocą usługi Azure Sentinel| Dokumenty firmy Microsoft
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zbadać alerty za pomocą usługi Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585207"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Samouczek: Wykrywanie zagrożeń gotowych do użycia


> [!IMPORTANT]
> Wykrywanie zagrożeń out-of-the-box jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [połączeniu źródeł danych z usługą](quickstart-onboard.md) Azure Sentinel użytkownik ma otrzymywać powiadomienia o podejrzanych zdarzeniach. Aby umożliwić ci to zrobić, usługa Azure Sentinel udostępnia gotowe wbudowane szablony. Szablony te zostały zaprojektowane przez zespół ekspertów i analityków zabezpieczeń firmy Microsoft na podstawie znanych zagrożeń, typowych wektorów ataków i łańcuchów eskalacji podejrzanych działań. Po włączeniu tych szablonów będą automatycznie wyszukiwać wszystkie działania, które wyglądają podejrzanie w danym środowisku. Wiele szablonów można dostosować do wyszukiwania lub odfiltrowywać działań zgodnie z potrzebami. Alerty generowane przez te szablony utworzą zdarzenia, które można przypisać i zbadać w swoim środowisku.

Ten samouczek ułatwia wykrywanie zagrożeń za pomocą usługi Azure Sentinel:

> [!div class="checklist"]
> * Używanie gotowych wykrywania
> * Automatyzacja reakcji na zagrożenia

## <a name="about-out-of-the-box-detections"></a>Informacje o gotowych funkcjach wykrywania

Aby wyświetlić wszystkie out-of-the-box wykrywania, przejdź do **Analytics,** a następnie **reguły szablonów**. Ta karta zawiera wszystkie wbudowane reguły usługi Azure Sentinel.

   ![Używanie wbudowanych funkcji wykrywania w celu znajdowania zagrożeń za pomocą usługi Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Dostępne są następujące typy szablonów:

- **Zabezpieczenia firmy Microsoft** — szablony zabezpieczeń firmy Microsoft automatycznie tworzą zdarzenia wartownicze platformy Azure na podstawie alertów generowanych w innych rozwiązaniach zabezpieczeń firmy Microsoft w czasie rzeczywistym. Reguły zabezpieczeń firmy Microsoft można używać jako szablonu do tworzenia nowych reguł z podobną logiką. Aby uzyskać więcej informacji na temat reguł zabezpieczeń, zobacz [Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft](create-incidents-from-alerts.md).
- **Fusion** — w oparciu o technologię Fusion zaawansowane wieloetapowe wykrywanie ataków w usłudze Azure Sentinel wykorzystuje skalowalne algorytmy uczenia maszynowego, które mogą korelować wiele alertów i zdarzeń o niskiej wierności w wielu produktach w zdarzenia o wysokiej wierności i zdarzeniach. Fuzja jest domyślnie włączona. Ponieważ logika jest ukryta, nie można użyć tego jako szablonu do utworzenia więcej niż jednej reguły.
- **Analiza behawioralna uczenia maszynowego** — te szablony są oparte na zastrzeżonych algorytmach uczenia maszynowego firmy Microsoft, więc nie można zobaczyć wewnętrznej logiki ich działania i ich uruchomienia. Ponieważ logika jest ukryta, nie można użyć tego jako szablonu do utworzenia więcej niż jednej reguły.
-   **Zaplanowane** — zaplanowane reguły analityczne to zaplanowane zapytania napisane przez ekspertów firmy Microsoft w dziedzinie zabezpieczeń. Można wyświetlić logikę kwerendy i wprowadzić zmiany do niego. Reguły zaplanowane można używać jako szablonu do tworzenia nowych reguł z podobną logiką.

## <a name="use-out-of-the-box-detections"></a>Używanie gotowych wykrywania

1. Aby użyć wbudowanego szablonu, kliknij pozycję **Utwórz regułę,** aby utworzyć nową aktywną regułę opartą na tym szablonie. Każdy wpis ma listę wymaganych źródeł danych, które są automatycznie sprawdzane, co może spowodować wyłączenie **reguły Create.**
  
   ![Używanie wbudowanych funkcji wykrywania w celu znajdowania zagrożeń za pomocą usługi Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Spowoduje to otwarcie kreatora tworzenia reguł na podstawie wybranego szablonu. Wszystkie szczegóły są wypełniane automatycznie, a w przypadku **reguł zaplanowanych** lub **reguł zabezpieczeń firmy Microsoft**można dostosować logikę do potrzeb organizacji lub utworzyć dodatkowe reguły na podstawie wbudowanego szablonu. Po wykonać kroki w kreatorze tworzenia reguł i zakończeniu tworzenia reguły na podstawie szablonu, nowa reguła pojawia się na karcie **Aktywne reguły.**

Aby uzyskać więcej informacji na temat pól [kreatora, zobacz Samouczek: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak rozpocząć wykrywanie zagrożeń przy użyciu usługi Azure Sentinel. 

Aby dowiedzieć się, jak zautomatyzować reagowanie na zagrożenia, [skonfiguruj automatyczne odpowiedzi na zagrożenia w usłudze Azure Sentinel.](tutorial-respond-threats-playbook.md)

