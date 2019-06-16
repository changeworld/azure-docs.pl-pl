---
title: Tworzenie obszaru roboczego usługi Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Aby korzystać z usługi Azure Machine Learning Studio, musisz mieć obszar roboczy usługi Machine Learning Studio. Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109352"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Utwórz i Udostępnij obszar roboczy usługi Azure Machine Learning Studio

Aby korzystać z usługi Azure Machine Learning Studio, musisz mieć obszar roboczy usługi Machine Learning Studio. Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

## <a name="create-a-studio-workspace"></a>Utwórz obszar roboczy Studio

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Zaloguj się i Utwórz obszar roboczy Studio, musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij przycisk **+ nowe**

3. W polu wyszukiwania wpisz **obszaru roboczego usługi Machine Learning Studio** i wybierz pasujący element. Następnie wybierz pozycję **Utwórz** w dolnej części strony.

4. Wprowadź swoje informacje o obszarze roboczym:

   - *Nazwa obszaru roboczego* może być maksymalnie 260 znaków, nie kończy się spacją. Nazwa nie może zawierać następujących znaków: `< > * % & : \ ? + /`
   - *Planu usługi sieci web* możesz wybrać (lub Utwórz), wraz ze skojarzonego *warstwy cenowej* wybrać, jest używany w przypadku wdrożenia usługi sieci web z obszaru roboczego.

     ![Utwórz nowy obszar roboczy Studio](./media/create-workspace/create-new-workspace.png)

5. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Usługa Machine Learning Studio opiera się na konto magazynu platformy Azure, który możesz udostępnić zapisywane są dane pośrednie podczas wykonywania przepływu pracy. Po utworzeniu obszaru roboczego, jeśli konto magazynu zostało usunięte lub klucze dostępu zostaną zmienione, obszar roboczy przestanie działać, a wszystkie eksperymenty w tym obszarze roboczym zakończą się niepowodzeniem.
Jeśli przypadkowo usuniesz konto magazynu, ponownie utwórz konto magazynu o tej samej nazwie w tym samym regionie co usunięte konto magazynu, a następnie ponownie zsynchronizować klucz dostępu. Jeśli doszło do zmiany kluczy dostępu do konta magazynu, należy ponownie zsynchronizować klucze dostępu w obszarze roboczym, korzystając z witryny Azure Portal.

Po wdrożeniu obszaru roboczego, możesz go otworzyć w usłudze Machine Learning Studio.

1. Przejdź do usługi Machine Learning Studio na [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Wybierz swój obszar roboczy w prawej prawym górnym rogu.

    ![Wybór obszaru roboczego](./media/create-workspace/open-workspace.png)

3. Kliknij przycisk **eksperymenty**.

    ![Otwórz eksperymentów](./media/create-workspace/my-experiments.png)

Aby uzyskać informacji o zarządzaniu obszaru roboczego usługi Studio, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio](manage-workspace.md).
Jeśli wystąpi problem podczas tworzenia obszaru roboczego, zobacz [przewodnik rozwiązywania problemów: Tworzenie i łączenie z obszaru roboczego usługi Machine Learning Studio](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Udostępnij obszar roboczy usługi Azure Machine Learning Studio
Raz usługi Machine Learning Studio obszar roboczy jest tworzony, możesz zaprosić użytkowników do swojego obszaru roboczego, aby udostępnić uprawnienia dostępu do obszaru roboczego i wszystkich jego eksperymentów, zestawów danych, notesy itp. Wystarczy dodać użytkowników, w jedną z dwóch ról:

* **Użytkownik** — użytkownik obszar roboczy można utworzyć, Otwórz, modyfikowanie i usuwanie eksperymenty, zestawy danych itp., w obszarze roboczym.
* **Właściciel** — właściciel może zapraszać i usuwanie użytkowników w obszarze roboczym, oprócz co można zrobić.

> [!NOTE]
> Konto administratora, który tworzy obszar roboczy jest automatycznie dodawane do obszaru roboczego jako właściciela obszaru roboczego. Jednak innych administratorów lub użytkowników w tej subskrypcji nie automatycznie uzyskają dostęp do obszaru roboczego — należy zaprosić go jawnie.
> 
> 

### <a name="to-share-a-studio-workspace"></a>Aby udostępnić obszar roboczy Studio

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



