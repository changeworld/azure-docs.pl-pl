---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885127"
---
1. Aby utworzyć nowy magazyn konfiguracji aplikacji, najpierw zaloguj się w witrynie [Azure Portal](https://aka.ms/azconfig/portal). W lewej górnej części strony kliknij polecenie **+ Utwórz zasób**. W polu tekstowym **Wyszukaj w witrynie Marketplace** wpisz ciąg **App Configuration** i naciśnij klawisz **Enter**.

    ![Wyszukiwanie usługi App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Kliknij pozycję **App Configuration** w wynikach wyszukiwania, a następnie kliknij pozycję **Utwórz**.

3. Na stronie **App Configuration** > **Utwórz** wprowadź następujące ustawienia:

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź unikatową nazwę zasobu magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 1 do 63 znaków i może zawierać tylko cyfry, litery i znak `-`. Na początku ani na końcu nazwy nie może występować znak `-`, a następujące po sobie znaki `-` nie są prawidłowe.  |
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli konto ma tylko jedną subskrypcję, zostanie ona automatycznie wybrana i lista rozwijana **Subskrypcja** nie będzie wyświetlana. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub utwórz grupę zasobów dla zasobu magazynu konfiguracji aplikacji. Ta grupa jest przydatna do organizowania wielu zasobów, umożliwiając ich jednoczesne usunięcie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany magazyn konfiguracji aplikacji. Aby uzyskać najlepszą wydajność, zalecamy utworzenie zasobu w tym samym regionie, co inne składniki aplikacji. |

    ![Tworzenie zasobu magazynu konfiguracji aplikacji](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Kliknij pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

5. Po zakończeniu wdrażania kliknij kolejno pozycje **Ustawienia** > **Klucze dostępu**. Zanotuj parametry połączenia dla podstawowego klucza tylko do odczytu lub podstawowego klucza do odczytu i zapisu. Użyjesz ich później do skonfigurowania aplikacji pod kątem komunikowania się z nowo utworzonym magazynem konfiguracji aplikacji.

6. Kliknij pozycje **Eksplorator kluczy/wartości** i **+ Utwórz**, aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | white (biały) |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | black (czarny) |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Pola **Etykieta** i **Typ zawartości** póki co pozostaną puste.
