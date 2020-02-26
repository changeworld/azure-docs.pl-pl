---
title: Zarządzanie problemami z programem Endpoint Protection w Azure Security Center | Microsoft Docs
description: Informacje o monitorowaniu programu Endpoint Protection Security Center i sposobach rozwiązywania wszelkich powstających problemów.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: memildin
ms.openlocfilehash: e1ed403babe66b465fb1800dc8c5a90c7a8f1a08
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604115"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Zarządzanie problemami z programem Endpoint Protection przy użyciu Azure Security Center
Azure Security Center monitoruje stan ochrony przed złośliwym kodem i raportuje ją na stronie problemów z programem Endpoint Protection. Security Center wyróżnia problemy, takie jak wykryte zagrożenia i niewystarczająca ochrona, które mogą sprawić, że maszyny wirtualne i komputery są narażone na ataki chroniące przed złośliwym kodem. Korzystając z informacji w obszarze **problemy z programem Endpoint Protection**, można zidentyfikować plan pozwalający rozwiązać wszelkie zidentyfikowane problemy.

Security Center zgłasza następujące problemy z programem Endpoint Protection:

- Program Endpoint Protection nie został zainstalowany na maszynach wirtualnych platformy Azure — obsługiwane rozwiązanie chroniące przed złośliwym kodem nie jest zainstalowane na tych maszynach wirtualnych platformy Azure.
- Program Endpoint Protection nie jest zainstalowany na komputerach spoza platformy Azure — nie zainstalowano obsługiwanego oprogramowania chroniącego przed złośliwym kodem na komputerach spoza platformy Azure.
- Kondycja programu Endpoint Protection:

  - Nieaktualny podpis — na tych maszynach wirtualnych i komputerach jest zainstalowane rozwiązanie chroniące przed złośliwym kodem, ale rozwiązanie nie ma najnowszych podpisów chroniących przed złośliwym kodem.
  - Brak ochrony w czasie rzeczywistym — na tych maszynach wirtualnych i komputerach jest zainstalowane rozwiązanie chroniące przed złośliwym kodem, ale nie jest ono skonfigurowane do ochrony w czasie rzeczywistym. Usługa może być wyłączona lub Security Center może nie być w stanie uzyskać stanu, ponieważ rozwiązanie nie jest obsługiwane. Listę obsługiwanych rozwiązań można znaleźć w temacie [integracja z partnerem](security-center-services.md#endpoint-supported) .
  - Nieraportowanie — rozwiązanie chroniące przed złośliwym kodem jest instalowane, ale nie zgłasza danych.
  - Nieznane — jest zainstalowane rozwiązanie chroniące przed złośliwym kodem, ale jego stan jest nieznany lub zgłasza nieznany błąd.

    > [!NOTE]
    > Zobacz [integrowanie rozwiązań zabezpieczeń](security-center-services.md#endpoint-supported) , aby zapoznać się z listą rozwiązań zabezpieczeń programu Endpoint Protection zintegrowanych z Security Center.
    >
    >

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
Problemy z programem Endpoint Protection są przedstawione jako zalecenie w Security Center. Jeśli środowisko jest narażone na zagrożenia przed złośliwym kodem, to zalecenie zostanie wyświetlone w obszarze **rekomendacje** i w obszarze **obliczenia**. Aby wyświetlić **pulpit nawigacyjny problemów z programem Endpoint Protection**, należy postępować zgodnie z przepływem pracy obliczeniowej.

W tym przykładzie będziemy używać **obliczeń**.  Dowiesz się, jak zainstalować oprogramowanie chroniące przed złośliwym kodem na maszynach wirtualnych platformy Azure oraz na komputerach spoza platformy Azure.

## <a name="install-antimalware-on-azure-vms"></a>Zainstaluj złośliwe oprogramowanie na maszynach wirtualnych platformy Azure

1. Wybierz pozycję **obliczenia & aplikacje** w menu głównym Security Center lub **Przegląd**.

   ![Wybierz pozycję obliczenia][1]

2. W obszarze **Obliczanie**wybierz pozycję problemy z programem **Endpoint Protection**. Zostanie otwarty pulpit nawigacyjny problemów z programem **Endpoint Protection** .

   ![Wybieranie problemów z programem Endpoint Protection][2]

   W górnej części pulpitu nawigacyjnego dostępne są następujące informacje:

   - Zainstalowani dostawcy programu Endpoint Protection — wyświetla listę różnych dostawców identyfikowanych przez Security Center.
   - Zainstalowany stan kondycji programu Endpoint Protection — pokazuje stan kondycji maszyn wirtualnych i komputerów, na których zainstalowano rozwiązanie Endpoint Protection. Wykres pokazuje liczbę maszyn wirtualnych i komputerów, które są w dobrej kondycji i liczbę z niewystarczającą ochroną.
   - Wykryto złośliwe oprogramowanie — pokazuje liczbę maszyn wirtualnych i komputerów, na których Security Center zgłasza wykryte złośliwe oprogramowanie.
   - Komputery atakowane — pokazuje liczbę maszyn wirtualnych i komputerów, na których Security Center zgłasza ataki przez złośliwe oprogramowanie.

   W dolnej części pulpitu nawigacyjnego znajduje się lista problemów z programem Endpoint Protection, które obejmują następujące informacje:  

   - **Łącznie** — liczba maszyn wirtualnych i komputerów, na które problem ma wpływ.
   - Pasek agregowania liczby maszyn wirtualnych i komputerów, których dotyczy problem. Kolory na pasku identyfikują priorytet:

      - Czerwony — wysoki priorytet i powinien zostać natychmiast rozkierowany
      - Pomarańczowy — średni priorytet i powinien zostać rozkierowany jak najszybciej

3. Wybierz opcję program **Endpoint Protection nie jest zainstalowany na maszynach wirtualnych platformy Azure**.

   ![Wybierz program Endpoint Protection nie jest zainstalowany na maszynach wirtualnych platformy Azure][3]

4. Program **Endpoint Protection nie został zainstalowany na maszynach wirtualnych platformy Azure** to lista maszyn wirtualnych platformy Azure, na których nie zainstalowano ochrony przed złośliwym oprogramowaniem.  Możesz zdecydować się na zainstalowanie oprogramowania chroniącego przed złośliwym kodem na wszystkich maszynach wirtualnych na liście lub wybrać poszczególne maszyny wirtualne w celu zainstalowania ochrony przed złośliwym kodem, klikając konkretną maszynę wirtualną.
5. W obszarze **Wybierz program Endpoint Protection**wybierz rozwiązanie Endpoint Protection, którego chcesz użyć. W tym przykładzie wybierz pozycję **Microsoft chroniące przed złośliwym oprogramowaniem**.
6. Wyświetlane są dodatkowe informacje o rozwiązaniu Endpoint Protection. Wybierz pozycję **Utwórz**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalowanie oprogramowania chroniącego przed złośliwym kodem na komputerach spoza platformy Azure

1. Wróć do problemów z programem **Endpoint Protection** i wybierz opcję **Endpoint Protection nie jest zainstalowana na komputerach spoza platformy Azure**.

   ![Wybierz program Endpoint Protection nie został zainstalowany na komputerach spoza platformy Azure][4]

2. W obszarze program **Endpoint Protection nie został zainstalowany na komputerach spoza platformy Azure**, wybierz obszar roboczy. Zostanie otwarte zapytanie wyszukiwania w dzienniku Azure Monitor przefiltrowane do obszaru roboczego i lista komputerów, na których brakuje złośliwego oprogramowania. Wybierz komputer z listy, aby uzyskać więcej informacji.

   ![Wyszukiwanie dzienników Azure Monitor][5]

Zostanie otwarty inny wynik wyszukiwania z informacjami przefiltrowanymi tylko dla tego komputera.

  ![Wyszukiwanie dzienników Azure Monitor][6]

> [!NOTE]
> Zalecamy, aby program Endpoint Protection został zainicjowany dla wszystkich maszyn wirtualnych i komputerów w celu ułatwienia identyfikowania i usuwania wirusów, programów szpiegujących i innego złośliwego oprogramowania.
>
>

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób implementacji zalecenia Security Center "Install Endpoint Protection". Aby dowiedzieć się więcej na temat włączania ochrony przed złośliwym oprogramowaniem firmy Microsoft na platformie Azure, zobacz następujący dokument:

* [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md) — informacje na temat wdrażania oprogramowania chroniącego przed złośliwym oprogramowaniem firmy Microsoft.

Aby dowiedzieć się więcej na temat Security Center, zobacz następujące dokumenty:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) --Dowiedz się, jak skonfigurować zasady zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
