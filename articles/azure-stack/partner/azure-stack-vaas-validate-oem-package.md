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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113286"
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

1. W [witryny Azure portal](https://portal.azure.com), przejdź do konta magazynu utworzonego w [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md).

2. W bloku po lewej stronie w obszarze **usługę Blob Service**, wybierz opcję **kontenery**.

3. Wybierz **+ kontener** z paska menu.
    1. Podaj nazwę kontenera, na przykład `vaaspackages`.
    1. Wybierz poziom dostępu odpowiednią dla nieuwierzytelnionych klientów, takie jak VaaS. Aby uzyskać więcej informacji na temat sposobu udostępnienia VaaS pakietów w każdym scenariuszu, zobacz [obsługi poziom dostępu do kontenera](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Przekaż pakiet do konta magazynu

1. Przygotowanie pakietu, który chcesz zweryfikować. Jest to `.zip` pliku, w których zawartość musi być zgodna ze strukturą opisaną w [Utwórz pakiet OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Upewnij się, że `.zip` zawartości są umieszczane w katalogu głównym `.zip` pliku. Powinna istnieć nie podfolderów w pakiecie.

1. W [witryny Azure portal](https://portal.azure.com), wybierz kontener pakietu i przekaż pakiet, wybierając na **przekazywanie** na pasku menu.

1. Wybierz pakiet `.zip` plik do przekazania. Zachowaj wartości domyślne dla **Blob typu** (czyli **blokowych obiektów Blob**) i **rozmiaru bloku**.

### <a name="generate-package-blob-url-for-vaas"></a>Generowanie adresu URL obiektu blob pakietu dla VaaS

Podczas tworzenia **sprawdzanie poprawności pakietu** przepływu pracy w portalu VaaS, musisz podać adres URL obiektu blob usługi Azure Storage, zawierającego pakiet. Niektóre *interaktywne* testy, w tym **miesięczne Weryfikacja aktualizacji AzureStack** i **weryfikacji pakietu rozszerzenia OEM**, również wymagają adresu URL do pakietu obiektów blob.

#### <a name="handling-container-access-level"></a>Obsługa kontenerów poziom dostępu

Minimalny poziom dostępu wymaganych przez VaaS zależy od tego, czy tworzysz przepływ pracy weryfikacji pakietu lub planowania *interaktywne* testu.

W przypadku właściwości **prywatnej** i **Blob** poziomy dostępu musi tymczasowo udzielić dostępu do obiektów blob pakietu, zapewniając VaaS [sygnatury dostępu współdzielonego](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). **Kontenera** poziom dostępu, który nie wymaga do generowania adresów URL sygnatury dostępu Współdzielonego, ale pozwala na nieuwierzytelniony dostęp do kontenera i jego obiektów blob.

|Poziom dostępu | Wymaganie przepływu pracy | Wymaganie testu |
|---|---------|---------|
|Private | Generuj adres URL sygnatury dostępu Współdzielonego poszczególnych obiektów blob pakietu ([opcja 1](#option-1-generate-a-blob-sas-url)). | Generowanie adresu URL sygnatury dostępu Współdzielonego na poziomie konta i ręcznie dodać nazwy obiektu blob pakietu ([opcja 2](#option-2-construct-a-container-sas-url)). |
|Obiekt blob | Podaj właściwość adres URL obiektu blob ([opcja 3](#option-3-grant-public-read-access)). | Generowanie adresu URL sygnatury dostępu Współdzielonego na poziomie konta i ręcznie dodać nazwy obiektu blob pakietu ([opcja 2](#option-2-construct-a-container-sas-url)). |
|Kontener | Podaj właściwość adres URL obiektu blob ([opcja 3](#option-3-grant-public-read-access)). | Podaj właściwość adres URL obiektu blob ([opcja 3](#option-3-grant-public-read-access)).

Opcje przyznawania dostępu do pakietów są uporządkowane z co najmniej dostęp do największej dostępu.

#### <a name="option-1-generate-a-blob-sas-url"></a>Opcja 1: Generowanie adresu URL SAS obiektu blob

Użyj tej opcji, jeśli ustawiono poziom dostępu do kontenera magazynu **prywatnej**, gdzie kontenera nie włączać publicznego dostępu do odczytu do kontenera lub jego obiektów blob.

> [!NOTE]
> Ta metoda nie będzie działać dla *interaktywne* testów. Zobacz [opcja 2: Skonstruować adres URL sygnatury dostępu Współdzielonego kontenera](#option-2-construct-a-container-sas-url).

1. W [witryny Azure portal](https://portal.azure.com/), przejdź do swojego konta magazynu i przejdź do .zip zawierający pakiet.

2. Wybierz **Generowanie sygnatury dostępu Współdzielonego** z menu kontekstowego.

3. Wybierz **odczytu** z **uprawnienia**.

4. Ustaw **czas rozpoczęcia** do bieżącego czasu i **czas zakończenia** do co najmniej 48 godzin od **czas rozpoczęcia**. Jeżeli zostanie tworzenia innych przepływów pracy za pomocą tego samego pakietu, należy rozważyć zwiększenie **czas zakończenia** długości testowania.

5. Wybierz pozycję **Generuj token i adres URL sygnatury dostępu współdzielonego obiektu blob**.

Użyj **adresu URL sygnatury dostępu Współdzielonego obiektu Blob** po zapewnieniu pakietu obiektu blob adresy URL do portalu.

#### <a name="option-2-construct-a-container-sas-url"></a>Opcja 2: Konstrukcja adresu URL sygnatury dostępu Współdzielonego kontenera

Użyj tej opcji, jeśli ustawiono poziom dostępu do kontenera magazynu **prywatnej** i musisz podać adres URL obiektu blob pakietu do *interaktywne* testu. Ten adres URL można również na poziomie przepływu pracy.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Wybierz **Blob** z **opcji dozwolone usług**. Usuń zaznaczenie pozostałych opcji.

1. Wybierz **kontenera** i **obiektu** z **dozwolone typy zasobów**.

1. Wybierz **odczytu** i **listy** z **dozwolone uprawnienia**. Usuń zaznaczenie pozostałych opcji.

1. Wybierz **czas rozpoczęcia** jako bieżący czas i **czas zakończenia** do co najmniej 14 dni od **czas rozpoczęcia**. Jeśli inne testy zostaną uruchomione przy użyciu tego samego pakietu, należy rozważyć zwiększenie **czas zakończenia** długości testowania. Wszystkie testy zaplanowane za pośrednictwem VaaS po **czas zakończenia** zakończy się niepowodzeniem i nowych sygnatur dostępu Współdzielonego będzie należy do wygenerowania.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Format powinien wyglądać w następujący sposób: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modyfikowanie wygenerowany adres URL sygnatury dostępu Współdzielonego do uwzględnienia kontenera pakietu `{containername}`i nazwę obiektu blob pakietu, `{mypackage.zip}`, wykonując następujące czynności:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Użyj tej wartości, jeśli dostarczanie pakietu blob adresy URL do portalu.

#### <a name="option-3-grant-public-read-access"></a>Opcja 3: Dostęp publiczny

Użyj tej opcji, jeśli jest dopuszczalne, aby umożliwić klientom nieuwierzytelniony dostęp do poszczególnych obiektów blob lub w przypadku programu *interaktywne* testy kontenera.

> [!CAUTION]
> Ta opcja zostanie otwarty z obiektów blob do anonimowego dostępu tylko do odczytu.

1. Ustaw poziom dostępu do kontenera pakietu **Blob** lub **kontenera** zgodnie z instrukcjami w sekcji [przyznawanie uprawnień użytkownikom anonimowym do kontenerów i obiektów blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Jeśli podajesz adres URL pakietu *interaktywne* test, musi przyznawać **pełny publiczny dostęp do odczytu** do kontenera, aby kontynuować testowanie.

1. W kontenerze pakietu wybierz obiekt blob pakietu, aby otworzyć okienko właściwości.

1. Kopiuj **adresu URL**. Użyj tej wartości, jeśli dostarczanie pakietu blob adresy URL do portalu.

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
    > Po pomyślnym zakończeniu testu **harmonogram** akcja zostanie wyłączona.

2. Wybierz odpowiedniego agenta, uruchom test. Informacje o dodawaniu lokalne wykonanie agentów testowych, zobacz [wdrożenia lokalnego agenta](azure-stack-vaas-local-agent.md).

3. Aby ukończyć weryfikacji pakietu rozszerzenia OEM, wybrać **harmonogram** z menu kontekstowego, aby otworzyć wiersz do planowania wystąpieniem testu.

4. Przejrzyj parametry testu, a następnie wybierz pozycję **przesyłania** Aby zaplanować wykonywanie weryfikacji pakietu rozszerzenia OEM.

    Weryfikacja pakietu rozszerzenia OEM jest podzielony na dwie ręczne wykonanie czynności: Aktualizacja usługi Azure Stack, a aktualizacja OEM.

   1. **Wybierz** "Uruchom" w Interfejsie użytkownika do uruchomienia skryptu precheck. Jest to testów automatycznych, który trwa około 5 minut i nie wymaga żadnej czynności.

   1. Po zakończeniu precheck skryptu, wykonaj krok wykonywany ręcznie: **zainstalować** najnowszej dostępnej usługi Azure Stack aktualizacji za pomocą portalu usługi Azure Stack.

   1. **Uruchom** Test-AzureStack dla sygnatury. Jeśli wystąpią jakiekolwiek błędy, nie Kontynuuj badanie i skontaktuj się z pomocą [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

       Aby uzyskać informacje na temat uruchamiania polecenia Test-AzureStack, zobacz [stanu systemu weryfikowanie usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

   1. **Wybierz** "Dalej", aby wykonać postcheck skrypt. Jest zautomatyzowany test, który oznacza zakończenie procesu aktualizacji usługi Azure Stack.

   1. **Wybierz** "Uruchom", można wykonać skryptu precheck aktualizacji OEM.

   1. Po zakończeniu wstępne sprawdzenie wykonać czynność ręczna: **zainstalować** pakiet rozszerzenia OEM za pośrednictwem portalu.

   1. **Uruchom** Test-AzureStack dla sygnatury.

      > [!NOTE]
      > Tak jak poprzednio, nie Kontynuuj badanie i skontaktuj się z pomocą [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) Jeśli zakończy się niepowodzeniem. Ten krok jest krytyczna, ponieważ zostanie ono zapisane możesz ponownego wdrażania.

   1. **Wybierz** "Dalej", aby wykonać postcheck skrypt. Oznacza koniec kroku aktualizacji OEM.

   1. Na wszelkie pytania pozostałe na końcu testu i **wybierz** "Prześlij".

   1. Oznacza koniec interaktywne testu.

5. Przejrzyj wynik weryfikacji pakietu rozszerzenia OEM. Gdy test zakończyła się pomyślnie, należy zaplanować aparatu symulacji chmury w celu wykonania.

Aby przesłać żądanie podpisania pakietu, należy wysłać [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) nazwę rozwiązania i sprawdzanie poprawności pakietu nazwę skojarzone z tego działania.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)
