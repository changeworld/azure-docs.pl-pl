---
title: Zastosuj aktualizacje systemu w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób implementowania zaleceń Centrum zabezpieczeń Azure **Zastosuj aktualizacje systemu** i **Uruchom ponownie po zaktualizowaniu systemu**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: ebd9939128d1f2b870541e82710792d13b69728e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095453"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Zastosuj aktualizacje systemu w usłudze Azure Security Center
Usługa Azure Security Center monitoruje dzienne Windows i Linux maszyn wirtualnych (VM) i komputerów w celu znalezienia brakujących aktualizacji systemu operacyjnego. Usługa Security Center pobiera listę dostępnych zabezpieczeń i aktualizacje krytyczne z witryny Windows Update lub Windows Server Update Services (WSUS) w zależności od którego usługa jest skonfigurowana na komputerze Windows. Centrum zabezpieczeń sprawdza również uzyskać najnowsze aktualizacje w systemach Linux. Jeśli komputera lub maszyny Wirtualnej, na których brakuje aktualizacji systemu, usługa Security Center zaleca zastosowanie aktualizacji systemu.

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
Zastosuj system aktualizacji jest przedstawiany jako zalecenia w usłudze Security Center. Jeśli komputera lub maszyny Wirtualnej, na których brakuje aktualizacji systemu, tego zalecenia zostanie wyświetlony w obszarze **zalecenia** i w obszarze **obliczenia**.  Wybranie zalecenie otwiera **Zastosuj aktualizacje systemu** pulpitu nawigacyjnego.

W tym przykładzie użyjemy **obliczenia**.

1. Wybierz **obliczenia** menu głównym usługi Security Center.

   ![Select Compute][1]

2. W obszarze **obliczenia**, wybierz opcję **brakujące aktualizacje systemu**. **Zastosuj aktualizacje systemu** zostanie otwarty pulpit nawigacyjny.

   ![Zastosuj pulpit nawigacyjny aktualizacji systemu][2]

   Udostępnia górnej części pulpitu nawigacyjnego:

    - Całkowita liczba Windows i maszyn wirtualnych systemu Linux oraz komputery z brakującymi aktualizacjami systemu.
    - Całkowita liczba aktualizacje krytyczne, brakuje w maszynach wirtualnych i komputerów.
    - Całkowita liczba aktualizacji zabezpieczeń na maszynach wirtualnych i komputerów.

   Dolnej części pulpitu nawigacyjnego Wyświetla wszystkie brakujące aktualizacje dla maszyn wirtualnych i komputerów oraz ważność brakujących aktualizacji.  Lista zawiera:

    - NAZWA: Nazwa brakującej aktualizacji.
    - NIE. Maszyn wirtualnych i komputerów: Całkowita liczba maszyn wirtualnych i komputerów, które mają tej aktualizacji.
    - STAN: Bieżący stan zalecenia:

      - Otwórz: Zalecenie nie rozpoczęto jeszcze wykonywania.
      - W toku: Zalecenie jest aktualnie stosowane do tych zasobów i jest wymagana żadna akcja.
      - Rozpoznane: Zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

    - WAŻNOŚĆ: Opisuje ważność określonego zalecenia:

      - Wysoka: Luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (aplikacji, maszyny wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
      - Średnie: Niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
      - Małe: Luki w zabezpieczeniach powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).

3. Wybierz brakujących aktualizacji na liście, aby wyświetlić szczegóły.

   ![Brak aktualizacji zabezpieczeń][3]

4. Wybierz **wyszukiwania** ikona na Wstążce najważniejsze.  Zapytanie wyszukiwania dzienników usługi Azure Monitor otwiera filtrowane na komputerach brakujących aktualizacji.

   ![Usługa Azure Monitor dzienniki wyszukiwania][4]

5. Wybierz komputer z listy, aby uzyskać więcej informacji. Zostanie otwarty inny wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

    ![Usługa Azure Monitor dzienniki wyszukiwania][5]

## <a name="reboot-after-system-updates"></a>Uruchom ponownie po zaktualizowaniu systemu
1. Wróć do **zalecenia** bloku. Wygenerowano nowy wpis, po zastosowaniu aktualizacji systemu, nazywany **Uruchom ponownie po zaktualizowaniu systemu**. Ten wpis informuje o tym, że musisz ponownie uruchomić maszynę Wirtualną w celu ukończenia procesu stosowania aktualizacji systemu.

   ![Uruchom ponownie po zaktualizowaniu systemu][6]
2. Wybierz **Uruchom ponownie po zaktualizowaniu systemu**. Spowoduje to otwarcie **ponownego uruchomienia oczekuje na ukończenie aktualizacji systemu** bloku, wyświetlanie listy maszyn wirtualnych, które należy ponownie uruchomić, aby ukończyć Zastosuj system aktualizuje procesu.

   ![Oczekiwanie na ponowne uruchomienie][7]

Uruchom ponownie maszynę Wirtualną platformy Azure, aby ukończyć proces.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
