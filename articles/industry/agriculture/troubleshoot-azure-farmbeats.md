---
title: Rozwiązywanie problemów z projektem Azure FarmBeats
description: W tym artykule opisano sposób rozwiązywania problemów z usługą Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: fb4b06eca0d6df6848e2e215d8890569701f7596
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705619"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Ten artykuł zawiera rozwiązania typowych problemów z usługą Azure FarmBeats.

Aby uzyskać dodatkową pomoc, skontaktuj się z nami na farmbeatssupport@microsoft.com. Pamiętaj o dołączeniu pliku **Deployer. log** do wiadomości e-mail.

Aby pobrać plik **Deployer. log** , wykonaj następujące czynności:

1. Zaloguj się do **Azure Portal** i wybierz subskrypcję i dzierżawę usługi Azure AD.
2. Uruchom usługę Cloud Shell z górnego obszaru nawigacyjnego witryny Azure Portal.
3. Wybierz pozycję **bash** jako preferowane środowisko Cloud Shell.
4. Wybierz wyróżnioną ikonę, a następnie na liście rozwijanej wybierz pozycję **Pobierz**.

    ![FarmBeats projektu](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. W następnym okienku wprowadź ścieżkę do pliku **Deployer. log** . Na przykład wprowadź **farmbeats-Deployer. log**.

## <a name="sensor-telemetry"></a>Dane telemetryczne czujnika

### <a name="cant-view-telemetry-data"></a>Nie można wyświetlić danych telemetrycznych

**Objaw**: wdrożono urządzenia lub czujniki, a połączono FarmBeats z partnerem urządzenia, ale nie można uzyskać ani wyświetlić danych telemetrycznych w FarmBeats.

**Działanie naprawcze**:

1. Przejdź do grupy zasobów FarmBeats Datahub.   
2. Wybierz **centrum zdarzeń** (DatafeedEventHubNamespace), a następnie wyszukaj liczbę wiadomości przychodzących.
3. Wykonaj jedną z następujących czynności:   
   - Jeśli nie ma *żadnych komunikatów przychodzących*, skontaktuj się z partnerem urządzenia.  
   - Jeśli istnieją *przychodzące komunikaty*, skontaktuj się z farmbeatssupport@microsoft.com. Dołącz dzienniki Datahub i akceleratora oraz przechwycone dane telemetryczne.

Aby zrozumieć sposób pobierania dzienników, przejdź do sekcji ["zbieranie dzienników ręcznie"](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nie można wyświetlić danych telemetrycznych po pozyskaniu danych historycznych/przesyłanych strumieniowo z czujników

**Objaw**: wdrożono urządzenia lub czujniki, a w usłudze EventHub zostały utworzone urządzenia/czujniki dotyczące FarmBeats i pozyskiwanej telemetrii, ale nie można uzyskać ani wyświetlić danych telemetrycznych w FarmBeats.

**Działanie naprawcze**:

1. Upewnij się, że pomyślnie ukończono rejestrację partnera — możesz to sprawdzić, przechodząc do datahub Swagger, przejdź do interfejsu API/partner, a następnie wybierz pozycję Pobierz i sprawdź, czy partner jest zarejestrowany. Jeśli nie, wykonaj [kroki opisane tutaj](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) , aby dodać partnera.
2. Upewnij się, że użyto poprawnego formatu komunikatów telemetrycznych:

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nie masz parametrów połączenia usługi Azure Event Hubs

**Działanie naprawcze**:

1. W programie Datahub Swagger przejdź do interfejsu API partnera.
2. Wybierz pozycję **pobierz** > **Wypróbuj** > **Wykonaj**.
3. Zanotuj identyfikator partnera, który Cię interesuje.
4. Wróć do interfejsu API partnera i wybierz pozycję **Pobierz/\<identyfikator >** .
5. Określ identyfikator partnera z kroku 3, a następnie wybierz pozycję **Wykonaj**.

   Odpowiedź interfejsu API powinna mieć Event Hubs parametry połączenia.

### <a name="device-appears-offline"></a>Urządzenie jest w trybie offline

**Objawy**: urządzenia są zainstalowane i połączono FarmBeats z partnerem urządzenia. Urządzenia są w trybie online i wysyłają dane telemetryczne, ale są wyświetlane w trybie offline.

**Działanie naprawcze**: Interwał raportowania nie jest skonfigurowany dla tego urządzenia. Aby ustawić interwał raportowania, skontaktuj się z producentem urządzenia. 

### <a name="error-deleting-a-device"></a>Błąd usuwania urządzenia

Podczas usuwania urządzenia może wystąpić jeden z następujących typowych scenariuszy błędów:  

**Komunikat**: "urządzenie jest przywoływane w czujników: istnieje co najmniej jedna czujników skojarzona z urządzeniem. Usuń czujniki, a następnie usuń urządzenie ".  

**Znaczenie**: urządzenie jest skojarzone z wieloma czujnikami, które są wdrożone w farmie.   

**Działanie naprawcze**:  

1. Usuń czujniki skojarzone z urządzeniem za pomocą akceleratora.  
2. Jeśli chcesz skojarzyć czujniki z innym urządzeniem, skontaktuj się z partnerem urządzenia, aby to zrobić.  
3. Usuń urządzenie przy użyciu wywołania `DELETE API` i ustaw wartość parametru Force jako *true*.  

**Komunikat**: "urządzenie jest przywoływane w urządzeniach jako ParentDeviceId: istnieje co najmniej jedno urządzenie, które jest skojarzone z urządzeniem jako urządzenia podrzędne. Usuń je, a następnie usuń to urządzenie ".  

**Znaczenie**: na urządzeniu są skojarzone inne urządzenia.  

**Akcja naprawcza**

1. Usuń urządzenia, które są skojarzone z tym określonym urządzeniem.  
2. Usuń określone urządzenie.  

    > [!NOTE]
    > Nie można usunąć urządzenia, jeśli są z nim skojarzone czujniki. Więcej informacji o sposobach usuwania skojarzonych czujników znajduje się w sekcji "Usuwanie czujnika" w temacie [pobieranie danych z czujników z partnerów czujników](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problemy związane z zadaniami

### <a name="farmbeats-internal-error"></a>Błąd wewnętrzny FarmBeats

**Komunikat**: "FarmBeats błąd wewnętrzny, zobacz Przewodnik rozwiązywania problemów, aby uzyskać więcej szczegółów".

**Działanie naprawcze**: ten problem może wynikać z tymczasowego błędu w potoku danych. Utwórz ponownie zadanie. Jeśli błąd będzie się powtarzać, Dodaj komunikat o błędzie w ogłoszeniu na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Rozwiązywanie problemów z akceleratorem

### <a name="access-control"></a>Kontrola dostępu

**Problem**: Wystąpił błąd podczas dodawania przypisania roli.

**Komunikat**: "nie znaleziono pasujących użytkowników".

**Akcja naprawcza**: Sprawdź identyfikator e-mail, dla którego próbujesz dodać przypisanie roli. Identyfikator poczty e-mail musi być dokładnym dopasowaniem identyfikatora, który jest zarejestrowany dla tego użytkownika w Active Directory. Jeśli błąd będzie się powtarzać, Dodaj komunikat o błędzie w ogłoszeniu na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Nie można zalogować się do akceleratora

**Komunikat**: "błąd: nie masz uprawnień do wywołania usługi. Skontaktuj się z administratorem, aby uzyskać autoryzację ".

**Akcja naprawcza**: poproszenie administratora o autoryzację w celu uzyskania dostępu do wdrożenia FarmBeats. Można to zrobić, wykonując wpis interfejsów API RoleAssignment lub za pomocą Access Control w okienku **Ustawienia** w akceleratorze.  

Jeśli masz już udzielony dostęp i występują ten błąd, spróbuj ponownie, odświeżając stronę. Jeśli błąd będzie się powtarzać, Dodaj komunikat o błędzie w ogłoszeniu na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com.

![FarmBeats projektu](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemy akceleratora  

**Problem**: Wystąpił błąd akceleratora nieokreślonej przyczyny.

**Komunikat**: "błąd: Wystąpił nieznany błąd".

**Działanie naprawcze**: ten błąd występuje, gdy strona bezczynna pozostanie zbyt długa. Odśwież stronę.  

Jeśli błąd będzie się powtarzać, Dodaj komunikat o błędzie w ogłoszeniu na forum FarmBeats lub skontaktuj się z FarmBeatsSupport@microsoft.com.

**Problem**: akcelerator FarmBeats nie pokazuje najnowszej wersji, nawet po uaktualnieniu FarmBeatsDeployment.

**Działanie naprawcze**: ten błąd występuje z powodu trwałości procesu roboczego usługi w przeglądarce. Wykonaj następujące czynności:
1. Zamknij wszystkie karty przeglądarki z otwartym akceleratorem, a następnie zamknij okno przeglądarki.
2. Uruchom nowe wystąpienie przeglądarki i ponownie załaduj akcelerator URI. Ta akcja spowoduje załadowanie nowej wersji akceleratora.

## <a name="sentinel-imagery-related-issues"></a>Wskaźnik: problemy związane z obrazami

### <a name="wrong-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło

**Komunikat o niepowodzeniu zadania**: "pełne uwierzytelnianie jest wymagane w celu uzyskania dostępu do tego zasobu".

**Działanie naprawcze**:

Wykonaj jedną z następujących czynności:
- Uruchom ponownie Instalatora, aby uaktualnić Datahub przy użyciu prawidłowej nazwy użytkownika i hasła.
- Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centrum centrów danych: niewłaściwy adres URL lub niedostępna witryna 

**Komunikat o niepowodzeniu zadania**: "Niestety, wystąpił problem. Strona, do której próbujesz uzyskać dostęp, jest (tymczasowo) niedostępna. " 

**Działanie naprawcze**:
1. Otwórz [wskaźnik](https://scihub.copernicus.eu/dhus/) kontrolny w przeglądarce, aby sprawdzić, czy witryna sieci Web jest dostępna. 
2. Jeśli witryna sieci Web jest niedostępna, sprawdź, czy Zapora, Sieć firmowa lub inne oprogramowanie blokujące uniemożliwia dostęp do witryny sieci Web, a następnie wykonaj niezbędne kroki, aby zezwolić na adres URL wskaźnikiem. 
3. Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.  

### <a name="sentinel-server-down-for-maintenance"></a>Serwer wskaźnikowy: do konserwacji

**Komunikat o niepowodzeniu zadania**: "Copernicus otwarte centrum dostępu zostanie wkrótce zakończone! Przepraszamy za niedogodności, ale w tej chwili wykonujemy pewne czynności konserwacyjne. Wkrótce będziemy z powrotem w trybie online. 

**Działanie naprawcze**:

Ten problem może wystąpić, jeśli na serwerze wskaźnikowym są wykonywane jakiekolwiek działania konserwacyjne.

1. Jeśli jakieś zadanie lub potok nie powiedzie się, ponieważ trwa wykonywanie konserwacji, prześlij ponownie zadanie po pewnym czasie. 

   Aby uzyskać informacje o wszelkich planowanych lub nieplanowanych działaniach związanych z konserwacją wskaźnikiem, przejdź do witryny [Copernicus Open Access Hub](https://scihub.copernicus.eu/news/) .  
2. Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Wskaźnik kontrolny: osiągnięto maksymalną liczbę połączeń

**Komunikat o niepowodzeniu zadania**: "Maksymalna liczba dwóch współbieżnych przepływów osiągniętych przez użytkownika"\<nazwy użytkownika > "."

**Znaczenie**: Jeśli zadanie nie powiedzie się, ponieważ osiągnięto maksymalną liczbę połączeń, to samo konto wskaźnikowe jest używane w innym wdrożeniu oprogramowania.

**Działanie naprawcze**: Spróbuj wykonać jedną z następujących czynności:
* Utwórz nowe konto wskaźnikiem, a następnie ponownie uruchom Instalatora, aby uaktualnić program Datahub przy użyciu nowej nazwy użytkownika i hasła.  
* Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-server-refused-connection"></a>Serwer wskaźnikowy: odmowa połączenia 

**Komunikat o niepowodzeniu zadania**: "serwer odmówił połączenia o: http://172.30.175.69:8983/solr/dhus". 

**Działania naprawcze**: ten problem może wystąpić, jeśli na serwerze wskaźnikowym są wykonywane jakiekolwiek działania konserwacyjne. 
1. Jeśli jakieś zadanie lub potok nie powiedzie się, ponieważ trwa wykonywanie konserwacji, prześlij ponownie zadanie po pewnym czasie. 

   Aby uzyskać informacje o wszelkich planowanych lub nieplanowanych działaniach związanych z konserwacją wskaźnikiem, przejdź do witryny [Copernicus Open Access Hub](https://scihub.copernicus.eu/news/) .  
2. Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

## <a name="collect-logs-manually"></a>Zbierz dzienniki ręcznie

[Zainstaluj i wdróż Eksplorator usługi Azure Storage]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Zbieranie dzienników zadań Azure Data Factory w Datahub
1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W polu **wyszukiwania** Wyszukaj grupę zasobów FarmBeats Datahub.

    > [!NOTE]
    > Wybierz grupę zasobów Datahub określoną podczas instalacji FarmBeats.

3. Na pulpicie nawigacyjnym **grupy zasobów** Wyszukaj konto magazynu *datahublogs\** . Na przykład wyszukaj ciąg **datahublogsmvxmq**.  
4. W kolumnie **Nazwa** wybierz konto magazynu, aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5. W okienku **\*datahubblogs** wybierz pozycję **Otwórz w Eksploratorze** , aby wyświetlić **otwarte Eksplorator usługi Azure Storage** aplikację.
6. W lewym okienku wybierz pozycję **kontenery obiektów BLOB**, a następnie wybierz pozycję **dzienniki zadań**.
7. W okienku **dzienniki zadania** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail z pobranym plikiem ZIP do farmbeatssupport@microsoft.com.

    ![FarmBeats projektu](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Zbieranie dzienników zadań Azure Data Factory w akceleratorze

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W polu **wyszukiwania** Wyszukaj grupę zasobów akceleratora FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów akceleratora, która została określona podczas instalacji FarmBeats.

3. Na pulpicie nawigacyjnym **grupy zasobów** Wyszukaj konto magazynu *\** Storage. Na przykład wyszukaj **storagedop4k\*** .
4. Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5. W okienku **\*magazynu** wybierz pozycję **Otwórz w Eksploratorze** , aby otworzyć aplikację Eksplorator usługi Azure Storage.
6. W lewym okienku wybierz pozycję **kontenery obiektów BLOB**, a następnie wybierz pozycję **dzienniki zadań**.
7. W okienku **dzienniki zadania** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail z pobranym plikiem ZIP do farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Zbieranie dzienników usługi App Service Datahub

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W polu **wyszukiwania** Wyszukaj grupę zasobów FarmBeats Datahub.

    > [!NOTE]
    > Wybierz grupę zasobów Datahub określoną podczas instalacji FarmBeats.

3. W grupie zasobów Wyszukaj konto magazynu *datahublogs\** . Na przykład wyszukaj **fordatahublogsmvxmq\*** .
4. Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5. W okienku **\*datahubblogs** wybierz pozycję **Otwórz w Eksploratorze** , aby otworzyć aplikację Eksplorator usługi Azure Storage.
6. W lewym okienku wybierz pozycję **kontenery obiektów BLOB**, a następnie wybierz pozycję **appinsights-Logs**.
7. W okienku **appinsights-Logs** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail z pobranym plikiem ZIP do farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Zbieranie dzienników usługi App Service Accelerator

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. W polu **wyszukiwania** Wyszukaj grupę zasobów akceleratora FarmBeats.

    > [!NOTE]
    > Wybierz grupę zasobów akceleratora FarmBeats, która została dostarczona podczas instalacji FarmBeats.

3. W grupie zasobów Wyszukaj konto magazynu *\** Storage. Na przykład wyszukaj **storagedop4k\*** .
4. Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5. W okienku **\*magazynu** wybierz pozycję **Otwórz w Eksploratorze** , aby otworzyć aplikację Eksplorator usługi Azure Storage.
6. W lewym okienku wybierz pozycję **kontenery obiektów BLOB**, a następnie wybierz pozycję **appinsights-Logs**.
7. W okienku **appinsights-Logs** wybierz pozycję **Pobierz**.
8. Pobierz dzienniki do folderu lokalnego na komputerze.
9. Wyślij wiadomość e-mail do pobranego folderu, aby farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Znane problemy

## <a name="batch-related-issues"></a>Problemy związane z usługą Batch

**Komunikat o błędzie**: "Osiągnięto limit przydziału konta usługi Batch dla określonej subskrypcji".

**Działanie naprawcze**: Zwiększ limit przydziału lub Usuń nieużywane konta partii i ponownie uruchom wdrożenie.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemy związane z Azure Active Directory (Azure AD)

**Komunikat o błędzie**: "nie można zaktualizować wymaganych ustawień do aplikacja usługi Azure AD d41axx40-xx21-4fbd-8xxf-97Xxx9e2xxc0: niewystarczające uprawnienia do ukończenia tej operacji. Upewnij się, że powyższe ustawienia zostały prawidłowo skonfigurowane dla aplikacja usługi Azure AD ".

**Znaczenie**: Konfiguracja rejestracji aplikacji usługi Azure AD nie została prawidłowo zakończona.  

**Akcja naprawcza**: poproszenie administratora IT (osoby z dostępem do odczytu dzierżawy), aby użyć naszego [skryptu](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) do utworzenia rejestracji aplikacji usługi Azure AD. Ten skrypt automatycznie wykonuje również czynności konfiguracyjne.

**Komunikat o błędzie**: "nie można utworzyć nowej aplikacji Active Directory"\<nazwę aplikacji\>"w tej dzierżawie: inny obiekt o tej samej wartości identyfikatorów URI identyfikatora właściwości już istnieje".

**Znaczenie**: Rejestracja aplikacji usługi Azure AD o tej samej nazwie już istnieje.

**Działanie naprawcze**: Usuń istniejącą rejestrację aplikacji usługi Azure AD lub użyj jej ponownie do zainstalowania. Jeśli używasz istniejącej rejestracji aplikacji usługi Azure AD, Przekaż identyfikator aplikacji i klucz tajny klienta do Instalatora i Wdróż go ponownie.

## <a name="issues-with-the-inputjson-file"></a>Problemy z plikiem Input. JSON

**Błąd**: Wystąpił błąd podczas odczytywania danych wejściowych z pliku *Input. JSON* .

**Działanie naprawcze**: ten problem zwykle występuje z powodu błędu podczas określania prawidłowej ścieżki lub nazwy pliku *Input. JSON* do Instalatora. Wprowadź odpowiednie poprawki i spróbuj ponownie wykonać wdrożenie.

**Błąd**: Wystąpił błąd podczas analizowania wartości w pliku *Input. JSON* .

**Działanie naprawcze**: ten problem występuje głównie z powodu nieprawidłowych wartości w pliku *Input. JSON* . Wprowadź odpowiednie poprawki i spróbuj ponownie wykonać wdrożenie.

## <a name="high-cpu-usage"></a>Wysokie użycie procesora

**Błąd**: otrzymasz alert e-mail, który odwołuje się do **alertu o wysokim zużyciu procesora CPU**. 

**Działanie naprawcze**: 
1. Przejdź do grupy zasobów FarmBeats Datahub.
2. Wybierz **usługę App Service**.  
3. Przejdź do strony skalowanie w górę [App Service cennika](https://azure.microsoft.com/pricing/details/app-service/windows/), a następnie wybierz odpowiednią warstwę cenową.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy z FarmBeats, skontaktuj się z naszym [forum pomocy technicznej](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
