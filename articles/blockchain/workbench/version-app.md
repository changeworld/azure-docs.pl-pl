---
title: Przechowywanie wersji aplikacji łańcucha bloków — Azure łańcucha bloków Workbench
description: Jak korzystać z wersji aplikacji w usłudze Azure łańcucha bloków Workbench w wersji zapoznawczej.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 35d9be219d1473ac56ca501f80fca96a265e814f
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284830"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Wersja aplikacji Azure łańcucha bloków Workbench Preview

Możesz tworzyć i używać wielu wersji aplikacji Azure łańcucha bloków Workbench Preview. Jeśli przekazano wiele wersji tej samej aplikacji, dostępna jest historia wersji, a użytkownicy mogą wybrać wersję, która ma być używana.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie usługi Blockchain Workbench. Aby uzyskać szczegółowe informacje na temat wdrożenia, zobacz [Wdrożenie usługi Azure Blockchain Workbench](deploy.md)
* Wdrożona aplikacja łańcucha bloków w usłudze Blockchain Workbench. Zobacz [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Dodawanie wersji aplikacji

Aby dodać nową wersję, Przekaż nową konfigurację i pliki kontraktów inteligentnych do łańcucha bloków Workbench.

1. W przeglądarce internetowej przejdź na adres internetowy usługi Blockchain Workbench. Na przykład `https://{workbench URL}.azurewebsites.net/`, aby uzyskać informacje na temat wyszukiwania adresu sieci Web łańcucha bloków Workbench, zobacz [łańcucha bloków Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Zaloguj się jako [administrator usługi Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Wybierz aplikację łańcucha bloków, którą chcesz zaktualizować przy użyciu innej wersji.
4. Wybierz pozycję **Dodaj wersję**. Zostanie wyświetlone okienko **Dodawanie wersji** .
5. Wybierz konfigurację kontraktu wersji i pliki kodu kontraktu do przekazania. Zostanie wykonana automatyczna walidacja pliku konfiguracji. Popraw błędy walidacji przed wdrożeniem aplikacji.
6. Wybierz pozycję **Dodaj wersję** , aby dodać nową wersję aplikacji łańcucha bloków.

    ![Dodawanie nowej wersji](media/version-app/add-version.png)

Wdrożenie aplikacji łańcucha bloków może potrwać kilka minut. Po zakończeniu wdrażania Odśwież stronę aplikacji. Po wybraniu aplikacji i wybraniu przycisku **historia wersji** zostanie wyświetlona historia wersji aplikacji.

> [!IMPORTANT]
> Poprzednie wersje aplikacji są wyłączone. Można indywidualnie ponownie włączyć poprzednią wersję.
>
> Może być konieczne ponowne dodanie członków do ról aplikacji, jeśli wprowadzono zmiany w rolach aplikacji w nowej wersji.

## <a name="using-app-versions"></a>Korzystanie z wersji aplikacji

Domyślnie Najnowsza włączona wersja aplikacji jest używana w programie łańcucha bloków Workbench. Jeśli chcesz użyć poprzedniej wersji aplikacji, musisz najpierw wybrać wersję ze strony aplikacji.

1. W sekcji łańcucha bloków Workbench Application (aplikacje) zaznacz pole wyboru aplikacji zawierające kontrakt, którego chcesz użyć. Jeśli poprzednia wersja jest włączona, dostępny jest przycisk historia wersji.
2. Wybierz przycisk **historia wersji** .
3. W okienku historia wersji wybierz wersję aplikacji, wybierając łącze w kolumnie *Data modyfikacji* .

    ![Wybierz poprzednią wersję](media/version-app/use-version.png)

    Możesz tworzyć nowe kontrakty lub podejmować działania związane z poprzednimi wersjami umów. Wersja aplikacji zostanie wyświetlona po wybraniu nazwy aplikacji, a wyświetlane jest ostrzeżenie o starszej wersji.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z usługą Azure łańcucha bloków Workbench](troubleshooting.md)
