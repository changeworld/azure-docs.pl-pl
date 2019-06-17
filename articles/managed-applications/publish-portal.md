---
title: Publikowanie platformy Azure zarządzanych aplikacji za pomocą portalu | Dokumentacja firmy Microsoft
description: Pokazuje, jak używać platformy Azure w portalu, aby utworzyć platformy Azure zarządzanych aplikacji, która jest przeznaczona dla członków Twojej organizacji.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: f27d30d4709fbf373c8458629d0c8c5af4333acf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61297016"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Opublikuj aplikację katalogu usług za pośrednictwem witryny Azure portal

Witryna Azure portal umożliwia publikowanie [zarządzanych aplikacji](overview.md) , są przeznaczone dla członków Twojej organizacji. Na przykład dział IT może publikować aplikacje zarządzane zapewniające zgodność z normami obowiązującymi w organizacji. Takie aplikacje zarządzane są dostępne w katalogu usług, a nie w witrynie Azure Marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

Podczas publikowania aplikacji zarządzanej, możesz określić tożsamość do zarządzania zasobami. Zaleca się, że określić grupę użytkowników usługi Azure Active Directory. Aby utworzyć grupę użytkowników usługi Azure Active Directory, zobacz [tworzyć grupy i dodawać członków w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Plik zip, który zawiera definicję aplikacji zarządzanej musi być dostępny za pośrednictwem identyfikatora URI. Zaleca się, Przekaż plik zip do magazynu obiektów blob. 

## <a name="create-managed-application-with-portal"></a>Tworzenie aplikacji zarządzanych za pomocą portalu

1. W lewym górnym rogu wybierz **+ nowy**.

   ![Nowa usługa](./media/publish-portal/new.png)

1. Wyszukaj **katalogu usług**.

1. W wynikach, przewiń do **definicji aplikacji zarządzanych katalogu usług**. Wybierz go.

   ![Wyszukiwanie definicji aplikacji zarządzanej](./media/publish-portal/select-managed-apps-definition.png)

1. Wybierz **Utwórz** można uruchomić proces tworzenia definicji aplikacji zarządzanej.

   ![Tworzenie definicji aplikacji zarządzanej](./media/publish-portal/create-definition.png)

1. Podaj wartości dla nazwy, nazwa wyświetlana, opis, lokalizacji, subskrypcji i grupy zasobów. Identyfikator URI z pliku pakietu podaj ścieżkę do pliku zip, który został utworzony.

   ![Podaj wartości](./media/publish-portal/fill-application-values.png)

1. Gdy pojawi się w sekcji uwierzytelnianie i poziom blokady, wybierz **Dodaj autoryzacji**.

   ![Dodaj autoryzację](./media/publish-portal/add-authorization.png)

1. Wybierz grupę usługi Azure Active Directory do zarządzania zasobami, a następnie wybierz pozycję **OK**.

   ![Dodaj grupę autoryzacji](./media/publish-portal/add-auth-group.png)

1. Po podaniu wszystkich wartości, wybierz **Utwórz**.

   ![Tworzenie aplikacji zarządzanej](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Przykłady aplikacji zarządzanych, zobacz [przykładowych projektów dla platformy Azure zarządzane aplikacje](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).