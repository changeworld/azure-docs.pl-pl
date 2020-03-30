---
title: Stosowanie aktualizacji systemu w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym dokumencie pokazano, jak zaimplementować zalecenia usługi Azure Security Center **Stosowanie aktualizacji systemu** i ponowne **uruchamianie po aktualizacji systemu**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604554"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Stosowanie aktualizacji systemu w usłudze Azure Security Center
Usługa Azure Security Center codziennie monitoruje maszyny wirtualne i komputery z systemami Windows i Linux w celu wyszukania brakujących aktualizacji systemu operacyjnego. Usługa Security Center pobiera listę dostępnych aktualizacji zabezpieczeń i aktualizacji krytycznych z usługi Windows Update lub Windows Server Update Services (WSUS), w zależności od tego, która z nich została skonfigurowana na komputerze z systemem Windows. Usługa Security Center wyszukuje również najnowsze aktualizacje w systemach Linux. Jeśli na maszynie wirtualnej lub komputerze brakuje aktualizacji systemu, usługa Security Center będzie zalecać stosowanie aktualizacji systemu.

## <a name="implement-the-recommendation"></a>Wdrożenie zalecenia
Zastosuj aktualizacje systemu jest przedstawiony jako zalecenie w Centrum zabezpieczeń. Jeśli na maszynie Wirtualnej lub komputerze brakuje aktualizacji systemu, to zalecenie zostanie wyświetlone w obszarze **Zalecenia** i w obszarze **Obliczenia**.  Wybranie zalecenia powoduje otwarcie pulpitu nawigacyjnego **Zastosuj aktualizacje systemu.**

W tym przykładzie **użyjemy compute**.

1. Wybierz **polecenie Oblicz** w menu głównym centrum zabezpieczeń.

   ![Wybierz obliczeń][1]

2. W obszarze **Obliczanie**wybierz pozycję **Brakujące aktualizacje systemu**. Zostanie otwarty pulpit nawigacyjny **Zastosuj aktualizacje systemu.**

   ![Zastosuj pulpit nawigacyjny aktualizacji systemu][2]

   Górna część pulpitu nawigacyjnego zapewnia:

    - Całkowita liczba maszyn wirtualnych z systemem Windows i Linux oraz komputerów, na których brakuje aktualizacji systemu.
    - Całkowita liczba aktualizacji krytycznych brakuje na maszynach wirtualnych i komputerach.
    - Całkowita liczba brakujących aktualizacji zabezpieczeń na maszynach wirtualnych i komputerach.

   W dolnej części pulpitu nawigacyjnego wymieniono wszystkie brakujące aktualizacje na maszynach wirtualnych i komputerach oraz ważność brakującej aktualizacji.  Lista zawiera:

    - NAZWA: nazwa brakującej aktualizacji.
    - №. Maszyny wirtualne & komputery: Całkowita liczba maszyn wirtualnych i komputerów, których brakuje w tej aktualizacji.
    - STAN: bieżący stan zalecenia:

      - Otwarte: Zalecenie nie zostało jeszcze uwzględnione.
      - W toku: zalecenie jest aktualnie stosowane do danych zasobów, żadna akcja użytkownika nie jest wymagana.
      - Rozwiązane: zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

    - WAŻNOŚĆ: opisuje ważność określonego zalecenia:

      - Wysoka: istnieje luka w zabezpieczeniach, która dotyczy istotnego zasobu (aplikacji, maszyny wirtualnej, sieciowej grupy zabezpieczeń) i wymaga uwagi.
      - Średnia: niekrytyczne lub dodatkowe kroki są wymagane w celu ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
      - Niska: luka w zabezpieczeniach powinna zostać usunięta, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).

3. Wybierz brakującą aktualizację na liście, aby wyświetlić szczegóły.

   ![Brak aktualizacji zabezpieczeń][3]

4. Wybierz ikonę **Wyszukaj** na górnej wstążce.  Zostanie otwarta kwerenda wyszukiwania dzienników platformy Azure Monitor filtrowana na komputerach, na których brakuje aktualizacji.

   ![Wyszukiwanie dzienników usługi Azure Monitor][4]

5. Wybierz komputer z listy, aby uzyskać więcej informacji. Zostanie otwarty inny wynik wyszukiwania z informacjami filtrowanymi tylko dla tego komputera.

    ![Wyszukiwanie dzienników usługi Azure Monitor][5]

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — dowiedz się, jak zalecenia pomagają chronić zasoby platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Blog usługi Azure Security](https://blogs.msdn.com/b/azuresecurity/) — znajdź wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
