---
title: Rozwiązywanie problemów z projektem Azure FarmBeats
description: W tym artykule opisano sposób rozwiązywania problemów z platformą Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422721"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Ten artykuł zawiera rozwiązania typowych problemów z usługą Azure FarmBeats.

Aby uzyskać dodatkową pomoc, skontaktuj się z nami pod adresem farmbeatssupport@microsoft.com. Upewnij się, że plik **deployer.log** w wiadomości e-mail.

Aby pobrać plik **deployer.log,** wykonaj następujące czynności:

1. Zaloguj się do **witryny Azure Portal** i wybierz subskrypcję i dzierżawę usługi Azure AD.
2. Uruchom usługę Cloud Shell z górnego obszaru nawigacyjnego witryny Azure Portal.
3. Wybierz **Bash** jako preferowane środowisko Cloud Shell.
4. Wybierz wyróżnioną ikonę, a następnie na liście rozwijanej wybierz pozycję **Pobierz**.

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. W następnym okienku wprowadź ścieżkę do pliku **deployer.log.** Na przykład wprowadź **farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Telemetria czujnika

### <a name="cant-view-telemetry-data"></a>Nie można wyświetlić danych telemetrycznych

**Symptom:** Urządzenia lub czujniki są wdrażane i zostały połączone FarmBeats z partnerem urządzenia, ale nie można uzyskać ani wyświetlić danych telemetrycznych na FarmBeats.

**Działania naprawcze:**

1. Przejdź do grupy zasobów FarmBeats Datahub.   

2. Wybierz **Centrum zdarzeń** (DatafeedEventHubNamespace), a następnie sprawdź liczbę wiadomości przychodzących.

3. Wykonaj jedną z następujących czynności:   
   - Jeśli *nie ma żadnych przychodzących wiadomości,* skontaktuj się z partnerem urządzenia.  
   - Jeśli są *wiadomości przychodzące,* kontakt farmbeatssupport@microsoft.com. Dołącz dzienniki datahub i akceleratora oraz przechwycone dane telemetryczne.

Aby dowiedzieć się, jak pobierać dzienniki, przejdź do sekcji ["Ręczne zbieranie dzienników".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nie można wyświetlić danych telemetrycznych po spożyciu historycznych/strumieniowych danych z czujników

**Symptom:** Urządzenia lub czujniki są wdrażane i utworzono urządzenia/czujniki na FarmBeats i pochłonął dane telemetryczne do EventHub, ale nie można uzyskać lub wyświetlić dane telemetryczne na FarmBeats.

**Działania naprawcze:**

1. Upewnij się, że poprawnie dokonałeś rejestracji partnera — możesz to sprawdzić, przechodząc do witryny datahub, przejdź do /Partner API, Zrób get i sprawdź, czy partner jest zarejestrowany. Jeśli nie, wykonaj [kroki tutaj,](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) aby dodać partnera.

2. Upewnij się, że użyto poprawnego formatu wiadomości telemetrycznych:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nie masz ciągu połączenia usługi Azure Event Hubs

**Działania naprawcze:**

1. W usłudze Datahub Swagger przejdź do interfejsu API partnera.
2. Wybierz **pozycję Wypróbuj** > **go.** > **Execute**

> [!NOTE]
> Identyfikator partnera partnera czujnika, którego jesteś zainteresowany.

3. Wróć do interfejsu API partnera i wybierz **pozycję\<Pobierz/ID>**.
4. Określ identyfikator partnera z kroku 3, a następnie wybierz pozycję **Wykonaj**.

   Odpowiedź interfejsu API powinna mieć parametry połączenia usługi Event Hubs.

### <a name="device-appears-offline"></a>Urządzenie jest wyświetlane w trybie offline

**Symptomy:** Urządzenia są zainstalowane i zostały połączone FarmBeats z partnerem urządzenia. Urządzenia są w trybie online i wysyłają dane telemetryczne, ale są wyświetlane w trybie offline.

**Działania naprawcze:** interwał raportowania nie jest skonfigurowany dla tego urządzenia. Aby ustawić interwał raportowania, skontaktuj się z producentem urządzenia. 

### <a name="error-deleting-a-device"></a>Błąd podczas usuwania urządzenia

Podczas usuwania urządzenia może wystąpić jeden z następujących typowych scenariuszy błędów:  

**Komunikat**: "Urządzenie odwołuje się do czujników: Z urządzeniem jest skojarzony jeden lub więcej czujników. Usuń czujniki, a następnie usuń urządzenie."  

**Znaczenie:** Urządzenie jest skojarzone z wieloma czujnikami, które są wdrażane w farmie.   

**Działania naprawcze:**  

1. Usuń czujniki skojarzone z urządzeniem za pomocą akceleratora.  
2. Jeśli chcesz skojarzyć czujniki z innym urządzeniem, poproś partnera urządzenia, aby zrobił to samo.  
3. Usuń urządzenie za `DELETE API` pomocą połączenia i ustaw parametr force jako *true*.  

**Komunikat**: "Urządzenie odwołuje się do urządzeń jako ParentDeviceId: Istnieje co najmniej jedno urządzenie, które są skojarzone z tym urządzeniem jako urządzenia podrzędne. Usuń je, a następnie usuń to urządzenie."  

**Znaczenie:** Urządzenie ma z nim skojarzone inne urządzenia.  

**Działania naprawcze**

1. Usuń urządzenia skojarzone z tym konkretnym urządzeniem.  
2. Usuń określone urządzenie.  

    > [!NOTE]
    > Nie można usunąć urządzenia, jeśli są z nim skojarzone czujniki. Aby uzyskać więcej informacji na temat usuwania skojarzonych czujników, zobacz sekcję **Usuwanie czujników** w [sekcji Pobierz dane z czujników od partnerów czujników](get-sensor-data-from-sensor-partner.md).

    > Partnerzy nie mają dostępu do usuwania urządzenia lub czujnika. Tylko administratorzy mają dostęp do tego samego.


## <a name="issues-with-jobs"></a>Problemy z zadaniami

### <a name="farmbeats-internal-error"></a>Błąd wewnętrzny FarmBeats

**Komunikat**: "Błąd wewnętrzny FarmBeats, zobacz przewodnik rozwiązywania problemów, aby uzyskać więcej informacji".

**Działania naprawcze:** Ten problem może wynikać z tymczasowego błędu w potoku danych. Utwórz zadanie ponownie. Jeśli błąd będzie się powtarzał, dodaj komunikat o błędzie w FarmBeatsSupport@microsoft.compoście na forum FarmBeats lub skontaktuj się z nim .

## <a name="accelerator-troubleshooting"></a>Rozwiązywanie problemów z akceleratorem

### <a name="access-control"></a>Kontrola dostępu

**Problem:** Podczas dodawania przypisania roli pojawia się błąd.

**Komunikat**: "Nie znaleziono pasujących użytkowników".

**Działanie naprawcze:** sprawdź identyfikator wiadomości e-mail, dla którego próbujesz dodać przypisanie roli. Identyfikator wiadomości e-mail musi być dokładnym dopasowaniem identyfikatora, który jest zarejestrowany dla tego użytkownika w usłudze Active Directory. Jeśli błąd będzie się powtarzał, dodaj komunikat o błędzie w FarmBeatsSupport@microsoft.compoście na forum FarmBeats lub skontaktuj się z nim .

### <a name="unable-to-log-in-to-accelerator"></a>Nie można zalogować się do akceleratora

**Komunikat**: "Błąd: Nie masz autoryzacji do wywołania usługi. Skontaktuj się z administratorem w celu autoryzacji."

**Działania naprawcze:** Poproś administratora o autoryzację dostępu do wdrożenia FarmBeats. Można to zrobić, wykonując post interfejsów API roleAssignment lub za pośrednictwem kontroli dostępu w okienku **Ustawienia** w akceleratorze.  

Jeśli masz już przyznany dostęp i masz do czynienia z tym błędem, spróbuj ponownie, odświeżając stronę. Jeśli błąd będzie się powtarzał, dodaj komunikat o błędzie w FarmBeatsSupport@microsoft.compoście na forum FarmBeats lub skontaktuj się z nim .

![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemy z akceleratorem  

**Problem:** Wystąpił błąd akceleratora o nieokreślonej przyczynie.

**Komunikat**: "Błąd: wystąpił nieznany błąd".

**Działanie naprawcze:** Ten błąd występuje, jeśli strona jest bezczynna zbyt długo. Odśwież stronę.  

Jeśli błąd będzie się powtarzał, dodaj komunikat o błędzie w FarmBeatsSupport@microsoft.compoście na forum FarmBeats lub skontaktuj się z nim .

**Problem:** Akcelerator FarmBeats nie pokazuje najnowszej wersji, nawet po uaktualnieniu FarmBeatsDeployment.

**Akcja naprawcza:** Ten błąd występuje z powodu trwałości pracownika serwisu w przeglądarce. Wykonaj następujące czynności:

1. Zamknij wszystkie karty przeglądarki z otwartym akceleratorem i zamknij okno przeglądarki.
2. Uruchom nowe wystąpienie przeglądarki i ponownie załaduj identyfikator URI akceleratora. Ta akcja ładuje nową wersję akceleratora.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Kwestie związane z obrazami

### <a name="wrong-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło

**Komunikat o niepowodzeniu zadania:**"Pełne uwierzytelnianie jest wymagane, aby uzyskać dostęp do tego zasobu."

**Działania naprawcze:**

Wykonaj jedną z następujących czynności:

- Uruchom ponownie instalatora w celu uaktualnienia usługi Datahub przy użyciu poprawnej nazwy użytkownika i hasła.
- Uruchom ponownie zadanie nie powiodło się lub uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centrum wartownika: nieprawidłowy adres URL lub witryna nie jest dostępna 

**Komunikat o niepowodzeniu zadania**: "Ups, coś poszło nie tak. Strona, do której próbujesz uzyskać dostęp, jest (tymczasowo) niedostępna." 

**Działania naprawcze:**
1. Otwórz [sentinel](https://scihub.copernicus.eu/dhus/) w przeglądarce, aby zobaczyć, czy strona jest dostępna. 
2. Jeśli witryna sieci Web nie jest dostępna, sprawdź, czy zapora sieciowa, sieć firmowa lub inne oprogramowanie blokujące uniemożliwia dostęp do witryny sieci Web, a następnie podejmij niezbędne kroki, aby zezwolić na adres URL sentinel. 
3. Uruchom ponownie zadanie nie powiodło się lub uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.  

### <a name="sentinel-server-down-for-maintenance"></a>Serwer Sentinel: W dół do konserwacji

**Komunikat o niepowodzeniu zadania:**"Centrum otwartego dostępu Copernicus wkrótce powróci! Przepraszamy za niedogodności, wykonujemy pewne konserwacji w tej chwili. Wkrótce wrócimy do trybu online!" 

**Działania naprawcze:**

Ten problem może wystąpić, jeśli wszelkie działania konserwacyjne są wykonywane na serwerze Sentinel.

1. Jeśli jakiekolwiek zadanie lub potok nie powiedzie się, ponieważ wykonywana jest konserwacja, należy ponownie przesłać zadanie po pewnym czasie. 

   Aby uzyskać informacje na temat planowanych lub nieplanowanych działań konserwacyjnych Sentinel, przejdź do witryny [Aktualności Centrum otwartego dostępu programu Copernicus.](https://scihub.copernicus.eu/news/)  

2. Uruchom ponownie zadanie nie powiodło się lub uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Wartownik: Maksymalna liczba połączeń osiągniętych

**Komunikat o niepowodzeniu zadania**: "Maksymalna liczba dwóch\<równoczesnych przepływów osiągniętych przez użytkownika ' nazwa użytkownika>'."

**Znaczenie:** Jeśli zadanie nie powiedzie się, ponieważ osiągnięto maksymalną liczbę połączeń, to samo konto Sentinel jest używane w innym wdrożeniu oprogramowania.

**Działania naprawcze:** Spróbuj wykonać jedną z następujących czynności:

* Utwórz nowe konto Sentinel, a następnie uruchom ponownie instalatora, aby uaktualnić program Datahub przy użyciu nowej nazwy użytkownika i hasła sentinel.  
* Uruchom ponownie zadanie niepowodzenia lub uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-server-refused-connection"></a>Serwer Sentinel: Odrzucone połączenie 

**Komunikat o niepowodzeniu zadania:** http://172.30.175.69:8983/solr/dhus"Serwer odmówił połączenia w: ." 

**Działania naprawcze:** Ten problem może wystąpić, jeśli na serwerze Sentinel wykonywane są jakiekolwiek czynności konserwacyjne. 
1. Jeśli jakiekolwiek zadanie lub potok nie powiedzie się, ponieważ wykonywana jest konserwacja, należy ponownie przesłać zadanie po pewnym czasie. 

   Aby uzyskać informacje na temat planowanych lub nieplanowanych działań konserwacyjnych Sentinel, przejdź do witryny [Aktualności Centrum otwartego dostępu programu Copernicus.](https://scihub.copernicus.eu/news/)  

2. Uruchom ponownie zadanie nie powiodło się lub uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="soil-moisture-map-has-white-areas"></a>Gleba Wilgotność mapa ma białe obszary 

**Problem**: Wygenerowano mapę Wilgotność gleby, ale mapa ma głównie białe obszary.

**Działania naprawcze:** Ten problem może wystąpić, jeśli indeksy satelitarne wygenerowane dla czasu, dla którego mapa została zażądana, mają wartości NDVI, które są mniejsze niż 0,3. Aby uzyskać więcej informacji, odwiedź [przewodnik techniczny firmy Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).
1. Uruchom ponownie zadanie dla innego zakresu dat i sprawdź, czy wartości NDVI w indeksach satelitarnych są większe niż 0,3

## <a name="collect-logs-manually"></a>Ręczne zbieranie dzienników

[Zainstaluj i wdrażaj Eksploratora usługi Azure Storage]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Zbieranie dzienników zadań usługi Azure Data Factory w usłudze Datahub

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W polu **Wyszukiwania** wyszukaj grupę zasobów FarmBeats Datahub.

    > [!NOTE]
    > Wybierz grupę zasobów Datahub, która została określona podczas instalacji FarmBeats.

3. Na **pulpicie nawigacyjnym grupy zasobów** wyszukaj konto magazynu *datahublogs.\* * Na przykład wyszukaj **plik datahublogsmvxmq**.  
4. W kolumnie **Nazwa** wybierz konto magazynu, aby wyświetlić pulpit nawigacyjny **konta magazynu.**
5. W okienku **datahubblogs\* ** wybierz pozycję **Otwórz w Eksploratorze,** aby wyświetlić aplikację Open Azure Storage **Explorer.**
6. W lewym okienku wybierz pozycję **Kontenery obiektów blob**, a następnie wybierz pozycję **Dzienniki zadań**.
7. W okienku **dzienniki zadań** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail farmbeatssupport@microsoft.comdo pobranego pliku zip na adres .

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Zbieranie dzienników zadań usługi Azure Data Factory w akceleratorze

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W polu **Wyszukiwania** wyszukaj grupę zasobów Akceleratora FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów akceleratora określoną podczas instalacji FarmBeats.

3. Na **pulpicie nawigacyjnym grupy zasobów** *wyszukaj\* * konto magazynu. Na przykład wyszukaj **storagedop4k\***.
4. Wybierz konto magazynu w kolumnie **Nazwa,** aby wyświetlić pulpit nawigacyjny **konta magazynu.**
5. W okienku **magazynu\* ** wybierz pozycję **Otwórz w Eksploratorze,** aby otworzyć aplikację Usługi Azure Storage Explorer.
6. W lewym okienku wybierz pozycję **Kontenery obiektów blob**, a następnie wybierz pozycję **Dzienniki zadań**.
7. W okienku **dzienniki zadań** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail farmbeatssupport@microsoft.comdo pobranego pliku zip na adres .


### <a name="collect-datahub-app-service-logs"></a>Zbieranie dzienników usługi aplikacji Datahub

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W polu **Wyszukiwania** wyszukaj grupę zasobów FarmBeats Datahub.

    > [!NOTE]
    > Wybierz grupę zasobów Datahub, która została określona podczas instalacji FarmBeats.

3. W grupie zasobów wyszukaj konto magazynu *datahublogs.\* * Na przykład wyszukaj **fordatahublogsmvxmq\***.
4. Wybierz konto magazynu w kolumnie **Nazwa,** aby wyświetlić pulpit nawigacyjny **konta magazynu.**
5. W okienku **datahubblogs\* ** wybierz pozycję **Otwórz w Eksploratorze,** aby otworzyć aplikację Usługi Azure Storage Explorer.
6. W lewym okienku wybierz pozycję **Kontenery obiektów Blob**, a następnie wybierz **pozycję appinsights-logs**.
7. W okienku **appinsights-logs** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail farmbeatssupport@microsoft.comdo pobranego pliku zip na adres .

### <a name="collect-accelerator-app-service-logs"></a>Zbieranie dzienników usługi aplikacji Akcelerator

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W polu **Wyszukiwania** wyszukaj grupę zasobów Akceleratora FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów FarmBeats Accelerator, która została udostępniony podczas instalacji FarmBeats.

3. W grupie zasobów *wyszukaj\* * konto magazynu. Na przykład wyszukaj **storagedop4k\***.
4. Wybierz konto magazynu w kolumnie **Nazwa,** aby wyświetlić pulpit nawigacyjny **konta magazynu.**
5. W okienku **magazynu\* ** wybierz pozycję **Otwórz w Eksploratorze,** aby otworzyć aplikację Usługi Azure Storage Explorer.
6. W lewym okienku wybierz pozycję **Kontenery obiektów Blob**, a następnie wybierz **pozycję appinsights-logs**.
7. W okienku **appinsights-logs** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail do pobranego folderu na adres farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Znane problemy

## <a name="batch-related-issues"></a>Problemy związane z partią

**Komunikat o błędzie**: "Osiągnięto przydział konta regionalnego kont wsadowych dla określonej subskrypcji."

**Działania naprawcze:** Zwiększ przydział lub usuń nieużywane konta wsadowe i uruchom ponownie wdrożenie.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemy związane z usługą Azure Active Directory (Azure AD)

**Komunikat o błędzie:**"Nie można zaktualizować wymaganych ustawień do usługi Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Niewystarczające uprawnienia do ukończenia operacji. Upewnij się, że powyższe ustawienia są poprawnie skonfigurowane dla aplikacji usługi Azure AD."

**Znaczenie:** Konfiguracja rejestracji aplikacji usługi Azure AD nie została poprawnie ukończona.  

**Działania naprawcze:** Poproś administratora IT (osobę z dostępem do odczytu dzierżawy), aby użył naszego [skryptu](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) do tworzenia rejestracji aplikacji usługi Azure AD. Ten skrypt automatycznie zajmuje się również krokami konfiguracji.

**Komunikat o błędzie:**"Nie można utworzyć\<nowej\>nazwy aplikacji usługi Active Directory ' w tej dzierżawie: Inny obiekt o tej samej wartości identyfikatorów właściwości identyfikator URI już istnieje."

**Znaczenie:** Rejestracja aplikacji usługi Azure AD o tej samej nazwie już istnieje.

**Działanie naprawcze:** Usuń istniejącą rejestrację aplikacji usługi Azure AD lub ponownie ją użyć do instalacji. Jeśli ponownie korzystasz z istniejącej rejestracji aplikacji usługi Azure AD, przekaż identyfikator aplikacji i klucz tajny klienta do instalatora i ponownie wdrożyć.

## <a name="issues-with-the-inputjson-file"></a>Problemy z plikiem input.json

**Błąd:** Wystąpił błąd odczytu danych wejściowych z pliku *input.json.*

**Działania naprawcze:** Ten problem zwykle występuje z powodu błędu w określaniu poprawnej ścieżki pliku *input.json* lub nazwy instalatora. Wykonuj odpowiednie poprawki i ponów próbę wdrożenia.

**Błąd:** W pliku *input.json* występuje błąd podczas analizowania wartości.

**Działania naprawcze:** Ten problem wynika głównie z niepoprawnych wartości w pliku *input.json.* Wykonuj odpowiednie poprawki i ponów próbę wdrożenia.

## <a name="high-cpu-usage"></a>Wysokie użycie procesora

**Błąd:** Pojawia się alert e-mail, który odnosi się do **alertu o wysokim użyciu procesora .** 

**Działania naprawcze:** 
1. Przejdź do grupy zasobów FarmBeats Datahub.
2. Wybierz **usługę aplikacji**.  
3. Przejdź do [strony cenowej usługi App Service,](https://azure.microsoft.com/pricing/details/app-service/windows/)a następnie wybierz odpowiednią warstwę cenową.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy z FarmBeats, skontaktuj się z naszym [Forum Pomocy Technicznej.](https://aka.ms/farmbeatssupport)
