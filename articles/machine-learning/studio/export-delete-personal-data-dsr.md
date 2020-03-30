---
title: Eksportowanie i usuwanie danych
titleSuffix: ML Studio (classic) - Azure
description: Dane w produkcie przechowywane przez usługę Azure Machine Learning Studio (klasyczne) są dostępne do eksportowania i usuwania za pośrednictwem witryny Azure portal, a także za pośrednictwem uwierzytelnionych interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu prywatności usługi Azure. W tym artykule pokazano, jak to zrobić.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: c380d10d0c68794ec3810cea25341d68bb41400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251689"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Eksportowanie i usuwanie danych użytkownika w produkcie z usługi Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Dane w produkcie przechowywane przez usługę Azure Machine Learning Studio (klasyczne) można usuwać lub eksportować przy użyciu witryny Azure portal, interfejsu Studio (klasycznego), programu PowerShell i uwierzytelnionych interfejsów API REST. W tym artykule dowiesz się, jak to zrobić. 

Dane telemetryczne są dostępne za pośrednictwem portalu prywatności platformy Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Jakie rodzaje danych użytkownika zbiera Studio (klasyczny) ?

W przypadku tej usługi dane użytkownika składają się z informacji o użytkownikach upoważnionych do uzyskiwania dostępu do obszarów roboczych i rekordów telemetrycznych interakcji użytkownika z usługą.

Istnieją dwa rodzaje danych użytkownika w umiań machine learning studio (klasyczny):
- **Dane konta osobistego:** Identyfikatory kont i adresy e-mail skojarzone z kontem.
- **Dane klienta:** Dane przesłane do analizy.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Typy kont studio (klasyczne) i sposób przechowywania danych

Istnieją trzy rodzaje kont w udiociepła machine learning (klasyczny). Rodzaj konta określa sposób przechowywania danych i sposób ich usuwania lub eksportowania.

- **Obszar roboczy gościa** to bezpłatne, anonimowe konto. Zarejestruj się bez podawania poświadczeń, takich jak adres e-mail lub hasło.
    -  Dane są czyszczane po wygaśnięciu obszaru roboczego gościa.
    - Użytkownicy-goście mogą eksportować dane klientów za pośrednictwem interfejsu użytkownika, interfejsów API REST lub pakietu programu PowerShell.
- **Bezpłatny obszar roboczy** to bezpłatne konto, do którego logujesz się przy użyciu poświadczeń konta Microsoft — adres e-mail i hasło.
    - Można eksportować i usuwać dane osobowe i dane klientów, które podlegają wymagam praw osób, których dane dotyczą (DSR).
    - Dane klientów można eksportować za pośrednictwem interfejsu użytkownika, interfejsów API REST lub pakietu programu PowerShell.
    - W przypadku bezpłatnych obszarów roboczych nieużywanych kont usługi Azure AD dane telemetryczne można eksportować za pomocą portalu prywatności.
    - Usunięcie obszaru roboczego powoduje usunięcie wszystkich osobistych danych klienta.
- **Standardowy obszar roboczy** to konto płatne, do którego uzyskujesz dostęp z poświadczeniami logowania.
    - Można eksportować i usuwać dane osobowe i dane klientów, które podlegają wymagam DSR.
    - Dostęp do danych można uzyskać za pośrednictwem portalu prywatności platformy Azure
    - Dane osobiste i dane klientów można eksportować za pośrednictwem interfejsu użytkownika, interfejsów API REST lub pakietu programu PowerShell
    - Dane można usunąć w witrynie Azure portal.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Usuwanie danych obszaru roboczego w Studio (klasyczny) 

### <a name="delete-individual-assets"></a>Usuwanie poszczególnych zasobów

Użytkownicy mogą usuwać zasoby w obszarze roboczym, zaznaczając je, a następnie wybierając przycisk usuń.

![Usuwanie zasobów w umiań machine learningu (klasyczny)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Usuwanie całego obszaru roboczego

Użytkownicy mogą również usunąć cały obszar roboczy:
- Płatny obszar roboczy: usuń za pośrednictwem witryny Azure portal.
- Wolny obszar roboczy: użyj przycisku usuń w okienku **Ustawienia.**

![Usuwanie bezpłatnego obszaru roboczego w umiań machine learningu (klasyczny)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Eksportowanie danych studyjnych (klasycznych) za pomocą programu PowerShell
Użyj programu PowerShell, aby wyeksportować wszystkie informacje do formatu przenośnego z usługi Azure Machine Learning Studio (klasyczne) przy użyciu poleceń. Aby uzyskać więcej informacji, zobacz [moduł programu PowerShell dla usługi Azure Machine Learning Studio (klasyczny)](powershell-module.md) artykuł.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dokumentację dotyczącą usług sieci web i rozliczeń planu zobowiązań, zobacz [Odwołanie do interfejsu API REST usługi Azure Machine Learning Studio (klasyczne).](https://docs.microsoft.com/rest/api/machinelearning/) 
