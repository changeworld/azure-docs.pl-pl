---
title: Publikowanie zarządzanych aplikacji za pośrednictwem portalu
description: Pokazuje, jak używać witryny Azure Portal do tworzenia aplikacji zarządzanej platformy Azure, która jest przeznaczona dla członków organizacji.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651724"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikowanie aplikacji wykazu usług za pośrednictwem witryny Azure Portal

Za pomocą witryny Azure Portal można publikować [aplikacje zarządzane,](overview.md) które są przeznaczone dla członków organizacji. Na przykład dział IT może publikować aplikacje zarządzane zapewniające zgodność z normami obowiązującymi w organizacji. Takie aplikacje zarządzane są dostępne w katalogu usług, a nie w witrynie Azure Marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

Podczas publikowania aplikacji zarządzanej należy określić tożsamość do zarządzania zasobami. Zalecamy określenie grupy użytkowników usługi Azure Active Directory. Aby utworzyć grupę użytkowników usługi Azure Active Directory, zobacz [Tworzenie grupy i dodawanie członków usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Plik zip, który zawiera definicję aplikacji zarządzanej musi być dostępny za pośrednictwem identyfikatora URI. Zaleca się przekazanie pliku zip do obiektu blob magazynu. 

## <a name="create-managed-application-with-portal"></a>Tworzenie aplikacji zarządzanej za pomocą portalu

1. W lewym górnym rogu wybierz pozycję **+ Nowy**.

   ![Nowa usługa](./media/publish-portal/new.png)

1. Wyszukaj **katalog usług**.

1. W wynikach przewiń aż do **znalezienia definicji aplikacji zarządzanej w wykazie usług**. Wybierz ją.

   ![Wyszukiwanie definicji aplikacji zarządzanych](./media/publish-portal/select-managed-apps-definition.png)

1. Wybierz **pozycję Utwórz,** aby rozpocząć proces tworzenia definicji aplikacji zarządzanej.

   ![Tworzenie definicji aplikacji zarządzanej](./media/publish-portal/create-definition.png)

1. Podaj wartości nazwy, nazwy wyświetlanej, opisu, lokalizacji, subskrypcji i grupy zasobów. W przypadku identyfikatora URI pliku pakietu podaj ścieżkę do utworzonego pliku zip.

   ![Podaj wartości](./media/publish-portal/fill-application-values.png)

1. Po dojście do sekcji Poziom uwierzytelniania i blokady wybierz pozycję **Dodaj autoryzację**.

   ![Dodaj autoryzację](./media/publish-portal/add-authorization.png)

1. Wybierz grupę usługi Azure Active Directory, aby zarządzać zasobami, i wybierz **przycisk OK**.

   ![Dodaj grupę autoryzacji](./media/publish-portal/add-auth-group.png)

1. Po podaniu wszystkich wartości wybierz pozycję **Utwórz**.

   ![Tworzenie aplikacji zarządzanej](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* W przypadku przykładów aplikacji zarządzanych zobacz [Przykładowe projekty dla aplikacji zarządzanych platformy Azure.](sample-projects.md)
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).