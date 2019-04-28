---
title: Element playbook zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia korzystanie z elementów playbook zabezpieczeń w usłudze Azure Security Center w celu automatyzowania odpowiedzi na zdarzenia związane z zabezpieczeniami.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ec16e6daec099adbede625c5ec6fe6909059143b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60907101"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Element playbook zabezpieczeń w usłudze Azure Security Center (wersja zapoznawcza)
Ten dokument ułatwia korzystanie z elementów playbook zabezpieczeń w usłudze Azure Security Center w celu reagowania na zdarzenia związane z zabezpieczeniami.

## <a name="what-is-security-playbook-in-security-center"></a>Co to jest element playbook zabezpieczeń w usłudze Security Center?
Element playbook zabezpieczeń to kolekcja procedur, które mogą być wykonywane w usłudze Security Center po wyzwoleniu określonego elementu playbook z poziomu wybranego alertu. Element playbook zabezpieczeń ułatwia automatyzowanie i organizowanie odpowiedzi na określony alert zabezpieczeń wykryty przez usługę Security Center. Elementy playbook zabezpieczeń w Security Center opierają się na usłudze [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), co oznacza, że możesz korzystać z szablonów należących do kategorii zabezpieczeń w szablonach usługi Logic Apps, możesz modyfikować je na podstawie Twoich potrzeb lub możesz tworzyć nowe elementy playbook, korzystając z [przepływu pracy usługi Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) i usługi Security Center jako wyzwalacza.

> [!NOTE]
> Element playbook korzysta z usługi Azure Logic Apps, dlatego naliczane są opłaty. Odwiedź stronę cennika usługi [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), aby zapoznać się ze szczegółami.

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Jak utworzyć element playbook zabezpieczeń w usłudze Security Center?
Wykonaj poniższe kroki, aby utworzyć nowy element playbook zabezpieczeń w usłudze Security Center:

1.  Otwórz pulpit nawigacyjny usługi **Security Center**.
2.  W obszarze **automatyzacji i aranżacji** w lewym okienku kliknij pozycję **Elementy playbook (wersja zapoznawcza)**.

    ![Aplikacja logiki](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. W obszarze **Security Center — elementy playbook (wersja zapoznawcza)** kliknij przycisk **Dodaj**.

    ![Tworzenie aplikacji logiki](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Na stronie **Tworzenie aplikacji logiki** wpisz informacje wymagane do utworzenia nowej aplikacji logiki, a następnie kliknij przycisk **Utwórz**. Po zakończeniu tworzenia na liście zostanie wyświetlony nowy element playbook. W przeciwnym razie kliknij przycisk **Odśwież**. Gdy element playbook zostanie wyświetlony, kliknij go w celu rozpoczęcia edycji.

    ![Tworzenie aplikacji logiki](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Zostanie wyświetlone okno **Projektant aplikacji logiki**. Kliknij pozycję **Pusta aplikacja logiki**, aby utworzyć nowy element playbook. Możesz również wybrać pozycję **Zabezpieczenia** w obszarze kategorii i użyć jednego z szablonów.

    ![Projektant aplikacji logiki](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. W polu **Wyszukaj wszystkie łączniki i wyzwalacze** wpisz *Azure Security Center* i wybierz pozycję **Po wyzwoleniu odpowiedzi na alert usługi Azure Security Center**.

    ![Wyzwalacz](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Teraz możesz zdefiniować zachowanie po wyzwoleniu elementu playbook. Możesz dodać akcję, warunek logiczny, warunki przypadku switch lub pętle.

    ![Projektant aplikacji logiki](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Jak uruchomić element playbook zabezpieczeń w usłudze Security Center?

Element playbook zabezpieczeń możesz uruchomić w usłudze Security Center, jeśli chcesz przeprowadzić aranżację lub korektę albo uzyskać dalsze informacje. Aby uzyskać dostęp do elementów playbook, wykonaj następujące czynności:

1.  Otwórz pulpit nawigacyjny usługi **Security Center**.
2.  W lewym okienku w obszarze **Wykrywanie zagrożeń** kliknij pozycję **Alerty i zdarzenia związane z bezpieczeństwem**.

    ![Alerty](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Kliknij alert, który chcesz zbadać.
4.  W górnej części strony alertu kliknij przycisk **Uruchom elementy playbook**.

    ![Uruchamianie elementu playbook](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Na stronie Elementy playbook wybierz element playbook, który chcesz uruchomić, a następnie kliknij przycisk **Uruchom**. Jeśli chcesz zobaczyć element playbook przed jego wyzwoleniem, możesz go kliknąć — spowoduje to otwarcie projektanta.

    ![Elementy playbook](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Historia

Po uruchomieniu elementu playbook możesz również uzyskać dostęp do poprzednich wykonań oraz kroków zawierających więcej informacji na temat stanu wcześniej wykonywanych elementów playbook. Kontekst historii jest określany dla poszczególnych alertów, co oznacza, że historia elementu playbook widoczna na tej stronie jest powiązana z alertem, który wyzwolił dany element.

![Historia](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Aby wyświetlić więcej szczegółów wykonania danego elementu playbook, kliknij ten element. Spowoduje to wyświetlenie strony uruchamiania aplikacji logiki z całym przepływem pracy.

![Szczegóły](./media/security-center-playbooks/security-center-playbooks-fig14.png)

W tym przepływie pracy można sprawdzić czas wykonywania poszczególnych zadań oraz rozwinąć każde zadanie, aby wyświetlić jego wynik.

### <a name="changing-an-existing-playbook"></a>Zmienianie istniejącego elementu playbook

Istniejący element playbook możesz zmienić w usłudze Security Center, aby dodać akcję lub warunki. W tym celu wystarczy kliknąć nazwę elementu playbook, który chcesz zmienić, na karcie Elementy playbook. Spowoduje to otwarcie projektanta aplikacji logiki.

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia własnego elementu playbook przy użyciu aplikacji logiki platformy Azure, zobacz [Tworzenie pierwszego przepływu pracy aplikacji logiki w celu automatyzacji procesów między aplikacjami w chmurze a usługami w chmurze](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app).


## <a name="see-also"></a>Zobacz także
W tym dokumencie omówiono korzystanie z elementów playbook w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
