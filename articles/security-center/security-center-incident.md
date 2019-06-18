---
title: Obsługa alertów zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument wyjaśnia, jak korzystać z funkcji usługi Azure Security Center do obsługi zdarzeń naruszenia zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 68bcd2b1916ccdf68eaa31ed251661a6b7e1bca0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704121"
---
# <a name="handling-security-incidents-in-azure-security-center"></a>Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center
Klasyfikowanie i badanie alertów zabezpieczeń może być czasochłonne nawet dla najbardziej doświadczonego analityka bezpieczeństwa, a wielu nie wie nawet od czego zacząć. Używając [analizy](security-center-detection-capabilities.md) w celu powiązania informacji z różnych [alertów zabezpieczeń](security-center-managing-and-responding-alerts.md), usługa Security Center może dostarczyć pojedynczego widoku kampanii ataku i wszystkich powiązanych alertów — dzięki temu można szybko dowiedzieć się, jakie akcje zostały podjęte przez osobę atakującą i na jakie zasoby miały wpływ.

W tym dokumencie omówiono sposób użycia funkcji alertu zabezpieczeń w usłudze Security Center, aby ułatwić obsługę zdarzeń naruszenia zabezpieczeń.

## <a name="what-is-a-security-incident"></a>Co to jest zdarzenie naruszenia zabezpieczeń?
W usłudze Security Center zdarzenie naruszenia zabezpieczeń to agregacja wszystkich alertów zasobu, które są zgodne ze wzorcami [kill chain](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/), tzw. „zabójczego łańcucha”. Zdarzenia są wyświetlane na kafelku i w bloku [Alerty zabezpieczeń](security-center-managing-and-responding-alerts.md). Zdarzenie wyświetli listę powiązanych alertów, co pozwala uzyskać więcej informacji na temat każdego wystąpienia.

## <a name="managing-security-incidents"></a>Zarządzanie zdarzeniami naruszenia zabezpieczeń
Bieżące zdarzenia naruszenia zabezpieczeń można przeglądać przy użyciu kafelka alertów zabezpieczeń. Otwórz witrynę Azure Portal i wykonaj poniższe kroki, aby wyświetlić więcej szczegółowych informacji dotyczących poszczególnych zdarzeń naruszenia zabezpieczeń:

1. Na pulpicie nawigacyjnym Centrum zabezpieczeń widoczny jest kafelek **Alerty zabezpieczeń**.

    ![Kafelek Alerty zabezpieczeń w usłudze Security Center](./media/security-center-incident/security-center-incident-fig1.png)

2. Kliknij kafelek, aby go rozwinąć. Jeśli zostanie wykryte zdarzenie naruszenia zabezpieczeń, pojawi się ono pod wykresem alertów zabezpieczeń, jak pokazano poniżej:

    ![Zdarzenie naruszenia zabezpieczeń](./media/security-center-incident/security-center-incident-fig2.png)

3. Należy zwrócić uwagę, że ikona opisu zdarzenia naruszenia zabezpieczeń różni się od ikon innych alertów. Kliknij tę ikonę, aby wyświetlić więcej informacji na temat tego zdarzenia.

    ![Zdarzenie naruszenia zabezpieczeń](./media/security-center-incident/security-center-incident-fig3.png)

4. W bloku **zdarzeń** pojawi się więcej szczegółów dotyczących danego zdarzenia, w tym: jego pełny opis, ważność (w tym przypadku jest wysoka), obecny stan (w tym przypadku nadal jest *aktywny*, co oznacza, że użytkownik nie podjął przy tym działań — można to zrobić, klikając prawym przyciskiem myszy zdarzenie w bloku **Alerty zabezpieczeń**). Znajdują się tutaj też informacje na temat zaatakowanego zasobu (w tym przypadku jest to *VM1*), kroki naprawcze dla tego zdarzenia, a w dolnym okienku są wyświetlone alerty, które wchodzą w skład tego zdarzenia. Jeśli chcesz uzyskać więcej informacji na temat każdego alertu, wystarczy kliknąć go, a otworzy się inny blok, jak pokazano poniżej:

    ![Zdarzenie naruszenia zabezpieczeń](./media/security-center-incident/security-center-incident-fig4.png)

Informacje w tym bloku będą się różnić w zależności od alertu. Więcej informacji na temat zarządzania alertami zabezpieczeń znajduje się w artykule [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md). Pewne istotne kwestie dotyczące tej funkcji:

* Nowy filtr pozwala na dostosowanie widoku tak, aby wyświetlane były tylko zdarzenia, tylko alerty albo zdarzenia i alerty jednocześnie.
* Ten sam alert może istnieć w ramach zdarzenia (jeśli go dotyczy), ale może też być wyświetlany jako samodzielny alert.

## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób użycia funkcji zdarzeń naruszenia zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
