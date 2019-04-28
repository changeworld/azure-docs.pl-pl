---
title: Wyeksportuj i usuń swoje dane
titleSuffix: Azure Machine Learning Studio
description: W ramach produktu — dane przechowywane w usłudze Azure Machine Learning Studio jest dostępna dla eksportowania i usuwania za pośrednictwem witryny Azure portal, a także za pośrednictwem uwierzytelnionych interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu Azure ochrony prywatności. W tym artykule przedstawiono, jak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 827714fea9618724ef058e1f76dc099f692482bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750118"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>Eksportowanie i usunąć dane użytkownika w produkcie z usługi Azure Machine Learning Studio

Można usunąć lub Eksportuj dane w ramach produktu przechowywane przez usługi Azure Machine Learning Studio za pomocą witryny Azure portal, interfejsu Studio, środowiska PowerShell i uwierzytelniony interfejsów API REST. W tym artykule wyjaśniono sposób. 

Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Jakiego rodzaju dane użytkownika są zbierane w Studio?

Dane użytkownika tej usługi składa się z informacji na temat użytkowników uprawnionych do dostępu do obszarów roboczych i rejestruje dane telemetryczne interakcji użytkowników z usługą.

Istnieją dwa rodzaje danych użytkownika w usłudze Machine Learning Studio:
- **Dane konto osobiste:** Identyfikatory konta i adresy skojarzonych z kontem e-mail.
- **Dane klienta:** Dane, który został przekazany do przeanalizowania.

## <a name="studio-account-types-and-how-data-is-stored"></a>Typy kont Studio i sposobu przechowywania danych

Istnieją trzy rodzaje kont w usłudze Machine Learning Studio. Rodzaj konta określa sposób przechowywania danych i jak usunąć lub wyeksportować je.

- A **obszaru roboczego gościa** jest bezpłatna, anonimowe konta. Możesz zarejestrować się bez podawania poświadczeń, takich jak adres e-mail lub hasło.
    -  Dane są przeczyszczane po wygaśnięciu obszaru roboczego gościa.
    - Użytkownicy-goście, można wyeksportować dane klientów za pośrednictwem pakietu interfejsu użytkownika, interfejsów API REST lub programu PowerShell.
- A **obszaru roboczego warstwy bezpłatna** jest utworzenie bezpłatnego konta, zaloguj się z firmą Microsoft poświadczenia — adres e-mail i hasło konta.
    - Można eksportować i usuwać dane osobiste i klienta, które podlegają żądań praw podmiotu danych.
    - Możesz wyeksportować dane klientów za pośrednictwem pakietu interfejsu użytkownika, interfejsów API REST lub programu PowerShell.
    - Bezpłatnie obszarów roboczych nie używa konta usługi Azure AD, dane telemetryczne można wyeksportować za pomocą portalu ochrony prywatności.
    - Jeśli usuniesz obszar roboczy, możesz usunąć wszystkich danych osobowych klientów.
- A **obszar roboczy warstwy standardowa** jest płatne konto, możesz uzyskać dostęp przy użyciu poświadczeń logowania.
    - Można eksportować i usuwać dane osobiste i klienta, które podlegają żądania praw podmiotu danych.
    - Dostęp do danych za pośrednictwem portalu Azure prywatności
    - Możesz wyeksportować dane osobiste i klientów za pośrednictwem pakietu interfejsu użytkownika, interfejsów API REST lub programu PowerShell
    - Można usunąć danych w witrynie Azure portal.

## <a name="delete"></a>Usuwanie danych obszaru roboczego w programie Studio 

### <a name="delete-individual-assets"></a>Usuwać poszczególne zasoby

Użytkownicy mogą usuwać zasoby w obszarze roboczym, wybierając je, a następnie wybierając przycisk Usuń.

![Usuń zasoby w usłudze Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Usuń całego obszaru roboczego

Użytkownicy mogą również usuwać ich całego obszaru roboczego:
- Płatne obszar roboczy: Usuń za pośrednictwem witryny Azure portal.
- Bezpłatny obszar roboczy: Użyj przycisku usuwania w **ustawienia** okienka.

![Usuń bezpłatny obszar roboczy w usłudze Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Eksportowanie danych Studio za pomocą programu PowerShell
Aby wyeksportować wszystkie informacje do formatu przenośnego z usługi Azure Machine Learning Studio za pomocą poleceń za pomocą programu PowerShell. Aby uzyskać informacje, zobacz [modułu programu PowerShell dla usługi Azure Machine Learning Studio](powershell-module.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Dokumentacja usług sieci web i zobowiązania plan rozliczeniowy, w temacie [odwołania API REST usługi Azure Machine Learning Studio](https://docs.microsoft.com/rest/api/machinelearning/). 
