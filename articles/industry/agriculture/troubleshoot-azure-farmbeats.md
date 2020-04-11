---
title: Rozwiązywanie problemów z projektem Azure FarmBeats
description: W tym artykule opisano sposób rozwiązywania problemów z platformą Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113486"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Ten artykuł zawiera rozwiązania typowych problemów z usługą Azure FarmBeats. Aby uzyskać dodatkową pomoc, skontaktuj się farmbeatssupport@microsoft.comz naszym Forum Pomocy [technicznej](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) lub napisz do nas na adres .

> [!NOTE]
  > Jeśli zainstalowano FarmBeats w kwietniu, a zadania nie pokonują się z pustym komunikatem o błędzie, instalacja może nie zostać przydzielona żadnego przydziału wsadowego, aby nadać priorytet pomocy technicznej dla krytycznych organizacji zajmujących się bezpieczeństwem i higieną pracy. Zobacz [tutaj,](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) aby uzyskać więcej informacji. Aby pomyślnie uruchomić zadania, należy zażądać, aby maszyny wirtualne zostały przydzielone do konta usługi Batch.

## <a name="install-issues"></a>Problemy z instalacją

  > [!NOTE]
  > Jeśli instalacja jest ponownie uruchamiana z powodu błędu, przed ponownym wyzwoleniem instalacji należy usunąć **grupę zasobów** lub usunąć wszystkie zasoby z grupy zasobów.

### <a name="invalid-sentinel-credentials"></a>Nieprawidłowe poświadczenia wartownika

Poświadczenia wartownika podane podczas instalacji są niepoprawne. Uruchom ponownie instalację przy użyciu poprawnych poświadczeń.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Osiągnięto przydział konta regionalnego kont wsadowych dla określonej subskrypcji

Zwiększ przydział lub usuń nieużywane konta wsadowe i uruchom ponownie instalację.

### <a name="invalid-resource-group-location"></a>Nieprawidłowa lokalizacja grupy zasobów

Upewnij się, że **grupa zasobów** znajduje się w tej samej lokalizacji co **region** określony podczas instalacji.

### <a name="other-install-issues"></a>Inne problemy z instalacją

Skontaktuj się z nami, podjąc następujące szczegóły:

- Twój identyfikator subskrypcji
- Nazwa grupy zasobów
- Wykonaj poniższe kroki, aby dołączyć plik dziennika dla błędu wdrożenia:

    1. Przejdź do **grupy zasobów** w witrynie Azure portal.

    2. Wybierz **pozycję Wdrożenia** w sekcji **Ustawienia** po lewej stronie.

    3. Dla każdego wdrożenia, które pokazuje **failed**, wybierz do szczegółów i pobierz szczegóły wdrożenia. Dołącz ten plik do poczty.

## <a name="sensor-telemetry"></a>Telemetria czujnika

### <a name="cant-view-telemetry-data"></a>Nie można wyświetlić danych telemetrycznych

**Symptom:** Urządzenia lub czujniki są wdrażane i zostały połączone FarmBeats z partnerem urządzenia, ale nie można uzyskać ani wyświetlić danych telemetrycznych na FarmBeats.

**Działania naprawcze**

1. Przejdź do grupy zasobów FarmBeats Datahub.
2. Wybierz **Centrum zdarzeń** (DatafeedEventHubNamespace), a następnie sprawdź liczbę wiadomości przychodzących.
3. Wykonaj jedną z następujących czynności:

   - Jeśli *nie ma żadnych przychodzących wiadomości,* skontaktuj się z partnerem urządzenia.  
   - Jeśli są *przychodzące wiadomości,* skontaktuj się z nami z dziennikami datahub i akceleratora oraz przechwyconymi danymi telemetrycznymi.

Aby dowiedzieć się, jak pobierać dzienniki, przejdź do sekcji ["Ręczne zbieranie dzienników".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nie można wyświetlić danych telemetrycznych po spożyciu historycznych/strumieniowych danych z czujników

**Symptom:** Urządzenia lub czujniki są wdrażane i utworzono urządzenia/czujniki na FarmBeats i pochłonął dane telemetryczne do EventHub, ale nie można uzyskać lub wyświetlić dane telemetryczne na FarmBeats.

**Działania naprawcze**

1. Upewnij się, że poprawnie dokonałeś rejestracji partnera — możesz to sprawdzić, przechodząc do witryny datahub, przejdź do /Partner API, Zrób get i sprawdź, czy partner jest zarejestrowany. Jeśli nie, wykonaj następujące [kroki,](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) aby dodać partnera.

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

**Działania naprawcze**

1. W usłudze Datahub Swagger przejdź do interfejsu API partnera.
2. Wybierz **pozycję Wypróbuj** > **go.** > **Execute**

> [!NOTE]
> Identyfikator partnera partnera czujnika, którego jesteś zainteresowany.

3. Wróć do interfejsu API partnera i wybierz **pozycję\<Pobierz/ID>**.
4. Określ identyfikator partnera z kroku 3, a następnie wybierz pozycję **Wykonaj**.

   Odpowiedź interfejsu API powinna mieć parametry połączenia usługi Event Hubs.

### <a name="device-appears-offline"></a>Urządzenie jest wyświetlane w trybie offline

**Symptomy:** Urządzenia są zainstalowane i zostały połączone FarmBeats z partnerem urządzenia. Urządzenia są w trybie online i wysyłają dane telemetryczne, ale są wyświetlane w trybie offline.

**Działania naprawcze** Interwał raportowania nie jest skonfigurowany dla tego urządzenia. Aby ustawić interwał raportowania, skontaktuj się z producentem urządzenia. 

### <a name="error-deleting-a-device"></a>Błąd podczas usuwania urządzenia

Podczas usuwania urządzenia może wystąpić jeden z następujących typowych scenariuszy błędów:  

**Komunikat**: "Urządzenie odwołuje się do czujników: Z urządzeniem jest skojarzony jeden lub więcej czujników. Usuń czujniki, a następnie usuń urządzenie."  

**Znaczenie:** Urządzenie jest skojarzone z wieloma czujnikami, które są wdrażane w farmie.

**Działania naprawcze**  

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
    > Partnerzy nie mają uprawnień do usuwania urządzenia lub czujnika. Tylko administratorzy mają uprawnienia do usuwania.

## <a name="issues-with-jobs"></a>Problemy z zadaniami

### <a name="farmbeats-internal-error"></a>Błąd wewnętrzny FarmBeats

**Komunikat**: "Błąd wewnętrzny FarmBeats, zobacz przewodnik rozwiązywania problemów, aby uzyskać więcej informacji."

**Działania naprawcze** Ten problem może wynikać z tymczasowego błędu w potoku danych. Utwórz zadanie ponownie. Jeśli błąd będzie się powtarzał, skontaktuj się z nami z komunikatem o błędzie/dziennikami.

## <a name="accelerator-troubleshooting"></a>Rozwiązywanie problemów z akceleratorem

### <a name="access-control"></a>Kontrola dostępu

**Problem:** Podczas dodawania przypisania roli pojawia się błąd.

**Komunikat**: "Nie znaleziono pasujących użytkowników".

**Działania naprawcze** Sprawdź identyfikator wiadomości e-mail, dla którego próbujesz dodać przypisanie roli. Identyfikator wiadomości e-mail musi być dokładnym dopasowaniem identyfikatora, który jest zarejestrowany dla tego użytkownika w usłudze Active Directory. Jeśli błąd będzie się powtarzał, skontaktuj się z nami z komunikatem o błędzie/dziennikami.

### <a name="unable-to-log-in-to-accelerator"></a>Nie można zalogować się do akceleratora

**Komunikat**: "Błąd: Nie masz autoryzacji do wywołania usługi. Skontaktuj się z administratorem w celu autoryzacji."

**Działania naprawcze** Poproś administratora o autoryzację dostępu do wdrożenia FarmBeats. Można to zrobić, wykonując post interfejsów API roleAssignment lub za pośrednictwem kontroli dostępu w okienku **Ustawienia** w akceleratorze.  

Jeśli masz już przyznany dostęp i wystąpił z tym błędem, spróbuj ponownie odświeżając stronę. Jeśli błąd będzie się powtarzał, skontaktuj się z nami z komunikatem o błędzie/dziennikami.

![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemy z akceleratorem  

**Problem:** Wystąpił błąd akceleratora o nieokreślonej przyczynie.

**Komunikat**: "Błąd: wystąpił nieznany błąd".

**Działania naprawcze** Ten błąd występuje, jeśli strona jest bezczynna zbyt długo. Odśwież stronę. Jeśli błąd będzie się powtarzał, skontaktuj się z nami z komunikatem o błędzie/dziennikami.

**Problem:** Akcelerator FarmBeats nie pokazuje najnowszej wersji, nawet po uaktualnieniu FarmBeatsDeployment.

**Działania naprawcze** Ten błąd występuje z powodu trwałości procesu roboczego usługi w przeglądarce. Wykonaj następujące czynności:

1. Zamknij wszystkie karty przeglądarki z otwartym akceleratorem i zamknij okno przeglądarki.
2. Uruchom nowe wystąpienie przeglądarki i ponownie załaduj identyfikator URI akceleratora. Ta akcja ładuje nową wersję akceleratora.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Kwestie związane z obrazami

### <a name="wrong-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło

**Komunikat o niepowodzeniu zadania:**"Pełne uwierzytelnianie jest wymagane, aby uzyskać dostęp do tego zasobu."

**Działania naprawcze**: Wykonaj jedną z następujących czynności:

- Zaktualizuj FarmBeats za pomocą poprawnej nazwy użytkownika/hasła, wykonując poniższe kroki i ponów próbę wykonania zadania.

  **Aktualizacja nazwy użytkownika sentinel**

    1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
    2. W polu **Wyszukiwania** wyszukaj grupę zasobów FarmBeats Datahub.
    3. Wybierz miejsce do przechowywania konta magazynu***** > **kontenery** > **prep-files** > **to_vm** > **config.ini**
    4. Wybierz **pozycję Edytuj**
    5. Aktualizowanie nazwy użytkownika w sekcji sentinel_account

  **Aktualizacja hasła sentinel**

    1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
    2. W polu **Wyszukiwania** wyszukaj grupę zasobów FarmBeats Datahub.
    3. Wybierz keyvault-*****
    4. Wybieranie zasad dostępu w obszarze Ustawienia
    5. Wybierz **pozycję Dodaj zasady dostępu**
    6. Użyj **zarządzania kluczem tajnym** do konfigurowania z szablonu i dodaj siebie do podmiotu zabezpieczeń
    7. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz** na stronie **Zasady dostępu**
    8. Wybieranie **wpisów tajnych** w obszarze **Ustawienia**
    9. Wybierz **sentinel-hasło**
    10. Utwórz nową wersję wartości i włącz ją.

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

**Znaczenie:** Jeśli zadanie nie powiedzie się, ponieważ osiągnięto maksymalną liczbę połączeń, to samo konto Sentinel jest używane w wielu zadaniach.

**Działania naprawcze:** Spróbuj wykonać jedną z następujących czynności:

* Poczekaj na zakończenie innych zadań przed ponownym uruchomieniem zadania nie powiodło się.
* Utwórz nowe konto Sentinel, a następnie zaktualizuj nazwę użytkownika i hasło Sentinel w Polach FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Serwer Sentinel: Odrzucone połączenie

**Komunikat o niepowodzeniu zadania:** http://172.30.175.69:8983/solr/dhus"Serwer odmówił połączenia w: ."

**Działania naprawcze:** Ten problem może wystąpić, jeśli na serwerze Sentinel wykonywane są jakiekolwiek czynności konserwacyjne.

1. Jeśli jakiekolwiek zadanie lub potok nie powiedzie się, ponieważ wykonywana jest konserwacja, należy ponownie przesłać zadanie po pewnym czasie.

   Aby uzyskać informacje na temat planowanych lub nieplanowanych działań konserwacyjnych Sentinel, przejdź do witryny [Aktualności Centrum otwartego dostępu programu Copernicus.](https://scihub.copernicus.eu/news/)  

2. Uruchom ponownie zadanie nie powiodło się lub uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="soil-moisture-map-has-white-areas"></a>Gleba Wilgotność mapa ma białe obszary

**Problem**: **Wygenerowano mapę Wilgotność gleby,** ale mapa ma głównie białe obszary.

**Działania naprawcze:** Ten problem może wystąpić, jeśli indeksy satelitarne wygenerowane dla czasu, dla którego mapa została zażądana, mają wartości NDVI, które są mniejsze niż 0,3. Aby uzyskać więcej informacji, odwiedź [przewodnik techniczny firmy Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Uruchom ponownie zadanie dla innego zakresu dat i sprawdź, czy wartości NDVI w indeksach satelitarnych są większe niż 0,3.

## <a name="collect-logs-manually"></a>Ręczne zbieranie dzienników

[Zainstaluj i wdrażaj Eksploratora usługi Azure Storage]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Zbieranie dzienników zadań usługi Azure Data Factory lub dzienników usługi App Service w usłudze Datahub

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W polu **Wyszukiwania** wyszukaj grupę zasobów FarmBeats Datahub.
3. Na **pulpicie nawigacyjnym grupy zasobów** wyszukaj konto magazynu *datahublogs.\* * Na przykład *datahublogsmvxmq*.  
4. W kolumnie **Nazwa** wybierz konto magazynu, aby wyświetlić pulpit nawigacyjny **konta magazynu.**
5. W okienku **datahubblogs\* ** wybierz pozycję **Otwórz w Eksploratorze,** aby wyświetlić aplikację Open Azure Storage **Explorer.**
6. W lewym okienku wybierz **kontenery obiektów blob**, a następnie wybierz **dzienniki zadań** dla dzienników usługi Azure Data Factory lub **dzienników appinsights-logów** dla dzienników usługi App Service.
7. Wybierz **pozycję Pobierz** i pobierz dzienniki do folderu lokalnego na komputerze.

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Zbieranie dzienników zadań usługi Azure Data Factory lub dzienników usługi App Service dla akceleratora

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W polu **Wyszukiwania** wyszukaj grupę zasobów Akceleratora FarmBeats.
3. Na **pulpicie nawigacyjnym grupy zasobów** *wyszukaj\* * konto magazynu. Na przykład *storagedop4k\**.
4. Wybierz konto magazynu w kolumnie **Nazwa,** aby wyświetlić pulpit nawigacyjny **konta magazynu.**
5. W okienku **magazynu\* ** wybierz pozycję **Otwórz w Eksploratorze,** aby otworzyć aplikację Usługi Azure Storage Explorer.
6. W lewym okienku wybierz **kontenery obiektów blob**, a następnie wybierz **dzienniki zadań** dla dzienników usługi Azure Data Factory lub **dzienników appinsights-logów** dla dzienników usługi App Service.
7. Wybierz **pozycję Pobierz** i pobierz dzienniki do folderu lokalnego na komputerze.

## <a name="high-cpu-usage"></a>Wysokie użycie procesora

**Błąd:** Pojawia się alert e-mail, który odnosi się do **alertu o wysokim użyciu procesora .**

**Działania naprawcze:**

1. Przejdź do grupy zasobów FarmBeats Datahub.
2. Wybierz **usługę aplikacji**.  
3. Przejdź do [strony cenowej usługi App Service,](https://azure.microsoft.com/pricing/details/app-service/windows/)a następnie wybierz odpowiednią warstwę cenową.
