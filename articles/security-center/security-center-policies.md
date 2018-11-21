---
title: Ustawianie zasad zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten artykuł zawiera informacje pomocne podczas konfigurowania zasad zabezpieczeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: 2be1f6ef5a10e23888f31c68c66b804f0be49632
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275320"
---
# <a name="set-security-policies-in-azure-security-center"></a>Ustawianie zasad zabezpieczeń w usłudze Azure Security Center
Ten artykuł zawiera informacje pomocne podczas konfigurowania zasad zabezpieczeń w usłudze Security Center.

Aby uzyskać instrukcje na temat sposobu ustawiania zasad przy użyciu programu PowerShell, zobacz [Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu modułu Azure RM PowerShell](../azure-policy/assign-policy-definition-ps.md).

## <a name="how-security-policies-work"></a>Jak działają zasady zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. W usłudze Security Center możesz edytować zasady i monitorować ich zgodność.

> [!NOTE]
> Teraz można rozszerzyć zasady zabezpieczeń w usłudze Security Center za pomocą usługi [Azure Policy](../azure-policy/azure-policy-introduction.md). Aby uzyskać więcej informacji, zobacz temat [Integracja zasad zabezpieczeń w usłudze Security Center z usługą Azure Policy](security-center-azure-policy.md).

Wymagania w zakresie zabezpieczeń dotyczące zasobów używanych do celów projektowania lub testowania mogą różnić się od wymagań dotyczących zasobów używanych do aplikacji produkcyjnych. Aplikacje wykorzystujące dane podlegające ochronie, takie jak dane osobowe, mogą wymagać wyższego poziomu zabezpieczeń. Zasady zabezpieczeń włączone w usłudze Azure Security Center regulują zalecenia dotyczące zabezpieczeń i monitorowania, które ułatwiają znalezienie potencjalnych luk i uniknięcie zagrożeń. Aby uzyskać więcej informacji na temat sposobu określenia, która opcja jest dla Ciebie odpowiednia, zobacz temat [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Edytowanie zasad zabezpieczeń
W usłudze Security Center możesz edytować domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Aby zmodyfikować zasady zabezpieczeń, musisz być właścicielem, współautorem lub administratorem zabezpieczeń subskrypcji. Aby skonfigurować zasady bezpieczeństwa w usłudze Security Center, wykonaj następujące czynności:

1. Zaloguj się do Portalu Azure.

2. Na pulpicie nawigacyjnym usługi **Security Center** w obszarze **ZASADY I ZGODNOŚĆ** wybierz pozycję **Zasady zabezpieczeń**.

3. Wybierz subskrypcję, dla której chcesz włączyć zasady zabezpieczeń.

4. Włącz zasady, które chcesz włączyć dla subskrypcji. Zostanie wyświetlone zalecenia w zależności od każdej zasady, którą wybierzesz.
  ![Lista zasad](./media/security-center-policies/policies.png)
5. Po zakończeniu edycji wybierz pozycję **Zapisz**.

## <a name="available-security-policy-definitions"></a>Dostępne definicje zasad zabezpieczeń

Aby uzyskać informacje o definicjach zasad dostępnych w ramach domyślnych zasad zabezpieczeń, zapoznaj się z poniższą tabelą:

| Zasady | Zadania zasad |
| --- | --- |
| Aktualizacje systemu |Codziennie pobiera listę dostępnych aktualizacji zabezpieczeń i aktualizacji krytycznych z usługi Windows Update lub Windows Server Update Services. Pobierana lista zależy od usługi, która została skonfigurowana dla tej maszyny wirtualnej. Zaleca się zastosowanie brakujących aktualizacji. W systemach Linux zasady korzystają z systemu zarządzania pakietami udostępnionego wraz z dystrybucją, aby ustalić, dla których pakietów są dostępne aktualizacje. Sprawdzane są również aktualizacje zabezpieczeń i aktualizacje krytyczne z maszyn wirtualnych usługi [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Konfiguracje zabezpieczeń |Analizuje codziennie konfigurację systemu operacyjnego w celu określenia problemów, które mogą uczynić maszynę wirtualną podatną na ataki. Zaleca także dokonanie zmian w konfiguracji w celu usunięcia tych luk w zabezpieczeniach. Więcej informacji na temat określonych monitorowanych konfiguracji znajduje się na [liście zalecanych linii bazowych](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Obecnie system Windows Server 2016 nie jest w pełni obsługiwany). |
| Ochrona punktów końcowych |Zalecają ochronę punktów końcowych do skonfigurowania dla wszystkich maszyn wirtualnych systemu Windows, aby ułatwić identyfikację i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. |
| Szyfrowanie dysków |Zaleca włączenie szyfrowania dysków na wszystkich maszynach wirtualnych w celu zwiększenia ochrony magazynowanych danych. |
| Funkcje adaptacyjnego sterowania aplikacjami | Zaleca się, że umożliwiają adaptacyjne kontrole aplikacji na maszynie Wirtualnej. |
| Grupy zabezpieczeń sieci |Zaleca się, aby [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) były skonfigurowane do kontrolowania ruchu przychodzącego i wychodzącego do maszyn wirtualnych z publicznymi punktami końcowymi. Sieciowe grupy zabezpieczeń skonfigurowane dla podsieci są dziedziczone przez wszystkie interfejsy sieciowe maszyny wirtualnej, chyba że określono inaczej. Oprócz sprawdzania, czy sieciowa grupa zabezpieczeń została skonfigurowana, ta opcja również ocenia reguły zabezpieczeń ruchu przychodzącego w celu określenia, czy istnieją takie, które zezwalają na ruch przychodzący. |
| Zapora aplikacji internetowej |Zalecają skonfigurowanie zapory aplikacji internetowej na maszynach wirtualnych, gdy został spełniony dowolny z następujących warunków: <ul><li>Używany jest [publiczny adres IP na poziomie wystąpienia](../virtual-network/virtual-networks-instance-level-public-ip.md), a konfiguracja reguł zabezpieczeń ruchu przychodzącego dla skojarzonej sieciowej grupy zabezpieczeń umożliwia dostęp do portu 80/443.</li><li>Używany jest adres IP ze zrównoważonym obciążeniem, a konfiguracja skojarzonych reguł równoważenia obciążenia i reguł translatora adresów sieciowych (NAT) dla ruchu przychodzącego umożliwia dostęp do portu 80/443. Aby uzyskać więcej informacji, zobacz artykuł [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Obsługa usługi Azure Resource Manager dla modułu równoważenia obciążenia).</li> |
| Zapora nowej generacji |Powoduje rozszerzenie ochrony sieci poza grupy zabezpieczeń sieci, które są wbudowane w platformę Azure. Usługa Security Center wykryje wdrożenia, dla których zaleca się zaporę nowej generacji, i umożliwi skonfigurowanie urządzenia wirtualnego. |
| Inspekcja SQL i wykrywanie zagrożeń |Zaleca się, aby inspekcja dostępu do bazy danych SQL była włączona w celu zapewnienia zgodności, umożliwienia zaawansowanego wykrywania zagrożeń i na potrzeby analizy. |
| Szyfrowanie SQL |Zaleca się, aby funkcja szyfrowania nieaktywnych danych była włączona dla bazy danych SQL, skojarzonych kopii zapasowych i plików dziennika transakcji. Dzięki temu nawet w przypadku włamania się do danych nie będzie można ich odczytać. |
| Ocena luk w zabezpieczeniach |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| Szyfrowanie w usłudze Storage |Obecnie ta funkcja jest dostępna dla plików i obiektów blob Azure. Po włączeniu szyfrowania w usłudze Storage szyfrowane będą tylko nowe dane, a wszystkie pliki istniejące już na tym koncie magazynu pozostaną niezaszyfrowane. |
| Dostęp do sieci JIT |Gdy zostanie włączony dostęp do sieci typu „dokładnie na czas” (JIT, just-in-time), usługa Security Center zablokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły sieciowej grupy zabezpieczeń. Należy wybrać porty na maszynie wirtualnej, do których ruch przychodzący powinien zostać zablokowany. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”). |


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md) — informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../azure-policy/azure-policy-introduction.md)
