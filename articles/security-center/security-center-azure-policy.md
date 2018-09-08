---
title: Zasady zabezpieczeń w usłudze Azure Security Center można ustawić indywidualnie lub jako część zasad platformy Azure | Dokumentacja firmy Microsoft
description: Ten dokument ułatwia do ustawiania zasad w usłudze Azure Security Center lub usługi Azure Policy.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: bc6226d462bac7e9c50ce3f348007023bf861ec3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162295"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Ustawianie zasad zabezpieczeń w usłudze Security Center lub usługa Azure Policy

Ten artykuł pomoże Ci skonfigurować zasady zabezpieczeń w usłudze Azure Security Center. Zasady Centrum zabezpieczeń Azure integracji z zasad platformy Azure, dzięki czemu możesz je ustawić w usłudze Security Center w określonej subskrypcji lub w [usługi Azure Policy](../azure-policy/azure-policy-introduction.md), co umożliwia ustawianie zasad w grupach zarządzania oraz między wieloma Subskrypcje...

## <a name="what-are-security-policies"></a>Czym są zasady zabezpieczeń?
Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy. W usłudze Azure Security Center można zdefiniować zasady dla subskrypcji platformy Azure i dostosowuj je do danego typu obciążenia lub wrażliwości danych. Na przykład aplikacje wykorzystujące dane podlegające ochronie, takie jak dane osobowe, mogą wymagać wyższego poziomu zabezpieczeń niż innych obciążeń. Aby ustawić zasady dla subskrypcji lub grupy zarządzania, należy ustawić je w [usługi Azure Policy](../azure-policy/azure-policy-introduction.md).

> [!NOTE]
> Jeśli wcześniej skonfigurowano zasady zabezpieczeń na subskrypcję, która jest częścią grupy zarządzania lub ma wiele zasad przydziały, te zasady są wyświetlane na szarym w usłudze Security Center tak, aby można było zarządzać zasadami na poziomie grupy zarządzania za pośrednictwem platformy Azure Strona zasad. 

## <a name="how-security-policies-work"></a>Jak działają zasady zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Możesz edytować zasady w usłudze Security Center lub wykonywać poniższe czynności przy użyciu usługi Azure Policy:
- Tworzenie nowych definicji zasad.
- Przypisywanie w subskrypcjach i grupach zarządzania zasad, które mogą reprezentować całą organizację lub jednostkę biznesową w organizacji.
- Monitorowanie zgodności zasad.

Aby uzyskać więcej informacji na temat usługi Azure Policy, zobacz [Create and manage policies to enforce compliance (Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności)](../azure-policy/create-manage-policy.md).

Zasady platformy Azure zawierają następujące składniki:

- **Zasada** jest regułą
- **Inicjatywa** jest zbiorem zasad
- **Przypisanie** to zastosowanie inicjatywy lub zasady do określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów)

Zasób jest oceniany pod kątem zasad, które zostały do niego przypisane, i otrzymuje współczynnik zgodności w zależności od liczby zasad, z którymi jest zgodny.

## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Usługa Security Center korzysta z opartej na rolach kontrola dostępu (RBAC), który zapewnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Po otwarciu Centrum zabezpieczeń, widzą tylko informacje związane z zasobami, które mają dostęp do. Co oznacza, że użytkownicy przypisano rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- Czytelnik zabezpieczeń: ma widoku praw do usługi Security Center, który zawiera zalecenia, alerty, zasady i kondycję, ale one nie może wprowadzać zmian.
- Administrator zabezpieczeń: ma te same prawa widok jako rola Czytelnik zabezpieczeń, a można również możliwość aktualizowania zasad zabezpieczeń oraz odrzucania zaleceń i alertów.

## <a name="edit-security-policies"></a>Edytowanie zasad zabezpieczeń
W usłudze Security Center możesz edytować domyślne zasady zabezpieczeń dla każdej Twojej grupy zarządzania i subskrypcji platformy Azure. Aby zmodyfikować zasady zabezpieczeń, musisz być właścicielem, współautorem lub administratorem zabezpieczeń tej subskrypcji lub zawierającej ją grupy zarządzania. Aby wyświetlić zasady zabezpieczeń w usłudze Security Center:

> [!NOTE]
> Żadnych zasad zabezpieczeń ustawionych na subskrypcję, która jest częścią grupy zarządzania lub ma wiele zasad przydziały, pojawi się szare w usłudze Security Center. Możesz edytować te zasady w [usługi Azure Policy](../azure-policy/azure-policy-introduction.md). 

1. Na pulpicie nawigacyjnym usługi **Security Center** w obszarze **ZASADY I ZGODNOŚĆ** wybierz pozycję **Zasady zabezpieczeń**. Zostanie otwarte okienko **Zarządzanie zasadami**.

    ![Okienko Zarządzanie zasadami](./media/security-center-azure-policy/security-center-policies-fig10.png)

  W okienku Zarządzanie zasadami jest wyświetlana liczba grup zarządzania, subskrypcji i obszarów roboczych, a także struktura Twojej grupy zarządzania.

  > [!NOTE]
  > Pulpit nawigacyjny usługi Security Center może pokazywać większą liczbę subskrypcji w obszarze **Pokrycie subskrypcji**, niż w obszarze **Zarządzanie zasadami**. W obszarze Pokrycie subskrypcji jest wyświetlana liczba subskrypcji w warstwie Standardowa, Bezpłatna i subskrypcji „nie pokrytych”. Subskrypcje „nie pokryte” nie mają włączonej usługi Security Center i nie są wyświetlane w obszarze **Zarządzanie zasadami**.
  >
  >

  Kolumny w tabeli wyświetlają następujące informacje:

 - Przypisanie inicjatywy zasad — Wbudowane zasady i inicjatywy usługi Security Center przypisane do subskrypcji lub grupy zarządzania.
 - Zgodność — Ogólny wynik zgodności dla grupy zarządzania, subskrypcji lub obszaru roboczego. Wynik ten jest średnią ważoną przypisań. Średnia ważona jest uwzględniania w liczbie zasad pojedynczego przypisania i liczbie zasobów, których dotyczy to przypisanie.

 Jeśli na przykład Twoja subskrypcja obejmuje dwie maszyny wirtualne i inicjatywę z przypisanymi do niej pięcioma zasadami, to masz w tej subskrypcji 10 ocen. Jeśli jedna z maszyn wirtualnych nie jest zgodna z dwoma zasadami, to ogólny wynik zgodności przypisania Twojej subskrypcji wynosi 80%.

 - Pokrycie — Określa warstwę cenową (Bezpłatna lub Standardowa), w której działa grupa zarządzania, subskrypcja lub obszar roboczy.  Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
 - Ustawienia — Subskrypcje zawierają link **Edytuj ustawienia**. Wybranie linku **Edytuj ustawienia** umożliwia aktualizację ustawień subskrypcji, takich jak zbieranie danych, warstwa cenowa i powiadomienia e-mail.

2. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz włączyć zasady zabezpieczeń. Zostanie otwarte okno **Zasady zabezpieczeń**.

3.  W obszarze **Zasady zabezpieczeń** wybierz kontrolki, które mają być monitorowane przez usługę Security Center, i podaj rekomendacje, wybierając opcję **Włączone**.  Wybierz opcję **Wyłączone**, jeśli nie chcesz, aby usługa Security Center monitorowała daną kontrolkę.

    ![Składniki zasad](./media/security-center-azure-policy/security-policy.png)

4. Wybierz pozycję **Zapisz**.

## <a name="available-security-policy-definitions"></a>Dostępne definicje zasad zabezpieczeń

Aby uzyskać informacje o definicjach zasad dostępnych w ramach domyślnych zasad zabezpieczeń, zapoznaj się z poniższą tabelą:

| Zasady | Jak działają włączone zasady |
| --- | --- |
| Aktualizacje systemu |Codziennie pobiera listę dostępnych aktualizacji zabezpieczeń i aktualizacji krytycznych z usługi Windows Update lub Windows Server Update Services. Pobierana lista zależy od usługi, która została skonfigurowana dla tej maszyny wirtualnej. Zaleca się zastosowanie brakujących aktualizacji. W systemach Linux zasady korzystają z systemu zarządzania pakietami udostępnionego wraz z dystrybucją, aby ustalić, dla których pakietów są dostępne aktualizacje. Sprawdzane są również aktualizacje zabezpieczeń i aktualizacje krytyczne z maszyn wirtualnych usługi [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Konfiguracje zabezpieczeń |Analizuje codziennie konfigurację systemu operacyjnego w celu określenia problemów, które mogą uczynić maszynę wirtualną podatną na ataki. Zaleca także dokonanie zmian w konfiguracji w celu usunięcia tych luk w zabezpieczeniach. Więcej informacji na temat określonych monitorowanych konfiguracji znajduje się na [liście zalecanych linii bazowych](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Obecnie system Windows Server 2016 nie jest w pełni obsługiwany). |
| Ochrona punktów końcowych |Zalecają ochronę punktów końcowych do skonfigurowania dla wszystkich maszyn wirtualnych systemu Windows, aby ułatwić identyfikację i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. |
| Szyfrowanie dysków |Zaleca włączenie szyfrowania dysków na wszystkich maszynach wirtualnych w celu zwiększenia ochrony magazynowanych danych. |
| Grupy zabezpieczeń sieci |Zaleca się, aby [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) były skonfigurowane do kontrolowania ruchu przychodzącego i wychodzącego do maszyn wirtualnych z publicznymi punktami końcowymi. Sieciowe grupy zabezpieczeń skonfigurowane dla podsieci są dziedziczone przez wszystkie interfejsy sieciowe maszyny wirtualnej, chyba że określono inaczej. Oprócz sprawdzania, czy sieciowa grupa zabezpieczeń została skonfigurowana, ta opcja również ocenia reguły zabezpieczeń ruchu przychodzącego w celu określenia, czy istnieją takie, które zezwalają na ruch przychodzący. |
| Zapora aplikacji internetowej |Zalecają skonfigurowanie zapory aplikacji internetowej na maszynach wirtualnych, gdy został spełniony dowolny z następujących warunków: <ul><li>Używany jest [publiczny adres IP na poziomie wystąpienia](../virtual-network/virtual-networks-instance-level-public-ip.md), a konfiguracja reguł zabezpieczeń ruchu przychodzącego dla skojarzonej sieciowej grupy zabezpieczeń umożliwia dostęp do portu 80/443.</li><li>Używany jest adres IP ze zrównoważonym obciążeniem, a konfiguracja skojarzonych reguł równoważenia obciążenia i reguł translatora adresów sieciowych (NAT) dla ruchu przychodzącego umożliwia dostęp do portu 80/443. Aby uzyskać więcej informacji, zobacz artykuł [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Obsługa usługi Azure Resource Manager dla modułu równoważenia obciążenia).</li> |
| Zapora nowej generacji |Powoduje rozszerzenie ochrony sieci poza grupy zabezpieczeń sieci, które są wbudowane w platformę Azure. Usługa Security Center wykryje wdrożenia, dla których zaleca się zaporę nowej generacji, i umożliwi skonfigurowanie urządzenia wirtualnego. |
| Inspekcja SQL i wykrywanie zagrożeń |Zalecają, aby inspekcja dostępu do bazy danych platformy Azure była włączona w celu zapewnienia zgodności, umożliwienia zaawansowanego wykrywania zagrożeń i na potrzeby analizy. |
| Szyfrowanie SQL |Zalecają, aby funkcja szyfrowania nieaktywnych danych była włączona dla usługi Azure SQL Database, skojarzonych kopii zapasowych i plików dziennika transakcji. Dzięki temu nawet w przypadku włamania się do danych nie będzie można ich odczytać. |
| Ocena luk w zabezpieczeniach |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| Szyfrowanie w usłudze Storage |Obecnie ta funkcja jest dostępna w usługach Azure Blob Storage i Azure Files. Po włączeniu szyfrowania w usłudze Storage szyfrowane będą tylko nowe dane, a wszystkie pliki istniejące już na tym koncie magazynu pozostaną niezaszyfrowane. |
| Dostęp do sieci JIT |Gdy zostanie włączony dostęp do sieci typu „dokładnie na czas” (JIT, just-in-time), usługa Security Center zablokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły sieciowej grupy zabezpieczeń. Należy wybrać porty na maszynie wirtualnej, do których ruch przychodzący powinien zostać zablokowany. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”). |

## <a name="management-groups"></a>Grupy zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje zasady nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. Do każdego katalogu jest przypisywana grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. Aby skonfigurować używanie grup zarządzania w usłudze Azure Security Center, postępuj zgodnie z instrukcjami w artykule [Gain tenant-wide visibility for Azure Security Center](security-center-management-groups.md) (Uzyskiwanie wglądu na poziomie dzierżawy w usłudze Azure Security Center). 

> [!NOTE]
> Ważne jest, aby poznać hierarchię grup zarządzania i subskrypcji. Zobacz [Organize your resources with Azure Management Groups](../azure-resource-manager/management-groups-overview.md#root-management-group-for-each-directory) (Organizowanie zasobów za pomocą grup zarządzania platformy Azure), aby dowiedzieć się więcej na temat grup zarządzania, zarządzania grupą główną i dostępu do grup zarządzania.
>
>


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md) — informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Uzyskiwanie wglądu na poziomie dzierżawy w usłudze Azure Security Center](security-center-management-groups.md) — informacje na temat sposobu konfigurowania grup zarządzania dla usługi Azure Security Center. 
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../azure-policy/azure-policy-introduction.md)
