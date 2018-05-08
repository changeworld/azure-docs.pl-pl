---
title: Integracja zasad zabezpieczeń w usłudze Azure Security Center z usługą Azure Policy | Microsoft Docs
description: Ten dokument zawiera informacje pomocne podczas konfigurowania integracji zasad zabezpieczeń w usłudze Azure Security Center z usługą Azure Policy.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: d0092f57c162cc8727cfed33218e440d4da11f1c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integracja zasad zabezpieczeń w usłudze Security Center z usługą Azure Policy
Ten artykuł zawiera informacje pomocne podczas konfigurowania w usłudze Azure Security Center zasad zabezpieczeń obsługiwanych przez usługę Azure Policy.

## <a name="how-security-policies-work"></a>Jak działają zasady zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Możesz edytować zasady w usłudze Security Center lub wykonywać poniższe czynności przy użyciu usługi [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction):
* Tworzenie nowych definicji zasad.
* Przypisywanie w grupach zarządzania zasad, które mogą reprezentować całą organizację lub jednostkę biznesową w organizacji.
* Monitorowanie zgodności zasad.

> [!NOTE]
> Usługa Azure Policy jest dostępna w ograniczonej wersji zapoznawczej. W celu dołączenia przejdź na stronę [tworzenia nowego konta usługi Azure Policy](https://aka.ms/getpolicy). Aby uzyskać więcej informacji na temat usługi Azure Policy, zobacz [Create and manage policies to enforce compliance (Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności)](http://docs.microsoft.com/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Edytowanie zasad zabezpieczeń
W usłudze Security Center możesz edytować domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Aby zmodyfikować zasady zabezpieczeń, musisz być właścicielem, współautorem lub administratorem zabezpieczeń tej subskrypcji lub zawierającej ją grupy zarządzania. Aby wyświetlić zasady bezpieczeństwa w usłudze Security Center, wykonaj następujące czynności:

1. Zaloguj się do Portalu Azure.

2. Na pulpicie nawigacyjnym usługi **Security Center** w obszarze **Ogólne** wybierz pozycję **Zasady zabezpieczeń**.

    ![Okienko Zarządzanie zasadami](./media/security-center-azure-policy/security-center-policies-fig10.png)

3. Wybierz subskrypcję, dla której chcesz włączyć zasady zabezpieczeń.  

4. W sekcji **Składniki zasad** kliknij pozycję **Zasady zabezpieczeń**.  
    Zostanie otwarte okno **Podstawowe informacje**.

    ![Składniki zasad](./media/security-center-azure-policy/security-center-policies-fig12.png)

5. Aby usunąć definicję zasad, w obszarze **Zasady i parametry** obok definicji, którą chcesz usunąć, wybierz pozycję **Usuń**.

6. Kliknij pozycję **Zapisz**.  
    Zostanie otwarte okno **Dostępne definicje** z domyślnymi zasadami przypisanymi do usługi Security Center za pośrednictwem usługi Azure Policy.

7. (Opcjonalnie) W oknie **Dostępne definicje** wykonaj jedną z następujących czynności:

    * Aby dodać definicję zasad, wybierz znak plus (+) obok definicji.

    ![Dostępne definicje zasad](./media/security-center-azure-policy/security-center-policies-fig11.png)

    * Aby wyświetlić szczegółowy opis zasad, wybierz je.  
    Zostanie otwarte okno **Podgląd** definicji. Będzie ono zawierać opis definicji i link do kodu JSON określającego strukturę [definicji zasad](../azure-policy/policy-definition.md).

    ![Okno Podgląd definicji](./media/security-center-azure-policy/security-center-policies-fig14.png)

7. Po zakończeniu edycji wybierz pozycję **Zapisz**.

## <a name="available-security-policy-definitions"></a>Dostępne definicje zasad zabezpieczeń

Aby uzyskać informacje o definicjach zasad dostępnych w ramach domyślnych zasad zabezpieczeń, zapoznaj się z poniższą tabelą:

| Zasady | Jak działają włączone zasady |
| --- | --- |
| Aktualizacje systemu |Codziennie pobiera listę dostępnych aktualizacji zabezpieczeń i aktualizacji krytycznych z usługi Windows Update lub Windows Server Update Services. Pobierana lista zależy od usługi, która została skonfigurowana dla tej maszyny wirtualnej. Zaleca się zastosowanie brakujących aktualizacji. W systemach Linux zasady korzystają z systemu zarządzania pakietami udostępnionego wraz z dystrybucją, aby ustalić, dla których pakietów są dostępne aktualizacje. Sprawdzane są również aktualizacje zabezpieczeń i aktualizacje krytyczne z maszyn wirtualnych usługi [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Konfiguracje zabezpieczeń |Analizuje codziennie konfigurację systemu operacyjnego w celu określenia problemów, które mogą uczynić maszynę wirtualną podatną na ataki. Zaleca także dokonanie zmian w konfiguracji w celu usunięcia tych luk w zabezpieczeniach. Więcej informacji na temat określonych monitorowanych konfiguracji znajduje się na [liście zalecanych linii bazowych](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Obecnie system Windows Server 2016 nie jest w pełni obsługiwany). |
| Ochrona punktów końcowych |Zalecają ochronę punktów końcowych do skonfigurowania dla wszystkich maszyn wirtualnych systemu Windows, aby ułatwić identyfikację i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. |
| Szyfrowanie dysków |Zaleca włączenie szyfrowania dysków na wszystkich maszynach wirtualnych w celu zwiększenia ochrony magazynowanych danych. |
| Grupy zabezpieczeń sieci |Zaleca się, aby [sieciowe grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md) były skonfigurowane do kontrolowania ruchu przychodzącego i wychodzącego do maszyn wirtualnych z publicznymi punktami końcowymi. Sieciowe grupy zabezpieczeń skonfigurowane dla podsieci są dziedziczone przez wszystkie interfejsy sieciowe maszyny wirtualnej, chyba że określono inaczej. Oprócz sprawdzania, czy sieciowa grupa zabezpieczeń została skonfigurowana, ta opcja również ocenia reguły zabezpieczeń ruchu przychodzącego w celu określenia, czy istnieją takie, które zezwalają na ruch przychodzący. |
| Zapora aplikacji sieci Web |Zalecają skonfigurowanie zapory aplikacji internetowej na maszynach wirtualnych, gdy został spełniony dowolny z następujących warunków: <ul><li>Używany jest [publiczny adres IP na poziomie wystąpienia](../virtual-network/virtual-networks-instance-level-public-ip.md), a konfiguracja reguł zabezpieczeń ruchu przychodzącego dla skojarzonej sieciowej grupy zabezpieczeń umożliwia dostęp do portu 80/443.</li><li>Używany jest adres IP ze zrównoważonym obciążeniem, a konfiguracja skojarzonych reguł równoważenia obciążenia i reguł translatora adresów sieciowych (NAT) dla ruchu przychodzącego umożliwia dostęp do portu 80/443. Aby uzyskać więcej informacji, zobacz artykuł [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Obsługa usługi Azure Resource Manager dla modułu równoważenia obciążenia).</li> |
| Zapora nowej generacji |Powoduje rozszerzenie ochrony sieci poza grupy zabezpieczeń sieci, które są wbudowane w platformę Azure. Usługa Security Center wykryje wdrożenia, dla których zaleca się zaporę nowej generacji, i umożliwi skonfigurowanie urządzenia wirtualnego. |
| Inspekcja SQL i wykrywanie zagrożeń |Zalecają, aby inspekcja dostępu do bazy danych platformy Azure była włączona w celu zapewnienia zgodności, umożliwienia zaawansowanego wykrywania zagrożeń i na potrzeby analizy. |
| Szyfrowanie SQL |Zalecają, aby funkcja szyfrowania nieaktywnych danych była włączona dla usługi Azure SQL Database, skojarzonych kopii zapasowych i plików dziennika transakcji. Dzięki temu nawet w przypadku włamania się do danych nie będzie można ich odczytać. |
| Ocena luk w zabezpieczeniach |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| Szyfrowanie w usłudze Storage |Obecnie ta funkcja jest dostępna w usługach Azure Blob Storage i Azure Files. Po włączeniu szyfrowania w usłudze Storage szyfrowane będą tylko nowe dane, a wszystkie pliki istniejące już na tym koncie magazynu pozostaną niezaszyfrowane. |
| Dostęp do sieci JIT |Gdy zostanie włączony dostęp do sieci typu „dokładnie na czas” (JIT, just-in-time), usługa Security Center zablokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły sieciowej grupy zabezpieczeń. Należy wybrać porty na maszynie wirtualnej, do których ruch przychodzący powinien zostać zablokowany. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”). |


## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md) — informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
