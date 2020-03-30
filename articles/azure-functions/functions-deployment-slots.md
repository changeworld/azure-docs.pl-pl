---
title: Miejsca wdrażania usług Azure Functions
description: Dowiedz się, jak tworzyć i używać gniazd wdrażania za pomocą usługi Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769221"
---
# <a name="azure-functions-deployment-slots"></a>Miejsca wdrażania usług Azure Functions

Gniazda wdrażania usługi Azure Functions umożliwiają aplikacji funkcji uruchamianie różnych wystąpień o nazwie "sloty". Szczeliny są różne środowiska udostępniane za pośrednictwem publicznie dostępnego punktu końcowego. Jedno wystąpienie aplikacji jest zawsze mapowane do gniazda produkcyjnego i można zamienić wystąpienia przypisane do gniazda na żądanie. Aplikacje funkcyjne działające w ramach planu usługi aplikacji mogą mieć wiele gniazd, podczas gdy w ramach planu zużycia dozwolone jest tylko jedno gniazdo.

Poniżej przedstawiono sposób, w jaki wymiana gniazd wpływa na funkcje:

- Przekierowanie ruchu jest bezproblemowe; żadne żądania nie są odrzucane z powodu wymiany.
- Jeśli funkcja jest uruchomiona podczas wymiany, wykonywanie jest kontynuowane, a kolejne wyzwalacze są kierowane do zamienionego wystąpienia aplikacji.

> [!NOTE]
> Gniazda nie są obecnie dostępne dla planu zużycia linuksa.

## <a name="why-use-slots"></a>Dlaczego warto korzystać z slotów?

Istnieje wiele zalet korzystania z gniazd wdrażania. W poniższych scenariuszach opisano typowe zastosowania dla slotów:

- **Różne środowiska do różnych celów:** korzystanie z różnych gniazd daje możliwość rozróżniania wystąpień aplikacji przed zamianą na produkcję lub miejsce przejściowe.
- **Przedogrzewanie:** Wdrożenie do gniazda zamiast bezpośrednio do produkcji pozwala aplikacji na rozgrzanie się przed rozpoczęciem transmisji na żywo. Ponadto przy użyciu gniazd zmniejsza opóźnienia dla obciążeń wyzwalanych przez PROTOKÓŁ HTTP. Wystąpienia są rozgrzewane przed wdrożeniem, co zmniejsza zimny start dla nowo wdrożonych funkcji.
- **Łatwe rezerwy:** Po zamianie z produkcją gniazdo z wcześniej zorganizowaną aplikacją ma teraz poprzednią aplikację produkcyjną. Jeśli zmiany zamienione w gniazdo produkcyjne nie są takie, jak się spodziewasz, możesz natychmiast wycofać zamianę, aby odzyskać "ostatnie znane dobre wystąpienie".

## <a name="swap-operations"></a>Operacje wymiany

Podczas wymiany jedno gniazdo jest uważane za źródło, a drugie miejsce docelowe. Gniazdo źródłowe ma wystąpienie aplikacji, która jest stosowana do gniazda docelowego. Następujące kroki zapewniają, że gniazdo docelowe nie doświadcza przestojów podczas wymiany:

1. **Zastosuj ustawienia:** Ustawienia z gniazda docelowego są stosowane do wszystkich wystąpień gniazda źródłowego. Na przykład ustawienia produkcji są stosowane do wystąpienia przemieszczania. Zastosowane ustawienia obejmują następujące kategorie:
    - [Ustawienia](#manage-settings) aplikacji specyficzne dla gniazda i parametry połączenia (jeśli dotyczy)
    - [Ustawienia ciągłego wdrażania](../app-service/deploy-continuous-deployment.md) (jeśli jest włączona)
    - Ustawienia [uwierzytelniania usługi app service](../app-service/overview-authentication-authorization.md) (jeśli jest włączona)

1. **Poczekaj na ponowne uruchomienie i dostępność:** Zamiana czeka na każde wystąpienie w gnieździe źródłowym, aby zakończyć jego ponowne uruchomienie i być dostępne dla żądań. Jeśli każde wystąpienie nie powiedzie się ponownie, operacja wymiany przywraca wszystkie zmiany do gniazda źródłowego i zatrzymuje operację.

1. **Aktualizacja routingu:** Jeśli wszystkie wystąpienia na gnieździe źródłowym są rozgrzane pomyślnie, dwa gniazda zakończyć swap przez przełączanie reguł routingu. Po tym kroku gniazdo docelowe (na przykład gniazdo produkcyjne) ma aplikację, która jest wcześniej rozgrzana w gnieździe źródłowym.

1. **Powtarzanie operacji:** Teraz, gdy gniazdo źródłowe ma wcześniej aplikację pre-swap w gnieździe docelowym, wykonaj tę samą operację, stosując wszystkie ustawienia i ponownie uruchamiając wystąpienia gniazda źródłowego.

Należy pamiętać o następujących kwestiach:

- W dowolnym momencie operacji wymiany inicjowanie zamienionych aplikacji odbywa się na źródle gniazda. Gniazdo docelowe pozostaje w trybie online, podczas gdy gniazdo źródłowe jest przygotowywane, niezależnie od tego, czy zamiana zakończy się powodzeniem, czy niepowodzeniem.

- Aby zamienić miejsce przejściowe z miejscem produkcyjnym, upewnij się, że gniazdo produkcyjne jest *zawsze* miejscem docelowym. W ten sposób operacja wymiany nie wpływa na aplikację produkcyjną.

- Ustawienia związane ze źródłami zdarzeń i powiązaniami muszą być skonfigurowane jako [ustawienia gniazda wdrożenia](#manage-settings) przed *zainicjowaniem wymiany*. Oznaczanie ich jako "lepkich" z wyprzedzeniem zapewnia zdarzenia i wyjścia są kierowane do właściwego wystąpienia.

## <a name="manage-settings"></a>Zarządzanie ustawieniami

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Tworzenie ustawienia wdrażania

Ustawienia można oznaczyć jako ustawienie wdrożenia, które sprawia, że jest ono "lepkie". Ustawienie przyklejone nie zamienia się z wystąpieniem aplikacji.

Jeśli tworzysz ustawienie wdrożenia w jednym gnieździe, upewnij się, że utworzysz to samo ustawienie z unikatową wartością w dowolnym innym gnieździe zaangażowanym w zamian. W ten sposób, podczas gdy wartość ustawienia nie zmienia się, nazwy ustawień pozostają spójne między gniazdami. Ta spójność nazwy gwarantuje, że kod nie próbuje uzyskać dostępu do ustawienia, które jest zdefiniowane w jednym gnieździe, ale nie innym.

Aby utworzyć ustawienie wdrażania, należy wykonać następujące czynności:

- Przejdź do *slotów* w aplikacji funkcji
- Kliknij nazwę gniazda
- W obszarze *Funkcje platformy > ustawienia ogólne*kliknij **konfigurację**
- Kliknij nazwę ustawienia, które chcesz przykleić do bieżącego gniazda
- Kliknij pole wyboru **ustawienia gniazda wdrożenia**
- Kliknij **przycisk OK**
- Gdy ustawienie bloku zniknie, kliknij przycisk **Zapisz,** aby zachować zmiany

![Ustawienie gniazda wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>wdrażania

Szczeliny są puste podczas tworzenia gniazda. Można użyć dowolnej z [obsługiwanych technologii wdrażania,](./functions-deployment-technologies.md) aby wdrożyć aplikację na gnieździe.

## <a name="scaling"></a>Skalowanie

Wszystkie szczeliny są skalowane do takiej samej liczby pracowników jak miejsce produkcyjne.

- W przypadku planów zużycia gniazdo jest skalowane w miarę skalowania aplikacji funkcji.
- W przypadku planów usługi app service aplikacja jest skalowana do stałej liczby pracowników. Gniazda działają na tej samej liczbie pracowników, co plan aplikacji.

## <a name="add-a-slot"></a>Dodawanie miejsca

Możesz dodać gniazdo za pośrednictwem [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) lub portalu. Poniższe kroki pokazują, jak utworzyć nowe gniazdo w portalu:

1. Przejdź do aplikacji funkcji i kliknij **znak plus** obok *slotów*.

    ![Dodawanie gniazda wdrażania usługi Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Wprowadź nazwę w polach tekstowych i naciśnij przycisk **Utwórz.**

    ![Nazwij gniazdo wdrażania usług Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Wymienianie gniazd

Sloty można wymieniać za pośrednictwem [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) lub portalu. Poniższe kroki pokazują, jak zamienić szczeliny w portalu:

1. Przejdź do aplikacji funkcji
1. Kliknij nazwę gniazda źródłowego, które chcesz zamienić
1. Na karcie *Przegląd* kliknij gniazdo ![wdrażania swap za pomocą przycisku **Swap** Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Sprawdź ustawienia konfiguracji dla swojego swapu i kliknij pozycję **Zamień** ![gniazdo wdrażania usługi Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Operacja może potrwać chwilę podczas wykonywania operacji wymiany.

## <a name="roll-back-a-swap"></a>Wycofywanie wymiany

Jeśli zamiana powoduje błąd lub po prostu chcesz "cofnąć" swap, można przywrócić do stanu początkowego. Aby powrócić do stanu wstępnie zamienione, wykonaj inną zamianę, aby odwrócić zamianę.

## <a name="remove-a-slot"></a>Wyjmij szczelinę

Gniazdo można usunąć za pośrednictwem [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) lub portalu. Poniższe kroki pokazują, jak usunąć gniazdo w portalu:

1. Przejdź do aplikacji funkcji Omówienie

1. Kliknij przycisk **Usuń**

    ![Dodawanie gniazda wdrażania usługi Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatyzacja zarządzania gniazdami

Za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)można zautomatyzować następujące akcje dla gniazda:

- [Utworzyć](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [Usunąć](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Wymiany](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatyczna zamiana](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Zmienianie planu usługi aplikacji

W przypadku aplikacji funkcji uruchomionej w ramach planu usługi app service można zmienić podstawowy plan usługi app service dla gniazda.

> [!NOTE]
> Nie można zmienić planu usługi aplikacji gniazda w ramach planu zużycia.

Aby zmienić plan usługi app service, należy wykonać następujące czynności:

1. Przechodzenie do gniazda

1. W obszarze *Funkcje platformy*kliknij pozycję **Wszystkie ustawienia**

    ![Zmienianie planu usługi aplikacji](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Kliknij **plan usługi aplikacji**

1. Wybieranie nowego planu usługi app service lub tworzenie nowego planu

1. Kliknij **przycisk OK**

    ![Zmienianie planu usługi aplikacji](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Ograniczenia

Miejsca wdrażania usług Azure Functions mają następujące ograniczenia:

- Liczba gniazd dostępnych dla aplikacji zależy od planu. Plan zużycia jest dozwolony tylko jedno gniazdo wdrażania. Dodatkowe gniazda są dostępne dla aplikacji działających w ramach planu usługi app service.
- Zamiana gniazda powoduje zresetowanie klawiszy `AzureWebJobsSecretStorageType` dla aplikacji, które mają ustawienie aplikacji równe `files`.
- Gniazda nie są dostępne dla planu zużycia linuksa.

## <a name="support-levels"></a>Poziomy pomocy technicznej

Istnieją dwa poziomy obsługi miejsc wdrażania:

- **Ogólna dostępność (GA)**: W pełni obsługiwane i zatwierdzone do użytku produkcyjnego.
- **Wersja zapoznawcza**: Nie jest jeszcze obsługiwana, ale oczekuje się, że osiągnie stan GA w przyszłości.

| System operacyjny/plan hostingowy           | Poziom wsparcia     |
| ------------------------- | -------------------- |
| Zużycie systemu Windows       | Ogólna dostępność |
| Windows Premium           | Ogólna dostępność  |
| Dedykowany system Windows         | Ogólna dostępność |
| Zużycie Linuksa         | Nieobsługiwane          |
| Linux Premium             | Ogólna dostępność  |
| Linux dedykowany           | Ogólna dostępność |

## <a name="next-steps"></a>Następne kroki

- [Technologie wdrażania w usłudze Azure Functions](./functions-deployment-technologies.md)
