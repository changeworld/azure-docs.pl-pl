---
title: Zastosuj aktualizacje systemu w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób implementowania zaleceń Centrum zabezpieczeń Azure **Zastosuj aktualizacje systemu** i **Uruchom ponownie po zaktualizowaniu systemu**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7b5f4415351ad5b51b874e5dc17349160f65e9ef
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240379"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Zastosuj aktualizacje systemu w usłudze Azure Security Center
Usługa Azure Security Center monitoruje dzienne Windows i Linux maszyn wirtualnych (VM) i komputerów w celu znalezienia brakujących aktualizacji systemu operacyjnego. Usługa Security Center pobiera listę dostępnych zabezpieczeń i aktualizacje krytyczne z witryny Windows Update lub Windows Server Update Services (WSUS) w zależności od którego usługa jest skonfigurowana na komputerze Windows. Centrum zabezpieczeń sprawdza również uzyskać najnowsze aktualizacje w systemach Linux. Jeśli komputera lub maszyny Wirtualnej, na których brakuje aktualizacji systemu, usługa Security Center zaleca zastosowanie aktualizacji systemu.

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
Zastosuj system aktualizacji jest przedstawiany jako zalecenia w usłudze Security Center. Jeśli komputera lub maszyny Wirtualnej, na których brakuje aktualizacji systemu, tego zalecenia zostanie wyświetlony w obszarze **zalecenia** i w obszarze **obliczenia**.  Wybranie zalecenie otwiera **Zastosuj aktualizacje systemu** pulpitu nawigacyjnego.

W tym przykładzie użyjemy **obliczenia**.

1. Wybierz **obliczenia** menu głównym usługi Security Center.

   ![Wybierz opcję Oblicz][1]

2. W obszarze **obliczenia**, wybierz opcję **brakujące aktualizacje systemu**. **Zastosuj aktualizacje systemu** zostanie otwarty pulpit nawigacyjny.

   ![Zastosuj pulpit nawigacyjny aktualizacji systemu][2]

   Udostępnia górnej części pulpitu nawigacyjnego:

    - Całkowita liczba Windows i maszyn wirtualnych systemu Linux oraz komputery z brakującymi aktualizacjami systemu.
    - Całkowita liczba aktualizacje krytyczne, brakuje w maszynach wirtualnych i komputerów.
    - Całkowita liczba aktualizacji zabezpieczeń na maszynach wirtualnych i komputerów.

  Dolnej części pulpitu nawigacyjnego Wyświetla wszystkie brakujące aktualizacje dla maszyn wirtualnych i komputerów oraz ważność brakujących aktualizacji.  Lista zawiera:

    - Nazwa: Nazwa brakującej aktualizacji.
    - NIE. Maszyn wirtualnych i komputerów: łączna liczba maszyn wirtualnych i komputerów, które mają tej aktualizacji.
    - Stan: Bieżący stan zalecenia:

      - Otwórz: Zalecenie nie rozpoczęto jeszcze wykonywania.
      - W toku: Zalecenie jest aktualnie stosowane do tych zasobów i jest wymagana żadna akcja.
      - Rozwiązano: Zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

    - Ważność: Opisuje ważność określonego zalecenia:

      - Wysoki: Luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (aplikacji, maszyny wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
      - Średni: Niekrytyczne lub dodatkowe kroki są wymagane w celu ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
      - Niski: Luki w zabezpieczeniach powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).

3. Wybierz brakujących aktualizacji na liście, aby wyświetlić szczegóły.

   ![Brak aktualizacji zabezpieczeń][3]

4. Wybierz **wyszukiwania** ikona na Wstążce najważniejsze.  Zapytanie wyszukiwania usługi Log Analytics spowoduje otwarcie filtrowane na komputerach brakujących aktualizacji.

   ![Wyszukiwania usługi log Analytics][4]

5. Wybierz komputer z listy, aby uzyskać więcej informacji. Zostanie otwarty inny wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

    ![Wyszukiwania usługi log Analytics][5]

## <a name="reboot-after-system-updates"></a>Uruchom ponownie po zaktualizowaniu systemu
1. Wróć do **zalecenia** bloku. Wygenerowano nowy wpis, po zastosowaniu aktualizacji systemu, nazywany **Uruchom ponownie po zaktualizowaniu systemu**. Ten wpis informuje o tym, że musisz ponownie uruchomić maszynę Wirtualną w celu ukończenia procesu stosowania aktualizacji systemu.

   ![Uruchom ponownie po zaktualizowaniu systemu][6]
2. Wybierz **Uruchom ponownie po zaktualizowaniu systemu**. Spowoduje to otwarcie **ponownego uruchomienia oczekuje na ukończenie aktualizacji systemu** bloku, wyświetlanie listy maszyn wirtualnych, które należy ponownie uruchomić, aby ukończyć Zastosuj system aktualizuje procesu.

   ![Oczekiwanie na ponowne uruchomienie][7]

Uruchom ponownie maszynę Wirtualną platformy Azure, aby ukończyć proces.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
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
