---
title: Eksportować i usuwać dane z usługi Machine Learning Studio — Azure | Dokumentacja firmy Microsoft
description: W ramach produktu — dane przechowywane w usłudze Azure Machine Learning Studio jest dostępna dla eksportowania i usuwania za pośrednictwem witryny Azure portal, a także za pośrednictwem uwierzytelnionych interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu Azure ochrony prywatności. W tym artykule przedstawiono, jak.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 2ebd777a9723732de6ebbdf07020802190cb4b61
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969286"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Eksportowanie i usunąć dane użytkownika w produkcie z usługi Machine Learning Studio

Można usunąć lub Eksportuj dane w ramach produktu przechowywane przez usługi Azure Machine Learning Studio za pomocą witryny Azure portal, interfejsu Studio, środowiska PowerShell i uwierzytelniony interfejsów API REST. W tym artykule wyjaśniono sposób. 

Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Jakiego rodzaju dane użytkownika są zbierane w Studio?

Dane użytkownika tej usługi składa się z informacji na temat użytkowników uprawnionych do dostępu do obszarów roboczych i rejestruje dane telemetryczne interakcji użytkowników z usługą.

Istnieją dwa rodzaje danych użytkownika w usłudze Machine Learning Studio:
- **Dane konto osobiste:** identyfikatorów kont i adresy e-mail skojarzonych z kontem.
- **Dane klienta:** danych przekazanych do przeanalizowania.

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

## <a name="delete-workspace-data-in-studio"></a>Usuwanie danych obszaru roboczego w programie Studio 

### <a name="delete-individual-assets"></a>Usuwać poszczególne zasoby

Użytkownicy mogą usuwać zasoby w obszarze roboczym, wybierając je, a następnie wybierając przycisk Usuń.

![Usuń zasoby w usłudze Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Usuń całego obszaru roboczego

Użytkownicy mogą również usuwać ich całego obszaru roboczego:
- Płatne obszaru roboczego: Usuń za pośrednictwem witryny Azure portal.
- Bezpłatny obszar roboczy: Użyj przycisku usuwania w **ustawienia** okienka.

![Usuń bezpłatny obszar roboczy w usłudze Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Eksportowanie danych Studio za pomocą programu PowerShell
Aby wyeksportować wszystkie informacje do formatu przenośnego z usługi Azure Machine Learning Studio za pomocą poleceń za pomocą programu PowerShell. Aby uzyskać informacje, zobacz [modułu programu PowerShell dla usługi Azure Machine Learning](powershell-module.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Dokumentacja usług sieci web i zobowiązania plan rozliczeniowy, w temacie [odwołania API REST usługi Azure Machine Learning](https://docs.microsoft.com/rest/api/machinelearning/). 
