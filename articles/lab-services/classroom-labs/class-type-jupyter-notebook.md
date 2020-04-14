---
title: Skonfiguruj laboratorium do nauczania nauki o danych za pomocą notesów Pythona i Jupytera | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium do nauczania nauki o danych przy użyciu notesów Pythona i Jupytera.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257729"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Skonfiguruj laboratorium do nauczania nauki o danych za pomocą notesów Pythona i Jupytera

W tym artykule opisano, jak skonfigurować maszynę szablonów w programach Lab Services za pomocą narzędzi potrzebnych do nauczania uczniów, jak używać [notesów Jupyter](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks to projekt typu open source, który pozwala łatwo łączyć tekst sformatowany i wykonywalny kod źródłowy [Pythona](https://www.python.org/) na jednym płótnie zwanym notesem.  Uruchomienie notesu powoduje liniowy rekord wejść i wyjść.  Te dane wyjściowe mogą zawierać tekst, tabele informacji, wykresy punktowe i inne.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure i konta laboratorium, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [samouczek konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Można również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów portalu Marketplace, zobacz [określanie obrazów z marketplace dostępnych dla twórców laboratoriów.](specify-marketplace-images.md)

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
| Obraz portalu Marketplace | Włącz obraz [maszyny wirtualnej do nauki o danych — windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) do użycia na koncie laboratorium. |

>[!TIP]
>W tym artykule skupimy się na konfigurowaniu komputera szablonu korzystającego z systemu operacyjnego Windows Server.  Jest również możliwe skonfigurowanie klasy nauki o danych za pomocą notesów języka Python i Jupyter przy użyciu [obrazów maszyny wirtualnej do nauki o danych dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) z portalu Azure Marketplace.

### <a name="lab-settings"></a>Ustawienia laboratorium

Podczas konfigurowania laboratorium w klasie należy używać ustawień w poniższej tabeli.  Aby uzyskać więcej informacji na temat tworzenia laboratorium w klasie, zobacz [konfigurowanie samouczka laboratorium w klasie](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcja |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| Mały procesor graficzny (Compute). Ten rozmiar najlepiej nadaje się do aplikacji intensywnie korzystających z zasobów obliczeniowych i sieciowych, takich jak sztuczna inteligencja i uczenie głębokie. |
|Obraz maszyny wirtualnej| Maszyna wirtualna do nauki o danych — Windows 2016|

## <a name="template-machine"></a>Maszyna szablonu

Maszyna wirtualna do nauki o danych — obraz [systemu Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) zawiera niezbędne struktury uczenia głębokiego i narzędzia wymagane dla tego typu klasy.  Obraz zawiera notesy Jupyter i Visual Studio Code.  [Notebooki Jupyter](http://jupyter-notebook.readthedocs.io) to aplikacja internetowa, która umożliwia analitykom danych do wykonywania nieprzetworzonych danych, uruchamiania obliczeń i zobacz wyniki w tym samym środowisku.  Dla naszego komputera szablonu aplikacja internetowa będzie działać lokalnie.  [Visual Studio Code](https://code.visualstudio.com/) to IDE, który zapewnia bogate środowisko interaktywne podczas pisania i testowania notesu.  Aby uzyskać więcej informacji, zobacz [Praca z notesami jupyter w programie Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

Zadaniem do skonfigurowania klasy jest dostarczenie notesów lokalnych.  Aby uzyskać instrukcje dotyczące korzystania z przykładów usługi Azure Machine Learning, zobacz [jak skonfigurować środowisko za pomocą notesów jupyter.](../../machine-learning/how-to-configure-environment.md#jupyter)  Można również podać własne notesy na komputerze szablonu.  Notesy zostaną skopiowane do wszystkich komputerów uczniów po opublikowaniu szablonu.

## <a name="cost-estimate"></a>Szacowanie kosztów

Pokryjmy możliwe szacunki kosztów dla tej klasy.  Użyjemy klasy 25 uczniów.  Zaplanowany czas zajęć jest 20 godzin.  Ponadto, każdy uczeń dostaje 10 godzin przydziału na pracę domową lub zadania poza zaplanowanym czasie zajęć.  Wybrany przez nas rozmiar maszyny wirtualnej to mały procesor graficzny (compute), który wynosi 139 jednostek laboratoryjnych.

Oto przykład możliwego koszto preliminarzu dla tej klasy:

25 \* studentów (20 godzin planowych + \* 10 godzin \* kontyngentowych) 139 jednostek laboratoryjnych 0.01 USD za godzinę = 1042.5 USD

Więcej informacji na temat cen można znaleźć w [cenniku usług Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie

W tym artykule przeszliśmy przez kroki, aby utworzyć laboratorium dla klasy Notesy Jupyter. Można użyć podobnej konfiguracji dla innych klas uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne dla konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji wiadomości e-mail do studentów](how-to-configure-student-usage.md#send-invitations-to-users)
