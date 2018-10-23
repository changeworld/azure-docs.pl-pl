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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: bcfc4cb65c94e34e9f6056ada53726f88489fefb
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646655"
---
# <a name="validate-oem-packages"></a>Sprawdzanie poprawności pakietów producenta OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Można przetestować nowy pakiet OEM, gdy nastąpiła zmiana oprogramowania układowego lub sterowników do weryfikacji ukończone rozwiązanie. Twój pakiet przeszedł testu, jest podpisany przez firmę Microsoft. Test musi zawierać zaktualizowany pakiet rozszerzenia producenta OEM z sterowniki i oprogramowanie układowe, które przeszły logo systemu Windows Server i komputerów z systemem testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Przed przekazaniem lub przesyłanie pakietów, zapoznaj się z [Utwórz pakiet OEM](azure-stack-vaas-create-oem-package.md) pakietu oczekiwany format i zawartość.

## <a name="managing-packages-for-validation"></a>Zarządzanie pakietami do sprawdzania poprawności

Korzystając z **sprawdzanie poprawności pakietu** przepływu pracy można zweryfikować pakietu, należy podać adres URL do **usługę Azure Storage blob**. Ten obiekt blob jest pakiet OEM, który został zainstalowany na rozwiązania w czasie wdrażania. Tworzenie obiektu blob przy użyciu konta magazynu platformy Azure, które są tworzone podczas instalacji (zobacz [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md)).

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

#### <a name="option-1-generating-an-account-sas-url"></a>Opcja 1: Generowanie adresu URL sygnatury dostępu Współdzielonego konta

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Wybierz **Blob** z **opcji dozwolone usług**. Usuń zaznaczenie pozostałych opcji.

1. Wybierz **kontenera** i **obiektu** z **dozwolone typy zasobów**. Usuń zaznaczenie pozostałych opcji.

1. Wybierz **odczytu** i **listy** z **dozwolone uprawnienia**. Usuń zaznaczenie pozostałych opcji.

1. Ustaw **czas rozpoczęcia** do bieżącego czasu i **czas zakończenia** do 1 godziny od bieżącego czasu.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Poniżej przedstawiono wygląd format: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modyfikowanie wygenerowany adres URL sygnatury dostępu Współdzielonego do uwzględnienia kontenera pakietu `{containername}`i nazwę obiektu blob pakietu, `{mypackage.zip}`, wykonując następujące czynności:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Użyj tej wartości, przy rozpoczynaniu nowego **sprawdzanie poprawności pakietu** przepływu pracy w portalu VaaS.

#### <a name="option-2-using-public-read-container"></a>Opcja 2: Za pomocą publicznego odczytu kontenera

> [!CAUTION]
> Ta opcja otwiera kontenera do anonimowego dostępu tylko do odczytu.

1. Udziel **publiczne odczytu dostępu tylko dla obiektów blob** do kontenera pakietu, postępując zgodnie z instrukcjami w sekcji [przyznawanie uprawnień użytkownikom anonimowym do kontenerów i obiektów blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. W kontenerze pakietu zaznacz na pakiet obiektu blob w kontenerze, aby otworzyć okienko właściwości.

3. Kopiuj **adresu URL**. Użyj tej wartości, przy rozpoczynaniu nowego **sprawdzanie poprawności pakietu** przepływu pracy w portalu VaaS.

## <a name="apply-monthly-update"></a>Zastosuj comiesięcznej aktualizacji

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Tworzenie przepływu pracy weryfikacji pakietu

1. Zaloguj się do [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Wybierz **Start** na **sprawdzanie poprawności pakietu** kafelka.

    ![Pakiet walidacji przepływu pracy kafelka](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Wprowadź adres URL obiektu blob usługi Azure Storage do pakietu OEM, który został zainstalowany na rozwiązania w czasie wdrażania. Aby uzyskać instrukcje, zobacz [wygenerować adresu URL obiektu blob pakietu dla VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Nie można modyfikować parametrów środowiska po utworzeniu przepływu pracy.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Nastąpi przekierowanie do strony Podsumowanie testów.

## <a name="run-package-validation-tests"></a>Uruchom testy walidacji pakietu

W **pakietu testów sprawdzania poprawności podsumowania** strony, zobaczysz listę testów, wymagane do zakończenia weryfikacji. Uruchom testy w tym przepływie pracy około 24 godzin.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

Gdy wszystkie testy zostały pomyślnie ukończone, Wyślij nazwę rozwiązania VaaS i sprawdzanie poprawności pakietu do [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) na żądanie, podpisywanie pakietów.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)