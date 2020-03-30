---
title: Korygowanie zaleceń w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak korygować zalecenia w usłudze Azure Security Center, aby chronić zasoby i przestrzegać zasad zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603503"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Rekomendacje dotyczące korygowania w usłudze Azure Security Center

Zalecenia dają sugestie, jak lepiej zabezpieczyć swoje zasoby. Zaimplementuj zalecenie, wykonując kroki korygowania podane w zaleceniu.

## <a name="remediation-steps"></a>Kroki korygowania<a name="remediation-steps"></a>

Po zapoznaniu się ze wszystkimi zaleceniami zdecyduj, który z nich należy najpierw zaradzić. Zalecamy użycie [wpływu bezpiecznego wyniku,](security-center-recommendations.md#monitor-recommendations) aby ustalić priorytety, co należy zrobić w pierwszej kolejności.

1. Na liście kliknij zalecenie.

1. Postępuj zgodnie z instrukcjami w sekcji **Kroki korygowania.** Każde zalecenie ma swój własny zestaw instrukcji. Poniższy zrzut ekranu przedstawia kroki korygowania dotyczące konfigurowania aplikacji w celu zezwalania tylko na ruch za pośrednictwem protokołu HTTPS.

    ![Szczegóły zalecenia](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po zakończeniu pojawi się powiadomienie informujące o pomyślnym wykonaniu korygowania.

## <a name="quick-fix-remediation"></a>Szybkie korygowanie<a name="one-click"></a>

Szybka poprawka umożliwia szybkie korygowanie zalecenia dotyczące wielu zasobów. Jest dostępna tylko dla określonych zaleceń. Quick Fix upraszcza korygowanie i umożliwia szybkie zwiększenie bezpiecznego wyniku, poprawiając bezpieczeństwo środowiska.

Aby zaimplementować szybkie korygowanie:

1. Z listy zaleceń, które mają **Quick Fix!** etykiety, kliknij na zalecenie.

    [![Wybierz szybką poprawkę!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na karcie **Zasoby w złej kondycji** wybierz zasoby, na których chcesz zaimplementować zalecenie, a następnie kliknij przycisk **Koryguj**.

    > [!NOTE]
    > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień do ich modyfikowania.

1. W polu potwierdzenia przeczytaj szczegóły korygowania i implikacje.

    ![Szybka poprawka](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Implikacje są wymienione w szarym polu w oknie **Korygowanie zasobów,** który otwiera się po kliknięciu przycisku **Koryguj**. Wymieniają one, jakie zmiany zachodzą podczas postępowania z szybkim korygowanie.

1. W razie potrzeby wstaw odpowiednie parametry i zatwierdz środki zaradcze.

    > [!NOTE]
    > Może upłynąć kilka minut po zakończeniu korygowania, aby wyświetlić zasoby na karcie Zasoby w **dobrej kondycji.** Aby wyświetlić akcje korygowania, sprawdź [dziennik aktywności](#activity-log).

1. Po zakończeniu pojawi się powiadomienie informujące o pomyślnym wykonaniu korygowania.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Rejestrowanie korygowania quick fix w dzienniku aktywności<a name="activity-log"></a>

Operacja korygowania używa wdrożenia szablonu lub wywołania interfejsu API PATCH REST, aby zastosować konfigurację do zasobu. Te operacje są rejestrowane w [dzienniku aktywności platformy Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Zalecenia z szybkim korygowaniem

|Zalecenie|Domniemanie|
|---|---|
|Inspekcja na serwerach SQL powinna być włączona|Ta akcja umożliwi inspekcję SQL na tych serwerach i ich bazach danych. <br>**Uwaga:** <ul><li>Dla każdego regionu wybranych serwerów SQL konto magazynu do zapisywania dzienników inspekcji zostanie utworzone i udostępnione przez wszystkie serwery w tym regionie.</li><li>Aby zapewnić prawidłową inspekcję, nie należy usuwać ani zmieniać nazwy grupy zasobów ani kont magazynu.</li></ul>|
|Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL|Ta akcja umożliwi SQL Advanced Data Security (ADS) w wybranych wystąpieniach zarządzanych SQL. <br>**Uwaga:** <ul><li>Dla każdego regionu i grupy zasobów wybranych wystąpień zarządzanych SQL konto magazynu do zapisywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li> Opłata ADS jest naliczana w wysokości $15 za wystąpienie zarządzane SQL.</li></ul>|
|Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Ta akcja umożliwi ocenę luk w zabezpieczeniach SQL w wybranych wystąpieniach zarządzanych SQL. <br>**Uwaga:**<ul><li>Ocena luk w zabezpieczeniach SQL jest częścią pakietu SQL Advanced Data Security (ADS). Jeśli usługa ADS nie jest już włączona, zostanie automatycznie włączona w wystąpieniu zarządzanym.</li><li>Dla każdego regionu i grupy zasobów wybranych wystąpień zarządzanych SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li>Opłata ADS jest naliczana w wysokości $15 za serwer SQL.</li></ul>||
|Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL|Ta akcja umożliwi zaawansowane bezpieczeństwo danych (ADS) na tych wybranych serwerach i ich bazach danych. <br>**Uwaga:**<ul><li>Dla każdego regionu i grupy zasobów wybranych serwerów SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie serwery w tym regionie.<</li><li>Opłata ADS jest naliczana w wysokości $15 za serwer SQL.</li></ul>||
|Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Ta akcja umożliwi ocenę luk w zabezpieczeniach SQL na tych wybranych serwerach i ich bazach danych. <br>**Uwaga:**<ul><li>Ocena luk w zabezpieczeniach SQL jest częścią pakietu SQL Advanced Data Security (ADS). Jeśli usługa ADS nie jest już włączona, zostanie automatycznie włączona na serwerze SQL.</li><li>Dla każdego regionu i grupy zasobów wybranych serwerów SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li>Opłata ADS jest naliczana w wysokości $15 za serwer SQL.</li></ul>||
|Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL|Ta akcja umożliwia szyfrowanie danych przezroczystych danych bazy danych SQL (TDE) w wybranych bazach danych. <br>**Uwaga:** Domyślnie będą używane klucze TDE zarządzane przez usługę.
|Należy włączyć bezpieczny transfer na konta magazynu|Ta akcja aktualizuje zabezpieczenia konta magazynu, aby zezwalać tylko na żądania przez bezpieczne połączenia. (HTTPS). <br>**Uwaga:**<ul><li>Wszystkie żądania przy użyciu protokołu HTTP zostaną odrzucone.</li><li>Podczas korzystania z usługi plików platformy Azure połączenie bez szyfrowania zakończy się niepowodzeniem, w tym scenariusze przy użyciu SMB 2.1, SMB 3.0 bez szyfrowania i niektóre smaki klienta SMB systemu Linux. Dowiedz się więcej.</li></ul>|
|Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do protokołu HTTPS w wybranych zasobach. <br>**Uwaga:**<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, pojawi się w przeglądarce z komunikatem "Błąd prywatności". Dlatego użytkownicy, którzy mają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że zapory aplikacji pakietowych i sieci web chroniące usługę aplikacji umożliwiają przekazywanie sesji HTTPS.</li></ul>|
|Aplikacja function powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do protokołu HTTPS w wybranych zasobach. <br>**Uwaga:**<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, pojawi się w przeglądarce z komunikatem "Błąd prywatności". Dlatego użytkownicy, którzy mają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że zapory aplikacji pakietowych i sieci web chroniące usługę aplikacji umożliwiają przekazywanie sesji HTTPS.</li></ul>|
|Aplikacja API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do protokołu HTTPS w wybranych zasobach. <br>**Uwaga:**<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, pojawi się w przeglądarce z komunikatem "Błąd prywatności". Dlatego użytkownicy, którzy mają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że zapory aplikacji pakietowych i sieci web chroniące usługę aplikacji umożliwiają przekazywanie sesji HTTPS.</li></ul>|
|Zdalne debugowanie powinno być wyłączone dla aplikacji sieci Web|Ta akcja wyłącza debugowanie zdalne.|
|Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji|Ta akcja wyłącza debugowanie zdalne.|
|Zdalne debugowanie powinno być wyłączone dla aplikacji interfejsu API|Ta akcja wyłącza debugowanie zdalne.|
|CORS nie powinien zezwalać każdemu zasobowi na dostęp do aplikacji sieci Web|Ta akcja blokuje innym domenom dostęp do aplikacji sieci Web. Aby zezwolić na określone domeny, wprowadź je w polu Dozwolone źródła (oddzielone przecinkami). <br>**Uwaga:** Pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami.'Tytuł pola Param: 'Dozwolone źródła'|
|CORS nie powinien zezwalać każdemu zasobom na dostęp do aplikacji function|Ta akcja blokuje innym domenom dostęp do aplikacji funkcji. Aby zezwolić na określone domeny, wprowadź je w polu Dozwolone źródła (oddzielone przecinkami). <br>**Uwaga:** Pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami.'Tytuł pola Param: 'Dozwolone źródła'|
|Usługa CORS nie powinna zezwalać każdemu zasobom na dostęp do aplikacji interfejsu API|Ta akcja blokuje innym domenom dostęp do aplikacji interfejsu API. Aby zezwolić na określone domeny, wprowadź je w polu Dozwolone źródła (oddzielone przecinkami). <br>**Uwaga:** Pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami.'Tytuł pola Param: 'Dozwolone źródła'|
|Agent monitorowania powinien być włączony na maszynach wirtualnych|Ta akcja instaluje agenta monitorowania na wybranych maszynach wirtualnych. Wybierz obszar roboczy dla agenta, do który ma się zgłosić.<ul><li>Jeśli zasady aktualizacji jest ustawiona na automatyczne, zostanie wdrożony na nowe istniejące wystąpienia.</li><li>Jeśli zasady aktualizacji są ustawione ręcznie i chcesz zainstalować agenta w istniejących wystąpieniach, zaznacz to pole wyboru. [Dowiedz się więcej](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Dzienniki diagnostyczne w magazynie kluczy powinny być włączone|Ta akcja umożliwia dzienniki diagnostyczne w magazynach kluczy. Dzienniki diagnostyczne i metryki są zapisywane w wybranym obszarze roboczym.|
|Dzienniki diagnostyczne w magistrali usługowej powinny być włączone|Ta akcja umożliwia dzienniki diagnostyczne na magistrali usług. Dzienniki diagnostyczne i metryki są zapisywane w wybranym obszarze roboczym.|

## <a name="next-steps"></a>Następne kroki

W tym dokumencie pokazano, jak korygować zalecenia w Centrum zabezpieczeń. Aby dowiedzieć się więcej o Centrum zabezpieczeń, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.
