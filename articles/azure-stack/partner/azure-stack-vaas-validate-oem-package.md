---
title: Sprawdź poprawność producenta sprzętu (OEM) pakiety usługi Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić pakiety producenta sprzętu (OEM), ze sprawdzaniem poprawności jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 02/19/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f5b884ddda292b1c523a5364d34753ccb3a5bbdf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194446"
---
# <a name="validate-oem-packages"></a>Sprawdzanie poprawności pakietów producenta OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Można przetestować nowy pakiet OEM, gdy nastąpiła zmiana oprogramowania układowego lub sterowników do weryfikacji ukończone rozwiązanie. Twój pakiet przeszedł testu, jest podpisany przez firmę Microsoft. Test musi zawierać zaktualizowany pakiet rozszerzenia producenta OEM z sterowniki i oprogramowanie układowe, które przeszły logo systemu Windows Server i komputerów z systemem testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Przed przekazaniem lub przesyłanie pakietów, zapoznaj się z [Utwórz pakiet OEM](azure-stack-vaas-create-oem-package.md) pakietu oczekiwany format i zawartość.

## <a name="managing-packages-for-validation"></a>Zarządzanie pakietami do sprawdzania poprawności

Korzystając z **sprawdzanie poprawności pakietu** przepływu pracy można zweryfikować pakietu, należy podać adres URL do **usługę Azure Storage blob**. Ten obiekt blob jest test podpisanych pakietów OEM, który zostanie zainstalowany jako część procesu aktualizacji. Tworzenie obiektu blob przy użyciu konta magazynu platformy Azure, które są tworzone podczas instalacji (zobacz [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Wymagania wstępne: Aprowizowanie kontenera magazynu

Tworzenie kontenerów w ramach konta magazynu obiektów blob pakietu. Ten kontener może służyć do wszystko, co działa Twoje pakietu sprawdzania poprawności.

1. W [witryny Azure Portal](https://portal.azure.com), przejdź do konta magazynu utworzonego w [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md).
2. W bloku po lewej stronie w obszarze **usługę Blob Service**, wybierz **kontenery**.
3. Wybierz **+ kontener** w menu paska, a następnie podaj nazwę kontenera, np. `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Przekaż pakiet do konta magazynu

1. Przygotowanie pakietu, który chcesz zweryfikować. Jeśli pakiet zawiera wiele plików, Kompresuj do `.zip` pliku.
2. W [witryny Azure Portal](https://portal.azure.com), wybierz kontener pakietu i przekaż pakiet, wybierając na **przekazywanie** na pasku menu.
3. Wybierz pakiet `.zip` plik do przekazania. Zachowaj wartości domyślne dla **typu Blob** (czyli **blokowych obiektów Blob**) i **rozmiaru bloku**.

> [!NOTE]
> Upewnij się, że `.zip` zawartości są umieszczane w katalogu głównym `.zip` pliku. Powinna istnieć nie podfolderów w pakiecie.

### <a name="generate-package-blob-url-for-vaas"></a>Generowanie adresu URL obiektu blob pakietu dla VaaS

Podczas tworzenia **sprawdzanie poprawności pakietu** przepływu pracy w portalu VaaS, musisz podać adres URL obiektu blob usługi Azure Storage, zawierającego pakiet.

#### <a name="option-1-generating-a-blob-sas-url"></a>Opcja 1: Trwa generowanie adresu URL SAS obiektu blob

Użyj tej opcji, jeśli nie chcesz włączyć publiczny dostęp do odczytu do obiektów blob lub kontenera magazynu.

1. W [witryny Azure portal](https://portal.azure.com/), przejdź do swojego konta magazynu i przejdź do .zip zawierający pakiet

2. Wybierz **Generowanie sygnatury dostępu Współdzielonego** z menu kontekstowego

3. Wybierz **odczytu** z **uprawnień**

4. Ustaw **czas rozpoczęcia** do bieżącego czasu i **czas zakończenia** do co najmniej 48 godzin od **czas rozpoczęcia**. Jeśli inne testy zostaną uruchomione przy użyciu tego samego pakietu, należy rozważyć zwiększenie **czas zakończenia** długości testowania. Wszystkie testy zaplanowane za pośrednictwem VaaS po **czas zakończenia** zakończy się niepowodzeniem i nowych sygnatur dostępu Współdzielonego będzie należy do wygenerowania.

5. Wybierz pozycję **Generuj token i adres URL sygnatury dostępu współdzielonego obiektu blob**.

Użyj **adresu URL sygnatury dostępu Współdzielonego obiektu Blob** po zapewnieniu pakietu obiektu blob adresy URL do portalu.

#### <a name="option-2-grant-public-read-access"></a>Opcja 2: Dostęp publiczny

> [!CAUTION]
> Ta opcja zostanie otwarty z obiektów blob do anonimowego dostępu tylko do odczytu.

1. Udziel **publiczne odczytu dostępu tylko dla obiektów blob** do kontenera pakietu, postępując zgodnie z instrukcjami w sekcji [przyznawanie uprawnień użytkownikom anonimowym do kontenerów i obiektów blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

> [!NOTE]
> Jeśli podajesz adres URL pakietu *interaktywne testu* (na przykład miesięczne AzureStack aktualizacji weryfikacji lub Weryfikacja pakietu rozszerzenia OEM), należy udzielić **pełny publiczny dostęp do odczytu** do Przejdź do testowania.

2. W kontenerze pakietu wybierz obiekt blob pakietu, aby otworzyć okienko właściwości.

3. Kopiuj **adresu URL**. Użyj tej wartości, jeśli dostarczanie pakietu blob adresy URL do portalu.

## <a name="apply-monthly-update"></a>Zastosuj comiesięcznej aktualizacji

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

> [!NOTE]
> Po zastosowaniu comiesięcznej aktualizacji zaleca się, że uruchomieniu AzureStack Test, aby sprawdzić, czy aktualizacja został pomyślnie zastosowany i są w dobrej kondycji. W przypadku niepowodzenia testu AzureStack zgłosić problem do firmy Microsoft. Nie Kontynuuj przebieg testu do czasu rozwiązania problemu. Informacje o sposobach uruchamiania polecenia testowanie na platformie Azure Stack można znaleźć w tym [artykułu](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

## <a name="create-a-package-validation-workflow"></a>Tworzenie przepływu pracy weryfikacji pakietu

1. Zaloguj się do [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Wybierz **Start** na **sprawdzanie poprawności pakietu** kafelka.

    ![Pakiet walidacji przepływu pracy kafelka](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Wprowadź usługi Azure Storage adresu URL obiektu blob do testu podpisanego pakietu OEM wymagających podpisu firmy Microsoft. Aby uzyskać instrukcje, zobacz [wygenerować adresu URL obiektu blob pakietu dla VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Nie można modyfikować parametrów środowiska po utworzeniu przepływu pracy.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Nastąpi przekierowanie do strony Podsumowanie testów.

## <a name="required-tests"></a>Wymagane testy

Następujące testy są wymagane do weryfikacji pakietu OEM:

- Weryfikacja pakietu rozszerzenia producenta OEM
- Aparat symulacji w chmurze

## <a name="run-package-validation-tests"></a>Uruchom testy walidacji pakietu

1. W **sprawdzanie poprawności pakietu testów Podsumowanie** stronie spowoduje uruchomienie podzbioru wymienione na liście testy odpowiednią do realizowanego scenariusza.

    W przepływach pracy sprawdzania poprawności **planowania** przetestujesz typowe parametry przepływu pracy na poziomie, które zostały określone podczas tworzenia przepływu pracy (zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md)). Jeśli dowolna z wartości parametrów testu stają się nieprawidłowe, użytkownik musi resupply je, zgodnie z instrukcją w [zmodyfikować parametry przepływu pracy](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Planowanie testów sprawdzania poprawności w istniejącym wystąpieniu utworzy nowe wystąpienie zamiast w starym wystąpieniu w portalu. Dzienniki starej instalacji zostaną zachowane, ale nie są dostępne z poziomu portalu.  
    Po pomyślnym zakończeniu testu **harmonogram** akcja zostanie wyłączona.

2. Wybierz odpowiedniego agenta, uruchom test. Informacje o dodawaniu lokalne wykonanie agentów testowych, zobacz [wdrożenia lokalnego agenta](azure-stack-vaas-local-agent.md).

3. Pełna Weryfikacja pakietu rozszerzenia OEM opcję **harmonogram** z menu kontekstowego, aby otworzyć wiersz do planowania wystąpieniem testu.

4. Przejrzyj parametry testu, a następnie wybierz pozycję **przesyłania** Aby zaplanować wykonywanie weryfikacji pakietu rozszerzenia OEM.

5. Przejrzyj wynik weryfikacji pakietu rozszerzenia OEM. Gdy test zakończyła się pomyślnie, należy zaplanować aparatu symulacji chmury w celu wykonania.

Gdy wszystkie testy zostały pomyślnie ukończone, Wyślij nazwę rozwiązania VaaS i sprawdzanie poprawności pakietu do [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) na żądanie, podpisywanie pakietów.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)
