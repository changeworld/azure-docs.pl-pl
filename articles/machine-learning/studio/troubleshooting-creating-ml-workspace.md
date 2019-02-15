---
title: 'Rozwiązywanie problemów: Tworzenie, łączenie do obszaru roboczego usługi Machine Learning Studio'
titleSuffix: Azure Machine Learning Studio
description: Ten przewodnik zawiera temat rozwiązania niektórych często spotykanych wyzwania podczas konfigurowania obszary robocze usługi Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: c12c87524ac1b8d0be5b691d599510f8e4573317
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267329"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Podręcznik rozwiązywania problemów: Tworzenie i łączenie z obszaru roboczego usługi Azure Machine Learning Studio
Ten przewodnik zawiera temat rozwiązania niektórych często spotykanych wyzwania podczas konfigurowania obszary robocze usługi Azure Machine Learning Studio.



## <a name="workspace-owner"></a>Właściciel obszaru roboczego
Aby otworzyć obszar roboczy w usłudze Machine Learning Studio, użytkownik musi być zalogowany Account Microsoft został użyty do utworzenia obszaru roboczego lub musisz otrzymać zaproszenie od właściciela do obszaru roboczego. W witrynie Azure portal można zarządzać obszaru roboczego, w tym możliwość skonfigurowania dostępu.

Aby uzyskać więcej informacji na temat zarządzania obszarem roboczym, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio].

[Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio]: manage-workspace.md

## <a name="allowed-regions"></a>Dozwolone regionów
Machine Learning jest obecnie dostępna w ograniczonej liczbie regionów. Jeśli Twoja subskrypcja obejmuje jedną z tych regionów, może zostać wyświetlony komunikat o błędzie, "Masz żadnych subskrypcji w regionach dozwolone."

Żądanie dodania regionie Twojej subskrypcji, Utwórz nowe żądanie pomocy technicznej firmy Microsoft w witrynie Azure portal wybierz pozycję **rozliczeń** jako typ problemu i postępuj zgodnie z monitami, aby przesłać żądanie.

## <a name="storage-account"></a>Konto magazynu
Usługa Machine Learning wymaga konta magazynu do przechowywania danych. Można użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu podczas tworzenia nowego obszaru roboczego usługi Machine Learning Studio (Jeśli masz przydziału do utworzenia nowego konta magazynu).

Po utworzeniu nowego obszaru roboczego usługi Machine Learning Studio można zalogowaniu usłudze Machine Learning Studio przy użyciu konta Microsoft, który został użyty do utworzenia obszaru roboczego. Jeśli wystąpi komunikat o błędzie "Obszaru roboczego nie znaleziono" (podobnie jak na poniższym zrzucie ekranu), użyj poniższe kroki można usunąć pliki cookie przeglądarki.

![Nie znaleziono obszaru roboczego][screen3]

**Aby usunąć pliki cookie przeglądarki**

1. Jeśli używasz programu Internet Explorer, kliknij przycisk **narzędzia** przycisk w prawym górnym rogu i wybierz **Opcje internetowe**.  

   ![Opcje internetowe][screen4]

2. W obszarze **ogólne** kliknij pozycję **Usuń...**

   ![Karta Ogólne][screen5]

3. W **usuwanie historii przeglądania** okna dialogowego pole, upewnij się, **pliki cookie i dane witryn internetowych** jest zaznaczone, a następnie kliknij przycisk **Usuń**.

   ![Usuń pliki cookie][screen6]

Po usunięciu plików cookie, uruchom ponownie przeglądarkę, a następnie przejdź do [Microsoft Azure Machine Learning](https://studio.azureml.net) strony. Po wyświetleniu monitu o nazwę użytkownika i hasło, wprowadź tego samego konta Microsoft, który został użyty do utworzenia obszaru roboczego.

## <a name="comments"></a>Komentarze

Naszym celem jest zapewnienie środowiska usługi Machine Learning jako Bezproblemowa, jak to możliwe. Opublikuj wszystkie komentarze i problemy w czasie [forum usługi Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) aby pomóc nam w udzieleniu skuteczniejszej.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
