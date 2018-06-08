---
title: Tworzenie obszaru roboczego Studio uczenia maszynowego | Dokumentacja firmy Microsoft
description: Tworzenie obszaru roboczego dla usługi Azure Machine Learning Studio
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 94502cbb0946ad1568cf33716480406b17fd57ac
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833958"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Tworzenie obszaru roboczego usługi Azure Machine Learning i zarządzanie nim
To menu łącza do tematów opisujących sposób konfigurowania różnych środowiskach nauki danych używany przez proces Analytics Cortana (CAP).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Aby korzystać z usługi Azure Machine Learning Studio, musisz mieć obszaru roboczego uczenia maszynowego. Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Aby utworzyć obszar roboczy
1. Zaloguj się do [portalu Azure](https://portal.azure.com/)

    > [!NOTE]
    > Zalogować się i utworzyć obszaru roboczego, musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij przycisk **+ nowy**

3. W polu wyszukiwania wpisz **obszaru roboczego Machine Learning Studio** i wybierz pasujący element. Następnie kliknij przycisk Wybierz **Utwórz** w dolnej części strony.

4. Wprowadź informacje obszaru roboczego:

    - *Nazwa obszaru roboczego* może mieć maksymalnie 260 znaków, nie kończy się spacją. Nazwa nie może zawierać następujących znaków: `< > * % & : \ ? + /`
    - *Plan usługi sieci web* możesz wybrać (lub Utwórz), oraz skojarzone *warstwy cenowej* wybrać, jest używany w przypadku wdrożenia usługi sieci web z obszaru roboczego.

    ![Tworzenie nowego obszaru roboczego](./media/create-workspace/create-new-workspace.png)

5. Kliknij przycisk **Utwórz**.

Po wdrożeniu obszaru roboczego, możesz otworzyć go w usłudze Machine Learning Studio.

1. Przejdź do usługi Machine Learning Studio w [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Wybierz obszar roboczy w prawej górnym rogu.

    ![Wybierz obszar roboczy](./media/create-workspace/open-workspace.png)

3. Kliknij przycisk **eksperymenty**.

    ![Otwórz eksperymenty](./media/create-workspace/my-experiments.png)

Informacje o zarządzaniu obszaru roboczego, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning](manage-workspace.md).
Jeśli napotkasz problem podczas tworzenia obszaru roboczego, zobacz [przewodnik rozwiązywania problemów: utworzyć i nawiązywanie z obszaru roboczego uczenia maszynowego](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Udostępnianie obszaru roboczego uczenia maszynowego Azure
Po utworzeniu obszaru roboczego uczenia maszynowego, można zaprosić użytkowników do swojego obszaru roboczego można udzielać dostępu do swojego obszaru roboczego i wszystkie jego eksperymentów, zestawy danych, notesy itp. Można dodać użytkowników, w jednym z dwóch ról:

* **Użytkownik** -użytkownika obszar roboczy można utworzyć, Otwórz, zmodyfikuj i Usuń eksperymentów, zestawy danych itp., w obszarze roboczym.
* **Właściciel** — poprosić właściciela i usuwać użytkowników w obszarze roboczym, oprócz jakie użytkownika mogą wykonywać.

> [!NOTE]
> Konto administratora, które tworzy obszaru roboczego jest automatycznie dodawany do obszaru roboczego jako właściciela obszaru roboczego. Jednak innych administratorów lub użytkowników w tej subskrypcji nie automatycznie otrzymują dostęp do obszaru roboczego — należy je jawnie zaprosić.
> 
> 

### <a name="to-share-a-workspace"></a>Aby udostępnić obszaru roboczego

1. Zaloguj się do usługi Machine Learning Studio w [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. W lewym panelu kliknij **ustawienia**

3. Kliknij przycisk **użytkowników** kartę

4. Kliknij przycisk **ZAPROSIĆ użytkowników więcej** w dolnej części strony

    ![Ustawienia Studio](./media/create-workspace/settings.png)

5. Wprowadź co najmniej jeden adres e-mail. Użytkownicy muszą prawidłowego konta Microsoft lub konta organizacyjnego (z usługą Azure Active Directory).

6. Określ, czy chcesz dodać użytkowników jako właściciela lub użytkowników.

7. Kliknij przycisk **OK** przycisk znacznika wyboru.

Każdy użytkownik dodawany otrzymają wiadomość e-mail z instrukcjami dotyczącymi sposobu Zaloguj się do udostępnionego obszaru roboczego.

> [!NOTE]
> Aby umożliwić użytkownikom możliwość wdrażania i zarządzania usługami sieci web, w tym obszarze roboczym musi być współautora lub administrator w subskrypcji platformy Azure. 



