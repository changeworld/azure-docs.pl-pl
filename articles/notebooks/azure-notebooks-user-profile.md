---
title: Profil użytkownika i identyfikator do użytku z usługą Azure Notebooks Preview
description: Jak utworzyć profil użytkownika i identyfikator użytkownika i zarządzać nim za pomocą notesów platformy Azure, która staje się częścią adresu URL notesów udostępnionych.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646283"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Twój profil i identyfikator użytkownika w wersji Zapoznawczej notesów platformy Azure

W zaawansowanej, współpracy przestrzeni notesów platformy Azure profil użytkownika przedstawia obraz publiczny innym osobom:

[![Strona profilu notesów platformy Azure](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Identyfikator użytkownika jest częścią adresów URL używanych do udostępniania projektów i notesów. Na poniższej liście opisano różne wzorce adresów URL:

- `https://notebooks.azure.com/<user_id>`: Twoja strona profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Twoje projekty. Widzisz wszystkie projekty; inni użytkownicy widzą tylko twoje projekty publiczne.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Pliki projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klony konkretnych projektów.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Podgląd HTML określonego notesu lub pliku.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Twój identyfikator użytkownika

Podczas logowania się do notesów platformy Azure po raz pierwszy, twoje konto jest automatycznie przypisywane tymczasowy identyfikator użytkownika, takich jak "anon-idr3ca". Tak długo, jak masz identyfikator użytkownika, który zaczyna się od "anon-", Notesy platformy Azure monituje, aby zmienić go przy każdym zalogowaniu:

![Monitowanie o utworzenie identyfikatora użytkownika podczas logowania do notesów platformy Azure](media/accounts/create-user-id.png)

Obok tymczasowej nazwy użytkownika pojawi się również polecenie **Konfiguruj identyfikator** użytkownika:

![Konfigurowanie polecenia Identyfikator użytkownika, które pojawia się podczas korzystania z tymczasowego identyfikatora](media/accounts/configure-user-id-command.png)

Identyfikator użytkownika można również zmienić w dowolnym momencie na stronie profilu.

Identyfikator użytkownika musi składać się z od czterech do szesnastu liter, cyfr i łączników. Żadne inne znaki nie są dozwolone, a identyfikator użytkownika nie może rozpoczynać ani kończyć łącznikiem ani używać wielu łączników w wierszu. Ponieważ identyfikatory użytkowników są unikatowe na wszystkich kontach notesów platformy Azure, może zostać wyświetlony komunikat "Identyfikator użytkownika jest już używany". (Komunikat pojawia się również w przypadku próby użycia znaku towarowego firmy Microsoft jako identyfikatora użytkownika). W takich przypadkach wybierz inny identyfikator użytkownika.

> [!Important]
> Zmiana identyfikatora powoduje unieważnienie adresów URL, które mogły zostać udostępnione przy użyciu poprzedniego identyfikatora. Możesz zmienić swój identyfikator z powrotem na poprzedni identyfikator, aby ponownie zamienić łącze. Jednak jest możliwe dla innego użytkownika, aby ubiegać się o nieużywane identyfikator w międzyczasie.

## <a name="your-profile"></a>Twój profil

Twój profil składa się z publicznie widocznych `https://notebooks.azure.com/<user_id>`informacji pod adresem URL. Na stronie profilu znajdują się również ostatnio używane projekty i wszystkie projekty oznaczone gwiazdką.

Aby edytować swój profil, użyj polecenia **Edytuj informacje o profilu** na stronie profilu. Sekcje profilu są następujące:

| Sekcja | Spis treści |
| --- | --- |
| Zdjęcie profilowe | Obraz wyświetlany na stronie profilu. |
| Informacje o koncie | Twoja nazwa wyświetlana, identyfikator użytkownika i publiczne konto e-mail. Konto e-mail w tym miejscu zapewnia innym użytkownikom oznacza kontakt z Tobą i może się różnić od [konta](azure-notebooks-user-account.md) używanego do logowania się do samych notesów platformy Azure. |
| Informacje o profilu | Twoja lokalizacja, firma, stanowisko, strona internetowa i krótki opis siebie. |
| Profile społecznościowe | Twoje identyfikatory GItHub, Twitter i Facebook, jeśli chcesz je udostępnić. |
| Ustawienia prywatności | Udostępnia dwa polecenia:<ul><li>**Eksportuj mój profil:** tworzy i pobiera plik *zip* zawierający wszystkie informacje zapisywane przez usługi Azure Notes w profilu, w tym zdjęcie, informacje o profilu i dzienniki zabezpieczeń.</li><li>**Usuń moje konto:** Trwale usuwa wszystkie twoje dane osobowe przechowywane w notesach platformy Azure.</li></ul> |
| Włączanie funkcji witryny | Umożliwia kontrolowanie aspektów zachowania notesów platformy Azure:<ul><li>**Ujednolicony frontend dla notebooków: umożliwia szybsze uruchamianie**notesu i lepszą trwałość.</li><li>**Uruchom w JupyterLab domyślnie:** Domyślnie notesy platformy Azure zapewnia prosty interfejs użytkownika, który jest odpowiedni dla większości użytkowników. JupyterLab zapewnia bogatszy, ale bardziej skomplikowany interfejs dla doświadczonych użytkowników.</li><li>**VNext Website**: umożliwia zmodernizowany układ www pokazany w tej dokumentacji.</li></ul> |

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchamiania notesu Jupyter do regresji liniowej](tutorial-create-run-jupyter-notebook.md)
