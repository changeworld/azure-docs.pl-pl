---
title: Rozwiązywanie problemów
description: Jak rozwiązywać problemy z usługą Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ab0d3aac6944e331223d91eb98b50b3308be12c3
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797636"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

W poniższych sekcjach opisano typowe problemy z usługą Azure FarmBeats i sposoby ich naprawiania.

Aby uzyskać dodatkową pomoc, Zapisz się do nas na farmbeatssupport@microsoft.com, Dołącz plik Deployer. log do tej wiadomości e-mail.

 Wykonaj następujące kroki, aby pobrać plik Deployer. log:

1. Wyróżniona ikona i wybierz opcję **Pobierz** z listy rozwijanej.

    ![Farmy projektów](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Na następnym ekranie wprowadź ścieżkę do pliku Deployer. log. Na przykład farmbeats-Deployer. log.

## <a name="sensor-telemetry"></a>Dane telemetryczne czujnika

### <a name="telemetry-not-seen"></a>Nie widzisz danych telemetrycznych

**Objaw**: wdrożono urządzenia/czujniki i połączono FarmBeats z partnerem urządzenia; nie można jednak uzyskać/wyświetlić danych telemetrii w usłudze FarmBeats.

**Działanie naprawcze**: odwiedź Azure Portal i wykonaj następujące kroki:

1. Przejdź do grupy zasobów centrum danych FarmBeats.   
2. Wybierz **centrum zdarzeń** (DatafeedEventHubNamespace....)  i sprawdź liczbę wiadomości przychodzących.   
3. W przypadku **braku komunikatów przychodzących**skontaktuj się z partnerem urządzenia.  
4. Jeśli istnieją **przychodzące komunikaty**, skontaktuj się farmbeatssupport@microsoft.com z centrum danych i dziennikami akceleratora i przechwyconą telemetrię.

Zobacz [sekcję Logs](#collect-logs-manually) dokumentu, aby poznać sposób pobierania dzienników.    

### <a name="device-appears-offline"></a>Urządzenie jest w trybie offline

**Objawy**: urządzenia są zainstalowane i połączony FarmBeats z partnerem urządzenia. Urządzenia są w trybie online i wysyłają dane telemetryczne, ale są wyświetlane w trybie offline.

**Działanie naprawcze**: Interwał raportowania nie jest skonfigurowany dla tego urządzenia. Skontaktuj się z producentem urządzenia, aby ustawić interwał raportowania. 

### <a name="error-deleting-a-resource"></a>Błąd usuwania zasobu

Poniżej przedstawiono typowe scenariusze błędów podczas usuwania urządzenia:  

**Komunikat**: urządzenie jest przywoływane w czujników: istnieje co najmniej jedna czujników skojarzona z urządzeniem. Usuń czujniki, a następnie usuń urządzenie.  

**Znaczenie**: urządzenie jest skojarzone z wieloma czujnikami wdrożonymi w farmie.   

**Działanie naprawcze**:  

1. Usuń czujniki skojarzone z urządzeniem za pomocą akceleratora.  
2. W przypadku, gdy chcesz skojarzyć czujniki z innym urządzeniem, skontaktuj się z partnerem urządzenia, aby to zrobić.  
3. Usuń urządzenie przy użyciu wywołania interfejsu API usuwania Ustawianie parametru Force jako "true".  

**Komunikat**: urządzenie jest przywoływane na urządzeniach jako ParentDeviceId: istnieje co najmniej jedno urządzenie, które jest skojarzone z urządzeniem jako urządzenia podrzędne. Usuń je, a następnie usuń to urządzenie.  

**Znaczenie**: urządzenie ma skojarzone inne urządzenia  

**Akcja naprawcza**

1. Usuwanie urządzeń skojarzonych z określonym urządzeniem  
2. Usuń określone urządzenie  

    > [!NOTE]
    > Nie można usunąć urządzenia, jeśli są z nim skojarzone czujniki. Aby uzyskać więcej informacji na temat usuwania skojarzonych czujników, zobacz sekcję [usuwanie czujników](get-sensor-data-from-sensor-partner.md) w rozdziale pobieranie danych z czujnika.


## <a name="issues-with-jobs"></a>Problemy związane z zadaniami

### <a name="farmbeats-internal-error"></a>Błąd wewnętrzny FarmBeats

**Komunikat**: FarmBeats błąd wewnętrzny, zobacz Przewodnik rozwiązywania problemów, aby uzyskać więcej szczegółów.

**Działanie naprawcze**: może to być spowodowane tymczasowym błędem w potoku danych. Utwórz zadanie ponownie. Jeśli błąd będzie się powtarzać, Dodaj błąd w wpisie na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Rozwiązywanie problemów z akceleratorem

### <a name="access-control"></a>Kontrola dostępu

**Wystąpił błąd podczas dodawania przypisania roli**

**Komunikat**: nie znaleziono pasujących użytkowników

**Akcja naprawcza**: Sprawdź identyfikator e-mail, dla którego chcesz wykonać przypisanie roli. Identyfikator poczty e-mail musi być dokładnym dopasowaniem zarejestrowanego dla tego użytkownika w Active Directory. Jeśli błąd będzie się powtarzać, Dodaj błąd w wpisie na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Nie można zalogować się do akceleratora

**Komunikat**: błąd: nie masz uprawnień do wywołania usługi. Skontaktuj się z administratorem, aby uzyskać autoryzację.

**Akcja naprawcza**: poproszenie administratora o autoryzację w celu uzyskania dostępu do wdrożenia FarmBeats. Można to zrobić, wykonując wpis interfejsów API RoleAssignment lub za pomocą Access Control w okienku ustawienia w akceleratorze.  

Jeśli użytkownik został już dodany i ma ten błąd, spróbuj ponownie, odświeżając stronę.  Jeśli błąd będzie się powtarzać, Dodaj błąd w wpisie na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com.

![Farmy projektów](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Akcelerator — wystąpił nieznany błąd  

**Komunikat**: błąd: Wystąpił nieznany błąd

**Działanie naprawcze**: dzieje się tak, jeśli strona bezczynna nie zostanie zbyt długa. Odśwież stronę.  

Jeśli błąd nadal występuje, Dodaj błąd w wpisie na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com

**Akcelerator FarmBeats nie pokazuje najnowszej wersji nawet po uaktualnieniu FarmBeatsDeployment**

**Działanie naprawcze**: dzieje się tak z powodu trwałości procesu roboczego usługi w przeglądarce.
Zamknij wszystkie karty przeglądarki z otwartym akceleratorem, a następnie zamknij okno przeglądarki. Uruchom nowe wystąpienie przeglądarki i ponownie załaduj identyfikator URI akceleratora. Spowoduje to załadowanie nowej wersji akceleratora.

## <a name="sentinel-imagery-related-issues"></a>Problemy związane z obrazami kontrolnymi

### <a name="sentinel-wrong-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło

**Komunikat o niepowodzeniu zadania**; Do uzyskania dostępu do tego zasobu wymagane jest pełne uwierzytelnianie.

**Akcja korekcji**: ponownie uruchom Instalatora w celu uaktualnienia centrum danych przy użyciu prawidłowej nazwy użytkownika i hasła.

**Działanie naprawcze**: Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat wynoszącego 5-7 dni i sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>Nieprawidłowy adres URL centrum centrów kontrolnych lub jest niedostępny 

**Komunikat o niepowodzeniu zadania**: Niestety, wystąpił problem. Strona, do której próbujesz uzyskać dostęp, jest (tymczasowo) niedostępna. 

**Działanie naprawcze**:
1.  Otwórz adres URL wskaźnikiem (https://scihub.copernicus.eu/dhus/) w przeglądarce i sprawdź, czy witryna sieci Web jest dostępna. 
2.  Jeśli witryna sieci Web jest niedostępna, sprawdź, czy nie ma żadnej zapory lub sieci firmy itp., a następnie wykonaj wymagane kroki, aby zezwolić na powyższy adres URL. 
3.  Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat 5-7 dni i sprawdź, czy zadanie zakończyło się pomyślnie.  

### <a name="sentinel-server-down-for-maintenance"></a>Serwer wskaźnikowy do konserwacji

**Komunikat o niepowodzeniu zadania**: Copernicus otwartym centrum dostępu zostanie wkrótce zajdzie! Przepraszamy za niedogodności, ale w tej chwili wykonujemy pewne czynności konserwacyjne. Wkrótce będziemy z powrotem w trybie online! 

**Działanie naprawcze**:

1.  Ten problem może wystąpić, jeśli na serwerze wskaźnikowym są wykonywane jakiekolwiek działania konserwacyjne. 
2.  Jeśli dowolne zadanie/potok zakończy się niepowodzeniem z powyższymi przyczynami, prześlij ponownie zadanie po pewnym czasie. 
3.  Użytkownik może odwiedzić https://scihub.copernicus.eu/news/, aby sprawdzić informacje o wszelkich planowanych/nieplanowanych działaniach związanych z konserwacją.  
4.  Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat 5-7 dni i sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Osiągnięto maksymalną liczbę połączeń

**Komunikat o niepowodzeniu zadania**: Maksymalna liczba dwóch współbieżnych przepływów osiągniętych przez użytkownika "<username>" 

**Akcja naprawcza**
1.  Jeśli jakieś zadanie zakończy się niepowodzeniem z powyższym powodem, w innym wdrożeniu/oprogramowaniu jest używane to samo konto wskaźnikowe. 
2.  Użytkownik może utworzyć nowe konto wskaźnikowego i ponownie uruchomić Instalatora w celu uaktualnienia centrum danych przy użyciu nowej nazwy użytkownika i hasła.  
3.  Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat 5-7 dni i sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-server-refused-connection"></a>Serwer wskaźnikowy odmówił połączenia 

**Komunikat o niepowodzeniu zadania**:

Serwer odrzucił połączenie w: http://172.30.175.69:8983/solr/dhus 

**Działania naprawcze**: ten problem może wystąpić, jeśli na serwerze wskaźnikowym są wykonywane jakiekolwiek działania konserwacyjne. 
1.  Jeśli dowolne zadanie/potok zakończy się niepowodzeniem z powyższymi przyczynami, prześlij ponownie zadanie po pewnym czasie. 
2.  Użytkownik może odwiedzić https://scihub.copernicus.eu/news/, aby sprawdzić informacje o wszelkich planowanych/nieplanowanych działaniach związanych z konserwacją.  
3.  Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat 5-7 dni i sprawdź, czy zadanie zakończyło się pomyślnie.


## <a name="collect-logs-manually"></a>Zbierz dzienniki ręcznie

[Zainstaluj i Wdróż]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) w Eksplorator usługi Azure Storage.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Jak zbierać dzienniki zadań ADF centrum danych
1. Zaloguj się do https://portal.azure.com
2. W polu tekstowym **Wyszukiwanie** Wyszukaj grupę zasobów centrum danych FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów centrum danych, która została określona w czasie wdrażania FarmBeats.

Na pulpicie nawigacyjnym grupy zasobów Wyszukaj konto magazynu (datahublogs....). Na przykład datahublogsmvxmq  

1.  Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
2.  Na stronie (datahubblogs....) wybierz pozycję **Otwórz w Eksploratorze** , aby wyświetlić **otwarte Eksplorator usługi Azure Storage** aplikacji.
3.  W lewym panelu (datahubblogs...), **kontenery obiektów BLOB**, wybierz pozycję **dzienniki zadań**.
4.  Na karcie **dzienniki zadania** wybierz pozycję **Pobierz**.
5.  Wybierz lokalizację do pobrania dzienników do folderu lokalnego na komputerze.
6.  Wyślij pocztą e-mail pobrany plik zip do farmbeatssupport@microsoft.com

    ![Farmy projektów](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Jak zbierać dzienniki zadań ADF akceleratora

1.  Zaloguj się do https://portal.azure.com
2.  W polu tekstowym **Wyszukiwanie** Wyszukaj grupę zasobów akceleratora FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów akceleratora, która została określona w czasie wdrażania FarmBeats.

3.  Na pulpicie nawigacyjnym grupy zasobów Wyszukaj pozycję Magazyn.... Konto magazynu. Na przykład storagedop4k
4.  Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny konta magazynu.
5.  Na stronie (magazyn...) wybierz pozycję **Otwórz w Eksploratorze** , aby wyświetlić otwarte Eksplorator usługi Azure Storage aplikacji.
6.  W lewym panelu, < magazyn....), **kontenery obiektów BLOB**, wybierz pozycję **dzienniki zadań**.
7.  Na karcie **dzienniki zadania** wybierz pozycję **Pobierz**.
8.  Wybierz lokalizację do pobrania dzienników do folderu lokalnego na komputerze.
9.  Wyślij pocztą e-mail pobrany plik zip do farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Jak zbierać dzienniki usługi aplikacji centrum danych

1.  Zaloguj się do https://portal.azure.com
2.  W polu tekstowym **Wyszukiwanie** Wyszukaj grupę zasobów centrum danych FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów centrum danych, która została określona w czasie wdrażania FarmBeats.

3.  W grupie zasobów Wyszukaj konto magazynu (datahublogs....). Na przykład datahublogsmvxmq
4.  Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5.  Na stronie (datahubblogs....) wybierz pozycję **Otwórz w Eksploratorze** , aby wyświetlić **otwarte Eksplorator usługi Azure Storage** aplikacji.
6.  W lewym panelu (datahubblogs....), **kontenery obiektów BLOB**wybierz pozycję appinsights-logs.
7.  Na karcie appinsights-Logs wybierz pozycję **Pobierz**.
8.  Wybierz ścieżkę, aby pobrać dzienniki do folderu lokalnego na komputerze.
9.  Wyślij wiadomość e-mail do pobranego folderu, aby farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Jak zbierać dzienniki usługi App Service Accelerator

1.  Zaloguj się do https://portal.azure.com
2.  W polu **wyszukiwania**Wyszukaj grupę zasobów akceleratora FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów akceleratora Farmbeats, która jest dostępna w momencie wdrożenia FarmBeats.

3.  W grupie zasobów Wyszukaj konto magazynu (magazyn....). Na przykład storagedop4k
4.  Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5.  Na stronie (magazyn...) wybierz pozycję **Otwórz w Eksploratorze** , aby wyświetlić **otwarte Eksplorator usługi Azure Storage** aplikacji.
6.  W lewym panelu (magazyn....), **kontenery obiektów BLOB**, wybierz pozycję appinsights — dzienniki.
7.  Na karcie appinsights-Logs wybierz pozycję **Pobierz**.
8.  Wybierz lokalizację do pobrania dzienników do folderu lokalnego na komputerze.
9.  Wyślij wiadomość e-mail do pobranego folderu, aby farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Znane problemy

## <a name="batch-related-issues"></a>Problemy związane z usługą Batch

**Błąd**: osiągnięto limit przydziału kont usługi Batch dla określonej subskrypcji.

**Działanie naprawcze**: Zwiększ limit przydziału lub Usuń nieużywane konta partii, a następnie uruchom wdrożenie.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory pokrewne problemy

**Błąd**: nie można zaktualizować wymaganych ustawień do aplikacja usługi Azure AD d41axx40-xx21-4fbd-8xxf-97Xxx9e2xxc0: niewystarczające uprawnienia do ukończenia tej operacji. Upewnij się, że powyższe ustawienia zostały prawidłowo skonfigurowane dla aplikacja usługi Azure AD.

**Znaczenie**: Konfiguracja rejestracji aplikacji usługi Azure AD nie została prawidłowo wykonana.  

**Akcja naprawcza**: poproszenie administratora IT (z dostępem do odczytu dzierżawy), aby użyć naszego [skryptu](https://aka.ms/PPCreateAADappregistration) do generowania rejestracji aplikacji usługi Azure AD. Ten skrypt automatycznie zajmie się również procedurami konfiguracji. definicj  

**Błąd**: nie można utworzyć nowej aplikacji Active Directory "fikcyjnej" w tej dzierżawie: inny obiekt o tej samej wartości identyfikatorów URI identyfikatora właściwości już istnieje

**Znaczenie**: Rejestracja aplikacji usługi Azure AD o tej samej nazwie już istnieje.

**Działanie naprawcze**: Usuń istniejącą rejestrację aplikacji usługi Azure AD lub użyj jej ponownie do zainstalowania. W przypadku ponownego użycia istniejącej usługi Azure AD Przekaż identyfikator aplikacji i klucz tajny klienta do Instalatora i Wdróż go ponownie.

## <a name="inputjson-related-issues"></a>Problemy związane z danymi wejściowymi. JSON

**Błąd podczas** odczytywania danych wejściowych z pliku Input. JSON

**Działanie naprawcze**: ten problem występuje głównie z powodu braku możliwości określenia prawidłowej wejściowej ścieżki JSON lub nazwy Instalatora. Wprowadź odpowiednie poprawki i ponów próbę ponownego wdrożenia.

**Błąd podczas analizowania danych wejściowych JSON**

**Działanie naprawcze**: ten problem występuje głównie z powodu nieprawidłowych wartości w wejściowym pliku JSON. Wprowadź odpowiednie poprawki i ponów próbę wdrożenia.

## <a name="high-cpu-usage"></a>Wysokie użycie procesora

**Błąd**: otrzymasz alert e-mail z informacją o wysokim alercie użycia procesora CPU. 

**Działanie naprawcze**: 
1.  Przejdź do grupy zasobów centrum danych FarmBeats.
2.  Wybierz usługę App Service.  
3.  Przejdź do pozycji Skaluj w górę (plan App Service) i wybierz odpowiednią [warstwę cenową](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>Następne kroki

Jeśli nadal napotykasz jakiekolwiek problemy, skontaktuj się z nami na naszym [forum pomocy technicznej](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
