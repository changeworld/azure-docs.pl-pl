---
title: Zarządzanie aktualizacjami w usłudze Azure Stack — omówienie | Dokumentacja firmy Microsoft
description: Więcej informacji na temat rozwiązania update management dla usługi Azure Stack, zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281577"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Zarządzanie aktualizacjami w usłudze Azure Stack — omówienie

*Dotyczy Zintegrowane systemy usługi Azure Stack*

Pakiety aktualizacji firmy Microsoft dla zazwyczaj zintegrowane systemy usługi Azure Stack wersji wokół czwarty wtorek każdego miesiąca. Zadaj swoje producenta sprzętu (OEM) dotyczące procesu ich powiadomień określonych w taki sposób, aby zapewnić powiadomienia o aktualizacji dotrzeć do Twojej organizacji. Możesz również sprawdzić w tej bibliotece dokumentacji w obszarze **Przegląd** > **informacje o wersji** informacji o wersjach, które są aktywne pomocy technicznej. 

Wszystkie wersje aktualizacji oprogramowania firmy Microsoft jest zawarte w pakiecie jako pakiet jedną aktualizację. Jako operatorów usługi Azure Stack można zaimportować, instalacji i Monitoruj postęp instalacji tych aktualizacji dla pakietów z portalu administratora. 

Dostawcy OEM będzie również aktualizacje, takich jak aktualizacje oprogramowania układowego i sterowników. Gdy te aktualizacje są dostarczane jako oddzielne pakiety przez dostawcę, są one importowane, zainstalowane i zarządzane taki sam sposób jak pakiety aktualizacji firmy Microsoft.

Aby zapewnić systemu w ramach pomocy technicznej, należy dysponować zaktualizowany do poziomu określonej wersji w usłudze Azure Stack. Upewnij się, że przeglądu [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Pakiety aktualizacji usługi Azure Stack nie można zastosować do usługi Azure Stack Development Kit. Pakiety aktualizacji są przeznaczone dla systemów zintegrowanych. Aby uzyskać informacje, zobacz [ponownie wdrożyć ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Dostawca zasobów aktualizacji

Usługa Azure Stack obejmuje dostawcy zasobów aktualizacji, który obsługuje stosowanie aktualizacji oprogramowania firmy Microsoft. Ten dostawca sprawdza, czy aktualizacje zostały zastosowane na wszystkie hosty fizyczne, aplikacje usługi Service Fabric i środowisk uruchomieniowych oraz wszystkich maszyn wirtualnych infrastruktury i ich skojarzone usługi.

Jak zainstalować aktualizacje, można wyświetlić stan wysokiego poziomu jako elementy docelowe proces aktualizacji różnych podsystemów w usłudze Azure Stack (na przykład hostów fizycznych i maszyn wirtualnych infrastruktury).

## <a name="plan-for-updates"></a>Planowanie aktualizacji

Zdecydowanie zalecamy powiadomienie użytkowników dowolne operacje konserwacji oraz zaplanowanie konserwacji systemu windows podczas poza godzinami, jeśli jest to możliwe. Operacje konserwacji może mieć wpływ na obciążeniami dzierżawy i operacje w portalu.

- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również przejrzeć aktywne alerty i rozwiązywanie tych, które wymagają akcji.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Za pomocą kafelka aktualizacji do zarządzania aktualizacjami

Zarządzanie aktualizacjami z portalu administratora. Jako operatorów usługi Azure Stack umożliwia Aktualizuj Kafelek na pulpicie nawigacyjnym, aby:

- Wyświetl ważne informacje, takie jak bieżąca wersja.
- Zainstaluj aktualizacje i monitorować postęp.
- Przejrzyj historię aktualizacji dla wcześniej zainstalowanych aktualizacji.
- Wyświetl bieżąca wersja pakietu producenta OEM w chmurze
 
## <a name="determine-the-current-version"></a>Określić bieżącej wersji

Bieżąca wersja usługi Azure Stack można wyświetlić na kafelku aktualizacji. Aby otworzyć Kafelek:

1. Otwórz portal usługi Azure Stack administracji.
2. Wybierz **pulpit nawigacyjny**. W **aktualizacji** Kafelek bieżącej wersji znajduje się na liście. 

    ![Aktualizacje kafelków na domyślny pulpit nawigacyjny](./media/azure-stack-updates/image1.png)

    Na przykład na ekranie wersja jest 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Zainstaluj aktualizacje i monitorować postęp


1. Otwórz portal usługi Azure Stack administracji.
2. Wybierz **pulpit nawigacyjny**. Wybierz Kafelek aktualizacji.
3. Wybierz **teraz zaktualizować**.

    ![Aktualizacja usługi Azure Stack szczegóły przebiegu](media/azure-stack-updates/azure-stack-update-button.png)

4.  Można wyświetlić stan wysokiego poziomu, ponieważ proces aktualizacji, który wykonuje iterację przez różnych podsystemów w usłudze Azure Stack. Przykład podsystemów uwzględniane maszyny wirtualne z hostów fizycznych, Usługa Service Fabric, infrastruktury i usług, podaj portali administratora i użytkownika. Przez cały proces aktualizacji dostawca zasobów aktualizacji raporty dodatkowe informacje o aktualizacji, takich jak liczba kroków, które zakończyły się powodzeniem, a także numer w toku.

5. Wybierz **Pobierz pełne dzienniki** aktualizacji uruchom blok szczegóły, aby pobrać pełne dzienniki.

    ![Aktualizacja usługi Azure Stack szczegóły przebiegu](media/azure-stack-updates/update-run-details.png)

6. Po zakończeniu aktualizacji dostawca zasobów udostępnia **Powodzenie** potwierdzenie z informacją, że proces aktualizacji zostało ukończone i jak długo. Z tego miejsca możesz wyświetlić informacje o wszystkich aktualizacji, dostępne aktualizacje lub zainstalowanych aktualizacji przy użyciu filtru.

    ![Aktualizacja usługi Azure stosu Uruchom szczegóły powodzenia](media/azure-stack-updates/update-success.png)

   W przypadku niepowodzenia aktualizacji aktualizacji kafelków raportów **wymaga uwagi**. Użyj **Pobierz pełne dzienniki** można pobrać wysokiego poziomu stanu, w którym aktualizacji może zakończyć się niepowodzeniem. Zbieranie dzienników usługi Azure Stack ułatwia ułatwienia diagnostyki i rozwiązywania problemów.

## <a name="next-steps"></a>Kolejne kroki

- [Obsługa zasad z usługi Azure Stack](azure-stack-servicing-policy.md) 
- [Zarządzanie regionami w usłudze Azure Stack](azure-stack-region-management.md)
