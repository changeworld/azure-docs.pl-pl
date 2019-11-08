---
title: Konfigurowanie reguł i zarządzanie alertami w usłudze Azure FarmBeats
description: Opisuje sposób konfigurowania reguł i zarządzania alertami w programie FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02df4dd97c9a98ff6dd1c42957884fa5ca64365f
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798533"
---
# <a name="configure-rules-and-manage-alerts-in-farmbeats"></a>Konfigurowanie reguł i zarządzanie alertami w programie FarmBeats

Usługa Azure FarmBeats umożliwia tworzenie reguł opartych na logice biznesowej, a także danych czujników, które są przepływów z czujników i urządzeń wdrożonych w farmie. Reguły wyzwalają alerty w systemie zawsze, gdy wartości z czujnika przekraczają wartość progową. Wyświetlając i analizując alerty utworzone po wartości progowych, można szybko podejmować problemy i uzyskać wymagane rozwiązania.

## <a name="create-rule"></a>Utwórz regułę

1. Na stronie głównej przejdź do pozycji **reguły**.
2. Wybierz pozycję **Nowa reguła**. Zostanie wyświetlone okno Nowa reguła.

    ![Farmy projektów](./media/configure-rules-and-alerts/new-rule-1.png)

3. Wprowadź **nazwę reguły** i **Opis reguły** , a następnie wybierz farmę z menu rozwijanego **Wybieranie farmy** .
4. Wpisz nazwę farmy, aby wybrać sekcję farmy i **warunki** w tym samym oknie.  

    ![Farmy projektów](./media/configure-rules-and-alerts/new-rule-condition-1.png)

5. W obszarze **warunki**wprowadź wartości dla **miary**, **operatora** i **wartości**.
6. W menu rozwijanym **pomiar** wpisz nazwę miary.
7. Wybierz pozycję **+ Dodaj warunek** , aby dodać więcej warunków do reguły.
8. Wybierz **poziom ważności**.
9. W obszarze **Akcja**przejdź do przycisku przełączania z **włączoną obsługą poczty** e-mail, aby włączyć alerty e-mail.

    ![Farmy projektów](./media/configure-rules-and-alerts/new-rule-email-1.png)

10. Wprowadź **adresy e-mail** , na które chcesz wysłać Alert e-mail, wraz z **tematem wiadomości e-mail** i **dodatkowymi notatkami**.  
11. Aby włączyć lub wyłączyć regułę, w obszarze **stan reguły**przejdź do opcji **włączony** przełącznik.
    Można wyświetlić liczbę urządzeń, na które wpłynie reguła.
12. Wybierz pozycję **Zastosuj** , aby utworzyć regułę.

## <a name="view-rule"></a>Wyświetl regułę

Na stronie **Farma** zostanie wyświetlona lista dostępnych reguł. Wybierz **nazwę reguły**. W oknie są wyświetlane następujące szczegóły dotyczące wybranej reguły:
 - Nazwa reguły
 - Połącz z farmą, z którą skojarzona jest reguła
 - Data utworzenia
 - Data ostatniej aktualizacji
 - Poziom ważności
 - Stan reguły
 - Lista warunków  
 - Liczba urządzeń, na które ma wpływ reguła

    ![Farmy projektów](./media/configure-rules-and-alerts/view-rule-1.png)

## <a name="edit-rule"></a>Edytuj regułę

Aby edytować regułę, wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **reguły** w menu nawigacji po lewej stronie.
   Zostanie wyświetlone okno reguły.
2. Wybierz regułę, dla której chcesz edytować.

    ![Farmy projektów](./media/configure-rules-and-alerts/edit-rule-action-bar-1.png)

3. Wybierz pozycję **Edytuj** na pasku akcji, aby wyświetlić okno **Edytowanie reguły** .

    ![Farmy projektów](./media/configure-rules-and-alerts/edit-rule-one-1.png)

4. Zmień **nazwę reguły**i **Opis reguły** , a następnie wybierz farmę z menu rozwijanego **Wybieranie farmy** .
5. Wpisz nazwę farmy, aby wybrać farmę i **warunki** pojawiają się w tym samym oknie.  
6. W obszarze **warunki**, Edycja **miary**, **operator** i **wartość**.
7. W menu rozwijanym **pomiar** wpisz nazwę miary.
8. Wybierz pozycję **+ Dodaj warunek** , aby dodać/edytować warunki do reguł.

    ![Farmy projektów](./media/configure-rules-and-alerts/edit-rule-two-1.png)

9.  Wybierz **poziom ważności**.  
10. W obszarze **Akcja**przejdź do przycisku przełączania z **włączoną obsługą poczty** e-mail, aby włączyć alerty e-mail.
11. Edytuj **adresy e-mail** , na które chcesz wysłać Alert e-mail, a także **temat wiadomości E-mail** i **dodatkowe uwagi**.  
12. Aby włączyć lub wyłączyć regułę, w obszarze **stan reguły**przejdź do opcji **włączony** przełącznik.
Można wyświetlić liczbę urządzeń, na które wpłynie ta reguła.
13. Wybierz pozycję **Zastosuj** , aby edytować regułę.

## <a name="change-rule-status"></a>Zmień stan reguły

Aby zmienić stan reguły, wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **reguły** w menu nawigacji po lewej stronie. Zostanie wyświetlone okno reguły.
2. Wybierz regułę, dla której chcesz zmienić stan.

    ![Farmy projektów](./media/configure-rules-and-alerts/change-status-rule-action-bar-1.png)

3. Wybierz pozycję **Zmień stan** na pasku akcji. Zostanie wyświetlone okno **zmiany stanu** .

    ![Farmy projektów](./media/configure-rules-and-alerts/rule-change-status-1.png)

3. Zmień stan reguły przy użyciu przycisku **Zmień stan** .
   Można wyświetlić liczbę urządzeń, na które wpłynie reguła.
4. Wybierz pozycję **Zastosuj** , aby zmienić stan reguły.

## <a name="delete-rule"></a>Usuń regułę

Aby usunąć regułę, wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **reguły** w menu nawigacji po lewej stronie. Zostanie wyświetlone okno reguły.
2. Wybierz regułę, dla której chcesz usunąć.

    ![Farmy projektów](./media/configure-rules-and-alerts/delete-rule-action-bar-1.png)

3. Na pasku akcji wybierz pozycję **Usuń** .

    ![Farmy projektów](./media/configure-rules-and-alerts/delete-rule-1.png)

4. Zostanie wyświetlone okno dialogowe **Usuwanie reguły** . Wybierz pozycję **Usuń**.
