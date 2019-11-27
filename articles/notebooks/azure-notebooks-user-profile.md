---
title: Profil użytkownika i identyfikator dla korzystanie z notesów usługi Azure
description: Tworzenie profilu użytkownika i identyfikatora użytkownika oraz zarządzanie nim przy użyciu Azure Notebooks, który jest częścią adresu URL udostępnionych notesów.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 0874fe8223ae9c49ccfe4e8efedf2620117a0f8c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277606"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Identyfikator profilu i użytkownika dla notesów usługi Azure

W obszarze Zaawansowane, współpracy notesów usługi Azure profilu użytkownika przedstawia publiczny obraz do innych osób:

[![strony profilu Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Identyfikator użytkownika jest częścią adresów URL umożliwia udostępnianie projektów i notesy. Poniższa lista zawiera opis różnych wzorców adresów URL:

- `https://notebooks.azure.com/<user_id>`: Twoja strona Twojego profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Twoje projekty. Zobacz wszystkie projekty; inni użytkownicy widzą tylko projekty publiczne.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: pliki projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: klony określonych projektów.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Podgląd HTML określonego notesu lub pliku.

## <a name="your-user-id"></a>Identyfikator użytkownika

Podczas logowania się do notesów usługi Azure po raz pierwszy, Twoje konto jest automatycznie przypisywany Identyfikatora tymczasowe użytkownika, takie jak "anon-idr3ca". Tak długo, jak długo ma identyfikator użytkownika, który rozpoczyna się od "anon-", notesy platformy Azure monituje o go zmienić po każdym zalogowaniu:

![Monit o utworzenie Identyfikatora użytkownika podczas logowania się do notesów usługi Azure](media/accounts/create-user-id.png)

W polu Nazwa użytkownika tymczasowego pojawia się również polecenie **Konfiguruj identyfikator użytkownika** :

![Konfigurowanie polecenia identyfikator użytkownika, który jest wyświetlany, gdy używasz tymczasowy identyfikator](media/accounts/configure-user-id-command.png)

Można również zmienić swój identyfikator użytkownika w dowolnym momencie na stronie profilu.

Identyfikator użytkownika musi składać się z czterech i szesnastu liter, cyfr i łączników. Inne znaki są niedozwolone, a identyfikator użytkownika nie można rozpocząć lub kończyć znakiem łącznika ani używać wiele łączników w wierszu. Ponieważ identyfikatory użytkowników są unikatowe dla wszystkich kont Azure Notebooks, może zostać wyświetlony komunikat "identyfikator użytkownika jest już w użyciu". (Komunikat jest również wyświetlany, jeśli próbujesz użyć znaku towarowego firmy Microsoft jako identyfikatora użytkownika). W takich przypadkach wybierz inny identyfikator użytkownika.

> [!Important]
> Zmiana Identyfikatora unieważnia wszelkie adresy URL mogą zostały udostępnione przy użyciu swojego identyfikatora poprzedniego. Możesz zmienić swój identyfikator powrót do poprzedniego Identyfikatora w celu ponownego zweryfikowania łącza. Jednak jest możliwe dla innego użytkownika skorzystać z nieużywane Identyfikatora, w tym samym czasie.

## <a name="your-profile"></a>Twój profil

Profil składa się z publicznie dodanych informacji w adresie URL, `https://notebooks.azure.com/<user_id>`. Strony swojego profilu pokazuje również niedawno używanych projektów i wszelkie projekty gwiazdkami.

Aby edytować profil, użyj polecenia **Edytuj informacje o profilu** na stronie Twojego profilu. Części Twojego profilu są następujące:

| Sekcja | Zawartość |
| --- | --- |
| Zdjęcie profilowe | Obraz, który jest wyświetlany na stronie profilu. |
| Informacje o koncie | Nazwa wyświetlana, identyfikator użytkownika i konto e-mail publicznych. Konto e-mail w tym miejscu daje innym użytkownikom średnią skontaktowanie się z Tobą i może się różnić od [konta](azure-notebooks-user-account.md) , którego używasz do logowania się do Azure Notebooks samego siebie. |
| Informacje o profilu | Twoja lokalizacja, firmy, stanowisko, witryny sieci web i krótki opis samodzielnie. |
| Profile społecznościowych | GItHub, Twitter i Facebook identyfikatory, jeśli chcesz udostępnić je. |
| Ustawienia prywatności | Udostępnia dwa polecenia:<ul><li>**Eksportuj mój profil**: tworzy i pobiera plik *. zip* zawierający wszystkie informacje, które Azure Notebooks zapisywane w Twoim profilu, w tym zdjęcie, informacje o profilu i dzienniki zabezpieczeń.</li><li>**Usuń moje konto**: trwale usuwa wszystkie dane osobowe przechowywane w Azure Notebooks.</li></ul> |
| Włącz funkcje lokacji | Umożliwia kontrolowanie aspektów zachowania notesy platformy Azure:<ul><li>**Ujednolicone frontony dla notesów**: umożliwiają szybsze uruchamianie notesu i lepszą trwałość.</li><li>**Domyślnie Uruchom program w JupyterLab**: domyślnie Azure Notebooks udostępnia prosty interfejs użytkownika, który jest odpowiedni dla większości użytkowników. JupyterLab zapewnia bardziej rozbudowane, ale bardziej skomplikowanych interfejs dla doświadczonych użytkowników.</li><li>**Witryna sieci Web vNext**: umożliwia korzystanie z nowoczesnego układu internetowego pokazanego w tej dokumentacji.</li></ul> |

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
