---
title: Ładowanie danych z rozwiązania SAP Business Warehouse za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Kopiowanie danych z usługi SAP Business Warehouse (BW) za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 607c3ff128c82c7baa268cf8f068232428ec5331
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733324"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Ładowanie danych z usługi SAP Business Warehouse (BW) przy użyciu usługi Azure Data Factory

W tym artykule opisano krok po kroku dotyczące sposobu używania usługi Data Factory _ładowanie danych z SAP Business Warehouse (BW) za pośrednictwem Centrum Otwórz do usługi Azure Data Lake Storage Gen2_. Można wykonać podobne kroki w celu kopiowania danych do innych [obsługiwanych magazynów danych ujścia](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Zapoznaj się [artykuł dotyczący łącznika SAP BW Open Centrum](connector-sap-business-warehouse-open-hub.md) na kopiowanie danych z systemu SAP BW ogólnie rzecz biorąc, w tym wprowadzenie przepływu wyodrębniania integracji i różnicowe SAP BW Open koncentratora.

## <a name="prerequisites"></a>Wymagania wstępne

- **Azure Data Factory (ADF):** Jeśli nie masz usługi data factory, postępuj zgodnie z "[utworzyć fabrykę danych](quickstart-create-data-factory-portal.md#create-a-data-factory)" sekcji, aby go utworzyć. 

- **Oprogramowanie SAP BW Open koncentrator docelowy (OHD) z typem docelowym jako "W tabeli bazy danych".** Postępuj zgodnie z [konfiguracji SAP BW Open koncentrator docelowy](#sap-bw-open-hub-destination-configurations) sekcji, aby utworzyć lub upewnij się, jeśli Twoje istniejące OHD jest niepoprawnie skonfigurowany do można zintegrować z usługą ADF.

- **System SAP BW użytkownika używany musi mieć następujące uprawnienia:**

  - Autoryzacja RFC i system SAP BW.
  - Uprawnienia do "**wykonania**"Activity obiekt autoryzacji"**S_SDSAUTH**".

- **[Self-Hosted Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) za pomocą łącznika SAP .NET 3.0 są wymagane**. Poniżej przedstawiono szczegółowe czynności, które należy wykonać:

  1. Zainstalować i zarejestrować własne środowisko IR w wersji > = 3.13 (co omówiono w następującym przewodniku). 

  2. Pobierz [64-bitowych SAP .NET łącznika 3.0](https://support.sap.com/en/product/connectors/msnet.html) z witryny sieci Web firmy SAP i zainstaluj go na maszynie własne środowisko IR ().  Podczas instalowania w oknie "opcjonalne kroki instalacji" Upewnij się, że wybierzesz "**zainstalować zestawy GAC**" opcji, jak pokazano na poniższej ilustracji.

     ![Konfigurowanie łącznika systemu SAP platformy .NET](media\connector-sap-business-warehouse-open-hub\install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Pełna kopia z Centrum Otwórz programu SAP BW

W witrynie Azure portal, przejdź do usługi data factory -> Wybierz **tworzenie i monitorowanie** Aby uruchomić interfejs użytkownika usługi ADF w osobnej karcie. 

1. Na stronie **Wprowadzenie** wybierz pozycję **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

2. Na **właściwości** Określ nazwę, **Nazwa zadania** i wybierz przycisk **dalej**.

3. Na **magazynu danych źródłowych** kliknij **+ Utwórz nowe połączenie** -> Wybierz **SAP BW Open Centrum** z galerii łączników -> Wybierz **Kontynuuj**. W polu wyszukiwania, aby filtrować łączniki, można wpisać "SAP".

4. Na **połączenia Określ SAP BW Open Centrum Hub** strony 

   ![Tworzenie usługi SAP BW Open Centrum połączone](media\load-sap-bw-data\create-sap-bw-open-hub-linked-service.png)

   1. Wybierz **Połącz za pośrednictwem środowiska Integration Runtime**: kliknij przycisk listy rozwijanej, aby wybrać istniejące IR produktem lub utworzyć katalog, jeśli nie masz jeszcze skonfigurowane własne środowisko IR (). 

      Aby utworzyć nowy element, kliknij przycisk **+ nowy** listy rozwijanej -> Wybierz typ **może być samodzielnie hostowane** -> określ **nazwa** i kliknij przycisk **dalej** -> Wybierz **Instalacja ekspresowa** do zainstalowania na komputerze bieżącym, lub wykonaj **instalacji ręcznej** kroki istnieje.

      Jak wspomniano w [wymagania wstępne](#prerequisites), upewnij się, że masz również **łącznika SAP .NET 3.0** zainstalowane na tym samym komputerze, na którym działa własne środowisko IR.

   2. Określ system SAP BW **nazwy serwera**, **numer systemu**, **identyfikator klienta** **języka** (jeśli jest inne niż EN), **nazwa_użytkownika**, i **hasło**.

   3. Kliknij przycisk **Testuj połączenie** Aby zweryfikować ustawienia, zaznacz opcję **Zakończ**.

   4. Zobaczysz, że nowe połączenie zostanie utworzona. Wybierz opcję **Dalej**.

5. Na **miejsc docelowych wybierz Otwórz Centrum** strony, przeglądania otwartych miejsc docelowych koncentratora, dostępny na Twojej systemu SAP BW i wybierz ten, który chcesz skopiować dane z, a następnie kliknij przycisk **dalej**.

   ![Wybierz tabelę Centrum Otwórz programu SAP BW](media\load-sap-bw-data\select-sap-bw-open-hub-table.png)

6. Jeśli to konieczne, należy określić filtr. Jeśli miejsce docelowe kopii Otwórz Centrum zawiera tylko dane z pojedynczego uruchomienia procesu transferu danych (DTP) o identyfikatorze pojedynczego żądania lub pewności, że Twoje DTP została zakończona i chcesz skopiować wszystkie dane, usuń zaznaczenie pola wyboru **Wyklucz ostatnie żądanie**. Dowiedz się więcej na temat używania tych ustawień odnoszą się do konfiguracji programu SAP BW w programie [konfiguracji SAP BW Open koncentrator docelowy](#sap-bw-open-hub-destination-configurations) sekcji. Kliknij przycisk **weryfikacji** do Sprawdź dane zwracane, następnie wybierz pozycję **dalej**.

   ![Konfiguruj filtr Centrum Otwórz programu SAP BW](media\load-sap-bw-data\configure-sap-bw-open-hub-filter.png)

7. W **docelowego magazynu danych** kliknij **+ Utwórz nowe połączenie**, a następnie wybierz pozycję **usługi Azure Data Lake Storage Gen2**i wybierz **Kontynuuj**.

8. W **połączenia Określ magazyn usługi Azure Data Lake** strony 

   ![Tworzenie usługi ADLS Gen2 połączone](media\load-sap-bw-data\create-adls-gen2-linked-service.png)

   1. Wybierz usługi Data Lake magazynu Gen2 — listy rozwijanej w stanie Zmiana warstwy konta z "Nazwa konta magazynu".
   2. Wybierz **Zakończ** do utworzenia połączenia. Następnie wybierz przycisk **Dalej**.

9. W **Wybieranie wyjściowego pliku lub folderu** strony, wprowadź nazwę folderu wyjściowego "copyfromopenhub" i wybierz **dalej**.

   ![Wybierz folder wyjściowy](media\load-sap-bw-data\choose-output-folder.png)

10. W **ustawienia formatu pliku** wybierz opcję **dalej** można użyć ustawień domyślnych.

   ![Określ format obiektu sink](media\load-sap-bw-data\specify-sink-format.png)

11. W **ustawienia** rozwiń **ustawienia wydajności**i ustaw **stopień równoległości kopiowania** takich jak 5, aby można było załadować z systemu SAP BW równolegle. Kliknij przycisk **Dalej**.

    ![Skonfiguruj ustawienia kopiowania](media\load-sap-bw-data\configure-copy-settings.png)

12. W **Podsumowanie** strony, przejrzyj ustawienia i wybierz **dalej**.

13. W **wdrożenia** wybierz opcję **Monitor** Aby monitorować potok.

    ![Strona Wdrażanie](media\load-sap-bw-data\deployment.png)

14. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera linki, aby wyświetlić szczegóły uruchamiania działania i ponowne uruchamianie potoku:

    ![Monitorowanie potoku](media\load-sap-bw-data\pipeline-monitoring.png)

15. Aby wyświetlić uruchomienia działań, które są skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** łącze w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby wrócić do widoku uruchomienia potoku, wybierz **potoki** link u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Monitorowanie aktywności](media\load-sap-bw-data\activity-monitoring.png)

16. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **szczegóły** link (obraz okularów) w obszarze **akcje** w działaniu, w widoku monitorowania. Możesz monitorować szczegółowe informacje, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, wykonywania kroków za pomocą odpowiedni czas i konfiguracje używane:

    ![Szczegóły monitorowania aktywności](media\load-sap-bw-data\activity-monitoring-details.png)

17. Przegląd **maksymalny identyfikator żądania** kopiowana. Wróć do działania widok monitorowania, kliknij przycisk **dane wyjściowe** w obszarze **akcje**.

    ![Wyjście działania](media\load-sap-bw-data\activity-output.png)

    ![Szczegóły danych wyjściowych działania](media\load-sap-bw-data\activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Przyrostowe kopiowanie danych z Centrum Otwórz programu SAP BW

> [!TIP]

> Zapoznaj się [przepływ wyodrębniania delta łącznika SAP BW Open Centrum](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) Aby dowiedzieć się więcej na temat działania ADF działania kopiowania do skopiowania danych przyrostowych programu SAP BW.

Teraz możemy w dalszym ciągu skonfigurować przyrostowe kopiowanie danych z Centrum Otwórz programu SAP BW. 

Przyrostowa kopia jest przy użyciu mechanizmu górnego limitu na podstawie żądania identyfikator jest generowany automatycznie w miejscu docelowym Centrum Otwórz SAP BW przy DTP. Na poniższym diagramie przedstawiono przepływ pracy dla tej metody:

![Przyrostowa kopia przepływu pracy](media\load-sap-bw-data\incremental-copy-workflow.png)

W interfejsie użytkownika usługi ADF **zaczynajmy** wybierz opcję **Utwórz potok**. 

1. Przeciągnij trzy czynności — **wyszukiwania, kopiowania danych i sieci Web** — na kanwie i marka łańcuchowa je w razie powodzenia. W tym przewodniku użyjemy obiektów Blob platformy Azure do przechowywania górny limit - max żądania skopiowanego identyfikatora Można również użyć usługi SQL database do przechowywania jego i korzystać działania procedura składowana zamiast działanie internetowe ją zaktualizować.

   ![Potoku kopiowania przyrostowego](media\load-sap-bw-data\incremental-copy-pipeline.png)

2. Skonfiguruj działanie wyszukiwania:

   1. W działanie Lookup **ustawienia** karcie wyboru **tylko pierwszy wiersz** opcji.

      ![Ustawienia wyszukiwania](media\load-sap-bw-data\lookup-settings.png)

   2. Konfigurowanie **zestaw danych źródłowych** jako zestaw danych obiektów Blob w **ścieżka pliku**punktu do obiektu blob, w której chcesz przechowywać identyfikator maksymalny skopiowany żądania jako górnego limitu i zachować format w formacie tekstowym.

      ![Ustawienia zestawu danych obiektów blob](media\load-sap-bw-data\blob-dataset.png)

   3. W odpowiednich ścieżka obiektu blob należy utworzyć obiekt blob z zawartością 0.

      ![Zawartość obiektu blob](media\load-sap-bw-data\blob.png)

3. Skonfiguruj sposób działania kopiowania: 

   1. W **źródła** skonfiguruj **zestaw danych źródłowych** jako zestaw danych SAP BW Open koncentratora. 

   2. Edytuj **zestawu danych SAP BW Open Centrum**:

      1. W **parametry** kartę, należy ustawić parametr o nazwie "identyfikator żądania."
      2. W **połączenia** karcie, określ nazwę tabeli Otwórz Centrum, Zachowaj "Wyklucz ostatnie identyfikator żądania" zaznaczone, a następnie ustaw identyfikator żądania podstawowego można użyć wyrażenia (Dodaj zawartość dynamiczną) `@dataset().requestId`.

   3. Wróć do działania kopiowania **źródła** Skonfiguruj wartość "identyfikator", aby użyć wyrażenia (Dodaj zawartość dynamiczną) `@{activity('<look up activity name>').output.firstRow.Prop_0}`. Zmień odpowiednio "odnośnika Nazwa działania" w tym wyrażeniu.

      ![Kopiowanie ustawień źródła](media\load-sap-bw-data\copy-source.png)

4. Skonfiguruj działanie internetowe: to działanie sieci Web będzie wywoływać aplikacji logiki do przechowywania identyfikator maksymalny żądania skopiowany do obiektu blob.

   1. Przejdź do witryny Azure portal -> Nowy **aplikacji logiki** przy użyciu jednego **żądania HTTP** i jeden **Utwórz obiekt blob** w następujący sposób. Użyj tego samego pliku obiektu blob skonfigurowane w powyższym źródło działania Lookup. A następnie skopiuj **HTTP POST URL** które będą używane w działaniu sieci web.

      ![Konfiguracja aplikacji logiki](media\load-sap-bw-data\logic-app-config.png)

   2. Przejdź wstecz, aby edytować ADF **działania w sieci Web** ustawienia zgodnie z poniższymi instrukcjami. Konfigurowanie treść jako wyrażenie (Dodaj zawartość dynamiczną) `{"sapOpenHubMaxRequestId":"@{activity('CopyFromSap').output.sapOpenHubMaxRequestId}"}`.

      ![Ustawienia działania w sieci Web](media\load-sap-bw-data\web-activity-settings.png)

5. Następnie możesz kliknąć polecenie **debugowania** aby umożliwić zweryfikowanie konfiguracji, lub zaznacz **Opublikuj wszystkie** opublikować wszystkie zmiany, a następnie kliknij pozycję **wyzwalacza** można wykonać uruchomienia.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfiguracje SAP BW Open koncentrator docelowy

Ta sekcja wprowadza wymagana konfiguracja po stronie SAP BW, aby można było używać łącznika SAP BW Open Centrum w usłudze ADF, aby skopiować dane.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurowanie wyodrębniania zmian w SAP BW

Jeśli potrzebujesz zarówno historycznej kopii i kopiowania przyrostowego lub tylko kopiowania przyrostowego należy skonfigurować delta wyodrębniania w SAP BW.

1. Tworzenie celu otwórz Centrum (OHD)

   Możesz utworzyć OHD w RSA1 transakcji SAP. To automatycznie tworzy wymagane przekształcania i proces transferu danych (DTP). Użyj następujących ustawień:

   - Może to być dowolny typ obiektu. Tutaj używamy InfoCube jako przykład.
   - **Typ docelowy:** *Tabela bazy danych*
   - **Klucz tabeli:** *Klucz Technical Preview*
   - **Wyodrębniania:** *Zachowaj dane oraz wstawiania rekordów do tabeli*

   ![Tworzenie programu SAP BW OHD delta wyodrębniania](media\load-sap-bw-data\create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media\load-sap-bw-data\create-sap-bw-ohd-delta2.png)

   Można zwiększyć liczbę równoległych uruchomionych procesów roboczych SAP dla DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data\create-sap-bw-ohd-delta3.png)

2. Harmonogram DTP w łańcuchy procesu

   DTP Delta dla modułu działa tylko wtedy, gdy potrzebne wiersze nie zostały skompresowane jeszcze. W związku z tym należy się upewnić, że kompresja modułu BW nie jest uruchomiony przed DTP do tabeli Otwórz Centrum. Najprostszym sposobem, w tym jest integrowanie DTP tym swoje istniejące łańcuchów procesu. W poniższym przykładzie DTP (w celu OHD) jest wstawiana w łańcuch proces między kroku Dostosuj (zbiorczy agregacji) i Zwiń (modułu kompresją).

   ![create-sap-bw-process-chain](media\load-sap-bw-data\create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurowanie pełnej wyodrębniania w SAP BW

Oprócz wyodrębniania delta można mieć pełną wyodrębniania samej InfoProvider. Odnosi się zazwyczaj do pełnego kopiowania bez konieczności przyrostowych lub chcesz [ponownie zsynchronizować wyodrębniania delta](#re-sync-delta-extraction).

Nie może mieć więcej niż jeden DTP dla tego samego OHD. W związku z tym należy utworzyć dodatkowe OHD niż wyodrębniania delta.

![Utwórz sap-bw-ohd — pełny](media\load-sap-bw-data\create-sap-bw-ohd-full.png)

Pełne ładowanie OHD można wybrać różne opcje niż delta wyodrębniania:

- W OHD: Ustaw opcję "Wyodrębniania" jako "*Usuń dane oraz wstawiania rekordów*". W przeciwnym razie dane będzie można wyodrębnić wielokrotnie powtarzające się kiedy DTP w łańcuch proces BW.

- W DTP: Ustaw "Tryb wyodrębniania" jako "*pełne*". Należy zmienić automatycznie utworzone DTP z różnicowego pełnej po utworzeniu OHD:

   ![Utwórz — sap-bw-ohd-full2](media\load-sap-bw-data\create-sap-bw-ohd-full2.png)

- W łączniku usługi ADF SAP BW Open Centrum: wyłącz opcję "*Wyklucz ostatnie żądanie*". W przeciwnym razie nic nie będzie można wyodrębnić. 

Zazwyczaj uruchomić pełne DTP ręcznie. Lub może być również utworzyć łańcuch procesów dla pełnego DTP — zwykle jest to niezależna od swojej istniejącej łańcuchów procesu łańcuch oddzielny proces. W obu przypadkach należy **upewnij się, DTP zostało zakończone przed rozpoczęciem ekstrakcji przy użyciu kopiowania usługi ADF**, w przeciwnym razie częściowe dane zostaną skopiowane.

### <a name="run-delta-extraction-the-first-time"></a>Uruchom wyodrębniania delta po raz pierwszy

Pierwszy wyodrębniania Delta jest technicznie **pełną wyodrębniania**. Uwaga przez łącznik usługi ADF SAP BW Open Centrum domyślne wyklucza ostatniego żądania podczas kopiowania danych. W przypadku różnicowej wyodrębniania po raz pierwszy w activtiy kopiowania usługi ADF, żadne dane nie zostaną wyodrębnione do momentu późniejszego DTP generuje dane różnicowe z tabeli o identyfikatorze oddzielne żądanie. Gdy istnieją dwa sposoby, aby uniknąć tego scenariusza:

1. Wyłącz opcję "Wykluczenie ostatniego żądania" dla pierwszego Delta wyodrębniania w takim, czego potrzebujesz, aby upewnić się, że pierwszy DTP Delta została zakończona przed rozpoczęciem wyodrębniania Delta po raz pierwszy
2. Procedura do ponownego zsynchronizowania wyodrębniania różnicowych zgodnie z poniższym opisem.

### <a name="re-sync-delta-extraction"></a>Ponownie zsynchronizować wyodrębniania delta

Istnieje kilka scenariuszy, zmiany danych SAP BW modułów, które nie są uwzględniane przy DTP zmian:

- Oprogramowanie SAP BW selektywne usuwanie (wiersze, używając dowolny warunek filtru)
- Oprogramowanie SAP BW żądania usunięcia (uszkodzone żądania)

Otwórz docelowy Centrum SAP nie jest celem dane podlegające składnicy danych (w wszystkich SAP BW obsługi pakietów od roku 2015). Dlatego jest możliwe usunięcie danych z modułu bez wprowadzania zmian w danych w OHD. W takim przypadku należy ponownie zsynchronizować danych modułu z danymi w usłudze ADF, wykonując następujące czynności:

1. Uruchom pełną wyodrębniania w usłudze ADF (przy użyciu pełnej DTP w systemie SAP)
2. Usunąć wszystkie wiersze w tabeli Centrum Otwórz Delta DTP
3. Ustaw stan Delta DTP pobrano

Dzięki temu wszystkie kolejne DTPs różnicowych i wyodrębniania Delta ADF działają prawidłowo zgodnie z oczekiwaniami.

Można ustawić stan Delta DTP pobrano, uruchamiając DTP Delta ręcznie przy użyciu następujących opcji: "*Żaden Transfer danych; Stan zmian w źródle: Pobrana*".

## <a name="next-steps"></a>Kolejne kroki

Przejdź z następującym artykułem, aby dowiedzieć się więcej o obsłudze łącznik SAP BW Open Centrum: 

> [!div class="nextstepaction"]
>[Łącznika systemu SAP Business Warehouse Otwórz Centrum](connector-sap-business-warehouse-open-hub.md)
