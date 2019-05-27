---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148982"
---
## <a name="prerequisites"></a>Wymagania wstępne

### <a name="get-sample-code"></a>Pobieranie przykładowego kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](../articles/container-registry/container-registry-tutorial-quick-task.md) oraz że przykładowe repozytorium zostało rozwidlone i sklonowane. Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites).

### <a name="container-registry"></a>Rejestr kontenerów

Aby ukończyć ten samouczek, w Twojej subskrypcji platformy Azure musisz posiadać rejestr kontenerów platformy Azure. Jeśli potrzebujesz rejestru, zobacz [poprzedni samouczek](../articles/container-registry/container-registry-tutorial-quick-task.md) lub podręcznik [Szybki start: tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Tworzenie osobistego tokenu dostępu GitHub

Aby wyzwolić zadanie na zatwierdzenia do repozytorium Git, rejestru Azure container Registry zadania wymagają osobistego tokenu dostępu (PAT), aby uzyskać dostępu do repozytorium. Jeśli jeszcze nie masz osobisty token dostępu, wykonaj następujące kroki, aby wygenerować w witrynie GitHub:

1. Przejdź do strony tworzenia tokenu PAT w witrynie GitHub pod adresem https://github.com/settings/tokens/new
1. Wprowadź krótki **opis** dla tokenu, na przykład „Przykładowe zadanie ACR Tasks”
1. W obszarze **repo** zaznacz pozycje **repo:status** i **public_repo**

   ![Zrzut ekranu strony generowania osobistego tokenu dostępu w usłudze GitHub][build-task-01-new-token]

1. Wybierz przycisk **Generate token** (Generuj token) (może zostać wyświetlony monit o potwierdzenie hasła)
1. Skopiuj i zapisz wygenerowany token w **bezpiecznej lokalizacji** (użyjesz tego tokenu podczas definiowania zadania w następnej sekcji)

   ![Zrzut ekranu przedstawiający wygenerowany osobisty token dostępu w usłudze GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
