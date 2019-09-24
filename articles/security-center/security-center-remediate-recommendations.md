---
title: Koryguj zalecenia w Azure Security Center | Microsoft Docs
description: W tym dokumencie wyjaśniono, jak skorygować zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure i zachować zgodność z zasadami zabezpieczeń.
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
ms.date: 08/18/2019
ms.author: memildin
ms.openlocfilehash: 9bd1586193d2e36c370217e37b77409298821a67
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201003"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Koryguj zalecenia w Azure Security Center

Zalecenia zawierają sugestie dotyczące lepszego zabezpieczania zasobów.  Zalecenie jest implementowane przez wykonanie czynności zaradczych, które zostały podane w zaleceniu. 

## Kroki zaradcze<a name="remediation-steps"></a>

Po przejrzeniu wszystkich zaleceń Zdecyduj, które z nich należy skorygować w pierwszej kolejności. Zalecamy użycie [wpływu na ocenę](security-center-recommendations.md#monitor-recommendations) , aby pomóc w ustaleniu, co należy zrobić w pierwszej kolejności.

1. Na liście kliknij zalecenie.
1. Postępuj zgodnie z instrukcjami w sekcji **kroki zaradcze** . Każde zalecenie ma swój własny zestaw instrukcji. Poniżej przedstawiono procedurę korygowania służącą do konfigurowania aplikacji w celu zezwalania na ruch tylko za pośrednictwem protokołu HTTPS.

    ![Szczegóły rekomendacji](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## Korygowanie poprawek jednym kliknięciem (wersja zapoznawcza)<a name="one-click"></a>

Poprawka jednego kliknięcia umożliwia skorygowanie zalecenia dotyczącego ilości zasobów przy użyciu jednego kliknięcia. Jest to opcja dostępna tylko dla konkretnych zaleceń. Naprawa jednego kliknięcia upraszcza korygowanie i umożliwia szybkie ulepszanie bezpiecznego oceny i zwiększenie bezpieczeństwa w środowisku.

Aby zaimplementować korygowanie jednym kliknięciem:

1. Na liście zaleceń, które mają etykietę **1-kliknięcie-Naprawa** , kliknij zalecenie.  

   ![Wybierz poprawkę jednokrotnego kliknięcia](./media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)

2. Na karcie **zasoby w złej kondycji** Wybierz zasoby, dla których chcesz wdrożyć zalecenie, a następnie kliknij przycisk **Koryguj**. 

    > [!NOTE]
    > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień, aby je modyfikować.

3. W polu potwierdzenia Przeczytaj szczegóły dotyczące korygowania i implikacje. 

   ![Naprawa jednego kliknięcia](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Implikacje są wymienione w szarym polu okna **korygowanie zasobów** , które jest otwierane po kliknięciu przycisku **Koryguj**. Określają, jakie zmiany są wykonywane podczas wykonywania korekty 1 kliknięcia.

4. W razie potrzeby Wstaw odpowiednie parametry i zatwierdź korygowanie.

    > [!NOTE]
    > Ukończenie korygowania zasobów na karcie **zasoby w dobrej kondycji** może potrwać kilka minut. Aby wyświetlić akcje korygowania, sprawdź [Dziennik aktywności](#activity-log).

5. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## Rejestrowanie korygowania jednym kliknięciem w dzienniku aktywności<a name="activity-log"></a>

Operacja korygowania używa wdrożenia szablonu lub wywołania interfejsu API poprawek REST w celu zastosowania konfiguracji w zasobie. Te operacje są rejestrowane w [dzienniku aktywności platformy Azure](../azure-resource-manager/resource-group-audit.md).


## <a name="recommendations-with-one-click-remediation"></a>Zalecenia dotyczące korygowania jednego kliknięcia

|Zalecenie|Domniemanie|
|---|---|
|Inspekcja na serwerach SQL powinna być włączona|Ta akcja spowoduje włączenie inspekcji SQL na tych serwerach i w ich bazach danych. <br>**Uwaga**: <ul><li>Dla każdego regionu wybranych serwerów SQL konto magazynu do zapisywania dzienników inspekcji zostanie utworzone i udostępnione przez wszystkie serwery w tym regionie.</li><li>Aby zapewnić odpowiednią inspekcję, nie należy usuwać ani zmieniać nazw grup zasobów ani kont magazynu.</li></ul>|
|Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL|Ta akcja spowoduje włączenie zabezpieczeń SQL Advanced Data Security (AD) w wybranych wystąpieniach zarządzanych SQL. <br>**Uwaga**: <ul><li>Dla każdego regionu i grupy zasobów wybranych wystąpień programu SQL konto magazynu do zapisywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li> Usługa ADS jest naliczana zgodnie z $15 na wystąpienie zarządzane SQL.</li></ul>|
|Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Ta akcja spowoduje włączenie oceny luk w zabezpieczeniach SQL na wybranych wystąpieniach zarządzanych przez program SQL. <br>**Uwaga**:<ul><li>Ocena luk w zabezpieczeniach SQL jest częścią pakietu SQL Advanced Data Security (AD). Jeśli usługa ADS nie została włączona, zostanie automatycznie włączona w zarządzanym wystąpieniu.</li><li>Dla każdego regionu i grupy zasobów wybranych wystąpień zarządzanych przez program SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li>Usługa ADS jest naliczana zgodnie z $15 na program SQL Server.</li></ul>||
|Zaawansowana ochrona danych powinna być włączona na serwerach SQL|Ta akcja spowoduje włączenie zaawansowanych zabezpieczeń danych (ADS) na tych wybranych serwerach i ich bazach danych. <br>**Uwaga**:<ul><li>Dla każdego regionu i grupy zasobów wybranych serwerów SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie serwery w tym regionie. <</li><li>Usługa ADS jest naliczana zgodnie z $15 na program SQL Server.</li></ul>||
|Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Ta akcja spowoduje włączenie oceny luk w zabezpieczeniach programu SQL dla wybranych serwerów i ich baz danych. <br>**Uwaga**:<ul><li>Ocena luk w zabezpieczeniach SQL jest częścią pakietu SQL Advanced Data Security (AD). Jeśli usługa ADS nie jest już włączona, zostanie automatycznie włączona na serwerze SQL.</li><li>Dla każdego regionu i grupy zasobów wybranych serwerów SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li>Usługa ADS jest naliczana zgodnie z $15 na program SQL Server.</li></ul>||
|Należy włączyć funkcję przezroczystego szyfrowania danych w bazach danych SQL.|Ta akcja włącza SQL Database Transparent Data Encryption (TDE) dla wybranych baz danych. <br>**Uwaga**: Domyślnie zostaną użyte klucze TDE zarządzane przez usługę. 
|Należy włączyć bezpieczny transfer do kont magazynu|Ta akcja aktualizuje zabezpieczenia konta magazynu tak, aby zezwalać na żądania przez bezpieczne połączenia. (HTTPS). <br>**Uwaga**:<ul><li>Wszystkie żądania przy użyciu protokołu HTTP zostaną odrzucone.</li><li>W przypadku korzystania z usługi Azure Files połączenie bez szyfrowania będzie kończyć się niepowodzeniem, w tym scenariusze korzystające z protokołu SMB 2,1, protokołu SMB 3,0 bez szyfrowania i niektórych typów klienta SMB systemu Linux.  Dowiedz się więcej.</li></ul>|
|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do HTTPS na wybranych zasobach. <br>**Uwaga**:<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, będzie wyświetlany w przeglądarce z powodu błędu "Prywatność". W związku z tym użytkownicy, którzy posiadają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że pakiet i zapory aplikacji sieci Web chronią usługę App Service, Zezwalaj na przekazywanie sesji HTTPS.</li></ul>|
|Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do HTTPS na wybranych zasobach. <br>**Uwaga**:<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, będzie wyświetlany w przeglądarce z powodu błędu "Prywatność". W związku z tym użytkownicy, którzy posiadają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że pakiet i zapory aplikacji sieci Web chronią usługę App Service, Zezwalaj na przekazywanie sesji HTTPS.</li></ul>|
|Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do HTTPS na wybranych zasobach. <br>**Uwaga**:<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, będzie wyświetlany w przeglądarce z powodu błędu "Prywatność". W związku z tym użytkownicy, którzy posiadają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że pakiet i zapory aplikacji sieci Web chronią usługę App Service, Zezwalaj na przekazywanie sesji HTTPS.</li></ul>|
|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Ta akcja powoduje wyłączenie debugowania zdalnego.|
|Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji|Ta akcja powoduje wyłączenie debugowania zdalnego.|
|Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API|Ta akcja powoduje wyłączenie debugowania zdalnego.|
|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Ta akcja blokuje dostęp do aplikacji sieci Web innym domenom. Aby zezwolić na określone domeny, wprowadź je w polu dozwolone źródła (oddzielone przecinkami). <br>**Uwaga**: Pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami. "Nazwa pola PARAM: "Dozwolone źródła"|
|Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji|Ta akcja blokuje dostęp do aplikacji funkcji przez inne domeny. Aby zezwolić na określone domeny, wprowadź je w polu dozwolone źródła (oddzielone przecinkami). <br>**Uwaga**: Pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami. "Nazwa pola PARAM: "Dozwolone źródła"|
|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API|Ta akcja uniemożliwia innym domenom uzyskanie dostępu do aplikacji interfejsu API. Aby zezwolić na określone domeny, wprowadź je w polu dozwolone źródła (oddzielone przecinkami). <br>**Uwaga**: Pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami. "Nazwa pola PARAM: "Dozwolone źródła"|
|Agent monitorowania powinien być włączony na maszynach wirtualnych|Ta akcja powoduje zainstalowanie agenta monitorowania na wybranych maszynach wirtualnych. Wybierz obszar roboczy, do którego ma zostać zaraportowany Agent.<ul><li>Jeśli zasady aktualizacji są ustawione na automatyczne, zostaną wdrożone w nowych istniejących wystąpieniach.</li><li>Jeśli zasady aktualizacji są ustawione na ręczne i chcesz zainstalować agenta w istniejących wystąpieniach, zaznacz pole wyboru.  [Dowiedz się więcej](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Dzienniki diagnostyczne w Key Vault powinny być włączone|Ta akcja włącza dzienniki diagnostyczne w magazynach kluczy. Dzienniki diagnostyczne i metryki są zapisywane w wybranym obszarze roboczym.|
|Należy włączyć dzienniki diagnostyczne w usłudze Service Bus|Ta akcja powoduje włączenie dzienników diagnostycznych w usłudze Service Bus. Dzienniki diagnostyczne i metryki są zapisywane w wybranym obszarze roboczym.|

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korygowania zaleceń w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
