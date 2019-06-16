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
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 4cd61db3ec0e8d88c9b1c6d6ba427b120b3f1af1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152414"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopiowanie danych z rozwiązania SAP Business Warehouse za pomocą usługi Azure Data Factory

W tym artykule pokazano, jak używać usługi Azure Data Factory do kopiowania danych z SAP Business Warehouse (BW) za pośrednictwem Centrum Otwórz do usługi Azure Data Lake Storage Gen2. Można użyć podobnej do kopiowania danych do innych [obsługiwanych magazynów danych ujścia](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Aby uzyskać ogólne informacje o kopiowaniu danych SAP BW, łącznie z integracją SAP BW Open Centrum i różnicowe wyodrębniania przepływu, zobacz [kopiowanie danych z rozwiązania SAP Business Warehouse, za pośrednictwem Centrum Otwórz za pomocą usługi Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Azure Data Factory**: Jeśli nie masz, wykonaj kroki, aby [utworzyć fabrykę danych](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **Oprogramowanie SAP BW Open koncentrator docelowy (OHD) z typem docelowym "W tabeli bazy danych"** : Aby utworzyć OHD lub Sprawdź poprawnie skonfigurowane usługi OHD integracji usługi Data Factory, zobacz [konfiguracji SAP BW Open koncentrator docelowy](#sap-bw-open-hub-destination-configurations) dalszej części tego artykułu.

- **Użytkownik systemu SAP BW musi mieć następujące uprawnienia**:

  - Autoryzacja dla wywołania funkcji zdalnego (RFC) i systemu SAP BW.
  - Uprawnienia do działania "Execute" **S_SDSAUTH** obiekt autoryzacji.

- **A [może być samodzielnie hostowane środowisko integration runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) za pomocą łącznika SAP .NET 3.0**. Wykonaj następujące kroki konfiguracji:

  1. Instalowanie i rejestrowanie własnego środowiska integration runtime, wersji 3.13 lub nowszej. (Ten proces jest opisany w dalszej części tego artykułu).

  2. Pobierz [łącznika systemu SAP 64-bitowych dla programu Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z witryny firmy SAP i zainstaluj go na tym samym komputerze co Self-Hosted IR Podczas instalacji upewnij się, że wybrano **zainstalować zestawy GAC** w **opcjonalne kroki instalacji** okno dialogowe, jak przedstawiono na poniższym obrazie:

     ![Konfigurowanie łącznika systemu SAP .NET, okno dialogowe](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Wykonaj pełną kopię SAP BW Open Centrum

W witrynie Azure portal przejdź do usługi data factory. Wybierz **tworzenie i monitorowanie** otworzyć Interfejs użytkownika usługi Data Factory na osobnej karcie.

1. Na **zaczynajmy** wybierz opcję **kopiowania danych** aby otworzyć narzędzie do kopiowania danych.

2. Na **właściwości** określ **Nazwa zadania**, a następnie wybierz pozycję **dalej**.

3. Na **magazynu danych źródłowych** wybierz opcję **+ Utwórz nowe połączenie**. Wybierz **SAP BW Open Centrum** z galerii łączników, a następnie wybierz **Kontynuuj**. Aby filtrować łączniki, można wpisać **SAP** w polu wyszukiwania.

4. Na **połączenia Określ SAP BW Open Centrum Hub** strony, wykonaj następujące kroki, aby utworzyć nowe połączenie.

   ![Utwórz stronę SAP BW Open Centrum połączona usługa](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Z **Połącz za pośrednictwem środowiska integration runtime** wybierz istniejący Self-Hosted IR Lub je utworzyć, jeśli nie masz jeszcze.

      Aby utworzyć nowy własnego środowiska IR, wybierz **+ nowy**, a następnie wybierz pozycję **może być samodzielnie hostowane**. Wprowadź **nazwa**, a następnie wybierz pozycję **dalej**. Wybierz **Instalacja ekspresowa** do zainstalowania na bieżącym komputerze lub wykonaj **instalacji ręcznej** kroki, które są dostarczane.

      Jak wspomniano w [wymagania wstępne](#prerequisites), upewnij się, że łącznika systemu SAP dla programu Microsoft .NET 3.0 zainstalowane na tym samym komputerze, na którym działa własne środowisko IR.

   2. Wypełnij systemu SAP BW **nazwy serwera**, **numer systemu**, **identyfikator klienta** **języka** (jeśli inne niż **EN**) , **Nazwa_użytkownika**, i **hasło**.

   3. Wybierz **Testuj połączenie** Sprawdź poprawność ustawień, a następnie wybierz pozycję **Zakończ**.

   4. Utworzono nowe połączenie. Wybierz opcję **Dalej**.

5. Na **miejsc docelowych wybierz Otwórz Centrum** strony, przeglądanie Otwórz Centrum miejsca docelowe są dostępne w Twojej systemu SAP BW. Wybierz OHD do kopiowania danych z, a następnie wybierz **dalej**.

   ![Wybierz tabelę SAP BW Open koncentrator docelowy](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Określ filtr, jeśli potrzebna jest jedna. Jeśli Twoje OHD zawiera tylko dane z wykonanie procesu (DTP) pojedynczy transfer danych, jest identyfikatorem pojedynczego żądania lub wiesz, że Twoje DTP zostanie zakończony, i chcesz skopiować dane, wyczyść **Wyklucz ostatnie żądanie** pole wyboru.

   Dowiedz się więcej na temat tych ustawień w [konfiguracji SAP BW Open koncentrator docelowy](#sap-bw-open-hub-destination-configurations) dalszej części tego artykułu. Wybierz **weryfikacji** dokładnie, jakie dane zostaną zwrócone. Następnie wybierz przycisk **Dalej**.

   ![Konfiguruj filtr Centrum Otwórz programu SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na **docelowego magazynu danych** wybierz opcję **+ Utwórz nowe połączenie** > **usługi Azure Data Lake Storage Gen2**  >   **Kontynuuj**.

8. Na **połączenia Określ magazyn usługi Azure Data Lake** strony, wykonaj następujące kroki, aby utworzyć połączenie.

   ![Tworzenie strony połączonej usługi Azure Data Lake Store Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Wybierz swoje konto usługi Data Lake Storage Gen2 możliwością z **nazwa** listy rozwijanej.
   2. Wybierz **Zakończ** do utworzenia połączenia. Następnie wybierz przycisk **Dalej**.

9. Na **Wybieranie wyjściowego pliku lub folderu** wpisz **copyfromopenhub** jako nazwa folderu danych wyjściowych. Następnie wybierz przycisk **Dalej**.

   ![Wybierz stronę folderu danych wyjściowych](media/load-sap-bw-data/choose-output-folder.png)

10. Na **ustawienia formatu pliku** wybierz opcję **dalej** można użyć ustawień domyślnych.

    ![Określ stronę format obiektu sink](media/load-sap-bw-data/specify-sink-format.png)

11. Na **ustawienia** rozwiń **ustawienia wydajności**. Wprowadź wartość w **stopień równoległości kopiowania** takich jak 5, aby załadować z systemu SAP BW równolegle. Następnie wybierz przycisk **Dalej**.

    ![Skonfiguruj ustawienia kopiowania](media/load-sap-bw-data/configure-copy-settings.png)

12. Na **Podsumowanie** Przejrzyj ustawienia. Następnie wybierz przycisk **Dalej**.

13. Na **wdrożenia** wybierz opcję **Monitor** Aby monitorować potok.

    ![Strona Wdrażanie](media/load-sap-bw-data/deployment.png)

14. Należy zauważyć, że **Monitor** automatycznie wybrana jest karta w lewej części strony. **Akcje** kolumny zawiera linki, aby wyświetlić szczegóły uruchomienia działania i ponowne uruchamianie potoku.

    ![Widok monitorowania potoku](media/load-sap-bw-data/pipeline-monitoring.png)

15. Aby wyświetlić uruchomienia działań, które są skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** w **akcje** kolumny. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby wrócić do widoku uruchomień potoków, wybierz **potoki** link u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Monitorowanie działania ekranu](media/load-sap-bw-data/activity-monitoring.png)

16. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **szczegóły** łącza, które znajduje się ikona okularów poniżej **akcje** w widoku Monitorowanie działania. Dostępne szczegółowe informacje zawierają ilość danych skopiowanych ze źródła do ujścia, przepływność danych, wykonywania i czasu trwania i konfiguracje używane.

    ![Szczegóły monitorowania aktywności](media/load-sap-bw-data/activity-monitoring-details.png)

17. Aby wyświetlić **maksymalny identyfikator żądania**, wróć do widoku oraz wybierz opcję Monitorowanie działania **dane wyjściowe** w obszarze **akcje**.

    ![Działania ekranu danych wyjściowych](media/load-sap-bw-data/activity-output.png)

    ![Dane wyjściowe działania w widoku szczegółów](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Czy przyrostowa kopia z Centrum Otwórz programu SAP BW

> [!TIP]
> Zobacz [przepływ wyodrębniania delta łącznika SAP BW Open Centrum](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) Aby dowiedzieć się, jak łącznik SAP BW Open Centrum w fabryce danych kopiuje dane przyrostowe z systemu SAP BW. Ten artykuł może także ułatwić zrozumienie konfiguracji łącznika podstawowe.

Teraz możemy w dalszym ciągu skonfigurować przyrostowe kopiowanie danych z Centrum Otwórz programu SAP BW.

Przyrostowa kopia wykorzystuje mechanizm "górnym limitem", który jest oparty na **identyfikator żądania**. Ten identyfikator jest automatycznie wygenerowane w SAP BW Open koncentrator docelowy DTP. Na poniższym diagramie przedstawiono ten przepływ pracy:

![Schemat blokowy przepływu pracy kopiowania przyrostowego](media/load-sap-bw-data/incremental-copy-workflow.png)

W fabryce danych **zaczynajmy** wybierz opcję **Utwórz potok z szablonu** do użycia wbudowanych szablonów.

1. Wyszukaj **systemu SAP BW** Aby znaleźć i wybrać **przyrostowe kopiowanie z systemu SAP BW, do usługi Azure Data Lake Storage Gen2** szablonu. Ten szablon kopiuje dane do usługi Azure Data Lake Storage Gen2. Podobne przepływu pracy można użyć do skopiowania do innych typów ujścia.

2. Na stronie głównej tego szablonu, wybierz lub utwórz następujące trzy połączenia, a następnie wybierz **za pomocą tego szablonu** w prawym dolnym rogu okna.

   - **Usługa Azure Blob storage**: W tym przewodniku używamy usługi Azure Blob storage do przechowywania górny limit jest *max skopiować identyfikator żądania*.
   - **Oprogramowanie SAP BW Open Centrum**: Jest to źródła do skopiowania danych z. Zapoznaj się z poprzednim przewodniku pełne kopie, aby uzyskać szczegółową konfigurację.
   - **Azure Data Lake Storage Gen2**: Jest to ujścia, aby skopiować dane. Zapoznaj się z poprzednim przewodniku pełne kopie, aby uzyskać szczegółową konfigurację.

   ![Przyrostowe kopiowanie danych z szablonu programu SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ten szablon generuje potok z następującymi działaniami trzy i sprawia, że ich łańcuchowa powodzenia: *Wyszukiwanie*, *kopiowanie danych*, i *Web*.

   Przejdź do potoku **parametry** kartę. Zobaczysz wszystkie konfiguracje, które należy podać.

   ![Przyrostowe kopiowanie danych z konfiguracji programu SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Określ nazwę tabeli Otwórz Centrum do skopiowania danych z.

   - **ADLSGen2SinkPath**: Określ ścieżkę usługi Azure Data Lake Storage Gen2 docelowego do kopiowania danych. Jeśli ścieżka nie istnieje, działanie kopiowania usługi Data Factory tworzy ścieżki podczas wykonywania.

   - **HighWatermarkBlobPath**: Określ ścieżkę do przechowywania wartości limitu wysoka, takich jak `container/path`.

   - **HighWatermarkBlobName**: Określ nazwę obiektu blob do przechowywania wartości górnego limitu, takich jak `requestIdCache.txt`. W magazynie obiektów Blob, przejdź do odpowiedniej ścieżki HighWatermarkBlobPath + HighWatermarkBlobName, takich jak *container/path/requestIdCache.txt*. Tworzenie obiektu blob z zawartością 0.

      ![Zawartość obiektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: W tym szablonie używamy WebActivity do wywołania usługi Azure Logic Apps, aby ustawić wartość limitu wysokiej w magazynie obiektów Blob. Alternatywnie można użyć usługi Azure SQL Database, zapisz go. Użyj działania procedury składowanej, aby zaktualizować wartość.

      Należy najpierw utworzyć aplikację logiki, jak przedstawiono na poniższym obrazie. Następnie wklej w **HTTP POST URL**.

      ![Konfiguracja aplikacji logiki](media/load-sap-bw-data/logic-app-config.png)

      1. Przejdź do witryny Azure Portal. Wybierz nową **Logic Apps** usługi. Wybierz **+ pusta aplikacja logiki** można przejść do **Projektant aplikacji logiki**.

      2. Tworzenie wyzwalacza **zostanie odebrane żądanie HTTP podczas**. Określ treść żądania HTTP w następujący sposób:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Dodaj **Utwórz obiekt blob** akcji. Dla **ścieżka folderu** i **nazwa obiektu Blob**, użyj tej samej wartości, które wcześniej skonfigurowane w **HighWatermarkBlobPath** i **HighWatermarkBlobName**.

      4. Wybierz pozycję **Zapisz**. Następnie skopiuj wartość **HTTP POST URL** do użycia w potoku usługi fabryka danych.

4. Po podaniu parametrów potoku fabryki danych wybierz **debugowania** > **Zakończ** aby wywołać przebieg, aby zweryfikować konfigurację. Lub wybierz **Opublikuj wszystkie** publikowania zmian, a następnie wybierz pozycję **wyzwalacza** można wykonać uruchomienia.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfiguracje SAP BW Open koncentrator docelowy

Ta sekcja wprowadza konfiguracji po stronie SAP BW do korzystania z łącznika SAP BW Open Centrum w usłudze Data Factory do kopiowania danych.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurowanie wyodrębniania zmian w SAP BW

Jeśli potrzebujesz historycznej kopii i kopiowania przyrostowego lub tylko kopiowania przyrostowego należy skonfigurować wyodrębniania delta w SAP BW.

1. Utwórz docelowy Otwórz Centrum. Możesz utworzyć OHD w RSA1 transakcji SAP, co powoduje automatyczne utworzenie procesu transferu danych i wymaganych przekształcania. Użyj następujących ustawień:

   - **ObjectType**: Można użyć dowolnego typu obiektu. W tym miejscu użyjemy **InfoCube** jako przykład.
   - **Typ docelowy**: Wybierz **bazy danych tabeli**.
   - **Klucz tabeli**: Wybierz **techniczne klucz**.
   - **Wyodrębnianie**: Wybierz **zachować dane i wstawiania rekordów do tabeli**.

   ![Utwórz okno dialogowe programu SAP BW OHD delta wyodrębniania](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Utwórz okno dialogowe programu SAP BW OHD delta2 wyodrębniania](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Można zwiększyć liczbę równoległych uruchomionych procesów roboczych SAP dla DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Zaplanuj DTP w łańcuchy procesu.

   Różnicowa DTP dla modułu tylko wtedy, gdy jeszcze nie zostały skompresowane niezbędne wierszy. Upewnij się, że BW modułu kompresji nie jest uruchomione przed DTP do tabeli Otwórz Centrum. W tym celu najłatwiej nad zintegrowaniem DTP swoje istniejące łańcuchów procesu. W poniższym przykładzie DTP (w celu OHD) są wstawiane do łańcucha procesu między *Dostosuj* (zbiorczy agregacji) i *Zwiń* kroki (modułu kompresją).

   ![Tworzenie schematu blokowego łańcuch proces programu SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurowanie pełnej wyodrębniania w SAP BW

Oprócz wyodrębniania delta możesz zechcieć pełną wyodrębniania samej InfoProvider BW SAP. Dotyczy to zazwyczaj aby pełna kopia, ale nie przyrostowe, lub jeśli chcesz [ponowna synchronizacja różnicowa wyodrębniania](#resync-delta-extraction).

Dla tego samego OHD, nie może mieć więcej niż jeden DTP. Tak należy utworzyć dodatkowe OHD przed wyodrębniania delta.

![Utwórz SAP BW OHD pełne](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Pełne ładowanie OHD wybierz różne opcje niż do wyodrębnienia zmian:

- W OHD: Ustaw **wyodrębniania** opcję **Usuń dane oraz wstawiania rekordów**. W przeciwnym razie dane zostaną wyodrębnione wiele razy, gdy Powtórz DTP w łańcuch proces BW.

- W DTP: Ustaw **tryb wyodrębniania** do **pełne**. Należy zmienić automatycznie utworzone DTP z **Delta** do **pełne** natychmiast po OHD, jak pokazano na tej ilustracji:

   ![Tworzenie programu SAP BW OHD skonfigurowane do wyodrębnienia "Pełnej" okno dialogowe](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- W łączniku BW Open Centrum usługi Data Factory: Wyłącz **Wyklucz ostatnie żądanie**. W przeciwnym razie nic nie zostanie wyodrębniona.

Zazwyczaj uruchomić pełne DTP ręcznie. Alternatywnie można utworzyć łańcuch procesów dla pełnego DTP. Zazwyczaj jest oddzielne łańcucha, która jest niezależna od istniejącej łańcuchów procesu. W obu przypadkach *upewnij się, że DTP została zakończona, przed rozpoczęciem ekstrakcji przy użyciu usługi fabryka danych kopii*. W przeciwnym razie zostaną skopiowane tylko częściowe dane.

### <a name="run-delta-extraction-the-first-time"></a>Uruchom wyodrębniania delta po raz pierwszy

Pierwszy wyodrębniania delta jest technicznie *pełne wyodrębniania*. Domyślnie łącznik SAP BW Open Centrum wyklucza ostatniego żądania podczas kopiowania danych. Dla pierwszej ekstrakcji delta nie wyodrębniania danych przez działanie kopiowania usługi Data Factory do momentu kolejnych DTP generuje dane przyrostowe w tabeli z identyfikatorem oddzielne żądanie. Istnieją dwa sposoby, aby uniknąć tego scenariusza:

- Wyłącz **Wyklucz ostatnie żądanie** opcję dla pierwszej ekstrakcji delta. Upewnij się, pierwsze delta DTP zostało zakończone przed rozpoczęciem wyodrębniania delta po raz pierwszy.
-  Na użytek Procedura ponownej synchronizacji wyodrębniania różnicowych zgodnie z opisem w następnej sekcji.

### <a name="resync-delta-extraction"></a>Ponowna synchronizacja różnicowa wyodrębniania

Następujące scenariusze zmiany danych w modułach programu SAP BW, ale nie są uważane za przez różnicowe DTP:

- System SAP BW selektywne usuwanie (wierszy przy użyciu dowolny warunek filtru)
- System SAP BW żądanie usunięcia (wadliwe żądań)

SAP Otwórz Centrum nie jest to cel dane podlegające składnicy danych (w wszystkich pakietów obsługi programu SAP BW od 2015). Tak można usunąć dane z modułu bez wprowadzania zmian w danych w OHD. Następnie należy ponownie zsynchronizować danych modułu przy użyciu usługi fabryka danych:

1. Uruchom pełną wyodrębniania w usłudze Data Factory (przy użyciu pełnej DTP w systemie SAP).
2. Usuń wszystkie wiersze w tabeli Centrum Otwórz różnicowych DTP.
3. Ustaw stan różnicowych DTP do **pobrano**.

Po przypisaniu wszystkich kolejnych różnicowych DTPs i fabryki danych różnicowych wyodrębniania działać zgodnie z oczekiwaniami.

Aby ustawić stan delta DTP do **pobrano**, można użyć następujących opcji ręcznie uruchomić delta DTP:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o obsłudze łącznik SAP BW Open Centrum:

> [!div class="nextstepaction"]
>[Łącznika systemu SAP Business Warehouse Otwórz Centrum](connector-sap-business-warehouse-open-hub.md)
