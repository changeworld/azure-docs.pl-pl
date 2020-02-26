---
title: Zastosuj aktualizacje systemu w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób implementacji zaleceń Azure Security Center **zastosowania aktualizacji systemu** i **ponownego uruchomienia po aktualizacji systemu**.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604554"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Zastosuj aktualizacje systemu w Azure Security Center
Azure Security Center monitoruje codzienne maszyny wirtualne z systemami Windows i Linux oraz komputery z brakującymi aktualizacjami systemu operacyjnego. Security Center pobiera listę dostępnych aktualizacji zabezpieczeń i krytycznych z Windows Update lub Windows Server Update Services (WSUS), w zależności od tego, która usługa została skonfigurowana na komputerze z systemem Windows. Security Center również sprawdza najnowsze aktualizacje w systemach Linux. Jeśli na maszynie wirtualnej lub komputerze brakuje aktualizacji systemu, Security Center będzie zalecać stosowanie aktualizacji systemu.

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
Zastosowanie aktualizacji systemu jest przedstawione jako zalecenie w Security Center. Jeśli na maszynie wirtualnej lub komputerze brakuje aktualizacji systemu, to zalecenie zostanie wyświetlone w obszarze **rekomendacje** i w obszarze **obliczenia**.  Wybranie zalecenia spowoduje otwarcie pulpitu nawigacyjnego **Zastosuj aktualizacje systemu** .

W tym przykładzie będziemy używać **obliczeń**.

1. Wybierz pozycję **obliczenia** w menu głównym Security Center.

   ![Wybierz pozycję obliczenia][1]

2. W obszarze **Obliczanie**wybierz pozycję **brakujące aktualizacje systemu**. Zostanie otwarty pulpit nawigacyjny **Zastosuj aktualizacje systemu** .

   ![Pulpit nawigacyjny zastosowania aktualizacji systemu][2]

   W górnej części pulpitu nawigacyjnego dostępne są następujące informacje:

    - Całkowita liczba maszyn wirtualnych z systemami Windows i Linux oraz komputerów, na których brakuje aktualizacji systemu.
    - Całkowita liczba brakujących aktualizacji krytycznych na maszynach wirtualnych i komputerach.
    - Całkowita liczba brakujących aktualizacji zabezpieczeń na maszynach wirtualnych i komputerach.

   Na dole pulpitu nawigacyjnego są wyświetlane wszystkie brakujące aktualizacje na maszynach wirtualnych i komputerach oraz ważność brakującej aktualizacji.  Lista zawiera:

    - Nazwa: Nazwa brakującej aktualizacji.
    - Znaleziono. Maszyn wirtualnych & komputerów: Łączna liczba maszyn wirtualnych i komputerów, na których brakuje tej aktualizacji.
    - STAN: bieżący stan zalecenia:

      - Otwarte: zalecenie nie zostało jeszcze uwzględnione.
      - W toku: zalecenie jest aktualnie stosowane do tych zasobów i nie jest wymagane wykonanie jakiejkolwiek akcji przez użytkownika.
      - Rozwiązano: zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

    - WAŻNOŚĆ: opisuje ważność danego zalecenia:

      - Wysoka: istnieje luka w zabezpieczeniach z istotnym zasobem (aplikacją, maszyną wirtualną lub sieciową grupą zabezpieczeń) i wymaga uwagi.
      - Średni: niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
      - Niska: należy rozwiązać usterkę, ale nie wymaga ona natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).

3. Wybierz brakującą aktualizację na liście, aby wyświetlić szczegóły.

   ![Brak aktualizacji zabezpieczeń][3]

4. Wybierz ikonę **wyszukiwania** na górnej Wstążce.  Zapytanie wyszukiwania w dzienniku Azure Monitor otwiera się w odfiltrowane na komputerach, na których brakuje aktualizacji.

   ![Wyszukiwanie dzienników Azure Monitor][4]

5. Wybierz komputer z listy, aby uzyskać więcej informacji. Zostanie otwarty inny wynik wyszukiwania z informacjami przefiltrowanymi tylko dla tego komputera.

    ![Wyszukiwanie dzienników Azure Monitor][5]

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — zawiera wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
