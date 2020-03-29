---
title: Konfigurowanie reguł i zarządzanie alertami
description: W tym artykule opisano sposób konfigurowania reguł i zarządzania alertami w polach FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482987"
---
# <a name="configure-rules-and-manage-alerts"></a>Konfigurowanie reguł i zarządzanie alertami

Usługa Azure FarmBeats umożliwia tworzenie reguł na podstawie logiki biznesowej, oprócz danych z czujników, które przepływają z czujników i urządzeń wdrożonych w farmie. Reguły wyzwalają alerty w systemie, gdy wartości czujnika przekraczają wartość progową. Przeglądając i analizując alerty utworzone po wartości progowych, można szybko działać na wszelkie problemy i uzyskać wymagane rozwiązania.

## <a name="create-rule"></a>Tworzenie reguły

1. Na stronie głównej przejdź do **strony Reguły**.
2. Wybierz **pozycję Nowa reguła**. Zostanie wyświetlone okno Nowa reguła.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Wprowadź **nazwę reguły** i **opis reguły,** a następnie wybierz farmę z menu rozwijanego **Wybierz farmę.**
4. Wpisz nazwę farmy, aby wybrać farmę i **warunki** sekcja pojawia się w tym samym oknie.  

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. W **polu Warunki**wprowadź wartości **miary,** **operatora** i **wartości**.
6. Wpisz nazwę miary w menu rozwijanym **Miara.**
7. Wybierz **+Dodaj warunek,** aby dodać więcej warunków do reguły.
8. Wybierz **poziom ważności**.
9. W **akcji**włącz przycisk **przełączania z włączoną obsługą poczty e-mail,** aby włączyć alerty e-mail.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Wprowadź **adresy e-mail,** na które chcesz wysłać alert e-mail, wraz z **tematem wiadomości e-mail** i **uwagami dodatkowymi**.  
11. W **stanie reguły**włącz przycisk **Włącz przełącznik,** aby włączyć lub wyłączyć regułę.
    Można wyświetlić liczbę urządzeń, na które będzie miała wpływ reguła.
12. Wybierz przycisk **Zastosuj,** aby utworzyć regułę.

## <a name="view-rule"></a>Wyświetl regułę

Na stronie **Farm** jest wyświetlana lista dostępnych reguł. Wybierz **nazwę reguły**. W oknie są wyświetlane następujące szczegóły, które mają zastosowanie do wybranej reguły:
 - Nazwa reguły
 - Łącze do gospodarstwa, z którym jest skojarzona reguła
 - Data utworzenia
 - Data ostatniej aktualizacji
 - Poziom ważności
 - Stan reguły
 - Lista warunków  
 - Liczba urządzeń, których dotyczy reguła

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Edytowanie reguły

Aby edytować regułę, wykonaj następujące czynności:

1. Na stronie głównej wybierz **pozycję Reguły** z lewego menu nawigacji.
   Zostanie wyświetlone okno reguł.
2. Wybierz regułę, dla której chcesz edytować.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Wybierz **pozycję Edytuj** na pasku akcji, zostanie wyświetlone okno Edytuj **regułę.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Zmień **nazwę reguły**i **opis reguły,** a następnie wybierz farmę z menu rozwijanego **Wybierz farmę.**
5. Wpisz nazwę farmy, aby wybrać farmę i **warunki** są wyświetlane w tym samym oknie.  
6. W **warunkach**, edytuj **miarę,** **operatora** i **wartość**.
7. Wpisz nazwę miary w menu rozwijanym **Miara.**
8. Wybierz **+Dodaj warunek,** aby dodać/edytować warunki do reguł.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Wybierz **poziom ważności**.  
10. W **akcji**włącz przycisk **przełączania z włączoną obsługą poczty e-mail,** aby włączyć alerty e-mail.
11. Edytuj **adresy e-mail,** które chcesz wysłać alert e-mail, wraz z **tematem wiadomości e-mail** i **uwagami dodatkowymi**.  
12. W **stanie reguły**włącz przycisk **Włącz przełącznik,** aby włączyć lub wyłączyć regułę.
Można wyświetlić liczbę urządzeń, których dotyczy ta reguła.
13. Wybierz pozycję **Zastosuj,** aby edytować regułę.

## <a name="change-rule-status"></a>Zmień stan reguły

Aby zmienić stan reguły, wykonaj następujące czynności:

1. Na stronie głównej wybierz **pozycję Reguły** z lewego menu nawigacji. Zostanie wyświetlone okno reguł.
2. Wybierz regułę, dla której chcesz zmienić stan.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Wybierz **pozycję Zmień stan** na pasku akcji. Zostanie wyświetlone okno **Zmień stan.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Zmień stan reguły za pomocą przycisku **Przełączania Zmień stan.**
   Można wyświetlić liczbę urządzeń, których dotyczy reguła.
4. Wybierz **Apply** zastosuj, aby zmienić stan reguły.

## <a name="delete-rule"></a>Usuwanie reguły

Aby usunąć regułę, wykonaj następujące czynności:

1. Na stronie głównej wybierz **pozycję Reguły** z lewego menu nawigacji. Zostanie wyświetlone okno reguł.
2. Wybierz regułę, dla której chcesz usunąć.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Z paska akcji wybierz **pozycję Usuń.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Zostanie **wyświetlone** okno dialogowe Usuń regułę. Wybierz pozycję **Usuń**.
