---
title: 'Samouczek: Uruchamianie podręcznika w usłudze Azure Sentinel'
description: 'Samouczek: W tym artykule opisano sposób uruchamiania podręcznika w usłudze Azure Sentinel.'
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77585088"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Samouczek: Konfigurowanie automatycznych odpowiedzi na zagrożenia w usłudze Azure Sentinel



Ten samouczek ułatwia używanie podręczników zabezpieczeń w usłudze Azure Sentinel do ustawiania automatycznych odpowiedzi na zagrożenia problemów związanych z zabezpieczeniami wykrytych przez usługę Azure Sentinel.


> [!div class="checklist"]
> * Opis podręczników
> * Tworzenie podręcznika
> * Uruchamianie podręcznika
> * Automatyzacja reakcji na zagrożenia


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Co to jest podręcznik zabezpieczeń w usłudze Azure Sentinel?

Podręcznik zabezpieczeń to zbiór procedur, które można uruchomić z usługi Azure Sentinel w odpowiedzi na alert. Podręcznik zabezpieczeń może pomóc zautomatyzować i zorganizować odpowiedź i można go uruchomić ręcznie lub ustawić tak, aby działał automatycznie po wyzwoleniu określonych alertów. Podręczniki zabezpieczeń w usłudze Azure Sentinel są oparte na [usłudze Azure Logic Apps,](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)co oznacza, że otrzymujesz całą moc, możliwość dostosowania i wbudowane szablony aplikacji logiki. Każdy podręcznik jest tworzony dla konkretnej subskrypcji, którą wybierzesz, ale gdy spojrzysz na stronę Podręczniki, zobaczysz wszystkie podręczniki w wybranych subskrypcjach.

> [!NOTE]
> Podręczniki wykorzystują usługi Azure Logic Apps, w związku z czym obowiązują opłaty. Odwiedź stronę cennika usługi [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), aby zapoznać się ze szczegółami.

Jeśli na przykład obawiasz się złośliwych osób atakujących uzyskujących dostęp do zasobów sieciowych, możesz ustawić alert, który wyszukuje złośliwe adresy IP uzyskujące dostęp do sieci. Następnie można utworzyć podręcznik, który wykonuje następujące czynności:
1. Po wyzwoleniu alertu otwórz bilet w ServiceNow lub innym systemie sprzedaży it ticketing.
2. Wyślij wiadomość do kanału operacji zabezpieczeń w usłudze Microsoft Teams lub Slack, aby upewnić się, że analitycy zabezpieczeń są świadomi zdarzenia.
3. Wyślij wszystkie informacje zawarte w alertie do starszego administratora sieci i administratora zabezpieczeń. Wiadomość e-mail zawiera również dwa przyciski opcji użytkownika **Blokuj** lub **Ignoruj**.
4. Podręcznik jest nadal uruchamiany po otrzymaniu odpowiedzi od administratorów.
5. Jeśli administratorzy wybiorą **opcję Blokuj,** adres IP jest zablokowany w zaporze, a użytkownik jest wyłączony w usłudze Azure AD.
6. Jeśli administratorzy wybierz **ignoruj**, alert zostanie zamknięty w usłudze Azure Sentinel i zdarzenie zostanie zamknięte w ServiceNow.

Podręczniki zabezpieczeń można uruchamiać ręcznie lub automatycznie. Uruchamianie ich ręcznie oznacza, że po otrzymaniu alertu można uruchomić podręcznik na żądanie w odpowiedzi na wybrany alert. Uruchamianie ich automatycznie oznacza, że podczas tworzenia reguły korelacji można ustawić ją tak, aby automatycznie uruchamiała jeden lub więcej podręczników po wyzwoleniu alertu.


## <a name="create-a-security-playbook"></a>Tworzenie podręcznika zabezpieczeń

Wykonaj następujące kroki, aby utworzyć nowy podręcznik zabezpieczeń w usłudze Azure Sentinel:

1. Otwórz pulpit nawigacyjny **usługi Azure Sentinel.**
2. W obszarze **Zarządzanie**wybierz **pozycję Playbooks**.

   ![Aplikacja logiki](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na stronie **Azure Sentinel — Playbooks** kliknij przycisk **Dodaj.**

   ![Tworzenie aplikacji logiki](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na stronie **Tworzenie aplikacji Logika** wpisz żądane informacje, aby utworzyć nową aplikację logiki, a następnie kliknij przycisk **Utwórz**. 

5. W [**projektancie aplikacji logiki**](../logic-apps/logic-apps-overview.md)wybierz szablon, którego chcesz użyć. Jeśli wybierzesz szablon, który wymaga poświadczeń, trzeba będzie je podać. Alternatywnie, można utworzyć nowy pusty podręcznik od podstaw. Wybierz **pustą aplikację logiki**. 

   ![Projektant aplikacji logiki](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Zostaniesz przesuń do projektanta aplikacji logiki, gdzie można tworzyć nowe lub edytować szablon. Aby uzyskać więcej informacji na temat tworzenia podręcznika za pomocą [aplikacji logiki](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Jeśli tworzysz pusty podręcznik, w polu **Wyszukaj wszystkie łączniki i wyzwalacze** wpisz *azure sentinel*i wybierz **opcję Po wyzwoleniu odpowiedzi na alert wartownicy platformy Azure**. <br>Po utworzeniu nowego podręcznika pojawi się na liście **Podręczniki.** Jeśli nie jest wyświetlany, kliknij przycisk **Odśwież**.

1. Użyj **funkcji Pobierz jednostki,** które umożliwiają uzyskanie odpowiednich jednostek z listy **Jednostki,** takich jak konta, adresy IP i hosty. Umożliwi to uruchamianie akcji na określonych encjach.

7. Teraz możesz zdefiniować zachowanie po wyzwoleniu elementu playbook. Można dodać akcję, warunek logiczny, przełącznik warunków sprawy lub pętli.

   ![Projektant aplikacji logiki](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Jak uruchomić podręcznik zabezpieczeń

Podręcznik można uruchomić na żądanie.

Aby uruchomić podręcznik na żądanie:

1. Na stronie **zdarzenia** wybierz zdarzenie i kliknij **Wyświetl pełne szczegóły**.

2. Na karcie **Alerty** kliknij alert, na który chcesz uruchomić podręcznik, i przewiń całą drogę w prawo i kliknij pozycję **Wyświetl podręczniki** i wybierz podręcznik do **uruchomienia** z listy dostępnych podręczników w ramach subskrypcji. 



## <a name="automate-threat-responses"></a>Automatyzacja reakcji na zagrożenia

Zespoły SIEM/SOC mogą być regularnie zalewane alertami zabezpieczeń. Ilość generowanych alertów jest tak ogromna, że dostępni administratorzy zabezpieczeń są przytłoczeni. Powoduje to zbyt często w sytuacjach, gdy wiele alertów nie może być zbadane, pozostawiając organizacji narażone na ataki, które nie sąuznane. 

Wiele, jeśli nie większość z tych alertów jest zgodnych z wzorcami cyklicznymi, które można rozwiązać za pomocą określonych i zdefiniowanych akcji korygowania. Usługa Azure Sentinel umożliwia już zdefiniowanie korygowania w podręcznikach. Istnieje również możliwość ustawienia automatyzacji w czasie rzeczywistym jako część definicji podręcznika, aby umożliwić pełną automatyzację zdefiniowanej odpowiedzi na określone alerty zabezpieczeń. Korzystając z automatyzacji w czasie rzeczywistym, zespoły reagowania mogą znacznie zmniejszyć obciążenie pracą, w pełni automatyzując rutynowe odpowiedzi na powtarzające się typy alertów, co pozwala bardziej skoncentrować się na unikatowych alertach, analizowaniu wzorców, polowaniu na zagrożenia i innych.

Aby zautomatyzować odpowiedzi:

1. Wybierz alert, dla którego chcesz zautomatyzować odpowiedź.
1. Na stronie **Reguła edytowania alertu** w obszarze **Automatyzacja w czasie rzeczywistym**wybierz **podręcznik Wyzwalany,** który chcesz uruchomić po dopasowaniu tej reguły alertu.
1. Wybierz **pozycję Zapisz**.

   ![automatyzacja w czasie rzeczywistym](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak uruchomić podręcznik w usłudze Azure Sentinel. Przejdź do [sposobu proaktywnego polowania na zagrożenia](hunting.md) za pomocą usługi Azure Sentinel.


