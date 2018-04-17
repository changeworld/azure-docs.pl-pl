---
title: Narzędzie migracji Power BI Embedded | Dokumentacja firmy Microsoft
description: Narzędzie do migracji Power BI Embedded może służyć do skopiowania do Power BI Embedded kolekcje Power BI obszaru roboczego raportów.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 4f76b1efb509745653bfde0926f56032030f7d47
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Narzędzie migracji Power BI Embedded

Narzędzie do migracji Power BI Embedded może służyć do skopiowania do Power BI Embedded kolekcje Power BI obszaru roboczego raportów.

Migrowanie zawartości z kolekcji obszaru roboczego do usługi Power BI usługa może odbywać się równolegle do bieżącego rozwiązania i nie wymaga żadnych przestojów.

## <a name="limitations"></a>Ograniczenia

* Wypychanie zestawy danych nie można pobrać i musi zostać ponownie utworzone przy użyciu interfejsów API REST Power BI dla usługi Power BI.
* Zaimportowany przed 26 listopada 2016 nie będzie dostępna do pobrania plików PBIX.

## <a name="download"></a>Do pobrania

Możesz pobrać przykładowe narzędzie do migracji z [GitHub](https://github.com/Microsoft/powerbi-migration-sample). Zip repozytorium albo można pobrać lub można ją sklonować lokalnie. Po pobraniu, można otworzyć *powerbi migracji sample.sln* w programie Visual Studio, aby skompilować i uruchomić narzędzie do migracji.

## <a name="migration-plans"></a>Migracja planów

Plan migracji są tylko metadane, który tworzy wykaz zawartości Power BI obszaru roboczego kolekcje i jak chcesz publikować je w usłudze Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Rozpoczynać się od nowego planu migracji

Plan migracji jest metadanych dostępnych w Power BI obszaru roboczego kolekcje, które następnie chcesz przenieść do Power BI Embedded elementów. Planowanie migracji jest przechowywana jako plik XML.

Należy rozpocząć od utworzenia nowego planu migracji. Aby utworzyć nowy plan migracji, wykonaj następujące czynności.

1. Wybierz **pliku** > **nowego planu migracji**.

    ![Nowy Plan migracji](media/migrate-tool/migrate-tool-plan.png)

2. W **wybierz grupę zasobów kolekcje Power BI obszaru roboczego** okna dialogowego, można wybrać **środowiska** listy rozwijanej i wybierz produkcyjnego.

3. Pojawi się zalogować. Użyjesz logowanie subskrypcji platformy Azure.

    > [!IMPORTANT]
    > Jest to **nie** logowania do usługi Power BI z konta organizacji usługi Office 365.

4. Wybierz subskrypcję Azure, która przechowuje zasobu Power BI obszaru roboczego kolekcji.

    ![Wybierz subskrypcję platformy Azure](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Poniższej listy subskrypcję, wybierz **grupy zasobów** zawiera odpowiednie kolekcje obszar roboczy i wybierz **wybierz**.

    ![Wybierz grupę zasobów](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Wybierz **analizowanie**. Programy spis elementów w Twojej subskrypcji platformy Azure, aby można było planu.

    ![Wybierz przycisk Analizuj](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > Analizuj proces może potrwać kilka minut w zależności od liczby kolekcji obszaru roboczego i ilości zawartości istnieje w kolekcji obszarów roboczych.

7. Gdy **Analizuj** jest zakończone, zostanie wyświetlony monit o zapisanie planu migracji.

W tym momencie połączyły plan migracji do subskrypcji platformy Azure. Odczyt poniżej zrozumienie przepływu pracy z planu migracji. W tym analizy i planowania migracji, pobierania, tworzenie grup oraz przekazywania.

### <a name="save-your-migration-plan"></a>Zapisz plan migracji

Można zapisać planu migracji do użycia w przyszłości. Spowoduje to utworzenie pliku XML, który zawiera wszystkie informacje w plan migracji.

Aby zapisać plan migracji, wykonaj następujące czynności.

1. Wybierz **pliku** > **Zapisz Plan migracji**.

    ![Zapisz opcji menu planu migracji](media/migrate-tool/migrate-tool-save-plan.png)

2. Nadaj nazwę pliku lub nazwę wygenerowanego pliku i wybierz **zapisać**.

### <a name="open-an-existing-migration-plan"></a>Otwórz istniejący plan migracji

Można także otworzyć planu migracji zapisanych na kontynuowanie pracy nad migracji.

Aby otworzyć istniejący plan migracji, wykonaj następujące czynności.

1. Wybierz **pliku** > **Otwórz istniejący Plan migracji**.

    ![Otwórz istniejący opcji menu planu migracji](media/migrate-tool/migrate-tool-open-plan.png)

2. Wybierz plik migracji, a następnie wybierz **Otwórz**.

## <a name="step-1-analyze-and-plan-migration"></a>Krok 1: Analizowanie i planowanie migracji

**Analizuj & planowanie migracji** karta zawiera widok co to jest aktualnie w grupie zasobów subskrypcji platformy Azure.

![Analizuj & planowanie migracji kartę](media/migrate-tool/migrate-tool-step1.png)

Zajmiemy *SampleResourceGroup* jako przykład.

### <a name="paas-topology"></a>Topologia PaaS

Jest to lista sieci *grupa zasobów > kolekcje obszaru roboczego > obszary robocze*. Kolekcje grupy i obszaru roboczego zasobu zostaną wyświetlone przyjazną nazwę. Obszary robocze zostaną wyświetlone identyfikatorem GUID.

Elementy na liście będą również wyświetlane w formacie (#/ #) koloru i numeru. Wskazuje liczbę raportów, które można pobrać.

Kolor czarny oznacza, że można pobrać wszystkich raportów. Kolor czerwony oznacza, że nie można pobrać niektórych raportów. Po lewej stronie numer wskaże całkowita liczba raportów, które mogą być pobrane. Liczba po prawej stronie wskazuje całkowitą liczbę raportów w ramach grupy.

Można wybrać elementu w obrębie topologii PaaS do wyświetlania raportów w sekcji raportów.

### <a name="reports"></a>Raporty

W sekcji raportów wyświetla się dostępne raporty i wskazuje, czy mogą być pobierane, czy nie.

![Lista raportów w kolekcjach obszaru roboczego programu Power BI](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Struktura obiektów docelowych

**Target struktury** jest, gdzie można sprawdzić narzędzie której zostaną pobrane rzeczy oraz sposób ich przekazywania.

#### <a name="download-plan"></a>Pobierz planu

Ścieżka zostanie automatycznie utworzone automatycznie. W razie potrzeby można zmienić tej ścieżki. Jeśli zmienisz ścieżki, należy wybrać **aktualizacja ścieżki**.

**To nie faktycznie wykonuje pobierania.** Jest to tylko Określanie struktury której będą pobierane raporty.

#### <a name="upload-plan"></a>Przekaż planu

W tym miejscu można określić prefiks do użycia dla aplikacji obszary robocze, które zostaną utworzone w ramach usługi Power BI. Po prefiksie będzie identyfikator GUID dla obszaru roboczego, które istniały na platformie Azure.

![Określ prefiks nazwy grupy](media/migrate-tool/migrate-tool-upload-plan.png)

**To nie powoduje utworzenia w grupach w usłudze Power BI.** Definiuje tylko nazewnictwa struktury w grupach.

Jeśli zmienisz prefiks, należy wybrać **Generowanie Przekaż Planowanie**.

Można grupy kliknij prawym przyciskiem myszy i wybierz można zmienić nazwy grupy w ramach planu przekazywania bezpośrednio, w razie potrzeby.

![Zmień nazwę grupy menu kontekstowego](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> Nazwa *grupy* nie może zawierać spacji ani nieprawidłowych znaków.

## <a name="step-2-download"></a>Krok 2: pobieranie

Na **Pobierz** kartę, zostanie wyświetlona lista raportów i skojarzonych metadanych. Widać, stan eksportu jest wraz z poprzedniego stanu eksportu.

![Pobierz kartę](media/migrate-tool/migrate-tool-download-tab.png)

Dostępne są dwie opcje.

* Wybierz szczegółowymi raportami, a następnie wybierz **pobrać wybrane**
* Wybierz **pobranie wszystkich**.

![Pobierz wybrany przycisk](media/migrate-tool/migrate-tool-download-options.png)

Do pomyślnego pobrania, pojawi się stan *gotowe* i będzie odzwierciedlać, czy istnieje plik PBIX.

Po zakończeniu pobierania wybierz **tworzenie grup** kartę.

## <a name="step-3-create-groups"></a>Krok 3: tworzenie grup

Po pobraniu raportów, które są dostępne, można przejść do **tworzenie grup** kartę. Na tej karcie utworzy obszarów roboczych aplikacji w usłudze Power BI na podstawie planu migracji, który został utworzony. Zostanie utworzony obszar roboczy aplikacji o nazwie znajdującego się na **przekazać** karcie w **Analizuj & planowanie migracji**.

![Utwórz kartę grupy](media/migrate-tool/migrate-tool-create-groups.png)

Do tworzenia obszarów roboczych aplikacji, można wybrać dowolną **tworzenia grup wybrane** lub **Utwórz wszystkie brakujące grupy**.

Po wybraniu jednej z tych opcji, pojawi się zalogować. *Można użyć poświadczeń dla usługi Power BI, który chcesz utworzyć aplikację obszarów roboczych na.*

![Power BI logowania ekranu](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Spowoduje to utworzenie obszaru roboczego aplikacji w usłudze Power BI. To nie przekazuje raporty do obszaru roboczego aplikacji.

Możesz sprawdzić, czy obszar roboczy aplikacji został utworzony przez podpisywania do usługi Power BI i weryfikowania, czy istnieje obszaru roboczego. Można zauważyć, że nic nie znajduje się w obszarze roboczym.

![Obszar roboczy aplikacji w usłudze Power BI](media/migrate-tool/migrate-tool-app-workspace.png)

Po utworzeniu obszaru roboczego można przenieść na **przekazać** kartę.

## <a name="step-4-upload"></a>Krok 4: Przekaż

Na **przekazać** kartę, to przekaże raporty do usługi Power BI. Na karcie pobierania oraz nazwę grupy docelowej, w oparciu o plan migracji zostanie wyświetlona lista raportów, które został pobrany.

![Przekaż karty](media/migrate-tool/migrate-tool-upload-tab.png)

Możesz przekazać wybrane raporty, lub można przekazać wszystkich raportów. Można również zresetować stan przekazywania, aby ponownie przekazać elementów.

Istnieje również możliwość wyboru co zrobić, jeśli raport o takiej samej nazwie już istnieje. Można wybrać **przerwać**, **Ignoruj** i **Zastąp**.

![Opcja listy rozwijanej, co zrobić, jeśli istnieje raportu](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Przekaż wybranych wyników](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Zduplikowane nazwy raportu

Jeśli masz raport, który ma taką samą nazwę, ale wiesz, jest inny raport, musisz zmienić **TargetName** raportu. Można zmienić nazwę, edytując ręcznie planu migracji XML.

Należy zamknąć narzędzie migracji, aby wprowadzić zmiany, a następnie ponownie otwórz narzędzie i planu migracji.

W powyższym przykładzie jeden z raportów sklonowany nie powiodło się wskazujący, że raport o takiej samej nazwie istnieje. Jeśli rozszerzana przyjrzeć się planu migracji XML, zostanie wyświetlone poniżej.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Dla elementu nie powiodło się możemy zmienić nazwę SaaSTargetReportName.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Firma Microsoft następnie otwórz ponownie plan, w narzędziu do migracji i przekazać raportu nie powiodło się.

Po powrocie do usługi Power BI, możemy stwierdzić, czy raporty i zestawy danych został przesłany w obszarze roboczym aplikacji.

![Listy raportów w usłudze Power BI](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Przekazywanie pliku PBIX lokalnego

Możesz przekazać lokalnej wersji pliku Power BI Desktop. Należy zamknąć narzędzie, Edytuj plik XML i umieść pełną ścieżkę do sieci lokalnej PBIX w **PbixPath** właściwości.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Po zakończeniu edycji kodu xml, otwórz ponownie plan w narzędzie do migracji i przekaż raportu.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>Raporty DirectQuery

Należy zaktualizować, aby zaktualizować parametry połączenia dla zapytania bezpośredniego raportów. Można to zrobić w ramach *witryny powerbi.com*, lub kwerendy można programowo parametrów połączenia z Power BI Embedded (Paas). Na przykład zobacz [wyodrębnić zapytania bezpośredniego parametry połączenia z raportu PaaS](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Następnie można zaktualizować parametry połączenia dla zestawu danych w ramach usługi Power BI i ustawić poświadczenia dla źródła danych. Można przyjrzeć się następujące przykłady, aby zobaczyć, jak to zrobić.

* [Zaktualizuj parametry połączenia zapytania bezpośredniego w usłudze Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Ustaw poświadczenia zapytania bezpośredniego w usłudze Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Kolejne kroki

Teraz, raporty zostały poddane migracji z kolekcji obszaru roboczego programu Power BI do Power BI Embedded, należy teraz zaktualizować aplikację i zacząć od osadzania raportów w tym obszarze roboczym aplikacji.

Aby uzyskać więcej informacji, zobacz [migrowania zawartości kolekcji obszarów roboczych usługi Power BI, aby usługa Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Masz więcej pytań? [Spróbuj skorzystać z społeczności Power BI](http://community.powerbi.com/)