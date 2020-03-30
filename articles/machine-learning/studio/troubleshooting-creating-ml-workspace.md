---
title: Rozwiązywanie problemów z obszarem roboczym
titleSuffix: ML Studio (classic) - Azure
description: Ten przewodnik zawiera rozwiązania dla niektórych często spotykanych problemów podczas konfigurowania obszarów roboczych usługi Azure Machine Learning Studio (klasycznych).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217832"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Przewodnik rozwiązywania problemów: tworzenie obszaru roboczego usługi Azure Machine Learning Studio (klasycznego) i łączenie się z nią

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ten przewodnik zawiera rozwiązania dla niektórych często spotykanych problemów podczas konfigurowania obszarów roboczych usługi Azure Machine Learning Studio (klasycznych).

## <a name="workspace-owner"></a>Właściciel obszaru roboczego
Aby otworzyć obszar roboczy w programie Machine Learning Studio (klasyczny), użytkownik musi być zalogowany do konta Microsoft użytego do utworzenia obszaru roboczego lub musi otrzymać zaproszenie od właściciela, aby dołączyć do obszaru roboczego. Z witryny Azure portal można zarządzać obszarem roboczym, który obejmuje możliwość konfigurowania dostępu.

Aby uzyskać więcej informacji na temat zarządzania obszarem [roboczym, zobacz Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio (klasycznym).]

[Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio (klasycznym)]: manage-workspace.md

## <a name="allowed-regions"></a>Dozwolone regiony
Uczenie maszynowe jest obecnie dostępne w ograniczonej liczbie regionów. Jeśli subskrypcja nie zawiera jednego z tych regionów, może zostać wyświetlony komunikat o błędzie "Nie masz żadnych subskrypcji w dozwolonych regionach".

Aby zażądać dodania regionu do subskrypcji, utwórz nowe żądanie pomocy technicznej firmy Microsoft z witryny Azure Portal, wybierz pozycję **Rozliczenia** jako typ problemu i postępuj zgodnie z monitami o przesłanie żądania.

## <a name="storage-account"></a>Konto magazynu
Usługa uczenie maszynowe potrzebuje konta magazynu do przechowywania danych. Można użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu podczas tworzenia nowego obszaru roboczego Machine Learning Studio (klasycznego) (jeśli masz przydział do utworzenia nowego konta magazynu).

Po utworzeniu nowego obszaru roboczego Machine Learning Studio (klasycznego) można zalogować się do usługi Machine Learning Studio (klasycznie) przy użyciu konta Microsoft użytego do utworzenia obszaru roboczego. Jeśli napotkasz komunikat o błędzie "Nie znaleziono obszaru roboczego" (podobnie jak poniższy zrzut ekranu), wykonaj następujące kroki, aby usunąć pliki cookie przeglądarki.

![Nie znaleziono obszaru roboczego](media/troubleshooting-creating-ml-workspace/screen3.png)

**Aby usunąć pliki cookie przeglądarki**

1. Jeśli korzystasz z programu Internet Explorer, kliknij przycisk **Narzędzia** w prawym górnym rogu i wybierz **pozycję Opcje internetowe**.  

   ![Opcje internetowe](media/troubleshooting-creating-ml-workspace/screen4.png)

2. W obszarze karty **Ogólne** kliknij pozycję **Usuń...**

   ![Karta Ogólne](media/troubleshooting-creating-ml-workspace/screen5.png)

3. W oknie dialogowym **Usuwanie historii przeglądania** upewnij się, że są **zaznaczone pliki cookie i dane witryny,** a następnie kliknij przycisk **Usuń**.

   ![Usuwanie plików cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po usunięciu plików cookie uruchom ponownie przeglądarkę, a następnie przejdź do strony [Microsoft Azure Machine Learning Studio (klasycznej).](https://studio.azureml.net) Po wyświetleniu monitu o podanie nazwy użytkownika i hasła wprowadź to samo konto Microsoft, które zostało użyte do utworzenia obszaru roboczego.

## <a name="comments"></a>Komentarze

Naszym celem jest uczynienie uczenia maszynowego tak płynnym, jak to tylko możliwe. Wszelkie uwagi i problemy prosimy publikować na [forum usługi Azure Machine Learning,](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) aby pomóc nam lepiej służyć.
