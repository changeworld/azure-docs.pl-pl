---
title: Zarządzanie problemy z ochroną punktu końcowego w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać problemy z ochroną punktu końcowego w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 882d4e0592b74e8af30ff5bf110a41e403c3bf7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789655"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Zarządzanie problemy z ochroną punktu końcowego w usłudze Azure Security Center
Usługa Azure Security Center monitoruje stan ochrony przed złośliwym kodem i to w raportach w obszarze bloku problemy dotyczące ochrony punktu końcowego. Usługa Security Center wyróżnia problemy, takie jak wykrytych zagrożeń i ochronę niewystarczające, co może narazić Twoje maszyny wirtualne (VM) i komputerów na zagrożenia ochrony przed złośliwym oprogramowaniem. Korzystając z informacji podanych w ramach **problemy z ochroną punktu końcowego**, można zidentyfikować plan, aby rozwiązać wszystkie zidentyfikowane problemy.

Usługa Security Center raportów następujące problemy związane z ochroną punktu końcowego:

- Program Endpoint protection nie jest zainstalowany na maszynach wirtualnych platformy Azure — rozwiązania obsługiwanych ochrony przed złośliwym kodem nie jest zainstalowany na tych maszynach wirtualnych platformy Azure.
- Program Endpoint protection nie jest zainstalowany na komputerach nienależących do platformy Azure — obsługiwane ochrony przed złośliwym kodem nie jest zainstalowany na tych komputerach nienależących do platformy Azure.
- Kondycji programu Endpoint protection:

  - Nieaktualny podpis — rozwiązanie do ochrony przed złośliwym kodem jest zainstalowany na tych maszynach wirtualnych i komputerów, ale to rozwiązanie nie ma najnowszych sygnatur złośliwego oprogramowania.
  - Brak ochrony w czasie rzeczywistym — rozwiązanie do ochrony przed złośliwym kodem jest zainstalowany na tych maszynach wirtualnych i komputerów, ale nie została ona skonfigurowana do ochrony w czasie rzeczywistym.   Usługi mogą być wyłączone lub Centrum zabezpieczeń może być nie można uzyskać stanu, ponieważ rozwiązania nie jest obsługiwany. Zobacz [Integracja z partnerami](security-center-os-coverage.md#supported-endpoint-protection-solutions) Aby uzyskać listę obsługiwanych rozwiązań.
  - Nie zgłasza — rozwiązanie do ochrony przed złośliwym kodem jest zainstalowany, ale nie zgłasza danych.
  - Nieznany — rozwiązanie do ochrony przed złośliwym kodem jest zainstalowany, ale jego stan jest nieznany lub raportowania wystąpił nieznany błąd.

    > [!NOTE]
    > Zobacz [integracja rozwiązań zabezpieczeń](security-center-os-coverage.md#supported-endpoint-protection-solutions) listę rozwiązań zabezpieczeń ochrony punktów końcowych zintegrowane z usługą Security Center.
    >
    >

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
Problemy z ochroną punktu końcowego jest przedstawiany jako zalecenia w usłudze Security Center.  Jeśli środowisko jest narażony na zagrożenia ochrony przed złośliwym oprogramowaniem, tego zalecenia zostanie wyświetlona w **zalecenia** i w obszarze **obliczenia**. Aby wyświetlić **programu Endpoint protection generuje pulpit nawigacyjny**, należy wykonać obliczeń przepływu pracy.

W tym przykładzie użyjemy **obliczenia**.  Przedstawiony zostanie sposób instalowania ochrony przed złośliwym oprogramowaniem na maszynach wirtualnych platformy Azure i na komputerach nienależących do platformy Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instalowanie ochrony przed złośliwym oprogramowaniem na maszynach wirtualnych platformy Azure

1. Wybierz **obliczenia i aplikacje** menu głównym usługi Security Center lub **Przegląd**.

   ![Select Compute][1]

2. W obszarze **obliczenia**, wybierz opcję **problemy z ochroną punktu końcowego**. **Problemy z ochroną punktu końcowego** zostanie otwarty pulpit nawigacyjny.

   ![Wybierz problemy z ochroną punktu końcowego][2]

   Udostępnia górnej części pulpitu nawigacyjnego:

   - Zainstalowanych dostawców ochrony punktu końcowego — listy różnych dostawców identyfikowane przez usługę Security Center.
   - Zainstalowano program endpoint protection kondycja — pokazuje stan kondycji maszyn wirtualnych i komputerów, na których jest zainstalowane oprogramowanie ochrony punktu końcowego. Wykres przedstawia liczbę maszyn wirtualnych i komputerów, które są w dobrej kondycji oraz numer z niewystarczającą ochroną.
   - Wykryto — złośliwe oprogramowanie zawiera liczbę maszyn wirtualnych i komputerów, których usługa Security Center jest raportowanie wykrytego złośliwego oprogramowania.
   - Zaatakowane komputery — pokazuje liczbę maszyn wirtualnych i komputerów, których usługa Security Center jest raportowanie ataków przez złośliwe oprogramowanie.

   W dolnej części pulpitu nawigacyjnego znajduje się lista punktu końcowego problemy związane z ochroną, które zawiera następujące informacje:  

   - **Łączna liczba** — liczba maszyn wirtualnych i komputerów dotyczy problem.
   - Element paska agregowania liczbę maszyn wirtualnych i komputerów dotyczy problem. Kolory na pasku określenie priorytetu:

      - Czerwony - o wysokim priorytecie i powinien być kierowany od razu
      - Pomarańczowy — średni priorytet i należy rozwiązać jak najszybciej

3. Wybierz **Endpoint protection nie jest zainstalowana na maszynach wirtualnych Azure**.

   ![Wybór ochrony punktu końcowego nie jest zainstalowany na maszynach wirtualnych platformy Azure][3]

4. W obszarze **Endpoint protection nie jest zainstalowana na maszynach wirtualnych Azure** znajduje się lista maszyn wirtualnych platformy Azure, bez ochrony przed złośliwym kodem zainstalowane.  Istnieje możliwość zainstalowania ochrony przed złośliwym kodem na wszystkich maszynach wirtualnych, na liście lub wybierz poszczególne maszyny wirtualne, aby zainstalować ochrony przed złośliwym kodem na, klikając określonej maszyny Wirtualnej.
5. W obszarze **wybierz program Endpoint protection**, wybierz rozwiązanie ochrony punktu końcowego, którego chcesz użyć. W tym przykładzie wybierz **Microsoft Antimalware**.
6. Dodatkowe informacje na temat rozwiązania do ochrony punktu końcowego jest wyświetlany. Wybierz pozycję **Utwórz**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalowanie ochrony przed złośliwym kodem na komputerach nienależących do platformy Azure

1. Wróć do **problemy z ochroną punktu końcowego** i wybierz **programu Endpoint protection nie jest zainstalowany na komputerach nienależących do platformy Azure**.

   ![Wybór ochrony punktu końcowego nie jest zainstalowany na komputerach nienależących do platformy Azure][4]

2. W obszarze **programu Endpoint protection nie jest zainstalowany na komputerach nienależących do platformy Azure**, wybierz obszar roboczy. Zapytanie wyszukiwania dzienników dla usługi Azure Monitor filtrowane do obszaru roboczego otwiera się i wyświetla komputery bez ochrony przed złośliwym oprogramowaniem. Wybierz komputer z listy, aby uzyskać więcej informacji.

   ![Usługa Azure Monitor dzienniki wyszukiwania][5]

Zostanie otwarty inny wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

  ![Usługa Azure Monitor dzienniki wyszukiwania][6]

> [!NOTE]
> Firma Microsoft zaleca udostępniane programu endpoint protection dla wszystkich maszyn wirtualnych i komputerów ułatwić identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania.
>
>

## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano sposób implementacji zalecenia usługi Security Center "Zainstaluj program Endpoint Protection". Aby dowiedzieć się więcej na temat włączania Microsoft Antimalware na platformie Azure, zobacz następujący dokument:

* [Microsoft Antimalware dla usług Cloud Services i Virtual Machines](../security/azure-security-antimalware.md) — informacje o wdrażaniu Microsoft Antimalware.

Aby dowiedzieć się więcej o usłudze Security Center, zobacz następujące dokumenty:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
