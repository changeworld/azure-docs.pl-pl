---
title: Monitorowanie integralności plików w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować monitorowanie integralności plików (FIM) w usłudze Azure Security Center przy użyciu tego przewodnika.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 4d65ca8d97e1cca81886259d4f15cc880e45be9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604277"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorowanie integralności plików w usłudze Azure Security Center
Dowiedz się, jak skonfigurować monitorowanie integralności plików (FIM) w usłudze Azure Security Center przy użyciu tego przewodnika.

## <a name="what-is-fim-in-security-center"></a>Co to jest FIM w Centrum zabezpieczeń?
Monitorowanie integralności plików (FIM), znane również jako monitorowanie zmian, sprawdza pliki i rejestry systemu operacyjnego, oprogramowania aplikacji i innych pod kątem zmian, które mogą wskazywać na atak. Metoda porównania jest używana do określenia, czy bieżący stan pliku różni się od ostatniego skanowania pliku. Można wykorzystać to porównanie, aby ustalić, czy prawidłowe lub podejrzane modyfikacje zostały wprowadzone do plików.

Monitorowanie integralności plików w centrum zabezpieczeń sprawdza integralność plików systemu Windows, rejestru systemu Windows i plików linux. Wybierz pliki, które mają być monitorowane, włączając fim. Usługa Security Center monitoruje pliki z włączoną funkcją FIM dla działań, takich jak:

- Tworzenie i usuwanie plików i rejestru
- Modyfikacje plików (zmiany rozmiaru pliku, listy kontroli dostępu i skrót zawartości)
- Modyfikacje rejestru (zmiany rozmiaru, listy kontroli dostępu, typ i zawartość)

Usługa Security Center zaleca jednostkom monitorowanie, na którym można łatwo włączyć funkcję FIM. Można również zdefiniować własne zasady FIM lub jednostki do monitorowania. Ten przewodnik pokazuje, jak to zrobić.

> [!NOTE]
> Funkcja monitorowania integralności plików (FIM) działa na komputerach z systemem Windows i Linux oraz na maszynach wirtualnych i jest dostępna w warstwie Standardowa Usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. FIM przesyła dane do obszaru roboczego usługi Log Analytics. Obowiązują opłaty za dane, oparte na ilości przesłanych danych. Aby dowiedzieć się więcej, zobacz [Ceny usługi Log Analytics.](https://azure.microsoft.com/pricing/details/log-analytics/)

FIM używa rozwiązania śledzenia zmian platformy Azure do śledzenia i identyfikowania zmian w środowisku. Gdy monitorowanie integralności plików jest włączone, masz zasób **śledzenia zmian** typu **Rozwiązanie**. Aby uzyskać szczegółowe informacje o częstotliwości zbierania danych, zobacz [szczegóły zbierania danych śledzenia zmian](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) w usłudze Azure Change Tracking.

> [!NOTE]
> Jeśli usuniesz zasób **Śledzenie zmian,** wyłączysz również funkcję Monitorowania integralności plików w Centrum zabezpieczeń.

## <a name="which-files-should-i-monitor"></a>Które pliki należy monitorować?
Należy pomyśleć o plikach, które są krytyczne dla systemu i aplikacji przy wyborze plików do monitorowania. Rozważ wybranie plików, których nie spodziewasz się zmienić bez planowania. Wybranie plików, które są często zmieniane przez aplikacje lub system operacyjny (takie jak pliki dziennika i pliki tekstowe) tworzy dużo szumu, który utrudnia identyfikację ataku.

Usługa Security Center zaleca, które pliki należy monitorować jako domyślne zgodnie ze znanymi wzorcami ataków, które zawierają zmiany w plikach i rejestrze.

## <a name="using-file-integrity-monitoring"></a>Korzystanie z monitorowania integralności plików
1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W lewym okienku w obszarze **Zaawansowana ochrona w chmurze**wybierz pozycję **Monitorowanie integralności plików**.
![Pulpit nawigacyjny usługi Security Center][1]

Zostanie otwarte **monitorowanie integralności plików.**
  ![Pulpit nawigacyjny usługi Security Center][2]

Dla każdego obszaru roboczego podano następujące informacje:

- Całkowita liczba zmian, które wystąpiły w ostatnim tygodniu (może pojawić się myślnik "-", jeśli FIM nie jest włączony w obszarze roboczym)
- Całkowita liczba komputerów i maszyn wirtualnych raportujących w obszarze roboczym
- Położenie geograficzne obszaru roboczego
- Subskrypcja platformy Azure, w ramach których znajduje się obszar roboczy

Dla obszaru roboczego mogą być również wyświetlane następujące przyciski:

- ![Ikona Włącz][3] Wskazuje, że fim nie jest włączony dla obszaru roboczego. Wybranie obszaru roboczego umożliwia włączenie fim na wszystkich komputerach w obszarze roboczym.
- ![Ikona][4] planu uaktualnienia Wskazuje, że obszar roboczy lub subskrypcja nie jest uruchomiona w warstwie Standardowa usługi Security Center. Aby korzystać z funkcji FIM, subskrypcja musi być uruchomiona w standardzie.  Wybranie obszaru roboczego umożliwia uaktualnienie do standardu. Aby dowiedzieć się więcej o warstwie Standardowa i sposobie uaktualniania, zobacz [Warstwa Standardowa centrum zabezpieczeń uaktualnienia do standardu, aby uzyskać rozszerzone zabezpieczenia.](security-center-pricing.md)
- Puste miejsce (nie ma przycisku) oznacza, że fim jest już włączony w obszarze roboczym.

W obszarze **Monitorowanie integralności plików**można wybrać obszar roboczy, aby włączyć fim dla tego obszaru roboczego, wyświetlić pulpit nawigacyjny monitorowania integralności plików dla tego obszaru roboczego lub [uaktualnić](security-center-pricing.md) obszar roboczy do standardu.

## <a name="enable-fim"></a>Włącz FIM
Aby włączyć fim w obszarze roboczym:

1. W obszarze **Monitorowanie integralności plików**wybierz obszar roboczy za pomocą przycisku **Włącz.**
2. **Włącz monitorowanie integralności plików** otwiera wyświetlanie liczby komputerów z systemem Windows i Linux w obszarze roboczym.

   ![Włącz monitorowanie integralności plików][5]

   Na liście znajdują się również zalecane ustawienia dla systemów Windows i Linux.  Rozwiń **pliki systemu Windows**, **Rejestr**i **Linux,** aby wyświetlić pełną listę zalecanych elementów.

3. Odznacz wszystkie zalecane encje, do których nie chcesz stosować FIM.
4. Wybierz **zastosuj monitorowanie integralności plików,** aby włączyć fim.

> [!NOTE]
> Ustawienia można zmienić w dowolnym momencie. Zobacz Edytowanie monitorowanych jednostek poniżej, aby dowiedzieć się więcej.
>
>

## <a name="view-the-fim-dashboard"></a>Wyświetlanie pulpitu nawigacyjnego FIM
Pulpit nawigacyjny **monitorowania integralności plików** jest wyświetlany dla obszarów roboczych, w których fim jest włączony. Pulpit nawigacyjny FIM otwiera się po włączeniu funkcji FIM w obszarze roboczym lub po wybraniu obszaru roboczego w oknie **Monitorowanie integralności plików,** w którym włączono już fim.

![Pulpit nawigacyjny monitorowania integralności plików][6]

Pulpit nawigacyjny FIM dla obszaru roboczego wyświetla następujące szczegóły:

- Całkowita liczba maszyn podłączonych do obszaru roboczego
- Całkowita liczba zmian, które wystąpiły w wybranym okresie
- Podział typu zmiany (pliki, rejestr)
- Podział kategorii zmian (zmodyfikowany, dodany, usunięty)

Wybranie opcji Filtr w górnej części pulpitu nawigacyjnego umożliwia zastosowanie okresu, dla którego chcesz zobaczyć zmiany.

![Filtr okresu czasu][7]

Karta **Komputery** (pokazana powyżej) zawiera listę wszystkich maszyn zgłaszających się do tego obszaru roboczego. Dla każdej maszyny pulpit nawigacyjny zawiera listę:

- Całkowite zmiany, które wystąpiły w wybranym okresie czasu
- Podział całkowitych zmian w miarę zmian w pliku lub w rejestrze

**Wyszukiwanie dzienników** zostanie otwarte po wprowadzeniu nazwy komputera w polu wyszukiwania lub wybraniu komputera wymienionego na karcie Komputery. Można rozwinąć zmianę, aby uzyskać więcej informacji.

![Przeszukiwanie dzienników][8]

Karta **Zmiany** (pokazana poniżej) zawiera listę wszystkich zmian dla obszaru roboczego w wybranym okresie. Dla każdej jednostki, która została zmieniona, pulpit nawigacyjny zawiera listę:

- Komputer, na który nastąpiła zmiana
- Typ zmiany (rejestr lub plik)
- Kategoria zmian (zmodyfikowana, dodana, usunięta)
- Data i godzina zmiany

![Zmiany w obszarze roboczym][9]

**Szczegóły zmiany** zostaną otwarte po wprowadzeniu zmiany w polu wyszukiwania lub wybraniu encji wymienionej na karcie **Zmiany.**

![Zmienianie szczegółów][10]

## <a name="edit-monitored-entities"></a>Edytowanie monitorowanych encji

1. Wróć do **pulpitu nawigacyjnego Monitorowania integralności plików** i wybierz pozycję **Ustawienia**.

   ![Ustawienia][11]

   Zostanie otwarta **konfiguracja obszaru roboczego** z trzema kartami: **Rejestr systemu Windows,** **Pliki systemu Windows**i Pliki systemu **Linux**. Każda karta zawiera listę encji, które można edytować w tej kategorii. Dla każdej wymienionej jednostki usługa Security Center identyfikuje, czy funkcja FIM jest włączona (true) czy nie włączona (fałsz).  Edycja encji umożliwia włączenie lub wyłączenie fim.

   ![Konfiguracja obszaru roboczego][12]

2. Wybierz ochronę tożsamości. W tym przykładzie wybraliśmy element w obszarze Rejestr systemu Windows. Zostanie **otwarta funkcja Edytuj dla śledzenia zmian.**

   ![Edytowanie lub zmienianie śledzenia][13]

W obszarze **Edytuj śledzenie zmian** możesz:

- Włącz (True) lub wyłącz (False) monitorowanie integralności plików
- Podaj lub zmieniaj nazwę encji
- Podaj lub zmieniaj wartość lub ścieżkę
- Usuwanie encji, odrzucanie zmiany lub zapisywanie zmiany

## <a name="add-a-new-entity-to-monitor"></a>Dodawanie nowej encji do monitorowania
1. Wróć do **pulpitu nawigacyjnego monitorowania integralności plików** i wybierz pozycję **Ustawienia** u góry. Zostanie otwarta **konfiguracja obszaru roboczego.**
2. W obszarze **Konfiguracja obszaru roboczego**wybierz kartę typu encji, którą chcesz dodać: Rejestr systemu Windows, Pliki systemu Windows lub Pliki systemu Linux. W tym przykładzie wybraliśmy **Linux Files**.

   ![Dodawanie nowego elementu do monitorowania][14]

3. Wybierz pozycję **Dodaj**. Zostanie otwarty **przycisk Dodaj do śledzenia zmian.**

   ![Wprowadzanie żądanych informacji][15]

4. Na stronie **Dodaj** wpisz żądane informacje i wybierz pozycję **Zapisz**.

## <a name="disable-monitored-entities"></a>Wyłączanie monitorowanych jednostek
1. Wróć do pulpitu nawigacyjnego **monitorowania integralności plików.**
2. Wybierz obszar roboczy, w którym fim jest aktualnie włączony. Obszar roboczy jest włączony dla FIM, jeśli brakuje przycisku Włącz lub planu uaktualnienia.

   ![Wybieranie obszaru roboczego, w którym jest włączony protokół FIM][16]

3. W obszarze Monitorowanie integralności plików wybierz pozycję **Ustawienia**.

   ![Wybieranie pozycji Ustawienia][17]

4. W obszarze **Konfiguracja obszaru roboczego**wybierz grupę, w której opcja Włączone jest **ustawiona** na wartość true.

   ![Konfiguracja obszaru roboczego][18]

5. W obszarze **Okno Edytuj dla śledzenia zmian** ustaw Włączone jako **Fałsz.**

   ![Ustaw włączone fałsz][19]

6. Wybierz **pozycję Zapisz**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitorowanie folderów i ścieżek przy użyciu symboli wieloznacznych

Użyj symboli wieloznacznych, aby uprościć śledzenie w katalogach. Podczas konfigurowania monitorowania folderów przy użyciu symboli wieloznacznych obowiązują następujące reguły:
-   Symbole wieloznaczne są wymagane do śledzenia wielu plików.
-   Symbole wieloznaczne mogą być używane tylko w ostatnim segmencie ścieżki, takim jak C:\folder\file lub /etc/*.conf
-   Jeśli zmienna środowiskowa zawiera ścieżkę, która nie jest prawidłowa, sprawdzanie poprawności zakończy się pomyślnie, ale ścieżka zakończy się niepowodzeniem po uruchomieniu zapasów.
-   Podczas ustawiania ścieżki należy unikać ścieżek\*ogólnych, takich jak c: .*, co spowoduje przechodzenie zbyt wielu folderów.

## <a name="disable-fim"></a>Wyłącz fim
Można wyłączyć FIM. FIM używa rozwiązania śledzenia zmian platformy Azure do śledzenia i identyfikowania zmian w środowisku. Wyłączając FIM, można usunąć rozwiązanie śledzenia zmian z wybranego obszaru roboczego.

1. Aby wyłączyć fim, wróć do pulpitu nawigacyjnego **monitorowania integralności plików.**
2. Wybierz obszar roboczy.
3. W obszarze **Monitorowanie integralności plików**wybierz pozycję **Wyłącz**.

   ![Wyłącz fim][20]

4. Wybierz **pozycję Usuń,** aby wyłączyć.

## <a name="next-steps"></a>Następne kroki
W tym artykule nauczysz się używać monitorowania integralności plików (FIM) w Centrum zabezpieczeń. Aby dowiedzieć się więcej o umyciu Centrum zabezpieczeń, zobacz następujące strony:

* [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) — dowiedz się, jak zalecenia pomagają chronić zasoby platformy Azure.
* [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md)— dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie](security-center-managing-and-responding-alerts.md)— dowiedz się, jak zarządzać alertami zabezpieczeń i odpowiadać na nie.
* [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — dowiedz się, jak monitorować stan kondycji rozwiązań partnerskich.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
