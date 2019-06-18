---
title: Skoryguj konfiguracje zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie pokazano, jak zaimplementować zalecenia usługi Azure Security Center, "Skoryguj konfiguracje zabezpieczeń".
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: f4558c6fdb1e5e4f0ffb7a4b4fdb1ab62eb4cfa9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332996"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Skoryguj konfiguracje zabezpieczeń w usłudze Azure Security Center
Usługa Azure Security Center analizuje codziennie systemu operacyjnego (OS) maszyn wirtualnych (VM) i konfigurację, która może spowodować, że maszyny wirtualne na komputerach i bardziej narażone na ataki. Usługa Security Center zaleca Rozwiąż luk w zabezpieczeniach w przypadku konfiguracji systemu operacyjnego jest niezgodny z reguły konfiguracji zabezpieczeń zaleca się zmiany tych luk w konfiguracji.

Aby uzyskać więcej informacji o konkretnych konfiguracji, które są monitorowane, zobacz [listę zalecanych reguł konfiguracji](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Aby dowiedzieć się, jak dostosować oceny konfiguracji zabezpieczeń, zobacz [Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego w usłudze Azure Security Center (wersja zapoznawcza)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
"Skoryguj konfiguracje zabezpieczeń" jest przedstawiany jako zalecenia w usłudze Security Center. Zalecenie jest wyświetlana w obszarze **zalecenia** > **obliczenia i aplikacje**.

W tym przykładzie opisano zalecenie "Skoryguj konfiguracje zabezpieczeń" w obszarze **obliczenia i aplikacje**.
1. W usłudze Security Center w okienku po lewej stronie wybierz **obliczenia i aplikacje**.  
   **Obliczenia i aplikacje** zostanie otwarte okno.

   ![Korygowanie konfiguracji zabezpieczeń][1]

2. Wybierz **skoryguj konfiguracje zabezpieczeń**.  
   **Konfiguracje zabezpieczeń** zostanie otwarte okno.

   ![W oknie "Konfiguracje zabezpieczeń"][2]

   Górna sekcja przedstawia pulpit nawigacyjny:

   - **Niespełnione reguły według ważności**: Całkowita liczba reguł, że konfiguracja systemu operacyjnego nie powiodło się dla maszyn wirtualnych i komputerów, podzielone według ważności.
   - **Niespełnione reguły według typu**: Całkowita liczba reguł, że konfiguracja systemu operacyjnego nie powiodło się dla maszyn wirtualnych i komputerów, podzielone według typu.
   - **Niespełnione reguły Windows**: Łączna liczba reguł, nie na podstawie konfiguracji systemu operacyjnego Windows.
   - **Niespełnione reguły Linux**: Łączna liczba reguł, nie na podstawie konfiguracji systemu operacyjnego Linux.

   W dolnej części pulpitu nawigacyjnego Wyświetla listę wszystkich reguł nie powiodło się dla maszyn wirtualnych i komputerów oraz ważność brakujących aktualizacji. Lista zawiera następujące elementy:

   - **CCEID**: CCE Unikatowy identyfikator dla tej reguły. Usługa Security Center używa Common Configuration Enumeration (CCE) do przypisywania unikatowych identyfikatorów dla reguły konfiguracji.
   - **Nazwa**: Nazwa reguły nie powiodło się.
   - **Typ reguły**: *Klucza rejestru*, *zasady zabezpieczeń*, *zasady inspekcji*, lub *IIS* typ reguły.
   - **Nie. maszyn wirtualnych i komputerów**: Całkowita liczba maszyn wirtualnych i komputerów, których dotyczy reguła z błędem.
   - **Ważność reguły**: Wartość CCE *krytyczny*, *ważne*, lub *ostrzeżenie*.
   - **Stan**: Bieżący stan zalecenia:

     - **Otwórz**: Zalecenie nie rozpoczęto jeszcze wykonywania.
     - **Trwającą**: Zalecenie jest aktualnie stosowane do zasobów i jest wymagana żadna akcja.
     - **Rozwiązane**: Zalecenia zostały zastosowane. Gdy problem zostanie rozwiązany, wpis jest wyszarzony.

3. Aby wyświetlić szczegóły reguły nie powiodło się, wybierz go z listy.

   ![Szczegółowy widok reguł konfiguracji nie powiodło się][3]

   W widoku szczegółowym wyświetlane następujące informacje:

   - **Nazwa**: Nazwa reguły.
   - **CCIED**: CCE Unikatowy identyfikator dla tej reguły.
   - **Wersja systemu operacyjnego**: Wersja systemu operacyjnego maszyny Wirtualnej lub komputera.
   - **Ważność reguły**: Wartość CCE *krytyczny*, *ważne*, lub *ostrzeżenie*.
   - **Pełny opis**: Opis reguły.
   - **Luki w zabezpieczeniach**: Opis luki w zabezpieczeniach lub ryzyko, gdy nie jest stosowana reguła.
   - **Potencjalny wpływ**: Znaczenie biznesowe, gdy reguła jest stosowana.
   - **Przeciwdziałanie**: Kroki korygowania.
   - **Oczekiwana wartość**: Wartość, która oczekuje się, gdy usługa Security Center analizuje konfigurację systemu operacyjnego maszyny Wirtualnej względem reguły.
   - **Wartość rzeczywista**: Wartość zwracana po przeprowadzeniu analizy konfiguracji systemu operacyjnego maszyny Wirtualnej względem reguły.
   - **Reguły operacji**: Operacja reguły, która jest używana przez usługę Security Center podczas analizy konfiguracji systemu operacyjnego maszyny Wirtualnej względem reguły.

4. W górnej części okna w widoku szczegółowym wybierz **wyszukiwania**.  
   Wyszukiwanie Otwiera listę obszarów roboczych, które mają maszyny wirtualne i komputery z niezgodności konfiguracji zabezpieczeń wybrane. Wybór obszaru roboczego jest wyświetlany tylko jeśli wybrana reguła ma zastosowanie do wielu maszyn wirtualnych, które są podłączone do różnych obszarów roboczych.

   ![Uwzględnione na liście obszarów roboczych][4]

5. Wybierz obszar roboczy.  
   Zapytanie wyszukiwania dzienników usługi Azure Monitor otwiera filtrowane do obszaru roboczego z niezgodności konfiguracji zabezpieczeń.

   ![Obszar roboczy z luk w zabezpieczeniach systemu operacyjnego][5]

6. Wybierz komputer, na liście.  
   Zostanie otwarty nowy wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

   ![Szczegółowe informacje o wybranym komputerze][6]

## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano sposób implementacji zalecenia usługi Security Center "Skoryguj konfiguracje zabezpieczeń". Aby dowiedzieć się, jak dostosować oceny konfiguracji zabezpieczeń, zobacz [Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego w usłudze Azure Security Center (wersja zapoznawcza)](security-center-customize-os-security-config.md).

Aby zapoznać się z określonych konfiguracji, które są monitorowane, zobacz [listę zalecanych reguł konfiguracji](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Usługa Security Center używa Common Configuration Enumeration (CCE) do przypisywania unikatowych identyfikatorów dla reguły konfiguracji. Aby uzyskać więcej informacji, przejdź do [CCE](https://nvd.nist.gov/cce/index.cfm) lokacji.

Aby dowiedzieć się więcej o usłudze Security Center, zobacz następujące zasoby:

* Aby uzyskać listę obsługiwanych maszyn wirtualnych systemu Linux i Windows, zobacz [obsługiwanych platform w usłudze Azure Security Center](security-center-os-coverage.md).
* Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md).
* Aby dowiedzieć się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).
* Informacje na temat monitorowania kondycji zasobów platformy Azure, zobacz [monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md).
* Aby dowiedzieć się, jak zarządzać i reagować na alerty zabezpieczeń, zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md).
* Informacje na temat monitorowania stanu kondycji rozwiązań partnerskich, zobacz [monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md).
* Aby uzyskać odpowiedzi na często zadawane pytania dotyczące korzystania z usługi, zobacz [często zadawane pytania dotyczące usługi Azure Security Center](security-center-faq.md).
* Aby uzyskać wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności, zobacz [blog Azure Security](https://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
