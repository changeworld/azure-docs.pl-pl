---
title: Przechowywanie wersji aplikacji Blockchain - Azure Blockchain Workbench
description: Jak używać wersji aplikacji w usłudze Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323908"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Przechowywanie wersji aplikacji w usłudze Azure Blockchain Workbench Preview

Można tworzyć i używać wielu wersji aplikacji Azure Blockchain Workbench Preview. Jeśli wiele wersji tej samej aplikacji są przekazywane, historia wersji jest dostępna i użytkownicy mogą wybrać, która wersja, której chcą użyć.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie usługi Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [Wdrażanie programu Azure Blockchain Workbench,](deploy.md) aby uzyskać szczegółowe informacje na temat wdrażania
* Wdrożona aplikacja łańcucha bloków w usłudze Blockchain Workbench. Zobacz [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Dodawanie wersji aplikacji

Aby dodać nową wersję, prześlij nową konfigurację i inteligentne pliki umowy do Blockchain Workbench.

1. W przeglądarce internetowej przejdź na adres internetowy usługi Blockchain Workbench. Aby `https://{workbench URL}.azurewebsites.net/` uzyskać na przykład informacje na temat znajdowania adresu internetowego blockchain workbench, zobacz [Adres URL sieci Web Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Zaloguj się jako [administrator usługi Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Wybierz aplikację blockchain, którą chcesz zaktualizować za pomocą innej wersji.
4. Wybierz **pozycję Dodaj wersję**. Zostanie wyświetlone okienko **Dodaj wersję.**
5. Wybierz nową wersję konfiguracji umowy i pliki kodu umowy do przekazania. Zostanie wykonana automatyczna walidacja pliku konfiguracji. Napraw wszelkie błędy sprawdzania poprawności przed wdrożeniem aplikacji.
6. Wybierz **dodaj wersję,** aby dodać nową wersję aplikacji blockchain.

    ![Dodawanie nowej wersji](media/version-app/add-version.png)

Wdrożenie aplikacji blockchain może potrwać kilka minut. Po zakończeniu wdrażania odśwież stronę aplikacji. Wybierając aplikację i wybierając przycisk **Historia wersji,** wyświetla historię wersji aplikacji.

> [!IMPORTANT]
> Poprzednie wersje aplikacji są wyłączone. Można indywidualnie ponownie włączyć poprzednie wersje.
>
> Może być konieczne ponowne dodanie członków do ról aplikacji, jeśli wprowadzono zmiany w rolach aplikacji w nowej wersji.

## <a name="using-app-versions"></a>Korzystanie z wersji aplikacji

Domyślnie najnowsza włączona wersja aplikacji jest używana w Blockchain Workbench. Jeśli chcesz użyć poprzedniej wersji aplikacji, musisz najpierw wybrać wersję ze strony aplikacji.

1. W sekcji Aplikacji Blockchain Workbench zaznacz pole wyboru aplikacji, które zawiera kontrakt, którego chcesz użyć. Jeśli poprzednie wersje są włączone, przycisk historii wersji jest dostępny.
2. Wybierz przycisk **Historia wersji.**
3. W okienku historii wersji wybierz wersję aplikacji, wybierając łącze w kolumnie *Data modyfikacji.*

    ![Wybieranie poprzedniej wersji](media/version-app/use-version.png)

    Można tworzyć nowe kontrakty lub podejmować działania w poprzednich umowach wersji. Wersja aplikacji jest wyświetlana po nazwie aplikacji i wyświetlane jest ostrzeżenie dotyczące starszej wersji.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z systemem Azure Blockchain Workbench](troubleshooting.md)
