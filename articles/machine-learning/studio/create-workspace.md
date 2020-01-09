---
title: Tworzenie obszaru roboczego
titleSuffix: ML Studio (classic) - Azure
description: Aby użyć Azure Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 91ba4d1f7d32071cce0de1de528abf02982ce7be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427614"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Tworzenie i udostępnianie obszaru roboczego Azure Machine Learning Studio (klasyczny)

Aby użyć Azure Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

## <a name="create-a-studio-classic-workspace"></a>Tworzenie obszaru roboczego programu Studio (klasycznego)

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Aby zalogować się i utworzyć obszar roboczy Studio (klasyczny), musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij przycisk **+ nowe**

3. W polu wyszukiwania wpisz **Machine Learning Studio (klasyczny) obszar roboczy** i wybierz pasujący element. Następnie wybierz pozycję **Utwórz** w dolnej części strony.

4. Wprowadź swoje informacje o obszarze roboczym:

   - *Nazwa obszaru roboczego* może być maksymalnie 260 znaków, nie kończy się spacją. Nazwa nie może zawierać następujących znaków: `< > * % & : \ ? + /`
   - *Planu usługi sieci web* możesz wybrać (lub Utwórz), wraz ze skojarzonego *warstwy cenowej* wybrać, jest używany w przypadku wdrożenia usługi sieci web z obszaru roboczego.

     ![Tworzenie nowego obszaru roboczego programu Studio (klasycznego)](./media/create-workspace/create-new-workspace.png)

5. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Machine Learning Studio (klasyczny) zależy od konta usługi Azure Storage, które podano w celu zapisania danych pośrednich podczas wykonywania przepływu pracy. Po utworzeniu obszaru roboczego, jeśli konto magazynu zostanie usunięte lub klucze dostępu zostaną zmienione, obszar roboczy przestanie działać, a wszystkie eksperymenty w tym obszarze roboczym zakończą się niepowodzeniem.
Jeśli przypadkowo usuniesz konto magazynu, Utwórz ponownie konto magazynu o tej samej nazwie w tym samym regionie co usunięte konto magazynu i ponownie zsynchronizuj klucz dostępu. Jeśli doszło do zmiany kluczy dostępu do konta magazynu, należy ponownie zsynchronizować klucze dostępu w obszarze roboczym, korzystając z witryny Azure Portal.

Po wdrożeniu obszaru roboczego można go otworzyć w Machine Learning Studio (klasyczny).

1. Przejdź do Machine Learning Studio (klasyczny) w [https://studio.azureml.net/](https://studio.azureml.net/).

2. Wybierz swój obszar roboczy w prawej prawym górnym rogu.

    ![Wybór obszaru roboczego](./media/create-workspace/open-workspace.png)

3. Kliknij przycisk **eksperymenty**.

    ![Otwórz eksperymentów](./media/create-workspace/my-experiments.png)

Aby uzyskać informacje na temat zarządzania obszarem roboczym programu Studio (klasycznego), zobacz [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznego)](manage-workspace.md).
Jeśli wystąpi problem podczas tworzenia obszaru roboczego, zobacz [Przewodnik rozwiązywania problemów: Tworzenie obszaru roboczego Machine Learning Studio (klasycznego) i nawiązywanie z nim połączenia](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Udostępnianie obszaru roboczego Azure Machine Learning Studio (klasyczny)
Po utworzeniu obszaru roboczego Machine Learning Studio (klasycznego) możesz zapraszać użytkowników do obszaru roboczego, aby udostępniać dostęp do obszaru roboczego i wszystkich jego eksperymentów, zestawów danych, notesów itp. Możesz dodać użytkowników w jednej z dwóch ról:

* **Użytkownik** — użytkownik obszar roboczy można utworzyć, Otwórz, modyfikowanie i usuwanie eksperymenty, zestawy danych itp., w obszarze roboczym.
* **Właściciel** — właściciel może zapraszać i usuwanie użytkowników w obszarze roboczym, oprócz co można zrobić.

> [!NOTE]
> Konto administratora, który tworzy obszar roboczy jest automatycznie dodawane do obszaru roboczego jako właściciela obszaru roboczego. Jednak innych administratorów lub użytkowników w tej subskrypcji nie automatycznie uzyskają dostęp do obszaru roboczego — należy zaprosić go jawnie.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Aby udostępnić obszar roboczy Studio (klasyczny)

1. Zaloguj się do Machine Learning Studio (klasyczny) w [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

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



