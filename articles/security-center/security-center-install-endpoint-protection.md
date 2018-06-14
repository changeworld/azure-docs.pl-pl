---
title: Rozwiązywanie problemów dotyczących ochrony punktu końcowego z Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywanie problemów dotyczących ochrony punktu końcowego w Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2017
ms.locfileid: "23936294"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Rozwiązywanie problemów dotyczących ochrony punktu końcowego z Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure służy do monitorowania stanu ochrony przed złośliwym kodem i to w raportach w obszarze bloku problemy ochrony punktu końcowego. Centrum zabezpieczeń zawiera wyróżnione zagadnienia, takie jak wykrytych zagrożeń i niewystarczającą ochroną, co może uniemożliwić maszynach wirtualnych (VM), a komputery narażony na zagrożenia ochrony przed złośliwym oprogramowaniem. Korzystając z informacji w obszarze **problemy z ochroną punktu końcowego**, można zidentyfikować plan, aby rozwiązać wszystkie zidentyfikowane problemy.

Centrum zabezpieczeń zgłasza następujące problemy z ochroną punktu końcowego:

- Program Endpoint protection nie jest zainstalowany na maszynach wirtualnych Azure — rozwiązanie obsługiwanych ochrony przed złośliwym oprogramowaniem nie jest zainstalowany na tych maszynach wirtualnych Azure.
- Program Endpoint protection nie jest zainstalowany na komputerach z systemem innym niż Azure — obsługiwanych ochrony przed złośliwym oprogramowaniem nie jest zainstalowany na tych komputerach innych niż Azure.
- Kondycji programu Endpoint protection:

   - Nieaktualny podpis — rozwiązanie chroniące przed złośliwym kodem jest zainstalowane na tych komputerach i maszyn wirtualnych, ale rozwiązanie nie ma najnowszej podpisy ochrony przed złośliwym oprogramowaniem.
   - Brak ochrony w czasie rzeczywistym — rozwiązanie chroniące przed złośliwym kodem jest zainstalowany na tych maszynach wirtualnych lub komputerach, ale nie jest skonfigurowany do ochrony w czasie rzeczywistym.   Usługi mogą być wyłączone lub Centrum zabezpieczeń może być nie można uzyskać stanu, ponieważ rozwiązanie nie jest obsługiwany. Zobacz [partnera integracji](security-center-partner-integration.md) listę obsługiwanych rozwiązania.
   - Raportowanie nie — rozwiązanie chroniące przed złośliwym kodem jest zainstalowany, ale nie zgłasza danych.
   - Nieznana — rozwiązanie chroniące przed złośliwym kodem jest zainstalowany, ale jego stan jest nieznany lub wykonywania raportu wystąpił nieznany błąd.

   > [!NOTE]
   > Zobacz [zintegrowanie rozwiązań zabezpieczeń](security-center-partner-integration.md#integrated-azure-security-solutions) listę rozwiązań zabezpieczeń do ochrony punktu końcowego zintegrowana z Centrum zabezpieczeń.
   >
   >

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
Problemy z ochroną punktu końcowego jest przedstawiany jako zalecenia w Centrum zabezpieczeń.  Jeśli dane środowisko jest narażony na zagrożenia ochrony przed złośliwym kodem, tego zalecenia zostanie wyświetlony w obszarze **zalecenia** i w obszarze **obliczeniowe**. Aby wyświetlić **problemy z ochroną punktu końcowego pulpitu nawigacyjnego**, należy wykonać obliczeń przepływ pracy.

W tym przykładzie używamy **obliczeniowe**.  Przedstawiono sposób instalowania ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure i na komputerach innych niż Azure.

## <a name="install-antimalware-on-azure-vms"></a>Zainstaluj ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure

1. Wybierz **obliczeniowe** w menu głównym Centrum zabezpieczeń lub **omówienie**.

   ![Wybierz obliczeń][1]

2. W obszarze **obliczeniowe**, wybierz pozycję **problemy z ochroną punktu końcowego**. **Problemy z ochroną punktu końcowego** otwiera pulpitu nawigacyjnego.

   ![Wybierz problemy z ochroną punktu końcowego][2]

   Udostępnia górnej części pulpitu nawigacyjnego:

   - Zainstalowanych dostawców ochrony punktu końcowego - list różnych dostawców zidentyfikowane przez Centrum zabezpieczeń.
   - Stan kondycji ochrony punktu końcowego zainstalowanych — przedstawia stan kondycji maszyn wirtualnych i komputerów, na których jest zainstalowane oprogramowanie ochrony punktu końcowego. Wykres pokazuje liczbę maszyn wirtualnych i komputerów, które są w dobrej kondycji i numer z niewystarczającą ochroną.
   - Wykryto — złośliwy kod pokazuje liczbę maszyn wirtualnych i komputerów, którym Centrum zabezpieczeń jest raportowania wykryto złośliwe oprogramowanie.
   - Zaatakowane komputery — pokazuje liczbę maszyn wirtualnych i komputerów, w którym Centrum zabezpieczeń jest raportowania ataków przez złośliwego.

   W dolnej części pulpitu nawigacyjnego znajduje się lista punktu końcowego problemy związane z ochroną, które zawiera następujące informacje:  

   - **Całkowita liczba** — liczba maszyn wirtualnych i komputerów dotyczy problem.
   - A paska agregowanie liczby maszyn wirtualnych i komputerów dotyczy problem. Kolory na pasku Określ priorytet:

      - Czerwony - o wysokim priorytecie i powinny być kierowane natychmiast
      - Kolor pomarańczowy — średni priorytet i powinny być kierowane jak najszybciej

3. Wybierz **programu Endpoint protection nie jest zainstalowany na maszynach wirtualnych Azure**.

   ![Wybierz program Endpoint protection nie jest zainstalowany na maszynach wirtualnych Azure][3]

4. W obszarze **programu Endpoint protection nie jest zainstalowany na maszynach wirtualnych Azure** znajduje się lista maszyn wirtualnych platformy Azure, które nie mają ochrony przed złośliwym kodem, zainstalować.  Można zainstalować na wszystkich maszynach wirtualnych na liście ochrony przed złośliwym kodem lub wybrać poszczególnych maszyn wirtualnych do zainstalowania ochrony przed złośliwym kodem na klikając określonej maszyny Wirtualnej.
5. W obszarze **wybierz program Endpoint protection**, wybierz rozwiązanie ochrony punktu końcowego, którego chcesz użyć. W tym przykładzie wybierz **Microsoft Antimalware**.
6. Dodatkowe informacje dotyczące funkcji ochrony punktów końcowych są wyświetlane. Wybierz pozycję **Utwórz**.

## <a name="install-antimalware-on-non-azure-computers"></a>Zainstaluj ochrony przed złośliwym kodem na komputerach z systemem innym niż Azure

1. Wróć do **problemy z ochroną punktu końcowego** i wybierz **programu Endpoint protection nie jest zainstalowany na komputerach z systemem innym niż Azure**.

   ![Wybierz program Endpoint protection nie jest zainstalowany na komputerach z systemem innym niż Azure][4]

2. W obszarze **programu Endpoint protection nie jest zainstalowany na komputerach z systemem innym niż Azure**, wybierz obszar roboczy. Filtrowane do obszaru roboczego analizy dzienników zapytania wyszukiwania zostanie otwarte i wyświetla listę komputerów bez ochrony przed złośliwym oprogramowaniem. Wybierz komputer z listy, aby uzyskać więcej informacji.

   ![Wyszukaj analizy dzienników][5]

Otwiera inny wynik wyszukiwania informacji filtrowane tylko dla tego komputera.

  ![Wyszukaj analizy dzienników][6]

> [!NOTE]
> Firma Microsoft zaleca udostępniane programu endpoint protection dla wszystkich maszyn wirtualnych i komputerów ułatwić identyfikację oraz usunięcie wirusów, programów szpiegujących i innego złośliwego oprogramowania.
>
>

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób wykonania zalecenia Centrum zabezpieczeń "Zainstaluj program Endpoint Protection". Aby dowiedzieć się więcej na temat włączania Antimalware firmy Microsoft na platformie Azure, zobacz następujący dokument:

* [Antimalware firmy Microsoft dla usługi w chmurze i maszyn wirtualnych](../security/azure-security-antimalware.md) — Dowiedz się, jak wdrożyć Antimalware firmy Microsoft.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, można znaleźć w następujących dokumentach:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
