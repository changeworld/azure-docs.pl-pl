---
title: Monitorowanie i zarządzanie testami w portalu usługi Azure Stack VaaS | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie testami w portalu usługi Azure Stack VaaS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c4a4b70e39731b3a78fd42078bfa1b7340343f9a
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334375"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorowanie i zarządzanie testami w portalu VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Po zaplanowaniu testy z rozwiązania do usługi Azure Stack, weryfikacji jako usługa (VaaS) rozpocznie raportowanie stanu wykonywania testu. Te informacje są dostępne w portalu VaaS wraz z akcji, takich jak korzystanie z odpraw i anulowaniu testów.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Przejdź do strony Podsumowanie testów przepływu pracy

1. Na pulpicie nawigacyjnym rozwiązania wybierz istniejącego rozwiązania, który ma co najmniej jeden przepływ pracy.

    ![Kafelki przepływu pracy](media/tile_all-workflows.png)

1. Wybierz **Zarządzaj** na kafelku przepływu pracy. Dalej znajduje się lista przepływy pracy utworzone dla wybranego rozwiązania.

1. Wybierz nazwę przepływu pracy, aby otworzyć Podsumowanie testu.

## <a name="change-workflow-parameters"></a>Zmień parametry przepływu pracy

Każdy typ przepływu pracy pozwala edytować [parametry testu](azure-stack-vaas-parameters.md#test-parameters) określone podczas tworzenia przepływu pracy.

1. Na stronie podsumowania testów, wybierz **Edytuj** przycisku.

1. Podaj nowe wartości zgodnie z opisem w [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md).

1. Wybierz **przesyłania** można zapisać wartości.

> [!NOTE]
> W **przebiegu testu** przepływu pracy, należy zakończyć wybór testów i przejdź do strony przeglądu, zanim będzie można zapisać nowe wartości parametru.

### <a name="add-tests-test-pass-only"></a>Dodaj testy (tylko dla przebiegu testu)

W **przebiegu testu** przepływów pracy, zarówno **Dodaj testy** i **Edytuj** przyciski umożliwiają planowanie nowe testy w przepływie pracy.

> [!TIP]
> Wybierz **Dodaj testy** jeśli tylko chcesz zaplanować nowe testy i nie trzeba edytować parametry **przebiegu testu** przepływu pracy.

## <a name="managing-test-instances"></a>Zarządzanie wystąpieniami testu

Dla uruchomień nieoficjalny (czyli **przebiegu testu** przepływu pracy), na stronie podsumowania testów zawiera testy, w zaplanowane przed rozwiązania usługi Azure Stack.

Oficjalna uruchomień (czyli **weryfikacji** przepływów pracy), na stronie podsumowania testów Wyświetla listę testów, wymagane do zakończenia weryfikacji rozwiązania usługi Azure Stack. Testy weryfikacyjne są planowane na tej stronie.

Każde wystąpienie zaplanowane testy zostaną wyświetlone następujące informacje:

| Kolumna | Opis |
| --- | --- |
| Nazwa testu | Nazwa i wersja testu. |
| Kategoria | Cel testu. |
| Utworzone | Czas, w którym test został zaplanowany. |
| Rozpoczęto | Czas, w którym test rozpoczął wykonywanie sekwencji. |
| Czas trwania | Długość czasu, przez który test był uruchomiony. |
| Stan | Stan lub wynik testu. Przed wykonaniem lub w trakcie stany to: `Pending`, `Running`. Terminalu stany to: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Nazwa agenta | Nazwa agenta, który prowadził test. |
| Łączna liczba operacji | Całkowita liczba operacji podjęto próbę podczas testu. |
| Operacje przekazany | Liczba operacji, które zakończyły się pomyślnie podczas testu. |
|  Operacje zakończone niepowodzeniem | Liczba operacji, które nie powiodło się podczas testu. |

### <a name="actions"></a>Akcje

Każde wystąpienie testu Wyświetla listę dostępnych akcji może zająć się po kliknięciu w jego menu kontekstowe **[...]**  w tabeli wystąpień testu.

#### <a name="view-information-about-the-test-definition"></a>Wyświetl informacje o definicji testu

Wybierz **służy do wyświetlania informacji** z menu kontekstowego, aby wyświetlić ogólne informacje o definicji testu. To jest współużytkowana przez każde wystąpienie test o tej samej nazwie i wersji.

| Właściwość testu | Opis |
| -- | -- |
| Nazwa testu | Nazwa testu. |
| Wersja testowa | Wersja testu. |
| Wydawca | Wydawca testu. |
| Kategoria |  Cel testu. |
| Usługi docelowej | Usługi Azure Stack usług, testowany. |
| Opis | Opis testu. |
| Szacowany czas trwania (w minutach) | Oczekiwany czas działania testu. |
| Linki | Wszelkie istotne informacje dotyczące testu lub punkty kontaktu. |

#### <a name="view-test-instance-parameters"></a>Wyświetl parametry wystąpienia testu

Wybierz **wyświetlania parametrów** z menu kontekstowego, aby wyświetlić parametry dostarczane do wystąpienia testu w czasie harmonogramu. Ciągi poufne, takie jak hasła nie są wyświetlane. Ta akcja jest dostępna tylko dla testów, które zostały zaplanowane.

To okno zawiera następujące metadane dla wszystkich wystąpień testu:

| Testowanie właściwości wystąpienia | Opis |
| -- | -- |
| Nazwa testu | Nazwa testu. |
| Wersja testowa | Wersja testu. |
| Identyfikator wystąpienia testu | Identyfikator GUID wystąpienia określonego testu. |

#### <a name="view-test-instance-operations"></a>Wyświetlanie testów wystąpienia operacji

Wybierz **wyświetlić operacje** z kontekstu menu, aby wyświetlić szczegółowy stan operacje wykonywane podczas testu. Ta akcja jest dostępna tylko dla testów, które zostały zaplanowane.

![Wyświetlanie operacji](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Pobieranie dzienników na potrzeby wystąpienia ukończonego testu

Wybierz **pobieranie dzienników** z menu kontekstowego, aby pobrać `.zip` pliku danych wyjściowych dzienników podczas wykonywania testów. Ta akcja jest dostępna tylko dla testów, które zostały wykonane, czyli testów ze stanem albo `Cancelled`, `Failed`, `Aborted`, lub `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Ponowne planowanie wystąpieniem testu lub zaplanować testu

Planowanie testów ze strony zarządzania zależy od typu przepływu pracy testu jest uruchamiana.

##### <a name="test-pass-workflow"></a>Przepływ pracy w przebiegu testu

W przepływie pracy przebiegu testu **korzystanie z odpraw** wystąpieniem testu ponownie używa tego samego zestawu parametrów jako oryginalnego wystąpienia testu i *zastępuje* oryginalnego wynik, łącznie z jej dzienników. Należy ponownie wprowadzić ciągi poufne, takie jak hasła w razie zmiany harmonogramu.

1. Wybierz **zmienić termin egzaminu** z menu kontekstowego, aby otworzyć monit o ponowne wystąpienie testu.

1. Wprowadź wszelkie parametry wyszukiwania.

1. Wybierz **przesyłania** zmienić termin egzaminu wystąpieniem testu i Zastąp istniejące wystąpienie.

##### <a name="validation-workflows"></a>Sprawdzanie poprawności przepływów pracy

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Anuluj wystąpieniem testu

Zaplanowane testy może zostać anulowana, jeśli jego stan to `Pending` lub `Running`.  

1. Wybierz **anulować** z menu kontekstowego, aby otworzyć wiersz anulowania wystąpieniem testu.

1. Wybierz **przesyłania** anulować wystąpieniem testu.

## <a name="next-steps"></a>Kolejne kroki

- [Rozwiązywanie problemów z weryfikacji jako usługa](azure-stack-vaas-troubleshoot.md)
