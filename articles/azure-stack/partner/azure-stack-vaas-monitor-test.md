---
title: Monitorowanie testów za pomocą usługi Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Monitorowanie testów weryfikacji usługi Azure Stack jako usługa, znane problemy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0bc45ea23cfb3f9b8cf7b117b5c3427d9945878e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235521"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Monitor testów za pomocą usługi Azure Stack weryfikacji jako usługa

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Wykonywanie testu można monitorować, wyświetlając **operacji** strony dla zestawów testów, które są w toku lub ukończone. Ta strona szczegółów stanu testu i jego operacji.

## <a name="monitor-a-test"></a>Monitor testu

1. Wybierz rozwiązanie.

2. Wybierz **Zarządzaj** w dowolnym kafelku przepływu pracy.

3. Kliknij przycisk przepływu pracy, aby otworzyć jego strona podsumowania testu.

4. Rozwiń menu kontekstowe **[...]**  dla każdego testu wystąpienie zestawu.

5. Wybierz **wyświetlić operacje**

![Tekst alternatywny](media\image4.png)

Dla testów, które przeszły uruchomiona, można pobrać dzienników z strona podsumowania testu, klikając **pobieranie dzienników** w menu kontekstowym testu **[...]** . Partnerzy usługi Azure Stack tych dzienników można używać do debugowania problemów dla testów zakończonych niepowodzeniem.

## <a name="open-the-test-pass-summary"></a>Otwórz podsumowanie przebiegu testu

1. Otwórz portal. 
2. Wybierz nazwę istniejącego rozwiązania, które zawiera wcześniej zaplanowane lub wykonywania testów.

    ![Zarządzanie przebiegów testowych](media/managetestpasses.png)

3. Wybierz **Zarządzaj** w **przebiegów testowych** panelu.
4. Wybierz przebieg testu, aby otworzyć podsumowanie przebiegu testu. Możesz przejrzeć nazwę testu, Data utworzenia, jest uruchamiany, czas trwania testu oraz wyników (zakończonych powodzeniem lub niepowodzeniem).
5. Wybierz pozycję [ **...  .** ].

### <a name="test-pass-summary"></a>Podsumowanie przebiegu testu

| Kolumna | Opis |
| --- | --- |
| Nazwa testu | Nazwa testu. Odwołuje się do liczby sprawdzania poprawności. |
| Utworzone | Godzina utworzenia przebiegu testu. |
| Uruchomiono | Razem, gdy ostatnie test był uruchomiony. |
| Czas trwania | Długość czasu, jaki zajęło przebiegu testu do uruchomienia. |
| Stan | Wynik (powodzenie lub niepowodzenie) do przekazywania rest. |
| Nazwa agenta | W pełni kwalifikowana nazwa domeny agenta. |
| Łączna liczba operacji | Całkowita liczba operacji, które podjęto w przebiegu testu. |
| Operacje przekazany | Liczba operacji, które pomyślnie w przebiegu testu. |
|  Operacje zakończone niepowodzeniem | Liczba operacji, które nie powiodło się. |

### <a name="group-columns-in-the-test-pass-summary"></a>Grupy kolumn w teście Przekaż podsumowanie

Można wybrać i przeciągnij kolumnę w nagłówku, aby utworzyć grupę w wartości kolumny.

## <a name="reschedule-a-test"></a>Zaplanuj ponownie test

1. [Otwórz podsumowanie przebiegu testu](#open-the-test-pass-summary).
2. Wybierz **zmienić termin egzaminu** ponownie zaplanować przebiegu testu.
3. Wprowadź hasło administratora chmury dla swojego wystąpienia usługi Azure Stack.
4. Wprowadź parametry połączenia magazynu diagnostyki, zdefiniowane przez użytkownika podczas konfigurowania konta.
5. Harmonogram testu.

## <a name="cancel-a-test"></a>Anuluj testu

1. [Otwórz podsumowanie przebiegu testu](#open-the-test-pass-summary).
2. Wybierz **anulować**.

## <a name="get-test-information"></a>Uzyskaj informacje o testach

1. [Otwórz podsumowanie przebiegu testu](#open-the-test-pass-summary).
2. Wybierz **służy do wyświetlania informacji** ponownie zaplanować przebiegu testu.

**Informacje o testach**

| Name (Nazwa) | Opis |
| -- | -- |
| Nazwa testu | Nazwa testu, na przykład OEM aktualizację dla usługi Azure Stack 1806 RC weryfikacji. |
| Wersja testowa | Wersja testu, na przykład 5.1.4.0. |
| Wydawca | Wydawca testu, takich jak Microsoft. |
| Kategoria | Kategoria testu, takie jak **funkcjonalnej** lub **niezawodność**. |
| Usługi docelowej | Usługi testowana, np. maszyn wirtualnych |
| Opis | Opis testu. |
| Szacowany czas trwania (w minutach) | Czas w minutach testu trwania. |
| Linki | Łącze do śledzenia problemu usługi GitHub. |

## <a name="get-test-parameters"></a>Pobieranie parametrów testu

1. [Otwórz podsumowanie przebiegu testu](#open-the-test-pass-summary).
2. Wybierz **wyświetlania parametrów** ponownie zaplanować przebiegu testu.

**Parametry**

| Name (Nazwa) | Opis |
| -- | -- |
| Nazwa testu | Nazwa testu, na przykład oemupdate1806test. |
| Wersja testowa | Wersja rest, na przykład 5.1.4.0. |
| Identyfikator wystąpienia testu | Określone wystąpienie testu, na przykład identyfikator GUID 20b20645-b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | Nazwa konta używana jako administrator chmury, na przykład **cloudadmin**. |
| DiagnosticsContainerName | Identyfikator kontenera diagnostyczne, na przykład 04dd3815-5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Pobierz operacje testu

1. [Otwórz podsumowanie przebiegu testu](#open-the-test-pass-summary).
2. Wybierz **wyświetlić operacje** ponownie zaplanować przebiegu testu. Zostanie otwarte okienko Podsumowanie operacji.

## <a name="get-test-logs"></a>Pobierz dzienniki testów

1. [Otwórz podsumowanie przebiegu testu](#open-the-test-pass-summary).
2. Wybierz **pobieranie dzienników** ponownie zaplanować przebiegu testu.  
    Plik zip o nazwie ReleaseYYYY-MM-DD.zip zawierający pliki do pobrania dzienników.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).