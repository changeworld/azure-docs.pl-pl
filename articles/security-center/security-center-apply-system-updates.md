---
title: Stosowanie aktualizacji systemu w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument zawiera implementowania zaleceń Centrum zabezpieczeń Azure ** zastosować aktualizacje systemu ** i ** ponowny rozruch po aktualizacji systemu **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866429"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Stosowanie aktualizacji systemu w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure monitoruje codzienne systemu Windows i Linux maszynach wirtualnych (VM) i na komputerach brakujących aktualizacji systemu operacyjnego. Centrum zabezpieczeń pobiera listę dostępnych zabezpieczeń i krytycznych aktualizacji z witryny Windows Update lub Windows Server Update Services (WSUS), w zależności od tego, który usługa jest skonfigurowana na komputerze z systemem Windows. Centrum zabezpieczeń sprawdza także najnowsze aktualizacje w systemach Linux. Jeśli maszyna wirtualna lub komputerze z Brak aktualizacji systemu, Centrum zabezpieczeń zaleca się zastosowanie aktualizacji systemu.

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
Zastosuj system aktualizacji zostaje przedstawiony jako zalecenia w Centrum zabezpieczeń. Jeśli maszyna wirtualna lub komputerze z Brak aktualizacji systemu, tego zalecenia zostanie wyświetlony w obszarze **zalecenia** i w obszarze **obliczeniowe**.  Wybieranie zalecenie otwiera **stosowanie aktualizacji systemu** pulpitu nawigacyjnego.

W tym przykładzie używamy **obliczeniowe**.

1. Wybierz **obliczeniowe** w menu głównym Centrum zabezpieczeń.

   ![Wybierz obliczeń][1]

2. W obszarze **obliczeniowe**, wybierz pozycję **Brak aktualizacji systemu**. **Stosowanie aktualizacji systemu** otwiera pulpitu nawigacyjnego.

   ![Zastosuj pulpitu nawigacyjnego aktualizacji systemu][2]

   Udostępnia górnej części pulpitu nawigacyjnego:

    - Całkowita liczba systemu Windows i maszyn wirtualnych systemu Linux i komputerach Brak aktualizacji systemu.
    - Całkowita liczba aktualizacji krytycznych, brak między maszyn wirtualnych i komputerów.
    - Całkowita liczba aktualizacji zabezpieczeń brak między maszyn wirtualnych i komputerów.

  Dolnej części pulpitu nawigacyjnego Wyświetla wszystkie brakujące aktualizacje maszyn wirtualnych i komputerów i ważności brakujących aktualizacji.  Lista zawiera:

    - Nazwa: Nazwa brakujących aktualizacji.
    - NIE. Maszyny wirtualne i komputerach: łączna liczba maszyn wirtualnych i komputerów, których brakuje tej aktualizacji.
    - Stan: Bieżący stan zalecenia:

      - Otwórz: Zalecenie nie rozpoczęto jeszcze.
      - W toku: Zalecenie jest aktualnie stosowane do tych zasobów i jest wymagana żadna akcja.
      - Rozpoznać: Zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

    - Ważność: Opisuje ważność określonego zalecenia:

      - Wysoki: Luka w zabezpieczeniach istnieje istotnego zasobu (aplikacji, maszyny wirtualnej lub grupy zabezpieczeń sieci) i wymaga uwagi.
      - Średnia liczba godzin: Niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
      - Niski: Luka w zabezpieczeniach powinna zostać usunięta, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).

3. Wybierz z listy, aby wyświetlić szczegóły brakujących aktualizacji.

   ![Brak aktualizacji zabezpieczeń][3]

4. Wybierz **wyszukiwania** ikona na Wstążce top.  Zapytania wyszukiwania analizy dzienników otwiera filtrowane do komputerów, Brak aktualizacji.

   ![Wyszukaj analizy dzienników][4]

5. Wybierz komputer z listy, aby uzyskać więcej informacji. Otwiera inny wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

    ![Wyszukaj analizy dzienników][5]

## <a name="reboot-after-system-updates"></a>Uruchom ponownie po zaktualizowaniu systemu
1. Wróć do **zalecenia** bloku. Wygenerowano nowy wpis, po zastosowaniu aktualizacji systemu, o nazwie **ponowny rozruch po aktualizacji systemu**. Ten wpis informuje o tym, że należy przeprowadzić ponowny rozruch maszyny Wirtualnej, aby ukończyć proces stosowania aktualizacji systemu.

   ![Uruchom ponownie po zaktualizowaniu systemu][6]
2. Wybierz **ponowny rozruch po aktualizacji systemu**. Spowoduje to otwarcie **oczekuje na ponowne uruchomienie do ukończenia aktualizacji systemu** blok zawierający listę maszyn wirtualnych, które należy ponownie przeprowadzić systemu Zastosuj aktualizacje procesu.

   ![Oczekiwanie na ponowne uruchomienie][7]

Uruchom ponownie maszynę Wirtualną z platformy Azure, aby ukończyć proces.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
