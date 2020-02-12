---
title: Tworzenie obszaru roboczego
titleSuffix: ML Studio (classic) - Azure
description: Aby użyć Azure Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 419fbd987c1deb95a7c903d85105958443d06b01
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153608"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Tworzenie i udostępnianie obszaru roboczego Azure Machine Learning Studio (klasyczny)

Aby użyć Azure Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

## <a name="create-a-studio-classic-workspace"></a>Tworzenie obszaru roboczego programu Studio (klasycznego)

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Aby zalogować się i utworzyć obszar roboczy Studio (klasyczny), musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij pozycję **+ Nowy**

3. W polu wyszukiwania wpisz **Machine Learning Studio (klasyczny) obszar roboczy** i wybierz pasujący element. Następnie wybierz pozycję **Utwórz** w dolnej części strony.

4. Wprowadź swoje informacje o obszarze roboczym:

   - *Nazwa obszaru roboczego* może zawierać maksymalnie 260 znaków, a nie kończy się spacją. Nazwa nie może zawierać następujących znaków: `< > * % & : \ ? + /`
   - W przypadku wdrażania usług sieci Web z tego obszaru roboczego jest używana wybrana (lub utworzona) *Usługa sieci Web* oraz skojarzona z nią *warstwa cenowa* .

     ![Tworzenie nowego obszaru roboczego programu Studio (klasycznego)](./media/create-workspace/create-new-workspace.png)

5. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Machine Learning Studio (klasyczny) zależy od konta usługi Azure Storage, które podano w celu zapisania danych pośrednich podczas wykonywania przepływu pracy. Po utworzeniu obszaru roboczego, jeśli konto magazynu zostanie usunięte lub klucze dostępu zostaną zmienione, obszar roboczy przestanie działać, a wszystkie eksperymenty w tym obszarze roboczym zakończą się niepowodzeniem.
Jeśli przypadkowo usuniesz konto magazynu, Utwórz ponownie konto magazynu o tej samej nazwie w tym samym regionie co usunięte konto magazynu i ponownie zsynchronizuj klucz dostępu. Jeśli doszło do zmiany kluczy dostępu do konta magazynu, należy ponownie zsynchronizować klucze dostępu w obszarze roboczym, korzystając z witryny Azure Portal.

Po wdrożeniu obszaru roboczego można go otworzyć w Machine Learning Studio (klasyczny).

1. Przejdź do Machine Learning Studio (klasyczny) w [https://studio.azureml.net/](https://studio.azureml.net/).

2. Wybierz swój obszar roboczy w prawej prawym górnym rogu.

    ![Wybór obszaru roboczego](./media/create-workspace/open-workspace.png)

3. Kliknij pozycję **Moje eksperymenty**.

    ![Otwórz eksperymentów](./media/create-workspace/my-experiments.png)

Aby uzyskać informacje na temat zarządzania obszarem roboczym programu Studio (klasycznego), zobacz [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznego)](manage-workspace.md).
Jeśli wystąpi problem podczas tworzenia obszaru roboczego, zobacz [Przewodnik rozwiązywania problemów: Tworzenie obszaru roboczego Machine Learning Studio (klasycznego) i nawiązywanie z nim połączenia](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Udostępnianie obszaru roboczego Azure Machine Learning Studio (klasyczny)
Po utworzeniu obszaru roboczego Machine Learning Studio (klasycznego) możesz zapraszać użytkowników do obszaru roboczego, aby udostępniać dostęp do obszaru roboczego i wszystkich jego eksperymentów, zestawów danych, notesów itp. Możesz dodać użytkowników w jednej z dwóch ról:

* **Użytkownik — użytkownik** obszaru roboczego może tworzyć, otwierać, modyfikować i usuwać eksperymenty, zestawy danych itp. w obszarze roboczym.
* **Właściciel** — właściciel może zapraszać i usuwać użytkowników w obszarze roboczym, a także do tego, co użytkownik może zrobić.

> [!NOTE]
> Konto administratora, który tworzy obszar roboczy jest automatycznie dodawane do obszaru roboczego jako właściciela obszaru roboczego. Jednak innych administratorów lub użytkowników w tej subskrypcji nie automatycznie uzyskają dostęp do obszaru roboczego — należy zaprosić go jawnie.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Aby udostępnić obszar roboczy Studio (klasyczny)

1. Zaloguj się do Machine Learning Studio (klasyczny) w [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. W lewym panelu kliknij pozycję **Ustawienia** .

3. Kliknij kartę **Użytkownicy** .

4. Kliknij pozycję **Zaproś więcej użytkowników** u dołu strony

    ![Ustawienia programu Studio](./media/create-workspace/settings.png)

5. Wprowadź co najmniej jeden adres e-mail. Użytkownicy muszą prawidłowego konta Microsoft lub kontem organizacyjnym (z usługi Azure Active Directory).

6. Wybierz, czy chcesz dodać użytkowników jako właściciela lub użytkowników.

7. Kliknij przycisk znacznik wyboru **OK** .

Każdy użytkownik dodawanych otrzyma wiadomość e-mail z instrukcjami dotyczącymi sposobu Zaloguj się w udostępnionym obszarze roboczym.

> [!NOTE]
> Aby użytkownicy mogli mieć możliwość wdrażania i zarządzania nią usług sieci web, w tym obszarze roboczym musi być współautorem lub administratorem w subskrypcji platformy Azure. 



