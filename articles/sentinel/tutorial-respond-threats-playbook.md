---
title: Uruchamianie elementu playbook w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: W tym artykule opisano uruchamianie elementu playbook w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: db529b45751ff418c93c43119f0d925f6b4b086e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605307"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Samouczek: Skonfiguruj odpowiedzi automatyczne zagrożeń w wersji zapoznawczej Azure przez wartownika

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten samouczek ułatwia umożliwia elementów playbook zabezpieczeń w Azure przez wartownika ustawienie odpowiedzi automatycznych zagrożenia wykryte przez Azure przez wartownika problemy związane z zabezpieczeniami.


> [!div class="checklist"]
> * Omówienie elementów playbook
> * Tworzenie elementu playbook
> * Uruchamianie elementu playbook


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Co to jest element playbook zabezpieczeń w Azure przez wartownika?

Element playbook zabezpieczeń to kolekcja procedur, które mogą być uruchamiane z platformy Azure przez wartownika w odpowiedzi na alert. Element playbook zabezpieczeń ułatwia Automatyzowanie i organizowanie odpowiedzi i można uruchomić ręcznie lub uruchamiane automatycznie po wyzwoleniu konkretnych alertów. Elementy playbook zabezpieczeń w Azure przez wartownika opierają się na [usługi Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), co oznacza, że otrzymujesz całe zasilania, dostosowywalności i wbudowane szablony aplikacji logiki. Każdy element playbook jest tworzony dla określonych subskrypcji, którą wybierzesz, ale jeśli przyjrzymy się na stronie elementy Playbook, widoczne będą wszystkie elementy playbook we wszystkich wybranych subskrypcji.

> [!NOTE]
> Elementy playbook korzystać z usługi Azure Logic Apps, dlatego naliczane są opłaty. Odwiedź stronę cennika usługi [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), aby zapoznać się ze szczegółami.

Na przykład martwisz złośliwe ataki, uzyskiwanie dostępu do zasobów sieciowych, można ustawić alert, który sprawdza, czy są złośliwe adresy IP, uzyskiwanie dostępu do sieci. Następnie należy utworzyć element playbook, który wykonuje następujące czynności:
1. Po wyzwoleniu alertu, otwórz bilet usługi ServiceNow lub innych IT, system biletów.
2. Wyślij wiadomość do Twojego kanału operacji zabezpieczeń w Microsoft Teams lub Slack, aby upewnić się, że analityków zabezpieczeń są znane zdarzenia.
3. Wyślij wszystkie informacje w alercie do wyższych sieci i administratora zabezpieczeń administratora. Wiadomość e-mail zawiera również dwa przyciski opcji użytkownika **bloku** lub **Ignoruj**.
4. Element playbook będzie nadal działać po otrzymaniu odpowiedzi od administratorów.
5. Jeśli Administratorzy **bloku**, adres IP jest blokowany w zaporze i użytkownik jest wyłączony w usłudze Azure AD.
6. Jeśli Administratorzy **Ignoruj**, alert zostanie zamknięty w przez wartownika platformy Azure i jest zamknięty incydentu usługi ServiceNow.

Elementy playbook zabezpieczeń można uruchomić ręcznie lub automatycznie. Ręczne uruchomienie oznacza, że po otrzymaniu alertu można uruchomić element playbook na żądanie w odpowiedzi na wybrany alert. Automatycznemu uruchamianiu oznacza, że podczas tworzenia reguły korelacji, zostanie ustawiona do automatycznego uruchamiania jednego lub więcej elementów playbook po wyzwoleniu alertu.


## <a name="create-a-security-playbook"></a>Utwórz element playbook zabezpieczeń

Wykonaj następujące kroki, aby utworzyć nowy element playbook zabezpieczeń w Azure przez wartownika:

1. Otwórz **Azure przez wartownika** pulpitu nawigacyjnego.
2. W obszarze **zarządzania**, wybierz opcję **elementy Playbook**.

   ![Aplikacja logiki](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. W **Azure przez wartownika — elementy Playbook (wersja zapoznawcza)** kliknij **Dodaj** przycisku.

   ![Tworzenie aplikacji logiki](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. W **tworzenie aplikacji logiki** wpisz wymagane informacje, aby utworzyć nową aplikację logiki, a następnie kliknij przycisk **Utwórz**. 

5. W [ **Projektant aplikacji logiki**](../logic-apps/logic-apps-overview.md), wybierz szablon, którego chcesz użyć. Jeśli wybierzesz szablon, który wymaga poświadczeń, należy podać je. Alternatywnie można utworzyć nowy pusty element playbook od podstaw. Wybierz **pusta aplikacja logiki**. 

   ![Projektant aplikacji logiki](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Nastąpi przekierowanie do projektanta aplikacji logiki, w którym można tworzyć nowe lub edytować szablon. Aby uzyskać więcej informacji na temat tworzenia elementu playbook z [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. W przypadku tworzenia pustej podręcznikiem **Wyszukaj wszystkie łączniki i wyzwalacze** wpisz *Azure przez wartownika*i wybierz **powyzwalanewodpowiedzinaalertusługiAzureprzezwartownika**. <br>Po jego utworzeniu nowy element playbook zostanie wyświetlony w **elementy Playbook** listy. Jeśli nie pojawia się, kliknij przycisk **Odśwież**. 

7. Teraz możesz zdefiniować zachowanie po wyzwoleniu elementu playbook. Możesz dodać akcję, warunek logiczny, warunki przypadku switch lub pętle.

   ![Projektant aplikacji logiki](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Jak uruchomić element playbook zabezpieczeń

Można uruchomić elementu playbook na żądanie.

Aby uruchomić element playbook na żądanie:

1. W **przypadków** stronie, wybierz przypadek i kliknij **Wyświetl pełne szczegóły**.

2. W **alerty** kartę, kliknij alert, aby uruchomić element playbook i przewiń do prawej strony i kliknij przycisk **Wyświetl elementy playbook** i wybierz element playbook, aby **Uruchom** z Lista dostępnych elementów playbook w ramach subskrypcji. 




## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uruchamianie elementu playbook w przez wartownika platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły: W tym samouczku przedstawiono sposób uruchamianie elementu playbook w przez wartownika platformy Azure. W dalszym ciągu [jak aktywnie możliwe pod kątem zagrożeń](hunting.md) przy użyciu platformy Azure przez wartownika.
> [!div class="nextstepaction"]
> [Możliwe, pod kątem zagrożeń](hunting.md) aby proaktywnie wykrywaj zagrożenia w sieci.

