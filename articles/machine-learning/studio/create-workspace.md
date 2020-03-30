---
title: Tworzenie obszaru roboczego
titleSuffix: ML Studio (classic) - Azure
description: Aby korzystać z usługi Azure Machine Learning Studio (classic), musisz mieć obszar roboczy Usługi Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204464"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Tworzenie i udostępnianie klasycznego obszaru roboczego usługi Azure Machine Learning Studio

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Aby korzystać z usługi Azure Machine Learning Studio (classic), musisz mieć obszar roboczy Usługi Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

## <a name="create-a-studio-classic-workspace"></a>Tworzenie obszaru roboczego studio (klasycznego)

1. Logowanie się do [witryny Azure portal](https://portal.azure.com/)

    > [!NOTE]
    > Aby zalogować się i utworzyć obszar roboczy Studio (klasyczny), musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij **+Nowy**

3. W polu wyszukiwania wpisz **obszar roboczy Machine Learning Studio (klasyczny)** i wybierz pasujący element. Następnie wybierz pozycję **Utwórz** u dołu strony.

4. Wprowadź informacje o obszarze roboczym:

   - *Nazwa obszaru roboczego* może mieć maksymalnie 260 znaków, a nie kończyć na spacji. Nazwa nie może zawierać następujących znaków:`< > * % & : \ ? + /`
   - *Plan usługi sieci web* wybrany (lub utworzony) wraz ze skojarzoną *warstwą cenową,* którą wybierzesz, jest używany w przypadku wdrażania usług sieci web z tego obszaru roboczego.

     ![Tworzenie nowego obszaru roboczego Studio (klasycznego)](./media/create-workspace/create-new-workspace.png)

5. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Machine Learning Studio (klasyczny) opiera się na koncie magazynu platformy Azure, które można podać, aby zapisać dane pośrednika podczas wykonywania przepływu pracy. Po utworzeniu obszaru roboczego, jeśli konto magazynu zostanie usunięte lub jeśli klucze dostępu zostaną zmienione, obszar roboczy przestanie działać, a wszystkie eksperymenty w tym obszarze roboczym nie powiodą się.
Jeśli przypadkowo usuniesz konto magazynu, ponownie stwórz konto magazynu o tej samej nazwie w tym samym regionie co usunięte konto magazynu i ponownie zsynchronizuj klucz dostępu. Jeśli doszło do zmiany kluczy dostępu do konta magazynu, należy ponownie zsynchronizować klucze dostępu w obszarze roboczym, korzystając z witryny Azure Portal.

Po wdrożeniu obszaru roboczego można go otworzyć w udaniu machine learning studio (klasycznym).

1. Przejdź do Machine Learning Studio [https://studio.azureml.net/](https://studio.azureml.net/)(classic) w .

2. Wybierz swój obszar roboczy w prawym górnym rogu.

    ![Wybór obszaru roboczego](./media/create-workspace/open-workspace.png)

3. Kliknij **moje eksperymenty**.

    ![Otwarte eksperymenty](./media/create-workspace/my-experiments.png)

Aby uzyskać informacje na temat zarządzania obszarem roboczym Studio (klasycznym), zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio (klasycznym).](manage-workspace.md)
Jeśli napotkasz problem z utworzeniem obszaru roboczego, zobacz [Przewodnik rozwiązywania problemów: Tworzenie i łączenie się z obszarem roboczym machine learning studio (klasycznym).](troubleshooting-creating-ml-workspace.md)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Udostępnianie klasycznego obszaru roboczego usługi Azure Machine Learning Studio
Po utworzeniu obszaru roboczego Machine Learning Studio (klasycznego) można zaprosić użytkowników do obszaru roboczego, aby udostępnili dostęp do obszaru roboczego i wszystkich jego eksperymentów, zestawów danych, notesów itp. Możesz dodać użytkowników w jednej z dwóch ról:

* **Użytkownik** — użytkownik obszaru roboczego może tworzyć, otwierać, modyfikować i usuwać eksperymenty, zestawy danych itp.
* **Właściciel** — właściciel może zapraszać i usuwać użytkowników w obszarze roboczym, oprócz tego, co użytkownik może zrobić.

> [!NOTE]
> Konto administratora, które tworzy obszar roboczy, jest automatycznie dodawane do obszaru roboczego jako właściciel obszaru roboczego. Jednak inni administratorzy lub użytkownicy w tej subskrypcji nie są automatycznie przyznane dostęp do obszaru roboczego — należy zaprosić ich jawnie.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Aby udostępnić obszar roboczy Studio (klasyczny)

1. Zaloguj się do Machine Learning Studio (classic)[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. W lewym panelu kliknij pozycję **USTAWIENIA**

3. Kliknij kartę **UŻYTKOWNICY**

4. Kliknij **zaproś więcej użytkowników** u dołu strony

    ![Ustawienia studio](./media/create-workspace/settings.png)

5. Wprowadź jeden lub więcej adresów e-mail. Użytkownicy potrzebują prawidłowego konta Microsoft lub konta organizacji (z usługi Azure Active Directory).

6. Wybierz, czy chcesz dodać użytkowników jako właściciela, czy użytkownika.

7. Kliknij przycisk znacznika wyboru **OK.**

Każdy użytkownik, który dodasz, otrzyma wiadomość e-mail z instrukcjami dotyczącymi logowania się do udostępnionego obszaru roboczego.

> [!NOTE]
> Aby użytkownicy mogli wdrażać usługi sieci web lub zarządzać nimi w tym obszarze roboczym, muszą być współautorami lub administratorami w ramach subskrypcji platformy Azure. 



