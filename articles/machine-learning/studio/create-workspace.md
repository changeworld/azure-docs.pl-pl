---
title: Tworzenie aplikacji Azure Machine Learning Studio w obszarze roboczym | Dokumentacja firmy Microsoft
description: Aby korzystać z usługi Azure Machine Learning Studio, musisz mieć obszar roboczy usługi Machine Learning Studio. Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.
services: machine-learning
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 8362468d329f12ad8ebf25c8d205ec7d35ac6c28
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307223"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Tworzenie obszaru roboczego usługi Azure Machine Learning i zarządzanie nim

Aby korzystać z usługi Azure Machine Learning Studio, musisz mieć obszar roboczy usługi Machine Learning Studio. Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Aby utworzyć obszar roboczy
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Zaloguj się i Utwórz obszar roboczy, musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij przycisk **+ nowe**

3. W polu wyszukiwania wpisz **obszaru roboczego usługi Machine Learning Studio** i wybierz pasujący element. Następnie wybierz pozycję **Utwórz** w dolnej części strony.

4. Wprowadź swoje informacje o obszarze roboczym:

    - *Nazwa obszaru roboczego* może być maksymalnie 260 znaków, nie kończy się spacją. Nazwa nie może zawierać następujących znaków: `< > * % & : \ ? + /`
    - *Planu usługi sieci web* możesz wybrać (lub Utwórz), wraz ze skojarzonego *warstwy cenowej* wybrać, jest używany w przypadku wdrożenia usługi sieci web z obszaru roboczego.

    ![Utwórz nowy obszar roboczy](./media/create-workspace/create-new-workspace.png)

5. Kliknij pozycję **Utwórz**.

Po wdrożeniu obszaru roboczego, możesz go otworzyć w usłudze Machine Learning Studio.

1. Przejdź do usługi Machine Learning Studio na [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Wybierz swój obszar roboczy w prawej prawym górnym rogu.

    ![Wybór obszaru roboczego](./media/create-workspace/open-workspace.png)

3. Kliknij przycisk **eksperymenty**.

    ![Otwórz eksperymentów](./media/create-workspace/my-experiments.png)

Aby dowiedzieć się, jak zarządzanie obszarem roboczym, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning](manage-workspace.md).
Jeśli wystąpi problem podczas tworzenia obszaru roboczego, zobacz [przewodnik rozwiązywania problemów: tworzenie i łączenie z obszaru roboczego usługi Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Udostępnianie obszaru roboczego usługi Azure Machine Learning
Po utworzeniu obszaru roboczego uczenia maszynowego, można zaprosić użytkowników do swojego obszaru roboczego, aby udostępnić uprawnienia dostępu do obszaru roboczego i wszystkich jego eksperymentów, zestawów danych, notesy itp. Wystarczy dodać użytkowników, w jedną z dwóch ról:

* **Użytkownik** — użytkownik obszar roboczy można utworzyć, Otwórz, modyfikowanie i usuwanie eksperymenty, zestawy danych itp., w obszarze roboczym.
* **Właściciel** — właściciel może zapraszać i usuwanie użytkowników w obszarze roboczym, oprócz co można zrobić.

> [!NOTE]
> Konto administratora, który tworzy obszar roboczy jest automatycznie dodawane do obszaru roboczego jako właściciela obszaru roboczego. Jednak innych administratorów lub użytkowników w tej subskrypcji nie automatycznie uzyskają dostęp do obszaru roboczego — należy zaprosić go jawnie.
> 
> 

### <a name="to-share-a-workspace"></a>Aby udostępnić obszar roboczy

1. Zaloguj się do usługi Machine Learning Studio na [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. W okienku po lewej stronie kliknij **ustawienia**

3. Kliknij przycisk **użytkowników** kartę

4. Kliknij przycisk **ZAPROŚ więcej użytkowników** w dolnej części strony

    ![Ustawienia programu Studio](./media/create-workspace/settings.png)

5. Wprowadź co najmniej jeden adres e-mail. Użytkownicy muszą prawidłowego konta Microsoft lub kontem organizacyjnym (z usługi Azure Active Directory).

6. Wybierz, czy chcesz dodać użytkowników jako właściciela lub użytkowników.

7. Kliknij przycisk **OK** przycisk znacznika wyboru.

Każdy użytkownik dodawanych otrzyma wiadomość e-mail z instrukcjami dotyczącymi sposobu Zaloguj się w udostępnionym obszarze roboczym.

> [!NOTE]
> Aby użytkownicy mogli mieć możliwość wdrażania i zarządzania nią usług sieci web, w tym obszarze roboczym musi być współautorem lub administratorem w subskrypcji platformy Azure. 



