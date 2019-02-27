---
title: Monitorowania zagrożeń w usłudze Azure Storage
description: Konfigurowanie usługi Azure Storage zaawansowanej ochrony przed zagrożeniami w celu wykrycia anomalii w aktywności konta i powiadomimy użytkownika o potencjalnie szkodliwych prób dostępu do Twojego konta.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 00de38aa7309179b92ff65f009f8aa780f60c284
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883695"
---
# <a name="azure-storage-advanced-threat-protection"></a>Azure Storage Advanced Threat Protection

Usługa Azure Storage zaawansowanej ochrony przed zagrożeniami wykrywają nieprawidłowości w działaniu konta i powiadamia o potencjalnie szkodliwych prób dostępu do Twojego konta. Ta warstwa ochrony umożliwia reagowanie na zagrożenia bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania systemami monitorowania bezpieczeństwa.

Zagrożenia są udostępniane przez zdefiniowanie alerty zabezpieczeń, które wyzwalania, gdy wystąpią nieprawidłowości w działaniu. Te alerty, integracja z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/) które zawierają szczegółowe informacje o podejrzanych działań i zalecenia dotyczące sposobu badaniu i usuwaniu zagrożeń. 

> [!NOTE]
> Usługa Azure Storage zaawansowanej ochrony przed zagrożeniami jest obecnie dostępna tylko dla usługi Blob. Alerty zabezpieczeń są zintegrowane z usługą Azure Security Center i są wysyłane za pośrednictwem poczty e-mail do administratorów subskrypcji.

Usługa Azure Storage zaawansowanej ochrony przed zagrożeniami pozyskuje dzienniki diagnostyczne odczytu, zapisu i usuwania żądań do usługi obiektów Blob w celu wykrywania zagrożeń. Aby zbadać alertów z zaawansowanej ochrony przed zagrożeniami, musisz [skonfigurować dzienniki diagnostyczne](storage-monitor-storage-account.md#configure-logging) włączyć wszystkie poziomy dzienników dla usługi Blob.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami w portalu

1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com/).

2. Przejdź do strony konfiguracji konta usługi Azure Storage, które mają być chronione. W **ustawienia** wybierz opcję **zaawansowanej ochrony przed zagrożeniami**.

3. W **zaawansowanej ochrony przed zagrożeniami** bloku konfiguracji
    * Włącz **ON** zaawansowane *ochroną przed zagrożeniami*
    * Kliknij przycisk **Zapisz** można zapisać zasad zaawansowanej ochrony przed zagrożeniami nowe lub zaktualizowane.

![Włącz Zaawansowana ochrona przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Eksploruj anomalie

W przypadku wystąpienia anomalii w działaniach magazynu otrzymasz powiadomienie e-mail z informacjami o podejrzanych zdarzeniach dotyczących zabezpieczeń. Szczegóły zdarzenia obejmują:

* rodzaj anomalii
* Nazwa konta magazynu
* Typ magazynu
* czas trwania zdarzenia

Wiadomości e-mail również zawiera szczegółowe informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń.

![Usługa Azure Storage, zaawansowane wiadomość e-mail z alertem ochrony przed zagrożeniami](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Można przeglądać i zarządzać bieżące alerty zabezpieczeń w usłudze Azure Security Center [Kafelek alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Kliknij określony alert zawiera informacje i akcje w przypadku badania bieżące zagrożenia i adresowania przyszłymi zagrożeniami.

![Usługa Azure Storage, zaawansowane wiadomość e-mail z alertem ochrony przed zagrożeniami](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alerty ochrony

Alerty są generowane na podstawie nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystać kont magazynu. Zdarzenia te można uruchomić następujące alerty:

* **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do konta magazynu. Na przykład, gdy ktoś uzyskał dostęp do konta magazynu z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (Nowa aplikacja lub operacji konserwacji dewelopera). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca itp.).

* **Wyodrębnianie danych**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca wyodrębniania danych z konta magazynu. Na przykład jeśli ktoś uzyskał dostęp do nietypowych ilość danych na koncie magazynu. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (działanie konserwacji). W innych przypadkach ten alert wykrywa złośliwe działanie (naruszenia wykradanie danych, nieautoryzowanych transfer danych).

* **Nietypowy dostęp anonimowy:** Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do konta magazynu. Na przykład załóżmy, że ktoś anonimowo uzyskiwał dostęp do konta magazynu. W niektórych przypadkach ten alert wykrywa legalny dostęp przy użyciu publicznego dostępu do odczytu. W innych przypadkach ten alert wykrywa przed nieautoryzowanym dostępem, która wykorzystuje publicznego dostępu do odczytu do kontenera i jego obiektów blob.

* **Nieoczekiwane usunięcie:** Ten alert jest wyzwalany, gdy co najmniej jednej operacji usuwania nieoczekiwany występują na koncie magazynu, na podstawie historycznych analizy na koncie magazynu. Na przykład załóżmy, że ktoś wykonywane *DeleteBlob* operację, używając nowej aplikacji i z nowego adresu IP. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (administrator użyć innej przeglądarki podczas podróży działalność). W innych przypadkach ten alert wykrywa złośliwe działanie (osoba atakująca usunięcie danych). 
 
* **Zmień uprawnienia dostępu:** Ten alert jest wyzwalany, gdy występuje nieoczekiwane zmiany uprawnień dostępu do konta magazynu. Na przykład załóżmy, że ktoś zmienił uprawnienia dostępu do konta magazynu przy użyciu nowej aplikacji i z nowego adresu IP. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (administrator użyć innej przeglądarki podczas podróży działalność). W innych przypadkach ten alert wykrywa złośliwe działanie (np. osoba atakująca zwiększanie uprawnień, który zdobył dostęp do konta). 

* **Przekaż pakiet usługi w chmurze platformy Azure:** Ten alert jest wyzwalany, gdy ma nieoczekiwany przekazywania pakietu usługi w chmurze Azure (*cspkg* plików) do konta magazynu. Na przykład załóżmy, że *cspkg* plik został przekazany z nowego adresu IP. W niektórych przypadkach ten alert wykrywa prawidłowe działanie. W innych przypadkach ten alert wykrywa złośliwe działanie (np. usługi w chmurze pakiet został przekazany w ramach przygotowań do wdrożenia usługi złośliwego).    
   

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [logowania na kontach magazynu Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Dowiedz się więcej o [usługi Azure Security Center](../../security-center/security-center-intro.md)