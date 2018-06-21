---
title: Korygowanie konfiguracjach zabezpieczeń w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument zawiera implementowania zalecenia Centrum zabezpieczeń Azure, "Skoryguj konfiguracje zabezpieczeń".
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2018
ms.author: terrylan
ms.openlocfilehash: 3af8f211c19fde9d2fc79f41fc13009570a9b4de
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285920"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Korygowanie konfiguracjach zabezpieczeń w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure codziennie analizuje systemu operacyjnego (OS), maszynach wirtualnych (VM) i na komputerach w konfiguracji, który można utworzyć maszyny wirtualne i bardziej narażony na ataki. Centrum zabezpieczeń zaleca Rozwiąż luk w zabezpieczeniach w przypadku konfiguracji systemu operacyjnego jest niezgodny z reguły konfiguracji zabezpieczeń i zaleca zmiany konfiguracji, aby rozwiązać te luki w zabezpieczeniach.

Aby uzyskać więcej informacji o konkretnych konfiguracji, które są monitorowane, zobacz [lista reguł zalecanych konfiguracji](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Aby dowiedzieć się, jak dostosować oceny konfiguracji zabezpieczeń, zobacz [dostosowanie systemu operacyjnego konfiguracjach zabezpieczeń w Centrum zabezpieczeń Azure (wersja zapoznawcza)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
"Skorygować konfiguracjach zabezpieczeń" zostaje przedstawiony jako zalecenia w Centrum zabezpieczeń. Zalecane jest wyświetlana w obszarze **zalecenia** > **obliczeniowe**.

W tym przykładzie przedstawiono zalecenia "Skorygować konfiguracjach zabezpieczeń" w obszarze **obliczeniowe**.
1. W Centrum zabezpieczeń, w okienku po lewej stronie wybierz **obliczeniowe**.  
  **Obliczeniowe** zostanie otwarte okno.

   ![Korygowanie konfiguracji zabezpieczeń][1]

2. Wybierz **skorygować konfiguracjach zabezpieczeń**.  
  **Konfiguracjach zabezpieczeń** zostanie otwarte okno.

   ![Okno "Konfiguracjach zabezpieczeń"][2]

  Górnej części pulpitu nawigacyjnego przedstawia:

  - **Nie powiodło się reguły według ważności**: Całkowita liczba reguł, że konfiguracja systemu operacyjnego nie powiodła się dla maszyn wirtualnych i komputerów, podzielone według ważności.
  - **Nie powiodło się reguły według typu**: Całkowita liczba reguł, że konfiguracja systemu operacyjnego nie powiodła się dla maszyn wirtualnych i komputerów, podzielone według typu.
  - **Nie powiodło się zasady Windows**: łączna liczba reguł nie na podstawie konfiguracji systemu operacyjnego Windows.
  - **Nie powiodło się reguły Linux**: łączna liczba reguł nie na podstawie konfiguracji systemu operacyjnego Linux.

  Dolnej części pulpitu nawigacyjnego zawiera listę wszystkich reguł nie powiodło się dla maszyn wirtualnych, komputerów i ważności brakujących aktualizacji. Lista zawiera następujące elementy:

  - **CCEID**: CCE Unikatowy identyfikator reguły. Centrum zabezpieczeń używane typowych konfiguracji wyliczenie CCE () do przypisywania unikatowych identyfikatorów do reguły konfiguracji.
  - **Nazwa**: Nazwa reguły nie powiodło się.
  - **Typ reguły**: *klucza rejestru*, *zasady zabezpieczeń*, *zasady inspekcji*, lub *IIS* typ reguły.
  - **Nie. maszyny wirtualne i komputerach**: Całkowita liczba maszyn wirtualnych i komputery, których dotyczy reguła nie powiodło się.
  - **Reguły ważność**: wartość CCE *krytyczny*, *ważne*, lub *ostrzeżenie*.
  - **Stan**: bieżący stan zalecenia:

    - **Otwarte**: nie rozpoczęto jeszcze wykonywania zalecenia.
    - **Trwa**: zalecenie jest aktualnie stosowane do zasobów i jest wymagana żadna akcja.
    - **Rozwiązane**: zalecenia zostały zastosowane. Gdy problem zostanie rozwiązany, wpis jest niedostępna.

3. Aby wyświetlić szczegóły reguły nie powiodło się, wybierz go z listy.

   ![Widok szczegółowy reguły konfiguracji nie powiodło się][3]

   W widoku szczegółowym wyświetlane następujące informacje:

   - **Nazwa**: Nazwa reguły.
   - **CCIED**: CCE Unikatowy identyfikator reguły.
   - **Wersja systemu operacyjnego**: wersja systemu operacyjnego maszyny Wirtualnej lub komputera.
   - **Reguły ważność**: wartość CCE *krytyczny*, *ważne*, lub *ostrzeżenie*.
   - **Pełny opis**: opis reguły.
   - **Luki w zabezpieczeniach**: wyjaśnienie usterki lub ryzyko, gdy nie jest stosowana reguła.
   - **Potencjalny wpływ**: znaczenie biznesowe, po zastosowaniu reguły.
   - **Przeciwdziałanie**: czynności korygujące.
   - **Oczekiwana wartość**: wartość, która jest oczekiwana w przypadku Centrum zabezpieczeń analizuje względem reguły konfiguracji systemu operacyjnego maszyny Wirtualnej.
   - **Bieżąca wartość**: wartość, która jest zwracana po analizie względem reguły konfiguracji systemu operacyjnego maszyny Wirtualnej.
   - **Reguły operacji**: operacji reguły, która jest używana przez Centrum zabezpieczeń podczas analizy konfiguracji systemu operacyjnego maszyny Wirtualnej względem reguły.

4. W górnej części okna szczegółowy widok, wybierz **wyszukiwania**.  
  Wyszukiwanie Otwiera listę obszarów roboczych, które mają maszyn wirtualnych i komputerów z niezgodność konfiguracji wybranego zabezpieczeń. Wybór obszaru roboczego jest wyświetlany tylko jeśli wybrana reguła dotyczy wiele maszyn wirtualnych, które są podłączone do różnych obszarów roboczych.

   ![Obszary robocze wymienione][4]

5. Wybierz obszar roboczy.  
  Zapytania wyszukiwania analizy dzienników otwiera filtrowane do obszaru roboczego z niezgodność konfiguracji zabezpieczeń.

   ![Obszar roboczy z luk w zabezpieczeniach systemu operacyjnego][5]

6. Wybierz komputer, na liście.  
  Otwiera nowy wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

   ![Szczegółowe informacje o wybranym komputerze][6]

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wykonania zalecenia Centrum zabezpieczeń "Skoryguj konfiguracje zabezpieczeń". Aby dowiedzieć się, jak dostosować oceny konfiguracji zabezpieczeń, zobacz [dostosowanie systemu operacyjnego konfiguracjach zabezpieczeń w Centrum zabezpieczeń Azure (wersja zapoznawcza)](security-center-customize-os-security-config.md).

Aby przejrzeć konkretnych konfiguracji, które są monitorowane, zobacz [lista reguł zalecanych konfiguracji](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centrum zabezpieczeń używane typowych konfiguracji wyliczenie CCE () do przypisywania unikatowych identyfikatorów do reguły konfiguracji. Aby uzyskać więcej informacji, przejdź do [CCE](https://nvd.nist.gov/cce/index.cfm) lokacji.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące zasoby:

* Aby uzyskać listę obsługiwanych systemu Windows i maszyn wirtualnych systemu Linux, zobacz [obsługiwanych platform w Centrum zabezpieczeń Azure](security-center-os-coverage.md).
* Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów, zobacz [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md).
* Aby dowiedzieć się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md).
* Informacje na temat monitorowania kondycji zasobów platformy Azure, zobacz [monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md).
* Informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi, zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md).
* Aby dowiedzieć się, jak monitorować stan kondycji rozwiązań partnerskich, zobacz [monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md).
* Aby uzyskać odpowiedzi na często zadawane pytania dotyczące korzystania z usługi, zobacz [często zadawane pytania dotyczące usługi Azure Security Center](security-center-faq.md).
* Aby ogłasza blogu dotyczące zabezpieczeń platformy Azure i zgodności, zobacz [blog Azure Security](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
