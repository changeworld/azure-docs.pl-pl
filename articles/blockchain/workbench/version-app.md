---
title: Przechowywanie wersji aplikacji łańcucha bloków, w aplikacji Azure Blockchain Workbench
description: Jak używać wersji aplikacji w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 63f18e3ee316b9791bb62bfcd20c07a30cbebb5e
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579219"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Wersji aplikacji w usłudze Azure Blockchain Workbench

Można tworzyć i używać wielu wersji aplikacji Azure Blockchain Workbench. Jeśli są przekazywane wielu wersji tej samej aplikacji, historii wersji jest dostępna i użytkownicy mogą wybrać wersję, która mają być użyte.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie usługi Blockchain Workbench. Aby uzyskać szczegółowe informacje na temat wdrożenia, zobacz [Wdrożenie usługi Azure Blockchain Workbench](deploy.md)
* Wdrożona aplikacja łańcucha bloków w usłudze Blockchain Workbench. Zobacz [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Dodaj wersję aplikacji

Aby dodać nową wersję, Przekaż nową konfigurację i pliki kontraktu inteligentnych do aplikacji Blockchain Workbench.

1. W przeglądarce internetowej przejdź na adres internetowy usługi Blockchain Workbench. Na przykład `https://{workbench URL}.azurewebsites.net/` uzyskać informacji na temat znajdowania Twojej aplikacji Blockchain Workbench adres sieci web, zobacz [Blockchain Workbench — adres URL sieci Web](deploy.md#blockchain-workbench-web-url)
2. Zaloguj się jako [administrator usługi Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Wybierz aplikację łańcucha bloków, które chcesz zaktualizować przy użyciu innej wersji.
4. Wybierz **wersji Dodaj**. **Wersji Dodaj** zostanie wyświetlone okienko.
5. Wybierz nową konfigurację wersja umowy i umowy plików kodu do przekazania. Zostanie wykonana automatyczna walidacja pliku konfiguracji. Napraw wszystkie błędy weryfikacji przed wdrożeniem aplikacji.
6. Wybierz **wersji Dodaj** można dodać nowej wersji aplikacji łańcucha bloków.

    ![Dodawanie nowej wersji](media/version-app/add-version.png)

Wdrażanie aplikacji łańcucha bloków może potrwać kilka minut. Po zakończeniu wdrożenia Odśwież stronę aplikacji. Wybieranie aplikacji i wybierając polecenie **historię wersji** przycisk, wyświetla historię wersji aplikacji.

> [!IMPORTANT]
> Poprzednie wersje aplikacji są wyłączone. Oddzielnie można ponownie włączyć wcześniejszą wersję.
>
> Konieczne może być ponownie dodać członków do ról aplikacji, jeśli wprowadzono zmiany do ról aplikacji w nowej wersji.

## <a name="using-app-versions"></a>Za pomocą wersji aplikacji

Domyślnie włączone najnowszej wersji aplikacji jest używany w aplikacji Blockchain Workbench. Jeśli chcesz użyć poprzedniej wersji aplikacji, musisz najpierw wybrać wersję ze strony aplikacji.

1. W sekcji aplikacji Blockchain Workbench zaznacz pole wyboru aplikacji, który zawiera kontrakt, który chcesz użyć. Jeśli poprzednie wersje są włączone, przycisk Historia wersji jest dostępna.
2. Wybierz **historię wersji** przycisku.
3. W okienku historii wersji, wybierz wersję aplikacji, wybierając łącze w *Data modyfikacji* kolumny.

    ![Wybierz poprzednią wersję](media/version-app/use-version.png)

    Można utworzyć nowych umów lub podjąć działania w poprzedniej wersji umów. Wersja aplikacji jest wyświetlany po nazwie aplikacji i zostanie wyświetlone ostrzeżenie dotyczące starszych wersji.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z platformy Azure Blockchain Workbench](troubleshooting.md)
