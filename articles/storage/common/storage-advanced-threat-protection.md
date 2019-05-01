---
title: Usługa Advanced Threat Protection dla usługi Azure Storage
description: Konfigurowanie usługi Azure Storage zaawansowanej ochrony przed zagrożeniami w celu wykrycia anomalii w aktywności konta i powiadomimy użytkownika o potencjalnie szkodliwych prób dostępu do Twojego konta.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: monhaber
ms.manager: shaik
ms.openlocfilehash: c42867ff7aea2210f20a2cd2adb5c067b8f36c80
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926502"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Usługa Advanced Threat Protection dla usługi Azure Storage

Usługa Advanced Threat Protection dla usługi Azure Storage zapewnia dodatkową warstwę analizy bezpieczeństwa, która wykrywa niezwykłe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania kont magazynu. Ta warstwa ochrony umożliwia reagowanie na zagrożenia bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania systemami monitorowania bezpieczeństwa. 

Alerty zabezpieczeń są wyzwalane, gdy wystąpią nieprawidłowości w działaniu.  Te alerty zabezpieczeń są zintegrowane z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/)oraz wysyłane za pośrednictwem poczty e-mail do administratorów subskrypcji, szczegółowe informacje o podejrzanych działań i zalecenia dotyczące sposobu badaniu i usuwaniu zagrożeń.

> [!NOTE]
> * Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępna wyłącznie dla magazynu obiektów Blob.
> * Aby uzyskać szczegóły cennika, łącznie z bezpłatnej 30-dniowej wersji próbnej, zobacz [usługi Azure Security Center, na stronie z cennikiem]( https://azure.microsoft.com/pricing/details/security-center/).
> * Zaawansowanej ochrony przed zagrożeniami dla funkcji usługi Azure storage nie jest obecnie dostępna w regionach należących do suwerennej chmury i platformy Azure dla instytucji rządowych.

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage pozyskuje dzienniki diagnostyczne odczytu, zapisu i usuwania żądań do usługi Blob storage na potrzeby wykrywania zagrożeń. Aby zbadać alertów z zaawansowanej ochrony przed zagrożeniami, można wyświetlić działania magazynu powiązanych korzystania z rejestrowania analizy magazynu. Aby uzyskać więcej informacji, zobacz instrukcje [skonfigurować rejestrowanie dla usługi Storage Analytics](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami 

### <a name="using-the-portal"></a>Korzystanie z portalu

1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com/).

2. Przejdź do strony konfiguracji konta usługi Azure Storage, które mają być chronione. W **ustawienia** wybierz opcję **zaawansowanej ochrony przed zagrożeniami**.

3. W **zaawansowanej ochrony przed zagrożeniami** bloku konfiguracji
    * Włącz **ON** zaawansowane *ochroną przed zagrożeniami*
    * Kliknij przycisk **Zapisz** można zapisać zasad zaawansowanej ochrony przed zagrożeniami nowe lub zaktualizowane. (Ceny na ilustracji są na przykład tylko do celów).

![Włącz Zaawansowana ochrona przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Za pomocą usługi Azure Security Center
Po zasubskrybowaniu do warstwy standardowa w usłudze Azure Security Center zaawansowanej ochrony przed zagrożeniami na skonfigurowano konta magazynu. Aby uzyskać więcej informacji, zobacz [uaktualnienie do warstwy standardowa Security Center w celu uzyskania zwiększonych zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Ceny na ilustracji są na przykład tylko do celów).

![W warstwie standardowa w ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Za pomocą szablonów usługi Azure Resource Manager

Szablon usługi Azure Resource Manager służy do wdrażania konta usługi Azure Storage za pomocą zaawansowanej ochrony przed zagrożeniami jest włączona.
Aby uzyskać więcej informacji, zobacz [konta magazynu za pomocą zaawansowanej ochrony przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Za pomocą usługi Azure Policy

Włącz zaawansowaną ochronę przed zagrożeniami na kontach magazynu w ramach określonej subskrypcji lub grupy zasobów za pomocą usługi Azure Policy.

1. Biedzie Azure **zasady — definicje** strony.

1. Wyszukaj **wdrażania zaawansowanej ochrony przed zagrożeniami dla kont magazynu** zasad.

     ![Wyszukaj zasady](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Wybierz Azure subskrypcji lub grupy zasobów.

    ![Wybierz subskrypcję lub grupę](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Przypisz zasady.

    ![Strona definicji zasad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST
Użyj poleceń interfejsu API Rest do utworzenia, aktualizacji lub pobieranie ustawienia zaawansowanej ochrony przed zagrożeniami dla konta określonego magazynu.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Zaawansowana ochrona przed zagrożeniami — Pobierz](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Użyj następujących poleceń cmdlet programu PowerShell:

  * [Włącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Pobierz Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Wyłącz Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Eksploruj anomalie zabezpieczeń

W przypadku wystąpienia anomalii w działaniach magazynu otrzymasz powiadomienie e-mail z informacjami o podejrzanych zdarzeniach dotyczących zabezpieczeń. Szczegóły zdarzenia obejmują:

* Rodzaj anomalii
* Nazwa konta magazynu
* Czas zdarzenia
* Typ magazynu
* Potencjalne przyczyny 
* Kroki badania
* Kroki korygowania


Wiadomości e-mail również zawiera szczegółowe informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń.

![Usługa Azure Storage, zaawansowane wiadomość e-mail z alertem ochrony przed zagrożeniami](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Można przeglądać i zarządzać bieżące alerty zabezpieczeń w usłudze Azure Security Center [Kafelek alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Kliknij określony alert zawiera informacje i akcje w przypadku badania bieżące zagrożenia i adresowania przyszłymi zagrożeniami.

![Usługa Azure Storage, zaawansowane wiadomość e-mail z alertem ochrony przed zagrożeniami](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alerty ochrony

Alerty są generowane na podstawie nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystać kont magazynu. Zdarzenia te można uruchomić następujące alerty:

### <a name="anomalous-access-pattern-alerts"></a>Dostęp z nietypowego wzorca alertów

* **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do konta magazynu. Na przykład, gdy ktoś uzyskał dostęp do konta magazynu z nietypowej lokalizacji geograficznej.
Potencjalne przyczyny:
   * Atakujący ma dostęp do konta magazynu
   * Przez wiarygodnego użytkownika ma dostęp do konta magazynu z nowej lokalizacji
 
* **Aplikacja anomalii**: Ten alert wskazuje, że aplikacja nietypowe uzyskał dostęp do tego konta magazynu. Potencjalne przyczyny:
   * Osoba atakująca ma dostęp do konta magazynu przy użyciu nowej aplikacji.
   * Przez wiarygodnego użytkownika został użyty Nowa aplikacja/przeglądarka dostępu do konta magazynu.

* **Dostęp anonimowy**: Ten alert wskazuje, że występuje w przypadku zmiany wzorca dostępu do konta magazynu. Na przykład, to konto zostało uzyskiwać dostęp anonimowo (tzn. bez żadnego uwierzytelniania), co jest nieoczekiwany w porównaniu do ostatnich wzorzec dostępu do tego konta.
Potencjalne przyczyny:
   * Osoba atakująca ma wykorzystać publicznego dostępu do odczytu do kontenera.
   * Wiarygodnego użytkownika lub aplikacji jest używany publiczny dostęp do odczytu do kontenera.

### <a name="anomalous-extractupload-alerts"></a>Wyodrębnij nietypowe lub przekazywanie alertów

* **Wykradanie danych**: Ten alert wskazuje, że został wyodrębniony nietypowo dużej ilości danych w porównaniu do ostatnią aktywność, w tym kontenerze magazynu. Potencjalne przyczyny:
   * Osoba atakująca ma wyodrębniania dużych ilości danych z kontenera. (Na przykład: naruszenia wykradanie danych, nieautoryzowanych transfer danych)
   * Nietypowe ilości danych ma wyodrębnić wiarygodnego użytkownika lub aplikacji z kontenera. (Na przykład: związanych z konserwacją)

* **Nieoczekiwane usunięcie**: Ten alert wskazuje, że na koncie magazynu, w porównaniu z ostatnią aktywność na tym koncie wystąpił co najmniej jednej operacji usuwania nieoczekiwany. Potencjalne przyczyny:
   * Osoba atakująca usunął dane z konta magazynu.
   * Przez wiarygodnego użytkownika przeprowadził nietypowe usunięcia.

* **Przekaż pakiet usługi w chmurze Azure**: Ten alert wskazuje, czy pakiet usługi w chmurze Azure (plik cspkg) został przekazany do konta magazynu w nietypowy sposób, w porównaniu do ostatnią aktywność na tym koncie. Potencjalne przyczyny: 
   * Osoba atakująca ma zostać przygotowywanie do wdrożenia złośliwy kod z konta magazynu w usłudze w chmurze platformy Azure.
   * Przez wiarygodnego użytkownika ma zostały przygotowywanie do wdrożenia usługi uzasadnione.

### <a name="suspicious-storage-activities-alerts"></a>Magazyn podejrzanych działań alertów

* **Zmień uprawnienia dostępu**: Ten alert wskazuje, że uprawnienia dostępu do tego kontenera magazynu zostały zmienione w nietypowy sposób. Potencjalne przyczyny: 
   * Osoba atakująca zmienił uprawnień kontenera, aby obniżyć poziom zabezpieczeń.
   * Przez wiarygodnego użytkownika został zmieniony uprawnień kontenera.

* **Dostęp do kontroli**: Ten alert wskazuje, że uprawnienia dostępu do konta magazynu zostały poddane w nietypowy sposób, w porównaniu do ostatnią aktywność na tym koncie. Potencjalne przyczyny: 
   * Osoba atakująca wykonała Rekonesans przyszłych ataku.
   * Przez wiarygodnego użytkownika ma wykonać konserwacji na koncie magazynu.

* **Eksploracja danych**: Ten alert wskazuje, że obiekty BLOB i kontenery na koncie usługi storage została wyliczane w nietypowy sposób, w porównaniu do ostatnią aktywność na tym koncie. Potencjalne przyczyny: 
   * Osoba atakująca wykonała Rekonesans przyszłych ataku.
   * Wiarygodnego użytkownika lub logice aplikacji ma eksplorować dane na koncie magazynu.






## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [logowania na kontach magazynu Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Dowiedz się więcej o [usługi Azure Security Center](../../security-center/security-center-intro.md)
