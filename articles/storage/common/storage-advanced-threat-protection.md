---
title: Usługa Advanced Threat Protection dla usługi Azure Storage
description: Skonfiguruj zaawansowaną ochronę przed zagrożeniami w usłudze Azure Storage, aby wykrywać anomalie w działaniu konta i powiadamiać o potencjalnie szkodliwych próbach uzyskania dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 2a88e778458da3c5faace401863998dda746ac75
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051488"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Usługa Advanced Threat Protection dla usługi Azure Storage

Usługa Advanced Threat Protection dla usługi Azure Storage zapewnia dodatkową warstwę analizy bezpieczeństwa, która wykrywa niezwykłe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania kont magazynu. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez potrzeby eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń. 

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania.  Te alerty zabezpieczeń są zintegrowane z usługą [Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń.

> [!NOTE]
> * Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępna tylko dla magazynu obiektów BLOB.
> * Aby uzyskać szczegółowe informacje o cenach, w tym bezpłatną 30-dniową wersję próbną, zobacz [stronę z cennikiem Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * Funkcja ATP dla usługi Azure Storage jest obecnie niedostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage umożliwia pobieranie dzienników diagnostycznych żądań odczytu, zapisu i usuwania do magazynu obiektów BLOB w celu wykrywania zagrożeń. Aby zbadać alerty z zaawansowanej ochrony przed zagrożeniami, można wyświetlić powiązane działanie magazynu za pomocą rejestrowania analityka magazynu. Aby uzyskać więcej informacji, zobacz jak [skonfigurować rejestrowanie analityka magazynu](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami 

### <a name="using-the-portal"></a>Korzystanie z portalu

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

2. Przejdź do strony konfiguracji konta usługi Azure Storage, które chcesz chronić. Na stronie **Ustawienia** wybierz pozycję zaawansowana **Ochrona przed zagrożeniami**.

3. W bloku konfiguracja **zaawansowanej ochrony przed zagrożeniami**
    * Włącz zaawansowaną *ochronę przed zagrożeniami*
    * Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady zaawansowanej ochrony przed zagrożeniami. (Ceny na obrazie są tylko do celów).

![Włączanie zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Za pomocą usługi Azure Security Center

Gdy subskrybujesz warstwę Standardowa w Azure Security Center, zaawansowana ochrona przed zagrożeniami jest automatycznie skonfigurowana na wszystkich kontach magazynu. Możesz włączyć lub wyłączyć zaawansowaną ochronę przed zagrożeniami dla kont magazynu w ramach określonej subskrypcji w następujący sposób:

1. Uruchom **Azure Security Center** w [Azure Portal](https://portal.azure.com).
1. W menu głównym kliknij pozycję **Cennik ustawienia &** .
1. Kliknij subskrypcję, którą chcesz włączyć lub wyłączyć ochronę przed zagrożeniami dla swoich kont magazynu.

    ![Wybieranie subskrypcji](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kliknij pozycję **warstwa cenowa**.
1. W sekcji **Wybieranie warstwy cenowej według typu zasobu** w wierszu **konta magazynu** kliknij pozycję **włączone** lub **wyłączone**.

    ![Włącz ATP w Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kliknij polecenie **Zapisz**.

### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów Azure Resource Manager

Użyj szablonu Azure Resource Manager, aby wdrożyć konto usługi Azure Storage z włączoną funkcją zaawansowanej ochrony przed zagrożeniami. Aby uzyskać więcej informacji, zobacz [konto magazynu z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Używanie Azure Policy

Użyj Azure Policy, aby włączyć zaawansowaną ochronę przed zagrożeniami na kontach magazynu w ramach określonej subskrypcji lub grupy zasobów.

1. Uruchom stronę **definicje zasad** platformy Azure.

1. Wyszukaj zasady **Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu** .

     ![Zasady wyszukiwania](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Wybierz subskrypcję platformy Azure lub grupę zasobów.

    ![Wybierz subskrypcję lub grupę](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Przypisz zasady.

    ![Strona definicji zasad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST
Użyj poleceń interfejsu API REST, aby utworzyć, zaktualizować lub pobrać ustawienie zaawansowanej ochrony przed zagrożeniami dla określonego konta magazynu.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Zaawansowana ochrona przed zagrożeniami — Pobierz](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Użyj następujących poleceń cmdlet programu PowerShell:

  * [Włącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Wyłącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Eksploruj anomalie zabezpieczeń

Gdy wystąpią anomalie aktywności magazynu, otrzymasz powiadomienie e-mail z informacjami o podejrzanym zdarzeniu zabezpieczeń. Szczegóły zdarzenia obejmują:

* Charakter anomalii
* Nazwa konta magazynu
* Czas zdarzenia
* Typ magazynu
* Możliwe przyczyny 
* Kroki badania
* Kroki zaradcze


Wiadomość e-mail zawiera również szczegółowe informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia.

![Adres e-mail alertu zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Bieżące alerty zabezpieczeń można przeglądać i zarządzać nimi z poziomu kafelka [alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Center. Kliknięcie określonego alertu zawiera szczegółowe informacje i akcje związane z badaniem bieżącego zagrożenia i rozwiązywanie przyszłych zagrożeń.

![Adres e-mail alertu zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alerty ochrony

Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby dostępu do kont magazynu lub korzystania z nich. Aby uzyskać listę tych alertów, zobacz alerty [usługi Azure Storage](../../security-center/security-center-alerts-data-services.md#azure-storage)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [dziennikach na kontach usługi Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Dowiedz się więcej o [Azure Security Center](../../security-center/security-center-intro.md)
