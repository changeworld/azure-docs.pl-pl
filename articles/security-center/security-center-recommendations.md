---
title: Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób zalecenia w usłudze Azure Security Center ułatwiają ochronę zasobów platformy Azure i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905946"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center
W tym dokumencie przedstawiono sposób korzystania z zaleceń Centrum zabezpieczeń Azure, aby pomóc w ochronie Twoich zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

## <a name="implementing-security-recommendations"></a>Wdrażanie zaleceń dotyczących zabezpieczeń
### <a name="set-recommendations"></a>Zestaw zaleceń
W [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md), Dowiedz się, jak:

* Podczas konfigurowania zasad zabezpieczeń.
* Włącz zbieranie danych.
* Wybierz, które zalecenia, aby zobaczyć jako część zasad zabezpieczeń.

Bieżący Centrum zalecenia dotyczące zasad wokół aktualizacji systemu, reguły linii bazowej, programy chroniące przed złośliwym kodem [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) podsieci i interfejsów sieciowych, inspekcji usługi SQL database, SQL database technologii transparent data encryption, i zapory aplikacji sieci web.  [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) zawiera opis każdej opcji zalecenia.

### <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania
Po skonfigurowaniu zasad zabezpieczeń usługa Security Center analizuje stan zabezpieczeń zasobów w celu identyfikowania potencjalnych luk w zabezpieczeniach. **Zalecenia** kafelka w obszarze **Przegląd** informuje Cię łączną liczbę zaleceniami zostały zidentyfikowane przez usługę Security Center.

![Zalecenia dotyczące kafelków][1]

Aby wyświetlić szczegóły każde zalecenie, wybierz **Kafelek zalecenia** w obszarze **Przegląd**. **Zalecenia dotyczące** zostanie otwarty.

![Zalecenia dotyczące filtru][2]

Można filtrować zalecenia. Aby filtrować zalecenia, wybierz **filtru** na **zalecenia** bloku. **Filtru** zostanie otwarty blok, a następnie wybierz ważność i stan wartości, które chcesz wyświetlić.


* **ZALECENIA DOTYCZĄCE**: Zalecenie.
* **ZABEZPIECZANIE OCENĘ WPŁYWU**:
* **ZASÓB**: Wyświetla listę zasobów, do których zostanie zastosowana tego zalecenia.
* **PASKI STANU**:  Opisuje ważność określonego zalecenia:
   * **Wysoki (czerwony)**: Luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (np. aplikacji, maszyny Wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
   * **Średni (kolor pomarańczowy)**: Istnieje luka w zabezpieczeniach i niekrytyczne lub dodatkowe kroki są wymagane, aby wyeliminować go lub do ukończenia procesu.
   * **Niski (niebieski)**: Istnieje luka w zabezpieczeniach, które powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale można filtrować według zalecenia o niskiej ważności, jeśli chcesz je zobaczyć). 
   * **Zdrowy (zielony)**:
   * **Niedostępny (szary)**:
 


> [!NOTE]
> Należy zrozumieć [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
> 
> 
> ### <a name="apply-recommendations"></a>Stosowanie zalecenia
> Po zapoznaniu się z wszystkich zaleceń decyzję, który co należy najpierw zastosować. Firma Microsoft zaleca używanie ocenę ważności, ponieważ główny parametr do oceny, zalecenia, które powinny być stosowane najpierw.



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie Przedstawiliśmy zalecenia dotyczące zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
