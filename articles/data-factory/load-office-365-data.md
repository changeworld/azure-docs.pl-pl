---
title: Ładowanie danych z usługi Office 365 za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Kopiowanie danych z usługi Office 365 za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60548104"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Ładowanie danych z usługi Office 365 za pomocą usługi Azure Data Factory

W tym artykule dowiesz się, jak używać usługi Data Factory _ładowanie danych z usługi Office 365 do usługi Azure Blob storage_. Można wykonać podobne kroki, aby skopiować dane do usługi Azure Data Lake Gen1 lub Gen2. Zapoznaj się [artykuł dotyczący łącznika usługi Office 365](connector-office-365.md) na ogólnie rzecz biorąc kopiowanie danych z usługi Office 365.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji:
      
   ![Strona Nowa fabryka danych](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź unikatową w skali globalnej nazwę fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadFromOffice365Demo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_ **LoadFromOffice365Demo**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybieranie subskrypcji platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub **Utwórz nową** opcji, a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych mogą być w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Store, Azure Storage, Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź z fabryką danych. Zostanie wyświetlony **usługi Data Factory** strony głównej, jak pokazano na poniższej ilustracji:
   
   ![Strona główna fabryki danych](./media/load-office-365-data/data-factory-home-page.png)

5. Wybierz **tworzenie i monitorowanie** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Na stronie "Wprowadzenie" Wybierz **Utwórz potok**.
 
    ![Tworzenie potoku](./media/load-office-365-data/create-pipeline-entry.png)

2. W **na karcie Ogólne** dla potoku, wprowadź "CopyPipeline" **nazwa** potoku.

3. W polu działania Narzędzia > Przenoszenie i przekształcanie kategorii > przeciąganie i upuszczanie **działanie kopiowania, które** z przybornika do powierzchni projektanta potoku. Określ "CopyFromOffice365ToBlob" jako nazwę działania.

### <a name="configure-source"></a>Konfigurowanie źródła

1. Przejdź do potoku > **kartę Source**, kliknij przycisk **+ nowy** utworzyć zestaw danych źródłowych. 

2. W oknie nowego zestawu danych wybierz **usługi Office 365**, a następnie wybierz pozycję **Zakończ**.

    ![Nowy zestaw danych usługi Office 365](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Zostanie wyświetlona nowa karta otwarte dla zestawu danych usługi Office 365. Na **na karcie Ogólne** w dolnej części okna właściwości wprowadź "SourceOffice365Dataset" dla nazwy.

    ![Ogólne dataset config Office 365](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Przejdź do **na karcie połączenie** w oknie właściwości. Obok pola tekstowego usługi połączonej kliknij **+ nowy**.
 
    ![Połączenie zestawu danych usługi Office 365 konfiguracji](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Okno Nowa połączona usługa wprowadź "Office365LinkedService" jako nazwy, wprowadź identyfikator jednostki usługi i klucz jednostki usługi, a następnie wybierz pozycję Zapisz, aby wdrożyć połączoną usługę.

    ![Nowa usługa połączona w usłudze Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Po utworzeniu połączonej usługi wrócisz do ustawień zestawu danych. Obok "Table" Wybierz strzałkę w dół, aby rozwinąć listę dostępnych zestawów danych usługi Office 365 i wybierz polecenie "BasicDataSet_v0. Contact_v0 "z listy rozwijanej:

    ![Tabela zestawu danych usługi Office 365 konfiguracji](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Przejdź do **kartę schemat** okno właściwości, a następnie wybierz pozycję **Importuj schemat**.  Należy zauważyć, że schemat i przykładowe wartości skontaktuj się z zestawu danych jest wyświetlana.

    ![Schemat zestawu danych usługi Office 365 konfiguracji](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Teraz wróć do potoku > źródło kartę, upewnij się, że wybrano SourceBlobDataset.
 
### <a name="configure-sink"></a>Konfigurowanie ujścia

1. Przejdź do potoku > **karta ujście**i wybierz **+ nowy** Aby utworzyć zestaw danych ujścia.
 
2. W oknie nowy zestaw danych należy zauważyć, że wybrane są tylko obsługiwanej lokalizacji docelowej podczas kopiowania z usługi Office 365. Wybierz **usługi Azure Blob Storage**, a następnie wybierz pozycję **Zakończ**.  W tym samouczku skopiujesz dane usługi Office 365 do usługi Azure Blob Storage.

    ![Nowy zestaw danych obiektów Blob](./media/load-office-365-data/new-blob-dataset.png)

4. Na **na karcie Ogólne** w oknie właściwości w polu Nazwa wprowadź "OutputBlobDataset".

5. Przejdź do **na karcie połączenie** w oknie właściwości. Obok pola tekstowego połączonej usługi, wybierz **+ nowy**.

    ![Połączenie zestawu danych obiektu Blob](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Okno Nowa połączona usługa wprowadzić "AzureStorageLinkedService" jako nazwę, wybierz pozycję "główna nazwa usługi" z listy rozwijanej metod uwierzytelniania, wypełnij punktu końcowego usługi, identyfikator jednostki usługi dzierżawy i usługi klucz jednostki, a następnie wybierz przycisk Zapisz, aby wdrożyć połączoną usługę.  Zapoznaj się [tutaj](connector-azure-blob-storage.md#service-principal-authentication) dotyczące sposobu konfigurowania uwierzytelniania jednostki usługi dla usługi Azure Blob Storage.

    ![Nowa usługa połączona obiektów Blob](./media/load-office-365-data/new-blob-linked-service.png)

7. Po utworzeniu połączonej usługi wrócisz do ustawień zestawu danych. Obok ścieżki pliku, wybierz **Przeglądaj** do folderu wyjściowego, w której zostaną wyodrębnione dane usługi Office 365 do wyboru.  W obszarze "Ustawienia formatu pliku", obok formatu pliku, wybierz opcję "**formatu JSON**", a obok wzorzec pliku, wybierz pozycję "**zestaw obiektów**".

    ![Ścieżka zestawu danych obiektu Blob i format](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Wróć do potoku > karta ujście, upewnij się, że wybrano OutputBlobDataset.

## <a name="validate-the-pipeline"></a>Weryfikowanie potoku

Wybierz na pasku narzędzi pozycję **Weryfikuj**, aby zweryfikować potok.

Można również wyświetlić kod JSON skojarzony z potokiem, klikając kodu w prawym górnym rogu.

## <a name="publish-the-pipeline"></a>Publikowanie potoku

Na górnym pasku narzędzi wybierz **Opublikuj wszystkie**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.

![Publikowanie zmian](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku

Wybierz pozycję **Wyzwól** na pasku narzędzi, a następnie wybierz pozycję **Wyzwól teraz**. Na stronie uruchomienie potoku wybierz **Zakończ**. 

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

Przejdź do **kartę Monitor** po lewej stronie. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Możesz użyć linków w **kolumnie Akcje** Aby wyświetlić szczegóły działań i ponowne uruchamianie potoku.

![Monitorowanie potoku](./media/load-office-365-data/pipeline-monitoring.png) 

Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz **Wyświetl uruchomienia działań** łącze w kolumnie akcje. W tym przykładzie istnieje tylko jedno działanie, dlatego na liście widnieje tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz **szczegóły łącza (ikona okularów)** w kolumnie akcje.

![Monitorowanie aktywności](./media/load-office-365-data/activity-monitoring.png) 

Jeśli po raz pierwszy są wnioskujące o udostępnienie danych dla tego kontekstu (kombinację danych, które tabeli jest dostęp, lokalizację docelową konta znajdują się dane są ładowane i tożsamości użytkownika, który osiągnął dane żądanie dostępu), zostanie wyświetlony działania kopiowania stan jako "**w toku**", i tylko wtedy, gdy kliknij na "Szczegóły" łącze w kolumnie akcje, będzie wyświetlany jest stan jako "**RequesetingConsent**".  Członek grupy osoba zatwierdzająca dostęp do danych musi zatwierdzić żądanie w zarządzania dostępem uprzywilejowanym, wyodrębnianie danych mógł kontynuować działanie.

_Stan jako żądanie zgody:_ 
![szczegóły wykonania działania — żądanie zgody](./media/load-office-365-data/activity-details-request-consent.png) 

_Stan jako wyodrębnianie danych:_

![Szczegóły wykonania działania — wyodrębnianie danych](./media/load-office-365-data/activity-details-extract-data.png) 

Po zgody, pod warunkiem, będą nadal wyodrębnianie danych i po pewnym czasie uruchomienia potoku znajdą się jako ukończone.

![Monitorowanie potoku — powiodło się.](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Teraz przejdź do lokalizacji docelowej usługi Azure Blob Storage i sprawdź, czy został wyodrębniony danych usługi Office 365 w formacie JSON.

## <a name="next-steps"></a>Kolejne kroki

Przejdź z następującym artykułem, aby dowiedzieć się więcej o obsłudze usługi Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Łącznik usługi Office 365](connector-office-365.md)