---
title: Publikowanie platformy Azure zarządzanych aplikacji za pośrednictwem portalu | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia platformy Azure portalu, aby utworzyć platformy Azure zarządzanych aplikacji, która jest przeznaczony dla członków organizacji.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 69d31a7199347574e8866b275ec17ba3997d80c2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikowanie aplikacji katalogu usług za pośrednictwem portalu Azure

Azure portal umożliwia publikowanie [zarządzane aplikacje](overview.md) które są przeznaczone do członków organizacji. Na przykład dział INFORMATYCZNY może opublikować zarządzanych aplikacji, które zapewnić zgodność ze standardami w organizacji. Te aplikacje zarządzane są dostępne za pośrednictwem katalogu usług, a nie witrynę Azure marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

Podczas publikowania aplikacji zarządzanej, możesz określić tożsamości do zarządzania zasobami. Firma Microsoft zaleca się, że możesz określić grupy użytkowników usługi Azure Active Directory. Aby utworzyć grupy użytkowników usługi Azure Active Directory, zobacz [Utwórz grupy i Dodaj elementy członkowskie w usłudze Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

Plik zip, który zawiera definicję aplikacji zarządzanej musi być dostępny za pomocą identyfikatora URI. Zaleca się przesłać plik zip do obiektu blob magazynu. 

## <a name="create-managed-application-with-portal"></a>Tworzenie aplikacji zarządzanych za pomocą portalu

1. W lewym górnym rogu wybierz **+ nowy**.

   ![Nowa usługa](./media/publish-portal/new.png)

1. Wyszukaj **katalogu usług**.

1. W wynikach, przewiń do pozycji **definicji aplikacji zarządzanych katalogu usługi**. Wybierz go.

   ![Wyszukaj definicje zarządzanej aplikacji](./media/publish-portal/select-managed-apps-definition.png)

1. Wybierz **Utwórz** do rozpoczęcia procesu tworzenia definicji zarządzanych aplikacji.

   ![Tworzenie definicji aplikacji zarządzanych](./media/publish-portal/create-definition.png)

1. Podaj wartości dla nazwy, wyświetlana nazwa, opis, lokalizacji, subskrypcji i grupy zasobów. Plik pakietu identyfikatora URI Podaj ścieżkę do pliku zip, który został utworzony.

   ![Podaj wartości](./media/publish-portal/fill-application-values.png)

1. Gdy pojawi się w sekcji uwierzytelnianie i poziom blokady, wybierz **dodać autoryzacji**.

   ![Dodaj autoryzacji](./media/publish-portal/add-authorization.png)

1. Wybierz grupę usługi Azure Active Directory do zarządzania zasobami i wybierz **OK**.

   ![Dodaj grupę autoryzacji](./media/publish-portal/add-auth-group.png)

1. Jeśli zostały podane wszystkie wartości, wybierz **Utwórz**.

   ![Tworzenie aplikacji zarządzanej](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Przykłady zarządzanej aplikacji można znaleźć [przykładowe projekty dla platformy Azure zarządzanych aplikacji](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).