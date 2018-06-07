---
title: Eksportowanie i usuwać dane z usługi Machine Learning Studio - Azure | Dokumentacja firmy Microsoft
description: W produkcie dane przechowywane przez usługi Azure Machine Learning Studio jest dostępna dla eksportu i usunięcia za pośrednictwem portalu Azure, a także za pośrednictwem uwierzytelnionego interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. W tym artykule opisano sposób.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 6317d4baba5775c1e5a4fda66de80dd8299d8fed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655611"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Eksportowanie i usunąć dane użytkownika w produktu z usługi Machine Learning Studio

Można usunąć lub Eksportuj produktu dane przechowywane w usłudze Azure Machine Learning Studio za pomocą portalu Azure, interfejsu Studio, programu PowerShell i uwierzytelniony interfejsów API REST. W tym artykule wyjaśniono sposób. 

Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Jakie typy danych użytkownika zbiera w Studio

Dla tej usługi danych użytkownika składa się z informacji na temat użytkowników uprawnionych do dostępu do obszarów roboczych i rejestruje dane telemetryczne interakcji użytkowników z usługą.

Istnieją dwa rodzaje danych użytkownika w usłudze Machine Learning Studio:
- **Dane osobowe:** identyfikatorów kont i adresów e-mail skojarzone z kontem.
- **Dane klienta:** dane przekazane do analizy.

## <a name="studio-account-types-and-how-data-is-stored"></a>Typy kont Studio i sposób przechowywania danych

Istnieją trzy rodzaje kont w usłudze Machine Learning Studio. Typ konta określa sposób przechowywania danych i jak usunąć lub wyeksportować je.

- A **roboczym gościa** jest bezpłatne konto anonimowego. Utwórz konto bez konieczności podawania poświadczeń, takich jak adres e-mail lub hasło.
    -  Dane są przeczyszczane po wygaśnięciu obszaru roboczego gościa.
    - Goście można eksportować dane klienta za pośrednictwem interfejsu użytkownika, interfejsów API REST lub programu PowerShell pakietu.
- A **wolnego obszaru roboczego** jest bezpłatne konto, zaloguj się z firmą Microsoft poświadczenia — adres e-mail i hasło konta.
    - Można wyeksportować i usunąć dane osobiste i klienta, które podlegają żądania praw (DSR) podmiotu danych.
    - Można wyeksportować dane klienta za pośrednictwem interfejsu użytkownika, interfejsów API REST lub programu PowerShell pakietu.
    - Bezpłatnie obszarów roboczych nie używa konta usługi Azure AD, dane telemetryczne zdarzenia można wyeksportować za pomocą portalu prywatności.
    - Po usunięciu obszaru roboczego, możesz usunąć wszystkich danych osobowych klientów.
- A **standardowe obszaru roboczego** jest płatne konto dostępu przy użyciu poświadczeń logowania.
    - Można wyeksportować i usunąć dane osobiste i klienta, które podlegają DSR żądania.
    - Dostęp do danych za pośrednictwem portalu Azure prywatności
    - Można eksportować dane osobowe oraz klienta za pośrednictwem interfejsu użytkownika, interfejsów API REST lub programu PowerShell pakietu
    - Możesz usunąć dane w portalu Azure.

## <a name="delete-workspace-data-in-studio"></a>Usuwanie obszaru roboczego danych w Studio 

### <a name="delete-individual-assets"></a>Usuń poszczególne zasoby

Użytkownicy mogą usuwać zasoby w obszarze roboczym, wybierając je, a następnie wybierając przycisk Usuń.

![Usuwanie zasobów w usłudze Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Usuń cały obszar roboczy

Użytkownicy mogą również usuwać ich całego obszaru roboczego:
- Płatnej obszaru roboczego: Usuń za pośrednictwem portalu Azure.
- Obszar roboczy wolne: Użyj przycisku Usuń na **ustawienia** okienka.

![Usuń obszar roboczy wolne w usłudze Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Eksportuj dane Studio przy użyciu programu PowerShell
Eksportuj wszystkie informacje w formacie portable z usługi Azure Machine Learning Studio za pomocą poleceń za pomocą programu PowerShell. Aby uzyskać informacje, zobacz [modułu programu PowerShell dla usługi Azure Machine Learning](powershell-module.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Dokumentacja obejmujące plan zobowiązań rozliczeń i usług sieci web, zobacz [dokumentacja interfejsu API REST Azure Machine Learning](https://docs.microsoft.com/en-us/rest/api/machinelearning/). 
